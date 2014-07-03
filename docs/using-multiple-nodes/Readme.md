# 複数のノードで利用する

<!--Using multiple nodes-->

原文：[http://socket.io/docs/using-multiple-nodes/](http://socket.io/docs/using-multiple-nodes/)

<!--#Sticky load balancing-->

## Sticky なロードバランシング

<!--
If you plan to distribute the load of connections among different processes or machines, you have to make sure that requests associated with a particular session id connect to the process that originated them.
-->

一連のコネクションを複数のプロセスやマシンに分散させたい場合には、あるセッション ID に関連するリクエストが、それらを開始したプロセスに対して送信されることを保証しなればなりません。

<!--
This is due to certain transports like XHR Polling or JSONP Polling relying on firing several requests during the lifetime of the “socket”.
-->

というのも、XHR Polling や JSONP Polling といったトランスポートは「ソケット」の生存サイクルの間に複数のリクエストを送信することで成立しているからです。

<!--
To illustrate why this is needed, consider the example of emitting an event to all connected clients:
-->

このような仕組みが必要な理由を説明する 1 つの例として、接続している全てのクライアントにイベントを emit する例を見てみます。

```
io.emit('hi', 'all sockets');
```

<!--
Chances are that some of those clients might have an active bi-directional communication channel like WebSocket that we can write to immediately, but some of them might be using long-polling.
-->

もしクライアントが WebSocket のような有効な双方向のコミュニケーションチャネルを持っていた場合、書き込みは即座に行なわれます。けれども、一部のクライアントは long-polling を使用するかもしれません。

<!--
If they’re using long polling, they might or might not have sent a request that we can write to. They could be “in between” those requests. In those situations, it means we have to buffer messages in the process. In order for the client to successfully claim those messages when he sends his request, the easiest way is for him to connect to be routed to that same process.
-->

クライアントが long-polling を使う場合、メッセージを書き込むためのリクエストがもう送られているか、まだ送られていないのかはわかりません。でも本当は、クライアントはリクエストの「間」にも存在してほしいわけです。そのような場合には、メッセージをプロセスの中にバッファしておく必要があります。クライアントのリクエスト時にそれらのメッセージを正しく受け取るための方法として最も楽なのは、クライアントが同じプロセスに接続されるようルーティングしてやる方法です。

<!--
An easy way to do that is by routing clients based on their originating address. An example follows using the NginX server:
-->

簡単なのは、クライアントをアクセス元のアドレスに基づいてルーティングしてしまう方法です。以下は NginX を用いた例です。

<!--#NginX configuration-->

## NginX の設定

<!--
Within the http { } section of your nginx.conf file, you can declare a upstream section with a list of Socket.IO process you want to balance load between:
-->

`nginx.conf` にある `http { }` のセクション内に、`upstream` セクションを宣言して、ロードバランシングしたい Socket.IO のプロセスを列挙することができます。

```
upstream io_nodes {
  ip_hash;
  server 127.0.0.1:6001;
  server 127.0.0.1:6002;
  server 127.0.0.1:6003;
  server 127.0.0.1:6004;
}
```

<!--
Notice the ip_hash instruction that indicates the connections will be sticky.
-->

注目すべきは、`ip_hash` という指定で、これによってコネクションが sticky になります。

<!--
In the same http { } section, you can declare a server { } that points to this upstream. In order for NginX to support and forward the WebSocket protocol, we explicitly pass along the required Upgrade headers:
-->

同じ `http { }` のセクションに、この upstream の方を向くような `server { }` を宣言することができます。NginX を `WebSocket` のプロトコルに対応させるためには、明示的に `Upgrade` ヘッダを渡します。

```
server {
  listen 3000;
  server_name io.yourhost.com;
  location / {
    proxy_set_header Upgrade $http_upgrade;
    proxy_set_header Connection "upgrade";
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_set_header Host $host;
    proxy_http_version 1.1;
    proxy_pass http://io_nodes;
  }
}
```

<!--
Make sure you also configure worker_processes in the topmost level to indicate how many workers NginX should use. You might also want to look into tweaking the worker_connections setting within the events { } block.
-->

`worker_process` の設定をトップレベルに記述することで、NginX が使うワーカ数を指定するのも忘れないで下さい。`events { }` ブロックの中にある `worker_connections` の設定の変更を検討してみるのもよいかもしれません。

<!--#Using Node.JS Cluster-->

## Node.JS Cluster を利用する

<!--
Just like NginX, Node.JS comes with built-in clustering support through the cluster module.
-->

NginX と同様に、Node.js では組み込みの `cluster` モジュールによるクラスタリングをサポートしています。

<!--
Fedor Indutny has created a module called sticky session that ensures file descriptors (ie: connections) are routed based on the originating remoteAddress (ie: IP).
-->

Fedor Indutny が作った [stick session](https://github.com/indutny/sticky-session) というモジュールを使えば、ファイルディスクリプタ（たとえばコネクション）は、元のリモートアドレス（たとえば IP）に基づいてルーティングされます。

<!--#Passing events between nodes-->

## ノード間でイベントをやりとりする

<!--
Now that you have multiple Socket.IO nodes accepting connections, if you want to broadcast events to everyone (or even everyone in a certain room) you’ll need some way of passing messages between processes or computers.
-->

さて、接続を受け取る複数の Socket.IO ノードの準備ができたわけですが、もし全員（あるいは特定のルームにいる全員）にイベントをブロードキャストしたい場合、何らかの方法を使ったプロセス同士／コンピュータ同士のメッセージのやりとりが必要になります。

<!--
The interface in charge of routing messages is what we call the Adapter. You can implement your own on top of the socket.io-adapter (by inheriting from it) or you can use the one we provide on top of Redis: socket.io-redis:
-->

Socket.IO では、メッセージのルーティングに対して責任を持つようなインターフェースを `Adapter` と呼んでいます。[socket.io-adapter](https://github.com/automattic/socket.io-adapter) を継承することで、自分だけの adapter を実装したり、[Redis](http://redis.io/) 上で実装された [socket.io-redis](https://github.com/automattic/socket.io-redis) が提供されているので、これを利用するのもよいでしょう。

```
var io = require('socket.io')(3000);
var redis = require('socket.io-redis');
io.adapter(redis({ host: 'localhost', port: 6379 }));
```

<!--
If you want to pass messages to it from non-socket.io processes, you should look into “Sending messages from the outside-world”.
-->

もし socket.io 以外のプロセスからメッセージを送信したい場合には「[外の世界からメッセージを送信する](http://socket.io/docs/rooms-and-namespaces/#sending-messages-from-the-outside-world)」をご覧下さい。
