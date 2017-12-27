データ型
===

<a name="strings"></a>
文字列（Strings）
---

文字列型（Strings）はもっとも基本的なタイプのバリューです。Redis においてはバイナリセーフで、これはつまりどんなデータも含めることができる、ということです。例えば JPEG画像や、シリアライズされた Rubyオブジェクトなど。

文字列型の長さは最大で 512MB となっています。

これを使って、いろいろなことができます。

* アトミックカウンターとして。INCRコマンド群の [INCR](/commands/incr) や [DECR](/commands/decr)、[INCRBY](/commands/incrby) などで活用できます。
* [APPEND](/commands/append)コマンドで文字列の追加もできます
* [GETRANGE](/commands/getrange) や [SETRANGE](/commands/setrange) によって、文字列をランダムアクセスのベクターとして使えます。
* [GETBIT](/commands/getbit) や [SETBIT](/commands/setbit) を使えば、たくさんのデータを整理して格納したり、ブルームフィルタの一部として活用できます。

[文字列に関するコマンドの一覧](/commands/#string) は、[introduction to Redis data types](/topics/data-types-intro) をお読みください。

<a name="lists"></a>
リスト型（Lists）
---

Redis のリスト型はシンプルな文字列のリストで、入力された順に並びます。
左側の頭に新しい要素を挿入したり、右側つまり末尾に要素を追加することもできます。

[LPUSH](/commands/lpush)コマンドは頭に新しい要素を挿入し、[RPUSH](/commands/rpush)コマンドは末尾に新しい要素を追加します。もともと空のキーに対してこれらの操作を行ったときは、新しいリストが作成されます。
同様に、操作を行った時にリストが空になると、キー空間からキーが削除されます。リストコマンドに存在しないキーが渡されたときは、空のリストを渡されたときと同じように動作するためです。

幾つかのリスト操作のサンプルを見てみましょう。

    LPUSH mylist a   # リストは "a" となる
    LPUSH mylist b   # リストは "b","a" となる
    RPUSH mylist c   # リストは "b","a","c" となります（RPUSHが使われているため）

リストの最大長は 2^32 -1 個の要素（4294967295、つまりリスト当たり 4億）です。

リスト型の主な特徴としては、計算量の観点から見たときに、たとえ数百万の要素を持つリストであっても、頭と末尾への要素の挿入や削除がコンスタントな時間で行えるということです。要素へのアクセスは末端部分では非常に高速ですが、サイズが大きなリストであれば中間部分はとても遅くなり、O(N) になるでしょう。

他にも興味深い機能があるので、紹介しましょう。

* ソーシャルネットワークのタイムラインを構成するときには、タイムラインに新しい要素を追加するために [LPUSH](/commands/lpush) が利用でき、最近のできごとを取得するために [LRANGE](/commands/lrange) が活用できます。
* 最新の N個の要素だけを残し、かつリストの長さを一定に保つためには、[LPUSH](/commands/lpush) と [LTRIM](/commands/ltrim) を組み合わせて使います。
* リスト型はメッセージを渡すプリミティブとしても活用できます。例としては、バックグラウンドジョブを作成するための Rubyライブラリ [Resque](https://github.com/defunkt/resque) があります。
* その他にもリスト型で実現できることがあります。このデータ型では、[BLPOP](/commands/blpop) などのブロッキングコマンドなど、さまざまなコマンドがあります。

他のコマンドは[リストで使えるコマンド群](/commands#list) を参照するか、[Redisデータ型のイントロダクション](/topics/data-types-intro) をお読みください。


<a name="sets"></a>
セット型
---

Redis におけるセット型は、順序付けられていない文字列の集合です。O(1)、つまり要素の数に関係なく一定の時間で要素の追加、削除、確認を行うことができます。

セット型では要素の繰り返しを許しません。同じ要素を複数回追加したとしても、この要素の中には一つだけが残ります。これは、*要素を追加するときに存在のチェックが必要ない*ということを意味します。

セット型の非常に興味深い点は、サーバサイドで計算を行うコマンドが幾つも実装されており、セット型について和集合や差集合、差分といった計算を非常に短い時間で実現できることです。

セット型の最大長は 2^32 -1 個の要素（4294967295、つまりセット当たり 4億）です。

セット型で実現できることを考えてみましょう。

* You can track unique things using Redis Sets. Want to know all the unique IP addresses visiting a given blog post? Simply use [SADD](/commands/sadd) every time you process a page view. You are sure repeated IPs will not be inserted.
* Redis Sets are good to represent relations. You can create a tagging system with Redis using a Set to represent every tag. Then you can add all the IDs of all the objects having a given tag into a Set representing this particular tag, using the [SADD](/commands/sadd) command. Do you want all the IDs of all the Objects having three different tags at the same time? Just use [SINTER](/commands/sinter).
* You can use Sets to extract elements at random using the [SPOP](/commands/spop) or [SRANDMEMBER](/commands/srandmember) commands.


As usual, check the [full list of Set commands](/commands#set) for more information, or read the [introduction to Redis data types](/topics/data-types-intro).

<a name="hashes"></a>
Hashes
---

Redis Hashes are maps between string fields and string values, so they are the perfect data type to represent objects (e.g. A User with a number of fields like name, surname, age, and so forth):

    @cli
    HMSET user:1000 username antirez password P1pp0 age 34
    HGETALL user:1000
    HSET user:1000 password 12345
    HGETALL user:1000

A hash with a few fields (where few means up to one hundred or so) is stored in a way
that takes very little space, so you can store millions of objects in a small
Redis instance.

While Hashes are used mainly to represent objects, they are capable of storing many elements, so you can use Hashes for many other tasks as well.

Every hash can store up to 2^32 - 1 field-value pairs (more than 4 billion).

Check the [full list of Hash commands](/commands#hash) for more information, or read the [introduction to Redis data types](/topics/data-types-intro).

<a name="sorted-sets"></a>
Sorted sets
---

Redis Sorted Sets are, similarly to Redis Sets, non repeating collections of
Strings. The difference is that every member of a Sorted Set is associated
with score, that is used in order to take the sorted set ordered, from the
smallest to the greatest score.  While members are unique, scores may be
repeated.

With sorted sets you can add, remove, or update elements in a very fast way
(in a time proportional to the logarithm of the number of elements). Since
elements are *taken in order* and not ordered afterwards, you can also get
ranges by score or by rank (position) in a very fast way.
Accessing the middle of a sorted set is also very fast, so you can use
Sorted Sets as a smart list of non repeating elements where you can quickly access
everything you need: elements in order, fast existence test, fast access
to elements in the middle!

In short with sorted sets you can do a lot of tasks with great performance
that are really hard to model in other kind of databases.

With Sorted Sets you can:

* Take a leader board in a massive online game, where every time a new score
is submitted you update it using [ZADD](/commands/zadd). You can easily
take the top users using [ZRANGE](/commands/zrange), you can also, given an
user name, return its rank in the listing using [ZRANK](/commands/zrank).
Using ZRANK and ZRANGE together you can show users with a score similar to
a given user. All very *quickly*.
* Sorted Sets are often used in order to index data that is stored inside Redis.
For instance if you have many hashes representing users, you can use a sorted set with elements having the age of the user as the score and the ID of the user as the value. So using [ZRANGEBYSCORE](/commands/zrangebyscore) it will be trivial and fast to retrieve all the users with a given interval of ages.


Sorted Sets are probably the most advanced Redis data types, so take some time to check the [full list of Sorted Set commands](/commands#sorted_set) to discover what you can do with Redis! Also you may want to read the [introduction to Redis data types](/topics/data-types-intro).

Bitmaps and HyperLogLogs
---

Redis also supports Bitmaps and HyperLogLogs which are actually data types
based on the String base type, but having their own semantics.

Please refer to the [introduction to Redis data types](/topics/data-types-intro) for information about those types.
