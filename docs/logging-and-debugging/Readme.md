# ロギングとデバッグ

<!--# Logging and Debugging-->

原文：[http://socket.io/docs/logging-and-debugging/](http://socket.io/docs/logging-and-debugging/)

<!--
Socket.IO is now completely instrumented by a minimalistic yet tremendously powerful utility called debug by TJ Holowaychuk.
-->

Socket.IO は TJ Holowaychuk の作成した [debug](https://github.com/visionmedia/debug) によって完璧に計測されます。ミニマムでしかも本当にパワフルなユーティリティです。

<!--
Before 1.0, the Socket.IO server would default to logging everything out to the console. This turned out to be annoyingly verbose for many users (although extremely useful for others), so now we default to being completely silent by default.
-->

1.0 以前の Socket.IO はデフォルトでコンソールに全てのログを表示していました。これは多くのユーザにとってはうるさいくらい冗長であると判明したため（もちろん他の人たちには本当に有用ではあったのですが）、1.0 ではデフォルトでは何も出力しません。

<!--
The basic idea is that each module used by Socket.IO provides different debugging scopes that give you insight into the internals. By default, all output is suppressed, and you can opt into seeing messages by supplying the DEBUG env variable (Node.JS) or the localStorage.debug property (Browsers).
-->

基本的な考え方は、Socket.IO で使用されるモジュールのそれぞれが固有のデバッグスコープに基づいて、それらの内部を観察する、というものです。デフォルトでは全ての出力は非表示となっており、（Node.js の場合は）環境変数 `DEBUG` や（ブラウザの場合は）`localStorage.debug` プロパティに値を明示的に設定することで、メッセージを見られるようになります。

<!--
You can see it in action for example on our homepage:
-->

ホームページから実際の動きを見ることができます。

<!--## Available debugging scopes-->

## 利用可能なデバッグスコープ

<!--
The best way to see what information is available is to use the *:
-->

どんな情報が取得できるのかを眺めるには `*` を使うのが良いでしょう。

```
DEBUG=* node yourfile.js
```

<!--
or in the browser:
-->

ブラウザの場合は

```
localStorage.debug='*';
```

<!--
And then filter by the scopes you’re interested in. You can use , to separate them.
-->

その後に、自分に関心のあるスコープで絞り込みます。`,` を使って複数のスコープを区切ることもできます。
