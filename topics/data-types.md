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
�Z�b�g�^�iSets�j
---

Redis �ɂ�����Z�b�g�^�́A�����t�����Ă��Ȃ�������̏W���ł��BO(1)�A�܂�v�f�̐��Ɋ֌W�Ȃ����̎��Ԃŗv�f�̒ǉ��A�폜�A�m�F���s�����Ƃ��ł��܂��B

�Z�b�g�^�ł͗v�f�̌J��Ԃ��������܂���B�����v�f�𕡐���ǉ������Ƃ��Ă��A���̗v�f�̒��ɂ͈�������c��܂��B����́A*�v�f��ǉ�����Ƃ��ɑ��݂̃`�F�b�N���K�v�Ȃ�*�Ƃ������Ƃ��Ӗ����܂��B

�Z�b�g�^�̔��ɋ����[���_�́A�T�[�o�T�C�h�Ōv�Z���s���R�}���h�������������Ă���A�Z�b�g�^�ɂ��Ęa�W���⍷�W���A�����Ƃ������v�Z����ɒZ�����ԂŎ����ł��邱�Ƃł��B

�Z�b�g�^�̍ő咷�� 2^32 -1 �̗v�f�i4294967295�A�܂�Z�b�g������ 4���j�ł��B

�Z�b�g�^�Ŏ����ł��邱�Ƃ��l���Ă݂܂��傤�B

* �Z�b�g�^�ŁA���j�[�N�Ȃ��̂�ǐՂł��܂��B����̃u���O�L����K�ꂽ�l�� IP�A�h���X������m�肽���H���̏ꍇ�͒P���ɖ��� [SADD](/commands/sadd) ���g�����ƂŎ����ł��܂��B�J��Ԃ����� IP�A�h���X���L�^����邱�Ƃ͂���܂���B
* �����[�V������\�����Ƃɂ������Ă��܂��B�^�O�t�����s���d�g�݂����A���̃^�O���Z�b�g�^�Ƃ��ĕۑ����邱�Ƃ��ł��܂��B���̂Ƃ��� [SADD](/commands/sadd) ���g�����ƂŁA����̃^�O�����v�f�����ׂĒǉ����邱�Ƃ��ł��܂��B3�̈قȂ�^�O�������ׂĂ̗v�f��m�肽���Ƃ��́H[SINTER](/commands/sinter) ���𗧂��܂��ˁB
* �Z�b�g�̗v�f�������_���Ɏ�肽���Ƃ��́A[SPOP](/commands/spop) �� [SRANDMEMBER](/commands/srandmember) �Ƃ������R�}���h���g���܂��B

���̑��A[�Z�b�g�R�}���h�̈ꗗ](/commands#set) ��A [�f�[�^�^�̃C���g���_�N�V����](/topics/data-types-intro) ���Q�l�ɂ��Ă��������B


<a name="hashes"></a>
�n�b�V���^�iHashes�j
---

�n�b�V���^�͕�����t�B�[���h�ƕ�����o�����[���}�b�s���O������̂ł���A�I�u�W�F�N�g���i�[����̂ɗ��z�I�Ȃ��̂ł��i�Ⴆ�΁A�����A�N��ȂǂƂ���������̃t�B�[���h�������[�U���j�B

    @cli
    HMSET user:1000 username antirez password P1pp0 age 34
    HGETALL user:1000
    HSET user:1000 password 12345
    HGETALL user:1000

���ɏ��Ȃ��t�B�[���h�i���100���x�܂Łj�����n�b�V���^�͂ƂĂ������ȋ�ԂɎ��܂�̂ŁA������ Redis�C���X�^���X�ł����Ă����S���Ƃ������P�ʂ̃I�u�W�F�N�g���i�[���邱�Ƃ��ł��܂��B

�n�b�V���^���g����ꍇ�́A��������̗v�f���i�[����Ă��邱�Ƃ��Ӗ����A���܂��܂ȑ��̃^�X�N�ɂ����p�ł��܂��B

�n�b�V���^�̍ő咷�� 2^32 -1 �i4���ȏ�j�́A�t�B�[���h�ƃo�����[�̃y�A�ł��B

�Q�l�Ƃ��āA[�n�b�V���R�}���h�̈ꗗ](/commands#hash) ��A [�f�[�^�^�̃C���g���_�N�V����](/topics/data-types-intro) ���ǂ����B


<a name="sorted-sets"></a>
����ς݃Z�b�g�iSorted sets�j
---

����ς݃Z�b�g�̓Z�b�g�^�Ɠ��l�A�d���������Ȃ�������̏W���ł��B�Ⴂ�́A���ׂĂ̗v�f���X�R�A���ɐ���ς݂ł���_�ł���A�X�R�A�̍ŏ�����ő�Ƃ������ɂȂ��Ă��܂��B�v�f���̂͏d�������������j�[�N�Ȃ��̂ł����A�X�R�A�͏d������ꍇ������܂��B

����ς݃Z�b�g�ł́A�v�f�̒ǉ��A�폜�A�X�V�͔��ɍ����ɍs���܂��i�A���S���Y����̏��v���Ԃ͗v�f���̑ΐ��ɂ��܂��j�BSince elements are *taken in order* and not ordered afterwards, you can also get ranges by score or by rank (position) in a very fast way.
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
