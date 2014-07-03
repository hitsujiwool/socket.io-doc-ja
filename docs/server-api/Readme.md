# サーバ API

<!--# Server API-->

原文：[http://socket.io/docs/server-api/](http://socket.io/docs/server-api/)

<!--## Server-->

## Server

`require('socket.io')` で expose できます。

<!--## Server()-->

## Server()

<!--
Creates a new Server. Works with and without new:
-->

新しい `Server` を生成します。`new` はあってもなくても動作します。

```
var io = require('socket.io')();
// or
var Server = require('socket.io');
var io = new Server();
```

<!--## Server(opts:Object)-->

## Server(opts:Object)

<!--
Optionally, the first or second argument (see below) of the Server
constructor can be an options object.
-->

オプションとして、`Server` コンストラクタの 1 番目あるいは 2 番目の引数（後述）にはオプションのオブジェクトを渡すことができます。

<!--
The following options are supported:
-->

以下のオプションがサポートされています。

<!--
- serveClient sets the value for Server#serveClient()
- path sets the value for Server#path()
-->

- `serveClient` - Server#serveClient() の値を設定します
- `path` - Server#path() の値を設定します

<!--
The same options passed to socket.io are always passed to the engine.io Server that gets created. See engine.io
options as reference.
-->

socket.io に渡されたオプションは、常に `engine.io` の `Server` にも渡されます。[engine.io のオプション](https://github.com/Automattic/engine.io#methods-1)も参照して下さい。

<!--## Server(srv:http#Server, opts:Object)-->

## Server(srv:http#Server, opts:Object)

<!--
Creates a new Server and attaches it to the given srv. Optionally
opts can be passed.
-->

新しい `Server` を生成し、引数として渡された `srv` にアタッチします。`opts` をオプションとして渡すこともできます。

<!--## Server(port:Number, opts:Object)-->

## Server(port:Number, opts:Object)

<!--
Binds socket.io to a new http.Server that listens on port.
-->

socket.io を、`port` をリッスンする新しい `http.Server` にバインドします。

<!--## Server#serveClient(v:Boolean):Server-->

## Server#serveClient(v:Boolean):Server

<!--
If v is true the attached server (see Server#attach) will serve
the client files. Defaults to true.
-->

`v` が `true` の場合、アタッチされたサーバ（`Server#attach` も見て下さい）はクライアント用のファイルも配信します。デフォルトは `true` です。

<!--
This method has no effect after attach is called.
-->

`attach` が呼び出された後は、このメソッドを実行しても意味はありません。

```
// サーバと `serveClient` オプションを渡す
var io = require('socket.io')(http, { serveClient: false });

// サーバを渡さずに、serveClient メソッドを呼び出すこともできる
var io = require('socket.io')();
io.serveClient(false);
io.attach(http);
```

<!--
If no arguments are supplied this method returns the current value.
-->

何の引数も渡さない場合は、現在の値を返します。

<!--## Server#path(v:String):Server-->

## Server#path(v:String):Server

<!--
Sets the path v under which engine.io and the static files will be
served. Defaults to /socket.io.
-->

`engine.io` やスタティックなファイルが配信されてるパスを `v` に設定します。デフォルトは `/socket.io` です。

<!--
If no arguments are supplied this method returns the current value.
-->

何の引数も渡さない場合は、現在の値を返します。

<!--## Server#adapter(v:Adapter):Server-->

## Server#adapter(v:Adapter):Server

<!--
Sets the adapter v. Defaults to an instance of the Adapter that
ships with socket.io which is memory based. See
socket.io-adapter.
-->

adapter の値を `v` として設定します。デフォルトは socket.io に付属している メモリベースの `Adapter` です。[https://github.com/Automattic/socket.io-adapter](https://github.com/Automattic/socket.io-adapter) を御覧下さい。

<!--
If no arguments are supplied this method returns the current value.
-->

何の引数も渡さない場合は、現在の値を返します。

<!--## Server#origins(v:String):Server-->

## Server#origins(v:String):Server

<!--
Sets the allowed origins v. Defaults to any origins being allowed.
-->

許可するオリジンの値を `v` として設定します。デフォルトは全てのオリジンが許可されます。

<!--
If no arguments are supplied this method returns the current value.
-->

何の引数も渡さない場合は、現在の値を返します。

<!--## Server#sockets:Namespace-->

## Server#sockets:Namespace

<!--
The default (/) namespace.
-->

デフォルトのネームスペース (`/`) を返します。

<!--## Server#attach(srv:http#Server, opts:Object):Server-->

## Server#attach(srv:http#Server, opts:Object):Server

<!--
Attaches the Server to an engine.io instance on srv with the
supplied opts (optionally).
-->

`Server` を `srv` 上の `engine.io` インスタンスにアタッチします。`opts` はオプションです。

<!--## Server#attach(port:Number, opts:Object):Server-->

## Server#attach(port:Number, opts:Object):Server

<!--
Attaches the Server to an engine.io instance that is bound to port
with the given opts (optionally).
-->

`Server` を `port` で指定されたポート番号にバインドされた engine.io インスタンスにアタッチします。`opts` はオプションです。

<!--## Server#listen-->

## Server#listen

<!--
Synonym of Server#attach.
-->

`Server#attach` と同じです。

<!--## Server#bind(srv:engine#Server):Server-->

## Server#bind(srv:engine#Server):Server

<!--
Advanced use only. Binds the server to a specific engine.io Server
(or compatible API) instance.
-->

応用的な使い方。`Server` を指定した engine.io の `Server` （あるいは同等の API を備える）インスタンスにバインドします。

<!--## Server#onconnection(socket:engine#Socket):Server-->

## Server#onconnection(socket:engine#Socket):Server

<!--
Advanced use only. Creates a new socket.io client from the incoming
engine.io (or compatible API) socket.
-->

応用的な使い方。接続された engine.io の（あるいは同等の API を備える）socket を元に新しい `socket.io` クライアントを生成します。

<!--## Server#of(nsp:String):Namespace-->

## Server#of(nsp:String):Namespace

<!--
Initializes and retrieves the given Namespace by its pathname
identifier nsp.
-->

`nsp` として与えられたパス名から一意に定まるようなネームスペースを探索・初期化します。

<!--
If the namespace was already initialized it returns it right away.
-->

ネームスペースがすでに初期化されている場合はそれを返します。

<!--## Server#emit-->

## Server#emit

<!--
Emits an event to all connected clients. The following two are
equivalent:
-->

接続している全てのクライアントにイベントを emit します。以下の 2 つは同等です。

```
var io = require('socket.io')();
io.sockets.emit('an event sent to all connected clients');
io.emit('an event sent to all connected clients');
```

<!--
For other available methods, see Namespace below.
-->

他に利用可能なメソッドについては、後述する `Namespece` の項を参照して下さい。

<!--## Server#use-->

## Server#use

<!--
See Namespace#use below.
-->

下記の `Namespace#use` を参照して下さい。

<!--## Namespace-->

## Namespace

<!--
Represents a pool of sockets connected under a given scope identified
by a pathname (eg: /chat).
-->

あるスコープの下で接続されているソケットのプールを表現します。ネームスペースはパス名によって一意に特定されます（例：`/chat`）。

<!--
By default the client always connects to /.
-->

デフォルトではクライアントは必ず `/` に接続します。

<!--### Events-->

### イベント

#### `connection` / `connect`

接続時に発生します。

パラメータ：

- `Socket` - 接続された socket

<!--## Namespace#name:String-->

## Namespace#name:String

<!--
The namespace identifier property.
-->

ネームスペースの識別子となるプロパティ

<!--## Namespace#connected:Object-->

## Namespace#connected:Object

<!--
Hash of Socket objects that are connected to this namespace indexed
by id.
-->

ネームスペースに接続している `Socket` オブジェクトを、その id をキーにして格納したハッシュ

<!--## Namespace#use(fn:Function):Namespace-->

## Namespace#use(fn:Function):Namespace

<!--
Registers a middleware, which is a function that gets executed for
every incoming Socket and receives as parameter the socket and a
function to optionally defer execution to the next registered
middleware.
-->

ミドルウェアを登録します。ミドルウェアは `Socket` が接続されるたびに実行される関数で、socket と、次に登録されたミドルウェアを実行する関数を引数に受け取ります。

```
var io = require('socket.io')();
io.use(function(socket, next){
  if (socket.request.headers.cookie) return next();
  next(new Error('Authentication error'));
});
```

<!--
Errors passed to middleware callbacks are sent as special error
packets to clients.
-->

ミドルウェアのコールバックにエラーが渡された場合、`error` パケットがクライアントに送信されます。

<!--## Socket-->

## Socket

<!--
A Socket is the fundamental class for interacting with browser
clients. A Socket belongs to a certain Namespace (by default /)
and uses an underlying Client to communicate.
-->

`Socket` はブラウザのクライアントとやり取りを行うための基盤となるクラスです。`Socket` は特定のネームスペース（デフォルトでは `/`）に所属し、背後で `Client` を用いて通信を行います。

<!--## Socket#rooms:Array-->

## Socket#rooms:Array

<!--
A list of strings identifying the rooms this socket is in.
-->

socket が所属しているルームの識別子となる文字列のリスト

<!--## Socket#client:Client-->

## Socket#client:Client

<!--
A reference to the underlying Client object.
-->

内部で用いられている `Client` オブジェクトへの参照

<!--## Socket#conn:Socket-->

## Socket#conn:Socket

<!--
A reference to the underyling Client transport connection (engine.io
Socket object).
-->

`Client` のトランスポートのコネクション（engine.io の `Socket` オブジェクト）への参照

<!--## Socket#request:Request-->

## Socket#request:Request

<!--
A getter proxy that returns the reference to the request that
originated the underlying engine.io Client. Useful for accessing
request headers such as Cookie or User-Agent.
-->

engine.io の `Client` となったリクエストへの参照。`Cookie` や `User-Agent` といったリクエストヘッダにアクセスしたい場合に有用です。

<!--## Socket#id:String-->

## Socket#id:String

<!--
A unique identifier for the socket session, that comes from the
underlying Client.
-->

socket のセッションに対する識別子。これは `Client` に由来します。

<!--## Socket#emit(name:String[, …]):Socket-->

## Socket#emit(name:String[, …]):Socket

<!--
Emits an event to the socket identified by the string name. Any
other parameters can be included.
-->

String `name` で指定した socket に対してイベントを emit します。パラメータとして任意の値を渡すことができます。

<!--
All datastructures are supported, including Buffer. JavaScript
functions can’t be serialized/deserialized.
-->

`Buffer` を始めとした全てのデータ構造に対応しています。JavaScript の関数はシリアライズ／デシリアライズできません。

```
var io = require('socket.io')();
io.on('connection', function(socket){
  socket.emit('an event', { some: 'data' });
});
```

<!--## Socket.join(name:String[, fn:Function]):Socket-->

## Socket.join(name:String[, fn:Function]):Socket

<!--
Adds the socket to the room, and fires optionally a callback fn
with err signature (if any).
-->

socket をルームに追加し、オプションでコールバック `fn` を実行します。

<!--
The socket is automatically a member of a room identified with its
session id (see Socket#id).
-->

socket は自身のセッション ID （Sokect#id を参照のこと）を識別子とするルームに自動的に入室します。

<!--
The mechanics of joining rooms are handled by the Adapter
that has been configured (see Server#adapter above), defaulting to
socket.io-adapter.
-->

事前に設定された `Adapter` （前述した `Server#adapter` を参照）によって、入室の仕組みをハンドリングする方法が決まります。デフォルトでは `socket.io-adapter` が設定されています。

<!--## Socket#leave(name:String[, fn:Function]):Socket-->

## Socket#leave(name:String[, fn:Function]):Socket

<!--
Removes the socket from room, and fires optionally a callback fn
with err signature (if any).
-->

socket をルームから削除し、オプションでコールバック `fn` を実行します。

<!--
Rooms are left automatically upon disconnection.
-->

切断時には自動的にルームから退室します。

<!--
The mechanics of leaving rooms are handled by the Adapter
that has been configured (see Server#adapter above), defaulting to
socket.io-adapter.
-->

事前に設定された `Adapter` （前述した `Server#adapter` を参照）によって、退室の仕組みをハンドリングする方法が決まります。デフォルトでは `socket.io-adapter` が設定されています。

<!--## Socket#to(room:String):Socket-->
<!--## Socket#in(room:String):Socket-->

## Socket#to(room:String):Socket
## Socket#in(room:String):Socket

<!--
Sets a modifier for a subsequent event emission that the event will
only be broadcasted to sockets that have joined the given room.
-->

後続して emit されるイベントに対して「修飾子」をつけることで、`room` で指定したルームに所属する socket にのみブロードキャストを行います。

<!--
To emit to multiple rooms, you can call to several times.
-->

複数のルームに emit するには `to` を複数回呼び出します。

```
var io = require('socket.io')();
io.on('connection', function(socket){
  socket.to('others').emit('an event', { some: 'data' });
});
```

<!--## Client-->

## Client

<!--
The Client class represents an incoming transport (engine.io)
connection. A Client can be associated with many multiplexed Socket
that belong to different Namespaces.
-->

`Client` クラスは接続しているトランスポート (engine.io) のコネクションを表現するクラスです。多重化された `Socket` が異なるネームスペースに所属する可能性があるため、1 つの `Client` に複数の  `Socket` を紐づけることができます。

<!--## Client#conn-->

## Client#conn

<!--
A reference to the underlying engine.io Socket connection.
-->

`engine.io` の `Socket` コネクションへの参照。

#Client#request

<!--
A getter proxy that returns the reference to the request that
originated the engine.io connection. Useful for accessing
request headers such as Cookie or User-Agent.
-->

engine.io の `Client` となったリクエストへの参照。`Cookie` や `User-Agent` といったリクエストヘッダにアクセスしたい場合に有用です。
