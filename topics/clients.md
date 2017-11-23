Redis クライアントハンドリング
===

このドキュメントでは、ネットワークレイヤー（コネクション、タイムアウト、バッファ、他の類似のトピック）の観点から Redis がどのようにクライアントを扱うか、情報をまとめている。

このドキュメントにある情報は、**Redis 2.6 以降のバージョンが対象である**。


コネクションの確立について
---

Redis はリッスンするように設定された TCPポートおよび Unixソケットに基づきコネクションを受け入れる。新規のコネクションが受け入れられた場合、以下のようなオペレーションが実行される。

* Redis は多重化（multiplexing）およびノンブロッキング I/O であるため、クライアントのソケットがノンブロッキングのステートとなる。
* コネクションに遅延は無いと考えてよいため、`TCP_NODELAY`オプションが設定される。
* *読み込み可能な*ファイルイベントが作成され、ソケットで新しいデータが受信され次第、Redis がクエリを受け取ることができる。
クライアントが初期化されたのち、Redis は並行して扱えるクライアント数が上限に達していないかどうかを確認する（次のセクションで説明するが、これは `maxclients`として設定される）。

その時点で接続しているクライアントの数が最大に達していて接続を受け入れできない場合、Redis はクライアントにその旨のエラーを返し、即座にコネクションを閉じる。
ソケットのアウトプットバッファは通常このようなエラーメッセージを含めるのに十分なサイズなので、このエラーメッセージは Redis がコネクションを閉じたとしてもすぐにクライアントに伝達される。エラーの伝送はカーネルによってハンドリングされる。


クライアントの順序について
---

順序はクライアントソケットファイルのデスクリプター番号と、カーネルがレポートするイベントの並びによって決まる。つまり順序は不定であることを考慮しなくてはならない。

Redis は以下のような挙動をとる。

* クライアントソケットから新しく読みだすたびに、単一の `read()`システムコールを行う。もし複数のクライアントが接続していて、そのうちの幾つかが高い頻度でクエリしている場合にも、他のクライアントが影響を受けたりレイテンシが悪化するといったことはない。
* しかし、新しいデータが読みだされたとき、その時点でバッファ上にあるデータはシーケンシャルに実行される。これによって局所性が向上し、改めてプロセス時間が必要なクライアントの存在を確認する必要がなくなる。


クライアントの最大数
---

Redis 2.4 では並列で処理できるクライアントの最大数はハードコードされていた。

Redis 2.6 では、この上限値は動的になった。デフォルトは 10000クライアントであり、Redis.conf に `maxclients` が設定されていれば、その値が使われる。

しかしながら、Redis はカーネル上でオープン可能なファイルデスクリプターの最大数を確認する（*ソフトリミット*が確認される）。もし Redis で処理したい最大クライアント数より、このリミット（内部的に Redis が利用するファイルデスクリプター数の 32 を考慮した上で）が小さかった場合、Redis は現在の OS上で*実際に扱える*値を上限値として設定する。

最大クライアント数の設定が書き換えられた場合、以下のように起動時にログが出力されるだろう。

```
$ ./redis-server --maxclients 100000
[41422] 23 Jan 11:28:33.179 # Unable to set the max number of files limit to 100032 (Invalid argument), setting the max clients configuration to 10112.
```

特定の数のクライアントを処理できるように設定を行っている場合は、OS 側におけるプロセス単位のファイルデスクリプター数についても、それに応じて設定することをお勧めします。

Linux環境下では、以下のようなコマンドで現在のセッションおよびシステム単位の設定を変更することができます。

* ulimit -Sn 100000 # This will only work if hard limit is big enough.
* sysctl -w fs.file-max=100000

アウトプットバッファの上限
---

クライアントに大きなデータを転送するコマンドもあるため、Redis では可変長のアウトプットバッファを扱う必要がある。

ただし、クライアントが多くのコマンドを生成する場合などでは、Redis が既存のアウトプットを返す速度よりも早く次以降のコマンドが到達する場合があります。特に、Pub/Sub を利用していてクライアントが新しいメッセージを十分に早く処理できない場合などで起こりえます。

こういった条件下では、クライアントアウトプットバッファが増加し、メモリ領域がより多く必要になります。このため、デフォルトで Redis はクライアント毎にアウトプットバッファの上限を設定します。この上限に達した場合、コネクションはクローズされ Redis のログファイルにイベントが記録されます。

以下のように 2種類のリミットがあります。

* **ハードリミット**は固定値で、この値に到達した時点でコネクションは即座に閉じられます。

