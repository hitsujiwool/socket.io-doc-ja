# クライアント API

<!--# Client API-->

原文：[http://socket.io/docs/client-api/](http://socket.io/docs/client-api/)

<!--## IO(url:String, opts:Object):Socket-->

## IO(url:String, opts:Object):Socket

<!--
Exposed as the io namespace in the standalone build, or the result
of calling require('socket.io-client').
-->

スタンドアローンなビルドの場合は、`io` という名前空間に、あるいは `require('socket.io-client')` で expose されます。

<!--
When called, it creates a new Manager for the given URL, and attempts
to reuse an existing Manager for subsequent calls, unless the
multiplex option is passed with false.
-->

呼び出されると、与えられた URL に対して新しい `Manager` を生成します。`multiplex` オプションが `false` に設定されていないかぎり、次回以降の呼び出しでは、既に存在する `Manager` を使い回そうとします。

<!--
The rest of the options are passed to the Manager constructor (see below
for details).
-->

それ以外のオプションは `Manager` コンストラクタに渡されます（詳細については以下を参照のこと）。

<!--
A Socket instance is returned for the namespace specified by the
pathname in the URL, defaulting to /. For example, if the url is
http://localhost/users, a transport connection will be established to
http://localhost and a Socket.IO connection will be established to
/users.
-->

返り値である `Socket` インスタンスは URL の pathname によって定められるネームスペースに対応しています。デフォルトのネームスペースは `/` です。たとえば `url` として `http://localhost/users` が渡された場合、トランスポートのコネクションは `http://localhost` に、Socket.IO のコネクションは `/users` として確立されます。

<!-- ## IO#protocol-->

## IO#protocol

<!--
Socket.io protocol revision number this client works with.
-->

クライアントが対応している Socket.io プロトコルのバージョン

<!--## IO#Socket-->

<!--
Reference to the Socket constructor.
-->

`Socket` コンストラクタへの参照

<!--#IO#Manager-->

<!--
Reference to the Manager constructor.
-->

`Manager` コンストラクタへの参照

<!--## IO#Emitter-->

## IO#Emitter

<!--
Reference to the Emitter constructor.
-->

`Emitter` コンストラクタへの参照

<!--## Manager(url:String, opts:Object)-->

## Manager(url:String, opts:Object)

<!--
A Manager represents a connection to a given Socket.IO server. One or
more Socket instances are associated with the manager. The manager
can be accessed through the io property of each Socket instance.
-->

`Manager` は指定された Socket.IO サーバへのコネクションを表現します。1 つ以上の `Socket` インスタンスが manager に紐づきます。manager は `Socket` インスタンスの `io` プロパティからアクセスできます。

<!--
The opts are also passed to engine.io upon initialization of the
underlying Socket.
-->

`opts` の値は `engine.io` 内部の Socket 初期化時に渡されます。

<!--
Options:
-->

オプション

<!--
- reconnection whether to reconnect automatically (true)
- reconnectionDelay how long to wait before attempting a new
reconnection (1000)
- reconnectionDelayMax maximum amount of time to wait between
reconnections (5000). Each attempt increases the reconnection by
the amount specified by reconnectionDelay.
- timeout connection timeout before a connect_error
and connect_timeout events are emitted (20000)
-->

- `reconnection` - 再接続を自動的に行うかどうか（デフォルトは `true`）
- `reconnectionDelay` - 再接続を行う前の待ち時間（デフォルトは `10000`）
- `reconnectionDelayMax` - ある再接続の試行から次の再接続の試行までの最大間隔（デフォルトは `5000`）。再接続ごとに `reconnectionDelay` で指定した分だけ間隔が長くなります
- `timeout` - `connect_error` や `connect_timeout` イベントが発生するまでのタイムアウト（デフォルトは `20000`）

<!--### Events-->

### イベント

<!--
connect. Fired upon a successful connection.
connect_error. Fired upon a connection error.
Parameters:
Object error object
connect_timeout. Fired upon a connection timeout.
reconnect. Fired upon a successful reconnection.
Parameters:
Number reconnection attempt number
reconnect_attempt. Fired upon an attempt to reconnect.
reconnecting. Fired upon an attempt to reconnect.
Parameters:
Number reconnection attempt number
reconnect_error. Fired upon a reconnection attempt error.
Parameters:
Object error object
reconnect_failed. Fired when couldn’t reconnect within reconnectionAttempts
The events above are also emitted on the individual sockets that
reconnect that depend on this Manager.
-->

#### `connect`

接続成功時に発生するイベント

#### `connect_error`

接続エラー時に発生するイベント

パラメータ

- `Object` - エラーオブジェクト

#### `connect_timeout`

接続タイムアウト時に発生するイベント

#### `reconnect`

再接続の成功時に発生するイベント

パラメータ

- `Number` - 再接続の試行回数

#### `reconnect_attempt`

再接続の試行時に発生するイベント

#### `reconnecting`

再接続の試行時に発生するイベント

パラメータ

- `Number` - 再接続の試行回数

#### `reconnect_error`

再接続の失敗時に発生するイベント

パラメータ

- `Object` - エラーオブジェクト

### `reconnect_failed`

`reconnectionAttempts` で指定した試行回数の内に再接続ができなかった場合に発生するイベント

`Manager` に従属するそれぞれの socket もまた上記のイベントを発生させます。

<!--## Manager#reconnection(v:Boolean):Manager-->

## Manager#reconnection(v:Boolean):Manager

<!--
Sets the reconnection option, or returns it if no parameters
are passed.
-->

`reconnection` オプションの値を指定します。何の引数も渡さない場合は、現在の値を返します。

<!--## Manager#reconnectionAttempts(v:Boolean):Manager-->

## Manager#reconnectionAttempts(v:Boolean):Manager

<!--
Sets the reconnectionAttempts option, or returns it if no parameters
are passed.
-->

`reconnectionAttempts` オプションの値を指定します。何の引数も渡さない場合は、現在の値を返します。

<!--## Manager#reconnectionDelay(v:Boolean):Manager-->

## Manager#reconnectionDelay(v:Boolean):Manager

<!--
Sets the reconectionDelay option, or returns it if no parameters
are passed.
-->

`reconnectionDelay` オプションの値を指定します。何の引数も渡さない場合は、現在の値を返します。

<!--## Manager#reconnectionDelayMax(v:Boolean):Manager-->

## Manager#reconnectionDelayMax(v:Boolean):Manager

<!--
Sets the reconectionDelayMax option, or returns it if no parameters
are passed.
-->

`reconnectionDelayMax` オプションの値を指定します。何の引数も渡さない場合は、現在の値を返します。

<!--## Manager#timeout(v:Boolean):Manager-->

## Manager#timeout(v:Boolean):Manager

<!--
Sets the timeout option, or returns it if no parameters
are passed.
-->

`timeout` オプションの値を指定します。何の引数も渡さない場合は、現在の値を返します。

<!--## Socket-->

## Socket

<!--### Events-->

### イベント

<!--
connect. Fired upon connecting.
error. Fired upon a connection error
Parameters:
Object error data
disconnect. Fired upon a disconnection.
reconnect. Fired upon a successful reconnection.
Parameters:
Number reconnection attempt number
reconnect_attempt. Fired upon an attempt to reconnect.
reconnecting. Fired upon an attempt to reconnect.
Parameters:
Number reconnection attempt number
reconnect_error. Fired upon a reconnection attempt error.
Parameters:
Object error object
reconnect_failed. Fired when couldn’t reconnect within reconnectionAttempts
-->

#### `connect`

接続時に発生するイベント

#### `error`

接続エラー時に発生するイベント

パラメータ

- `Object` - エラーデータ

#### `disconnect`

切断時に発生するイベント

#### `reconnect`

再接続成功時に発生するイベント

パラメータ

- `Number` - 再接続の試行回数

#### `reconnect_attempt`

再接続の試行時に発生するイベント

#### `reconnecting`

再接続の試行時に発生するイベント

パラメータ

- `Number` - 再接続の試行回数

#### `reconnect_error`

再接続の失敗時に発生するイベント

パラメータ

- `Object` - エラーオブジェクト

### `reconnect_failed`

`reconnectionAttempts` で指定した試行回数の内に再接続ができなかった場合に発生するイベント
