Redis コンフィギュレーション
===

Redis は設定ファイルなしでもデフォルトの設定で起動させることが可能だが、このセットアップ方法はテストや開発目的での利用のみとすることをお勧めする。

正しい方法は、Redis設定ファイル、つまり `redis.conf` を編集して設定を行うことだ。

`redis.conf`ファイルは多数の構成要素を含んでいるが、以下のように非常にシンプルなフォーマットとなっている。

    keyword argument1 argument2 ... argumentN

例えば一例を挙げると以下である。

    slaveof 127.0.0.1 6380

文字列に空白を含めたい場合は、以下のサンプルのようにクォートで囲めばよい。

    requirepass "hello world"

設定における構成要素の一覧や意味、正しい使い方は各 Redis ディストリビューションに含まれるデフォルトの redis.conf を参照してほしい。

* The self documented [redis.conf for Redis 4.0](https://raw.githubusercontent.com/antirez/redis/4.0/redis.conf).
* The self documented [redis.conf for Redis 3.2](https://raw.githubusercontent.com/antirez/redis/3.2/redis.conf).
* The self documented [redis.conf for Redis 3.0](https://raw.githubusercontent.com/antirez/redis/3.0/redis.conf).
* The self documented [redis.conf for Redis 2.8](https://raw.githubusercontent.com/antirez/redis/2.8/redis.conf).
* The self documented [redis.conf for Redis 2.6](https://raw.githubusercontent.com/antirez/redis/2.6/redis.conf).
* The self documented [redis.conf for Redis 2.4](https://raw.githubusercontent.com/antirez/redis/2.4/redis.conf).


コマンドラインから引数を渡す
---

Redis 2.6 から、コマンドライン経由で直接 Redis にパラメータを渡すことができるようになっている。これはテスト目的には非常に便利だ。

以下は新しい Redis インスタンスをポート 6380 で起動し、127.0.0.1:6379 のスレーブとするコマンドの例だ。

    ./redis-server --port 6380 --slaveof 127.0.0.1 6379

コマンドラインから渡す引数のフォーマットは、頭に `--` が必要な点を除けば redis.conf ファイルと同じフォーマットとなっている。

留意点として、内部的にはこの一時的な設定はメモリ内で保管され（必要に応じて設定ファイルと結合した上で）、redis.conf のフォーマットに変換されて扱われる。


サーバー動作中の Redis コンフィグレーションの変更
---


Redis では、停止あるいは再起動を行うことなく、コマンド [CONFIG SET](/commands/config-set) および 
[CONFIG GET](/commands/config-get) で設定の書き換えや確認を行うことができる。

設定におけるすべての構成要素がサポートされているわけではないが、サポートされるものは多い。詳しくは [CONFIG SET](/commands/config-set) と [CONFIG GET](/commands/config-get) を参照してほしい。

なお、この方法で設定を書き換えた場合、**redis.confファイルには影響は及ばない**ので、Redis を再起動したときには設定ファイルが読みだされ、古い設定に戻る点は留意すべきだろう。

[CONFIG SET](/commands/config-set)コマンドで設定の変更を行った場合には、`redis.conf`ファイルも同様に変更すべきだ。手動で編集するか、Redis 2.8 からは [CONFIG REWRITE](/commands/config-rewrite) コマンドが利用でき、このコマンドは現在の `redis.conf` ファイルを自動的に読みだして現在の設定と比較し、異なる部分について設定ファイルを書き換える。項目が存在せずデフォルト値のままになっているものは追加しない。設定ファイル内のコメントは保持される。


Redis をキャッシュとして設定する
---

もし Redis を単純なキャッシュ用途で設計しており、すべてのキーに TTL を設定する場合には、その代わりに以下のような設定を利用してもよい。
（この例では最大メモリを 2MB としている）

    maxmemory 2mb
    maxmemory-policy allkeys-lru

この設定では、アプリケーション側で `EXPIRE`（あるいは同等の）コマンドを使って TTL を設定する必要はない。利用されているメモリ量が 2MB に達した時点で、疑似的な LRUアルゴリズムによってキーが削除の対象となるからだ。

基本的に、この設定では Redis は memcached とよく似た動きをする。
詳細なドキュメントは [using Redis as an LRU cache](/topics/lru-cache) がある。