* **ソフトリミット**は時間によって計算され、例えば 10秒あたり 32MB であれば、アウトプットバッファが 32MB を 10秒間超えていたときにコネクションが閉じられる。

クライアントによって異なるデフォルトリミットが設けられている。

* **通常のクライアント**は、デフォルトリミットが 0 であり、つまりリミットは存在しない。ほとんどの一般的なクライアントはブロッキングされる実装、つまり 1つのコマンドを送ったら応答を待ってから次のコマンドを送るようになっている。したがって、このようなクライアントであればコネクションを閉じる必要はない。
* **Pub/Subクライアント**はデフォルトでハードリミットが 32MB、ソフトリミットが 60秒あたり 8MB となっている。
* **スレーブ**はデフォルトでハードリミットが 256MB、ソフトリミットが 60秒あたり 64MB となっている。

これらのリミットは `CONFIG SET`コマンドあるいは永続的な `redis.conf`、いずれかの変更によって変えることができる。設定例は Redis ディストリビューション内の `redis.conf`を参照すること。


クエリ―バッファのハードリミット
---

Every client is also subject to a query buffer limit. This is a non-configurable hard limit that will close the connection when the client query buffer (that is the buffer we use to accumulate commands from the client) reaches 1 GB, and is actually only an extreme limit to avoid a server crash in case of client or server software bugs.

Client timeouts
---

By default recent versions of Redis don't close the connection with the client
if the client is idle for many seconds: the connection will remain open forever.

However if you don't like this behavior, you can configure a timeout, so that
if the client is idle for more than the specified number of seconds, the client connection will be closed.

You can configure this limit via `redis.conf` or simply using `CONFIG SET timeout <value>`.

Note that the timeout only applies to normal clients and it **does not apply to Pub/Sub clients**, since a Pub/Sub connection is a *push style* connection so a client that is idle is the norm.

Even if by default connections are not subject to timeout, there are two conditions when it makes sense to set a timeout:

* Mission critical applications where a bug in the client software may saturate the Redis server with idle connections, causing service disruption.
* As a debugging mechanism in order to be able to connect with the server if a bug in the client software saturates the server with idle connections, making it impossible to interact with the server.

Timeouts are not to be considered very precise: Redis avoids to set timer events or to run O(N) algorithms in order to check idle clients, so the check is performed incrementally from time to time. This means that it is possible that while the timeout is set to 10 seconds, the client connection will be closed, for instance, after 12 seconds if many clients are connected at the same time.

CLIENT command
---

The Redis client command allows to inspect the state of every connected client, to kill a specific client, to set names to connections. It is a very powerful debugging tool if you use Redis at scale.

`CLIENT LIST` is used in order to obtain a list of connected clients and their state:

```
redis 127.0.0.1:6379> client list
addr=127.0.0.1:52555 fd=5 name= age=855 idle=0 flags=N db=0 sub=0 psub=0 multi=-1 qbuf=0 qbuf-free=32768 obl=0 oll=0 omem=0 events=r cmd=client
addr=127.0.0.1:52787 fd=6 name= age=6 idle=5 flags=N db=0 sub=0 psub=0 multi=-1 qbuf=0 qbuf-free=0 obl=0 oll=0 omem=0 events=r cmd=ping
```

In the above example session two clients are connected to the Redis server. The meaning of a few of the most interesting fields is the following:

* **addr**: The client address, that is, the client IP and the remote port number it used to connect with the Redis server.
* **fd**: The client socket file descriptor number.
* **name**: The client name as set by `CLIENT SETNAME`.
* **age**: The number of seconds the connection existed for.
* **idle**: The number of seconds the connection is idle.
* **flags**: The kind of client (N means normal client, check the [full list of flags](http://redis.io/commands/client-list)).
* **omem**: The amount of memory used by the client for the output buffer.
* **cmd**: The last executed command.

See the [CLIENT LIST](http://redis.io/commands/client-list) documentation for the full list of fields and their meaning.

Once you have the list of clients, you can easily close the connection with a client using the `CLIENT KILL` command specifying the client address as argument.

The commands `CLIENT SETNAME` and `CLIENT GETNAME` can be used to set and get the connection name. Starting with Redis 4.0, the client name is shown in the
`SLOWLOG` output, so that it gets simpler to identify clients that are creating
latency issues.

TCP keepalive
---

Recent versions of Redis (3.2 or greater) have TCP keepalive (`SO_KEEPALIVE` socket option) enabled by default and set to about 300 seconds. This option is useful in order to detect dead peers (clients that cannot be reached even if they look connected). Moreover, if there is network equipment between clients and servers that need to see some traffic in order to take the connection open, the option will prevent unexpected connection closed events.

