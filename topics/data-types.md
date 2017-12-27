�f�[�^�^
===

<a name="strings"></a>
������iStrings�j
---

������^�iStrings�j�͂����Ƃ���{�I�ȃ^�C�v�̃o�����[�ł��BRedis �ɂ����Ă̓o�C�i���Z�[�t�ŁA����͂܂�ǂ�ȃf�[�^���܂߂邱�Ƃ��ł���A�Ƃ������Ƃł��B�Ⴆ�� JPEG�摜��A�V���A���C�Y���ꂽ Ruby�I�u�W�F�N�g�ȂǁB

������^�̒����͍ő�� 512MB �ƂȂ��Ă��܂��B

������g���āA���낢��Ȃ��Ƃ��ł��܂��B

* �A�g�~�b�N�J�E���^�[�Ƃ��āBINCR�R�}���h�Q�� [INCR](/commands/incr) �� [DECR](/commands/decr)�A[INCRBY](/commands/incrby) �ȂǂŊ��p�ł��܂��B
* [APPEND](/commands/append)�R�}���h�ŕ�����̒ǉ����ł��܂�
* [GETRANGE](/commands/getrange) �� [SETRANGE](/commands/setrange) �ɂ���āA������������_���A�N�Z�X�̃x�N�^�[�Ƃ��Ďg���܂��B
* [GETBIT](/commands/getbit) �� [SETBIT](/commands/setbit) ���g���΁A��������̃f�[�^�𐮗����Ċi�[������A�u���[���t�B���^�̈ꕔ�Ƃ��Ċ��p�ł��܂��B

[������Ɋւ���R�}���h�̈ꗗ](/commands/#string) �́A[introduction to Redis data types](/topics/data-types-intro) �����ǂ݂��������B

<a name="lists"></a>
���X�g�^�iLists�j
---

Redis �̃��X�g�^�̓V���v���ȕ�����̃��X�g�ŁA���͂��ꂽ���ɕ��т܂��B
�����̓��ɐV�����v�f��}��������A�E���܂薖���ɗv�f��ǉ����邱�Ƃ��ł��܂��B

[LPUSH](/commands/lpush)�R�}���h�͓��ɐV�����v�f��}�����A[RPUSH](/commands/rpush)�R�}���h�͖����ɐV�����v�f��ǉ����܂��B���Ƃ��Ƌ�̃L�[�ɑ΂��Ă����̑�����s�����Ƃ��́A�V�������X�g���쐬����܂��B
���l�ɁA������s�������Ƀ��X�g����ɂȂ�ƁA�L�[��Ԃ���L�[���폜����܂��B���X�g�R�}���h�ɑ��݂��Ȃ��L�[���n���ꂽ�Ƃ��́A��̃��X�g��n���ꂽ�Ƃ��Ɠ����悤�ɓ��삷�邽�߂ł��B

����̃��X�g����̃T���v�������Ă݂܂��傤�B

    LPUSH mylist a   # ���X�g�� "a" �ƂȂ�
    LPUSH mylist b   # ���X�g�� "b","a" �ƂȂ�
    RPUSH mylist c   # ���X�g�� "b","a","c" �ƂȂ�܂��iRPUSH���g���Ă��邽�߁j

���X�g�̍ő咷�� 2^32 -1 �̗v�f�i4294967295�A�܂胊�X�g������ 4���j�ł��B

���X�g�^�̎�ȓ����Ƃ��ẮA�v�Z�ʂ̊ϓ_���猩���Ƃ��ɁA���Ƃ����S���̗v�f�������X�g�ł����Ă��A���Ɩ����ւ̗v�f�̑}����폜���R���X�^���g�Ȏ��Ԃōs����Ƃ������Ƃł��B�v�f�ւ̃A�N�Z�X�͖��[�����ł͔��ɍ����ł����A�T�C�Y���傫�ȃ��X�g�ł���Β��ԕ����͂ƂĂ��x���Ȃ�AO(N) �ɂȂ�ł��傤�B

���ɂ������[���@�\������̂ŁA�Љ�܂��傤�B

* �\�[�V�����l�b�g���[�N�̃^�C�����C�����\������Ƃ��ɂ́A�^�C�����C���ɐV�����v�f��ǉ����邽�߂� [LPUSH](/commands/lpush) �����p�ł��A�ŋ߂̂ł����Ƃ��擾���邽�߂� [LRANGE](/commands/lrange) �����p�ł��܂��B
* �ŐV�� N�̗v�f�������c���A�����X�g�̒��������ɕۂ��߂ɂ́A[LPUSH](/commands/lpush) �� [LTRIM](/commands/ltrim) ��g�ݍ��킹�Ďg���܂��B
* ���X�g�^�̓��b�Z�[�W��n���v���~�e�B�u�Ƃ��Ă����p�ł��܂��B��Ƃ��ẮA�o�b�N�O���E���h�W���u���쐬���邽�߂� Ruby���C�u���� [Resque](https://github.com/defunkt/resque) ������܂��B
* ���̑��ɂ����X�g�^�Ŏ����ł��邱�Ƃ�����܂��B���̃f�[�^�^�ł́A[BLPOP](/commands/blpop) �Ȃǂ̃u���b�L���O�R�}���h�ȂǁA���܂��܂ȃR�}���h������܂��B

���̃R�}���h��[���X�g�Ŏg����R�}���h�Q](/commands#list) ���Q�Ƃ��邩�A[Redis�f�[�^�^�̃C���g���_�N�V����](/topics/data-types-intro) �����ǂ݂��������B


<a name="sets"></a>
�Z�b�g�^
---

Redis �ɂ�����Z�b�g�^�́A�����t�����Ă��Ȃ�������̏W���ł��BO(1)�A�܂�v�f�̐��Ɋ֌W�Ȃ����̎��Ԃŗv�f�̒ǉ��A�폜�A�m�F���s�����Ƃ��ł��܂��B

�Z�b�g�^�ł͗v�f�̌J��Ԃ��������܂���B�����v�f�𕡐���ǉ������Ƃ��Ă��A���̗v�f�̒��ɂ͈�������c��܂��B����́A*�v�f��ǉ�����Ƃ��ɑ��݂̃`�F�b�N���K�v�Ȃ�*�Ƃ������Ƃ��Ӗ����܂��B

�Z�b�g�^�̔��ɋ����[���_�́A�T�[�o�T�C�h�Ōv�Z���s���R�}���h�������������Ă���A�Z�b�g�^�ɂ��Ęa�W���⍷�W���A�����Ƃ������v�Z����ɒZ�����ԂŎ����ł��邱�Ƃł��B

�Z�b�g�^�̍ő咷�� 2^32 -1 �̗v�f�i4294967295�A�܂�Z�b�g������ 4���j�ł��B

�Z�b�g�^�Ŏ����ł��邱�Ƃ��l���Ă݂܂��傤�B

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
