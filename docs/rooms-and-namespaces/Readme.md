# ネームスペース

<!--# Namespaces-->

原文：[http://socket.io/docs/rooms-and-namespaces/](http://socket.io/docs/rooms-and-namespaces/)

<!--
Socket.IO allows you to “namespace” your sockets, which essentially means assigning different endpoints or paths.
-->

Socket.IO を使うと、socket を「ネームスペース」に区切ること、内部的には別個のエンドポイントやパスを割り当てることができます。

<!--
This is a useful feature to minimize the number of resources (TCP connections) and at the same time separate concerns within your application by introducing separation between communication channels.
-->

ネームスペースの機能は リソース数（TCP のコネクション数）を最小化し、同時に、コミュニケーションチャネルを分離することで、アプリケーション内での関心の分離に役立ちます。

<!--## Default namespace-->

## デフォルトのネームスペース

<!--
We call the default namespace / and it’s the one Socket.IO clients connect to by default, and the one the server listens to by default.
-->

Socket.IO におけるデフォルトのネームスペースは `/` です。クライアントはデフォルトでここに接続し、サーバもデフォルトでリッスンします。

<!--
This namespace is identified by io.sockets or simply io:
-->

このネームスペースは `io.sockets` あるいは単に `io` によって識別されます。

```javascript
// the following two will emit to all the sockets connected to `/`
io.sockets.emit('hi', 'everyone');
io.emit('hi', 'everyone'); // short form
```

<!--
Each namespace emits a connection event that receives each Socket instance as a parameter
-->

それぞれのネームスペースは `Socket` インスタンスをコールバックの引数に取る `connection` イベントを emit します。

```javascript
io.on('connection', function(socket){
  socket.on('disconnect', function(){ });
});
```

<!--## Custom namespaces-->

## 独自のネームスペース

<--
To set up a custom namespace, you can call the of function on the server-side:
-->

独自のネームスペースを設定する場合には、サーバサイドで `of` を呼び出します。

```javascript
var nsp = io.of('/my-namespace');
nsp.on('connection', function(socket){
  console.log('someone connected'):
});
nsp.emit('hi', 'everyone!');
```

<!--
On the client side, you tell Socket.IO client to connect to that namespace:
-->

クライアントサイドでは、そのネームスペースに接続するようSocket.IO のクライアントに指定してやります。

```javascript
var socket = io('/my-namespace');
```

<!--
Important note: The namespace is an implementation detail of the Socket.IO protocol, and is not related to the actual URL of the underlying transport, which defaults to /socket.io/….
-->

重要：ネームスペースは Socket.IO のプロトコルの実装の話なので、デフォルトで `/socket.io/...` が指定された、トランスポートが使用する実際の URL とは関係ありません。

<!--## Rooms-->

# ルーム

<!--
Within each namespace, you can also define arbitrary channels that sockets can join and leave.
-->

それぞれのネームスペース内に、socket が入室／退室できるような任意のチャネルを定義することができます。

<!--## Joining and leaving-->

## 入室と退室

<!--
You can call join to subscribe the socket to a given channel:
-->

socket に特定のチャネルを購読させるには、`join` を呼び出します。

```javascript
io.on('connection', function(socket){
  socket.join('some room');
});
```

<!--
And then simply use to or in (they are the same) when broadcasting or emitting:
-->

ブロードキャストしたり emit する場合には `to` や `in` （両者は同じものです）を用います。

```javascript
io.to('some room').emit('some event'):
```

<!--
To leave a channel you call leave in the same fashion as join.
-->

チャネルから退室するには `join` と同様のやり方で `leave` を呼び出します。

<!--## Default room-->

## デフォルトのルーム

<!--
Each Socket in Socket.IO is identified by a random, unguessable, unique identifier Socket#id. For your convenience, each socket automatically joins a room identified by this id.
-->

Socket.IO 内の socket は、`Socket#id` というランダムで推測不可能なユニークな識別子によって一意に特定されます。便宜上、socket は自動的に、この ID と同名のルームに入室したことになります。

<!--
This makes it easy to broadcast messages to other sockets:
-->

こうすることで、他の socket に対してメッセージを簡単にブロードキャストできます。

```javascript
io.on('connection', function(socket){
  socket.on('say to someone', function(id, msg){
    socket.broadcast.to(id).emit('my message', msg);
  });
});
```

<!--## Disconnection-->

## 切断

<!--
Upon disconnection, sockets leave all the channels they were part of automatically, and no specially teardown is needed on your part.
-->

切断時にはsocket は入室している全てのチャネルから退室するため、特に teardown を指定する必要はありません。

<!--## Sending messages from the outside-world-->

## 外の世界からメッセージを送る

<!--
In some cases, you might want to emit events to sockets in Socket.IO namespaces / rooms from outside the context of your Socket.IO processes.
-->

時には、Socket.IO のネームスペース／ルーム内の socket に対して、Socket.IO のプロセスの外側のコンテキストからイベントを emit したい場合もあるでしょう。

<!--
There’s several ways to tackle this problem, like implementing your own channel to send messages into the process.
-->

この問題に対してはいくつかの解決策があります。たとえば、プロセスにメッセージを送信するようなチャネルを自力で実装する方法です。

<!--
To facilitate this use case, we created two modules:
-->

このようなユースケースに対応するために、2 つのモジュールが用意されています。

- [socket.io-redis](https://github.com/automattic/socket.io-redis)
- [socket.io-emitter](https://github.com/automattic/socket.io-emitter)

<!--
By implementing the Redis Adapter:
-->

Redis の `Adapter` を実装すると

```javascript
var io = require('socket.io')(3000);
var redis = require('socket.io-redis');
io.adapter(redis({ host: 'localhost', port: 6379 }));
```

<!--
you can then emit messages from any other process to any channel
-->

好きなプロセスから好きなチャネルに対してメッセージを emit できるようになります。

```javascript
var io = require('socket.io-emitter')();
setInterval(function(){
  io.emit('time', new Date);
}, 5000);
```
