# 使い方

<!--# How to use-->

原文：[http://socket.io/docs/](http://socket.io/docs/)

<!--## Installing-->

## インストール

```
$ npm install socket.io
```

<!-- ## Using with Node http server-->

## Node の http サーバと一緒に使う

<!--### Server (app.js)-->

### サーバ (app.js)

```
var app = require('http').createServer(handler)
var io = require('socket.io')(app);
var fs = require('fs');

app.listen(80);

function handler (req, res) {
  fs.readFile(__dirname + '/index.html',
  function (err, data) {
    if (err) {
      res.writeHead(500);
      return res.end('Error loading index.html');
    }

    res.writeHead(200);
    res.end(data);
  });
}

io.on('connection', function (socket) {
  socket.emit('news', { hello: 'world' });
  socket.on('my other event', function (data) {
    console.log(data);
  });
});
```

<!--### Client (index.html)-->

### クライアント (index.html)

```
<script src="/socket.io/socket.io.js"></script>
<script>
  var socket = io('http://localhost');
  socket.on('news', function (data) {
    console.log(data);
    socket.emit('my other event', { my: 'data' });
  });
</script>
```

<!--## Using with Express 3/4-->

## Express 3/4 と一緒に使う

<!--### Server (app.js)-->

### サーバ (app.js)

```
var app = require('express')();
var server = require('http').Server(app);
var io = require('socket.io')(server);

server.listen(80);

app.get('/', function (req, res) {
  res.sendfile(__dirname + '/index.html');
});

io.on('connection', function (socket) {
  socket.emit('news', { hello: 'world' });
  socket.on('my other event', function (data) {
    console.log(data);
  });
});
```

<!--Client (index.html)-->

### クライアント (index.html)

```
<script src="/socket.io/socket.io.js"></script>
<script>
  var socket = io.connect('http://localhost');
  socket.on('news', function (data) {
    console.log(data);
    socket.emit('my other event', { my: 'data' });
  });
</script>
```

<!--## Using with the Express framework-->

## Express 2 と一緒に使う

<!--### Server (app.js)-->

### サーバ (app.js)

```
var app = require('express').createServer();
var io = require('socket.io')(app);

app.listen(80);

app.get('/', function (req, res) {
  res.sendfile(__dirname + '/index.html');
});

io.on('connection', function (socket) {
  socket.emit('news', { hello: 'world' });
  socket.on('my other event', function (data) {
    console.log(data);
  });
});
```

<!--Client (index.html)-->

### クライアント (index.html)

```
<script src="/socket.io/socket.io.js"></script>
<script>
  var socket = io.connect('http://localhost');
  socket.on('news', function (data) {
    console.log(data);
    socket.emit('my other event', { my: 'data' });
  });
</script>
```

<!--## Sending and receiving events-->

## イベントを送信／受信する

<!--
Socket.IO allows you to emit and receive custom events. Besides connect, message and disconnect, you can emit custom events:
-->

Socket.IO ではカスタムイベントを送信／受信することができます。`connect`, `message`, `disconnect` に加えて、独自のイベントを emit できます。

<!--### Server-->

### サーバ

```
// io.listen(<port>) が http サーバを立ち上げることに注意
var io = require('socket.io')(80);

io.on('connection', function (socket) {
  io.emit('this', { will: 'be received by everyone'});

  socket.on('private message', function (from, msg) {
    console.log('I received a private message by ', from, ' saying ', msg);
  });

  socket.on('disconnect', function () {
    io.sockets.emit('user disconnected');
  });
});
```

<!--## Restricting yourself to a namespace-->

## ネームスペースで区切る

<!--
If you have control over all the messages and events emitted for a particular application, using the default / namespace works. If you want to leverage 3rd-party code, or produce code to share with others, socket.io provides a way of namespacing a socket.
-->

もしあるアプリケーションで emit される全てのイベントを管理したいのであれば、デフォルトのネームスペースである `/` を使えば事足ります。もしサードパーティのコードを利用したり、他の人と共有するためのコードを書きたい場合には、socket.io は socket をネームスペースで区切る機能を提供しています。

<!--
This has the benefit of multiplexing a single connection. Instead of socket.io using two WebSocket connections, it’ll use one.
-->

こうすることで、1 つのコネクションの「多重化」の恩恵を受けることができます。2 つの WebSocket コネクションを使わずに、1 つで済ますことができます。

<!-- ### Server (app.js)-->

### サーバ (app.js)

```
var io = require('socket.io').listen(80);
var chat = io
  .of('/chat')
  .on('connection', function (socket) {
    socket.emit('a message', {
        that: 'only'
      , '/chat': 'will get'
    });
    chat.emit('a message', {
        everyone: 'in'
      , '/chat': 'will get'
    });
  });

var news = io
  .of('/news')
  .on('connection', function (socket) {
    socket.emit('item', { news: 'item' });
  });
```

<!--Client (index.html)-->

### クライアント (index.html)

```
<script>
  var chat = io.connect('http://localhost/chat')
    , news = io.connect('http://localhost/news');
  
  chat.on('connect', function () {
    chat.emit('hi!');
  });
  
  news.on('news', function () {
    news.emit('woot');
  });
</script>
```

<!--### Sending volatile messages-->

### volatile なメッセージを送信する

<!--
Sometimes certain messages can be dropped. Let’s say you have an app that shows realtime tweets for the keyword bieber.
-->

ときには、あるメッセージが脱落してしまうことがあります。たとえば `bieber` キーワードで検索したツイートをリアルタイムで表示するアプリケーションを考えます。

<!--
If a certain client is not ready to receive messages (because of network slowness or other issues, or because he’s connected through long polling and is in the middle of a request-response cycle), if he doesn’t receive ALL the tweets related to bieber your application won’t suffer.
-->

もしあるクライアントがメッセージを受け取る準備ができていないとき（たとえばネットワーク周りで遅延などが発生した場合や、クライントがポーリングで接続しており、リクエストーレスポンスのサイクルの間にいるときなど）、クライアントが `bieber` を含むツイートを必ずしも「全て」受け取る必要がないときもあるかもしれません。

<!--
In that case, you might want to send those messages as volatile messages.
-->

そのような場合には、メッセージを volatile なものとして送信すると良いかもしれません。

<!--### Server-->

### サーバ

```
var io = require('socket.io').listen(80);

io.sockets.on('connection', function (socket) {
  var tweets = setInterval(function () {
    getBieberTweet(function (tweet) {
      socket.volatile.emit('bieber tweet', tweet);
    });
  }, 100);

  socket.on('disconnect', function () {
    clearInterval(tweets);
  });
});
```

<!--## Sending and getting data (acknowledgements)-->

## データを送信／受信する（確認付き）

<!--
Sometimes, you might want to get a callback when the client confirmed the message reception.
-->

クライアントがメッセージの受信を確認した場合に、コールバックを受け取ることもできます。

<!--
To do this, simply pass a function as the last parameter of .send or .emit. What’s more, when you use .emit, the acknowledgement is done by you, which means you can also pass data along:
-->

単に `.send` や `.emit` の最後の引数に関数を渡すだけでこれを実現することができます。`.emit` の場合には、確認はあなた自身で行なう必要があります。すなわち、以下のようにデータを渡すことができます。

<!--### Server (app.js)-->

### サーバ (app.js)

```
var io = require('socket.io').listen(80);

io.sockets.on('connection', function (socket) {
  socket.on('ferret', function (name, fn) {
    fn('woot');
  });
});
Client (index.html)
<script>
  var socket = io(); // TIP: io() with no args does auto-discovery
  socket.on('connect', function () { // TIP: you can avoid listening on `connect` and listen on events directly too!
    socket.emit('ferret', 'tobi', function (data) {
      console.log(data); // data will be 'woot'
    });
  });
</script>
```

<!--## Broadcasting messages-->

## メッセージをブロードキャストする

<!--
To broadcast, simply add a broadcast flag to emit and send method calls. Broadcasting means sending a message to everyone else except for the socket that starts it.
-->

`emit` や `send` の呼び出し時に `broadcast` フラグを立てるだけで、メッセージをブロードキャストできます。ブロードキャストとは、自分自身であるところの socket 以外の全ての人にメッセージを送信することを指します。

<!--### Server-->

```
var io = require('socket.io').listen(80);

io.sockets.on('connection', function (socket) {
  socket.broadcast.emit('user connected');
});
```

<!--## Using it just as a cross-browser WebSocket-->

## クロスブラウザ対応の WebSocket として利用する

<!--
If you just want the WebSocket semantics, you can do that too. Simply leverage send and listen on the message event:
-->

WebSocket のセマンティクスを使うだけ、という使い方も可能です。`send` を使ったり、`message` イベントをリッスンするだけです。

<!--### Server (app.js)-->

### サーバ (app.js)

```
var io = require('socket.io').listen(80);

io.sockets.on('connection', function (socket) {
  socket.on('message', function () { });
  socket.on('disconnect', function () { });
});
```

<!--### Client (index.html)-->

### クライアント (index.html)

```
<script>
  var socket = io('http://localhost/');
  socket.on('connect', function () {
    socket.send('hi');

    socket.on('message', function (msg) {
      // my msg
    });
  });
</script>
```

<!--
If you don’t care about reconnection logic and such, take a look at Engine.IO, which is the WebSocket semantics transport layer Socket.IO uses.
-->

もし再接続のロジックなども不要な場合は、[Engine.IO](https://github.com/Automattic/engine.io) に目を通してみて下さい。Socket.IO で使われている、WebSocket のセマンティクスに対応したトランスポート層です。
