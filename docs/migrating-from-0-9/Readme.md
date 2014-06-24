# 0.9 からの移行

<!--## Migration from 0.9-->

原文：[http://socket.io/docs/migrating-from-0-9/](http://socket.io/docs/migrating-from-0-9/)

<!--
For most applications, the transition to 1.0 should be completely seamless and devoid of any hiccups. That said, we’ve done some work to streamline some APIs, and we have changed some internals, so this is a recommended read for most existing users.
-->

1.0 への以降は多くのアプリケーションにとって完全にシームレスでなくてはならず、いかなる障害も発生すべきではありません。とは言うものの、一部の API は効率化のために手を加えられ、内部のコードもまた変更されています。したがって、既存のユーザはこれを読むことを推奨します。

<!--## Authentication differences-->

## 認証方式の変更

<!--### Socket.io uses middleware now-->

### Socket.io はミドルウェアを使うようになりました

<!--
You can give a Socket.io server arbitrary functions via io.use() that are run when a socket is created. Check out this example:
-->

`io.use()` を使って任意の関数を渡すことができます。関数は Socket.io サーバがソケットの生成する際に実行されます。次の例を見て下さい。

```
var srv = http();
var sio = require('socket.io')(srv);
var run = 0;
sio.use(function(socket, next){
  run++; // 0 -&gt; 1
  next();
});
sio.use(function(socket, next) {
  run++; // 1 -&gt; 2
  next();
});
var socket = require('socket.io-client')();
socket.on('connect', function(){
  // run == 2 here
});
```

<!--
… so its cleaner to do auth via middleware now
The old io.set() and io.get() methods are deprecated and only supported for backwards compatibility. Here is a translation of an old authorization example into middleware-style.
-->

これにより、ミドルウェア経由でよりすっきりとした認証を行えます。古い `io.set()` や `io.get()` のメソッドは非推奨となり、後方互換性を維持するためのみにサポートされます。古い認証のサンプルコードをミドルウェアスタイルに書き換えたものが以下のコードです。

```
io.set('authorization', function (handshakeData, callback) {
  // make sure the handshake data looks good
  callback(null, true); // error first, 'authorized' boolean second
});
```

は

```
io.use(function(socket, next) {
  var handshakeData = socket.request;
  // make sure the handshake data looks good as before
  // if error do this:
    // next(new Error('not authorized');
  // else just call next
  next();
});
```

のように書き換えられます。

<!--### Namespace authorization?-->

### ネームスペースごとに認証するには？

```
io.of('/namespace').use(function(socket, next) {
  var handshakeData = socket.request;
  next();
});
```

<!--## Log differences-->

## ロギング方式の変更

<!--### Logging is now based on debug-->

### ロギングに debug を用いるようになりました

<!--
To print all debug logging, set the environment variable DEBUG to *. ie: DEBUG=* node index.js
-->

デバッグログを出力するには、環境変数 `DEBUG` を `*.` に設定します（例：`DEBUG=* node index.js`）。

<!--
To print only socket.io related logging: DEBUG=socket.io:* node index.js.
-->

socket.io に関連するログのみを出力するには `DEBUG=socket.io:* node index.js`

<!--

To print logging only from the socket object: DEBUG=socket.io:socket node index.js.
-->
socket オブジェクトに関連するログのみを出力するには `DEBUG=socket.io:socket node index.js.`

<!--
This pattern should hopefully be making sense at this point. The names of the files in socket.io/lib are equivalent to their debug names.
-->

このパターンは現時点では。`socket.io/lib` 以下のファイルはデバッグ名と同等です。

<!--
Debug also works in the browser; logs are persisted to localstorage.
To use: open the developer console and type debug.enable('socket.io:*') (or any debug level) and then refresh the page. Everything is logged until you run debug.disable().
-->

debug はブラウザでも動作します。ログはローカルストレージに永続化されます。
使い方：developer console を開いて `debug.enable('socket.io:*)` （もちろん別のデバッグレベルでもOK）をタイプした後、ページを更新します。`debug.disable()` を実行しない限り、全てのログが記録されます。

<!--
See more at the debug documentation here.
-->
デバッグについての詳細なドキュメンテーションは[こちら](https://www.npmjs.org/package/debug)を参照して下さい。

<!--## Shortcuts-->

## ショートカット

<!--
In general there are some new shortcuts for common things. The old versions should still work, but shortcuts are nice.
-->

<!--### Broadcasting to all clients in default namespace-->

### デフォルトのネームスペースに接続している全クライアントにブロードキャストする

<!-- Previously:-->

これまでは

```
io.sockets.emit('eventname', 'eventdata');
```

現在は

```
io.emit('eventname', 'eventdata');
```

<!--
Neat. Note that in both cases, these messages reach all clients connected to the default ‘/’ namespace, but not clients in other namespaces.
-->

どちらの場合でも、デフォルトの `/` のネームスペースに接続している全てのクライアントにメッセージが送信されますが、それ以外のネームスペースにいるクライアントには送信されません。

<!--### Starting the server-->

### サーバを起動する

<!--Previously:-->

これまでは

```
var io = require('socket.io');
var socket = io.listen(80, { /* options */ });
```

<!-- Now: -->

現在は

```
var io = require('socket.io');
var socket = io({ /* options */ });
```

<!--## Exposed Events-->

## イベント名の予約語

<!--
Some of the reserved events from before seem to be missing from 1.0.
-->

これまで予約されていたイベント名のいくつかは、1.0 で削除されました。

<!--
Those include: 'connecting', 'connect_failed', 'reconnect_failed', 'reconnect', and 'reconnecting'.
The newer architecture aims to simplify the connection / reconnection process and reduce the number of reserved events to prevent collision.
-->

これらは `'connecting'`, `'connect_failed'`, `'reconnect_failed'`, `'reconnect'`, `'reconnecting'` です。新しいアーキテクチャは、接続／再接続のプロセスを簡素化し、予約されたイベント名の数を減らして衝突を防ぐことを目指しています。

<!--
On a reconnection now instead of 'reconnect' another 'connect' event is fired instead.
-->

再接続時には `reconnect` に代わって `connect` イベントが再発火するようになりました。

<!--
As for the '(re)connecting' and '(re)connect_failed' socket events: connection failure can probably be assumed from lack of successful connection, and the same can be said for connecting.
-->

今のところ、socketの `(re)connect_failed` については、成功したコネクションがないことからコネクションの失敗を推測できます。`(re)connecting` についても同様のことが言えます。

<!--
The socket.io-client manager (lib/manager.js) emits 'connect_error', 'connect_timeout', 'open', 'close', 'reconnect_failed', 'reconnect_attempt', and 'reconnect_error' events via Emitter (not through socket.io). You can subscribe to these by providing a manager to sockets and listening like so:
-->

socket.io-client manager (lib/manager.js) は `connect_error`, `connect_timeout`, `open`, `close`, `reconnect_failed`, `reconnect_attempt`, `reconnect_error` のイベントを Emitter 経由で emit します（socket.io 経由ではありません）。これらのイベントを受け取るには、socket に manager を渡して、以下のようにリッスンします。

```
var manager = io.Manager('url', { /* options */ });
var socket = manager.socket('/namespace');
manager.on('event_name_like_reconnect_attempt', function() {

});
```

<!--## Configuration differences-->

## 設定方法の変更

<!--### io.set is gone-->
io.set は使わなくなりました。

<!--
Instead do configuration in server initialization like this:
-->

代わりにサーバの初期化時に以下のように設定します。

```
var socket = require('socket.io')({
  // options go here
});
```

<!--
Options like log-level are gone. io.set('transports'), io.set('heartbeat interval'), io.set('heartbeat timeout', and io.set('resource') are still supported for backwards compatibility.
-->

`log-level` といったオプションは廃止されましたが、`io.set('transports')`, `io.set('heartbeat interval')`, `io.set('heartbeat timeout'`, `io.set('resource')` は、後方互換性のために引き続きサポートされます。

<!--## Parser / Protocol differences-->

## パーザ／プロトコルの変更

<!--
This is only relevant for updating things like socket.io implementations in other languages, custom socket.io clients, etc.
-->

他の言語での socket.io 実装や、自作の socket.io クライアントなどをアップグレードしたい場合のみに関連する話題です。

<!--### Difference 1 – packet encoding-->

### 変更その 1 - パケットのエンコード

<!--
Parsing is now class based and asynchronous. Instead of returning a single encoded string, encode calls callback with an array of encodings as the only argument. Each encoding should be written to the transport in order. This is more flexible and makes binary data transport work. Here’s an example:
-->

パーズはクラスベースで非同期になりました。エンコードされた 1 つの文字列を返す代わりに、`encode()` はコールバックに対して、エンコードされたパケットを要素とする配列を渡して呼び出します。各パケットは順序を保ったままトランスポートに書き込まれるべきです。より柔軟で、バイナリデータの転送にも対応できるようになりました。以下に例を挙げます。

```
var encoding = parser.encode(packet);
console.log(encoding); // fully encoded packet
```

<!--vs.-->

は

```
var encoder = new parser.Encoder();
encoder.encode(packet, function(encodings) {
  for (var i = 0; i &lt; encodings.length; i++) {
    console.log(encodings[i]); // encoded parts of the packet
  }
});
```

となります。

<!--### Difference 2 – packet decoding-->

### 変更その 2 - パケットのデコード

<!--
Decoding takes things a step further and is event-based. This is done because some objects (binary-containing) are both encoded and decoded in multiple parts. This example should help:
-->

デコードのプロセスは以前よりも踏み込んだ、イベントベースなものとなりました。というのも一部の（バイナリを含んだ）オブジェクトは、分割された状態でエンコード／デコードされるからです。

```
var packet = parser.decode(decoding);
console.log(packet); // formed socket.io packet to handle
```

<!--vs.-->

は

```
var decoder = new parser.Decoder();
decoder.on('decoded', function(packet) {
  console.log(packet); // formed socket.io packet to handle
});
decoder.add(encodings[0]); // say encodings is array of two encodings received from transport
decoder.add(encodings[1]); // after adding the last element, 'decoded' is emitted from decoder
```

となります。
