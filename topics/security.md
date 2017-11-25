Redis のセキュリティ
===

このドキュメントは Redis に関するセキュリティの観点から情報を記載していく。Redis のアクセスコントロール、コードのセキュリティに関する懸念、不正な入力による外部からの攻撃など、そういったトピックを取り扱う。

セキュリティに関する問い合わせは、GitHub の issue を作るか、もし非常に重大な内容を含めたやりとりが必要になる場合には、このドキュメントに添付する GPG鍵を使うことができる。


一般的な Redis のセキュリティモデル
----

Redis は信頼された環境において、信頼されたクライアントからのみアクセスされることを想定してデザインされている。

これはつまり、Redis をインターネットからアクセス可能にしておくのは得策ではないということであり、未知のクライアントが直接 Redis の TCPポートや UNIXソケットにアクセスできる状況を指す。

例えば、ウェブアプリケーションが Redis をデータベースやキャッシュ、メッセージングシステムとして利用するよう実装されている一般的なケースを考えると、フロントエンド（ウェブサイド）におけるクライアントのアプリケーションは、ページ生成あるいはリクエストを処理するためにクエリを行うだろう。

このケースにおいては、ウェブアプリケーションが Redis と未知のクライアントを仲介する役割を担っている（ブラウザはウェブアプリケーションにアクセスする）。

これは特定の例ではあるものの、一般的に未知のアクセスは ACL やインプットのバリデーション、Redis へアクセスする内容の決定のため、Redis の手前でレイヤー化して抽象化されるべきである。

Redis はセキュリティを高める目的では最適化されておらず、専らパフォーマンスと単純化に比重を置いている。


ネットワークセキュリティ
---

Redis のポートへのアクセスは信頼されたクライアントを除いてすべて拒否されるべきだ。したがって Redis が稼働しているサーバーは、アプリケーションが動作しているコンピュータからのみアクセスできることが望ましい。

一台のコンピュータがインターネットに疎通性を持った状態で配置される一般的なケースを考えると、そのようなケースでは仮想化された Linuxインスタンスになるだろうが（Linode, EC2, ...）、Redis のポートはファイヤウォールによって外部からアクセスできないようにするべきだ。クライアントはループバックインターフェイスから引き続きアクセスすることになる。

**redis.conf**ファイルに以下のような bind の設定を含めることで、単一のインターフェイスのみ利用するよう設定することが可能だ。

    bind 127.0.0.1

Redis の性質上、外部からのアクセスに対してポートを守ることを怠ると、大きなセキュリティインパクトになるだろう。例えば、たったひとつの **FLUSHALL**コマンドによって、外部の攻撃者はすべてのデータを削除することができる。


保護モード
---

残念ながら、多くのユーザは Redis インスタンスを外部のネットワークから保護できているとは言えない。多くのインスタンスは、パブリックな IPアドレスによってアクセスできる環境に放置されている。このため、バージョン 3.2.0 からはデフォルト設定で起動したとき（すべてのインターフェイスをバインドした場合）、かつパスワードを設定していないのであれば、特別な**保護モード**になる。このモードでは Redis はループバックインターフェイスからのクエリにのみ応答し、外部からのリクエストにはエラーを返す。このとき Redis はなぜ保護モードになっているか、どう設定を変更すべきか、なども返す。

この保護モードによって、適切に管理されない Redis インスタンスで深刻な問題が回避できることを望む限りだ。だが、システム管理者が保護モードを無効化したり、手動でインターフェイスを bind するといった方法でエラーを無視することはできる。


認証の失敗
---

Redis ではアクセスコントロールを実装していませんが、**redis.conf**ファイルを編集することで認証のレイヤを設けることができます。

この認証レイヤが有効化されている場合、Redis は未認証のクライアントからのクエリを拒否します。クライアントは **AUTH**コマンドとパスワードによって認証を行うことができます。

パスワードは、システムの管理者が平文で redis.conf ファイルに書き込みます。以下の 2つの理由により、ブルートフォースアタックで解読されないよう、十分な長さである必要があります。

* Redis は非常に高速にクエリを処理します。このため、短い時間でたくさんのパスワードを試すことができます。
* パスワードは **redis.conf**ファイルおよびクライアントの設定ファイルに格納されるものですから、システムの管理者が覚えておく必要はありません。

認証のレイヤを設ける際、そのゴールは冗長性にあります。ファイヤウォールや他の仕組みによる Redis の保護が十分に機能しない場合でも、外部からは引き続きアクセスするときに認証用のパスワードを求められるのでアクセスできません。

AUTHコマンドは他のコマンドと同様に平文で送信されるため、ネットワークレイヤで盗聴が可能な攻撃者に対しては有効な防御ではありません。


データ暗号化のサポート
---

Redis は暗号化をサポートしません。信頼される組織だけがインターネットなどを経由してアクセスできるように実装したい場合、SSLプロキシなど追加のレイヤーを実装して防護すべきです。[spiped](http://www.tarsnap.com/spiped.html) をお勧めします。


特定のコマンドを無効化する
---

Redis では特定のコマンドを無効化したり、推測が難しい名前にリネームすることができます。これによって、クライアントから一部のコマンドだけしか使えないようにする、といったことも可能です。

例えば、仮想サーバによるマネージドな Redis インスタンスが提供されるかもしれません。この場合、通常のユーザで **CONFIG**コマンドを使って設定を変更できることは好ましくないでしょう。しかし、システム側ではインスタンスを追加したり取り除いたりといった管理が可能でなくてはなりません。

このケースでは、コマンドの一覧から特定のコマンドをリネームしたり取り除くことができます。この機能は redis.conf 設定ファイルにおいて以下のような記法でサポートされます。

    rename-command CONFIG b840fc02d524045429941cc15f59e41cb7be6c52

上記の例では、**CONFIG**コマンドは推測が難しい名前にリネームされています。また、完全にコマンドを無効化したい場合には、空文字列でリネームをします。

    rename-command CONFIG ""


外部からの不正な入力による攻撃
---

外部から Redis インスタンスへの疎通性が無い場合であっても、攻撃者は幾つかの方法で攻撃を行うことができます。一例としては、Redis に何らかの悪いデータを追加することで、（最悪ケースでは）Redis 内部で実装されているデータ構造に基づき、アルゴリズムの複雑性に悪い影響を与える可能性があります。

例えば、攻撃者がウェブフォームから結果が同じバケットになる既知のハッシュを選んで送信すると、平均 O(1) の計算量で計算できるところが最悪で O(N) になり、より多くの CPUリソースが必要になることで最終的にはサービス拒否（Denial of Service）が起こるかもしれません。

このような特定の攻撃を防ぐために、Redis はハッシュ関数において実行毎に疑似ランダムなシード値を用いています。

Redis は SORTコマンドを qsort アルゴリズムで実装しています。現時点で、このアルゴリズムはランダマイズされておらず、そのためインプットを注意深く選択することで、計算量は最悪ケースで二次関数的に増大するでしょう。



エスケープと NoSQLインジェクション
---

Redis のプロトコルは文字列のエスケーピングを想定しておらず、通常のクライアントライブラリではインジェクションは不可能です。このプロトコルは固定長の文字列を使っていて、完全にバイナリセーフです。

Luaスクリプトは **EVAL** および **EVALSHA**コマンドで実行され、同じ規則に沿って動作し、いずれのコマンドも安全です。

非常に奇妙なユースケースですが、アプリケーションは信頼できないソースから入力された文字列を Luaスクリプトのボディに含めないようにする必要があります。


コードのセキュリティ
---

少し前のセットアップにおいては、クライアントはすべてのコマンドセットおよびフルアクセスの権限が必要でした。しかし、インスタンスにアクセスすることが、Redis が稼働しているシステムを操作するという結果に繋がってはいけません。

内部的に、Redis は良く知られた非常に多くのプラクティスを用いてセキュアにコーディングされており、バッファオーバーフロー、フォーマットのバグ、メモリ上での衝突といった問題を回避します。
しかしながら、**CONFIG**コマンドによる設定の操作機能によって、プログラムが動作しているディレクトリやダンプファイルの名前を変更できます。これは RDBファイルを任意の場所に書き込むようにできるということであり、システム的に Redis と同じユーザで任意のコードを動作させうる [問題](http://antirez.com/news/96) となっています。

Redis はルート権限を必要としません。特権を持たない *Redis* ユーザを専用に用意することをお勧めします。Redis のプログラマは現在、**CONFIG SET/GET dir**や他の類似のランタイム設定ディレクティブを使わずに新規の設定を追加するための仕組みを検討しています。この機能が実現できれば、クライアントから任意の場所にダンプファイルを書かせることが可能という問題も、回避できるでしょう。


GPG key
---

```
-----BEGIN PGP PUBLIC KEY BLOCK-----
Version: GnuPG v1.4.13 (Darwin)

mQINBFJ7ouABEAC5HwiDmE+tRCsWyTaPLBFEGDHcWOLWzph5HdrRtB//UUlSVt9P
tTWZpDvZQvq/ujnS2i2c54V+9NcgVqsCEpA0uJ/U1sUZ3RVBGfGO/l+BIMBnM+B+
TzK825TxER57ILeT/2ZNSebZ+xHJf2Bgbun45pq3KaXUrRnuS8HWSysC+XyMoXET
nksApwMmFWEPZy62gbeayf1U/4yxP/YbHfwSaldpEILOKmsZaGp8PAtVYMVYHsie
gOUdS/jO0P3silagq39cPQLiTMSsyYouxaagbmtdbwINUX0cjtoeKddd4AK7PIww
7su/lhqHZ58ZJdlApCORhXPaDCVrXp/uxAQfT2HhEGCJDTpctGyKMFXQbLUhSuzf
IilRKJ4jqjcwy+h5lCfDJUvCNYfwyYApsMCs6OWGmHRd7QSFNSs335wAEbVPpO1n
oBJHtOLywZFPF+qAm3LPV4a0OeLyA260c05QZYO59itakjDCBdHwrwv3EU8Z8hPd
6pMNLZ/H1MNK/wWDVeSL8ZzVJabSPTfADXpc1NSwPPWSETS7JYWssdoK+lXMw5vK
q2mSxabL/y91sQ5uscEDzDyJxEPlToApyc5qOUiqQj/thlA6FYBlo1uuuKrpKU1I
e6AA3Gt3fJHXH9TlIcO6DoHvd5fS/o7/RxyFVxqbRqjUoSKQeBzXos3u+QARAQAB
tChTYWx2YXRvcmUgU2FuZmlsaXBwbyA8YW50aXJlekBnbWFpbC5jb20+iQI+BBMB
AgAoBQJSe6LgAhsDBQld/A8ABgsJCAcDAgYVCAIJCgsEFgIDAQIeAQIXgAAKCRAx
gTcoDlyI1riPD/oDDvyIVHtgHvdHqB8/GnF2EsaZgbNuwbiNZ+ilmqnjXzZpu5Su
kGPXAAo+v+rJVLSU2rjCUoL5PaoSlhznw5PL1xpBosN9QzfynWLvJE42T4i0uNU/
a7a1PQCluShnBchm4Xnb3ohNVthFF2MGFRT4OZ5VvK7UcRLYTZoGRlKRGKi9HWea
2xFvyUd9jSuGZG/MMuoslgEPxei09rhDrKxnDNQzQZQpamm/42MITh/1dzEC5ZRx
8hgh1J70/c+zEU7s6kVSGvmYtqbV49/YkqAbhENIeZQ+bCxcTpojEhfk6HoQkXoJ
oK5m21BkMlUEvf1oTX22c0tuOrAX8k0y1M5oismT2e3bqs2OfezNsSfK2gKbeASk
CyYivnbTjmOSPbkvtb27nDqXjb051q6m2A5d59KHfey8BZVuV9j35Ettx4nrS1Ni
S7QrHWRvqceRrIrqXJKopyetzJ6kYDlbP+EVN9NJ2kz/WG6ermltMJQoC0oMhwAG
dfrttG+QJ8PCOlaYiZLD2bjzkDfdfanE74EKYWt+cseenZUf0tsncltRbNdeGTQb
1/GHfwJ+nbA1uKhcHCQ2WrEeGiYpvwKv2/nxBWZ3gwaiAwsz/kI6DQlPZqJoMea9
8gDK2rQigMgbE88vIli4sNhc0yAtm3AbNgAO28NUhzIitB+av/xYxN/W/LkCDQRS
e6LgARAAtdfwe05ZQ0TZYAoeAQXxx2mil4XLzj6ycNjj2JCnFgpYxA8m6nf1gudr
C5V7HDlctp0i9i0wXbf07ubt4Szq4v3ihQCnPQKrZZWfRXxqg0/TOXFfkOdeIoXl
Fl+yC5lUaSTJSg21nxIr8pEq/oPbwpdnWdEGSL9wFanfDUNJExJdzxgyPzD6xubc
OIn2KviV9gbFzQfOIkgkl75V7gn/OA5g2SOLOIPzETLCvQYAGY9ppZrkUz+ji+aT
Tg7HBL6zySt1sCCjyBjFFgNF1RZY4ErtFj5bdBGKCuglyZou4o2ETfA8A5NNpu7x
zkls45UmqRTbmsTD2FU8Id77EaXxDz8nrmjz8f646J0rqn9pGnIg6Lc2PV8j7ACm
/xaTH03taIloOBkTs/Cl01XYeloM0KQwrML43TIm3xSE/AyGF9IGTQo3zmv8SnMO
F+Rv7+55QGlSkfIkXUNCUSm1+dJSBnUhVj/RAjxkekG2di+Jh/y8pkSUxPMDrYEa
OtDoiq2G/roXjVQcbOyOrWA2oB58IVuXO6RzMYi6k6BMpcbmQm0y+TcJqo64tREV
tjogZeIeYDu31eylwijwP67dtbWgiorrFLm2F7+povfXjsDBCQTYhjH4mZgV94ri
hYjP7X2YfLV3tvGyjsMhw3/qLlEyx/f/97gdAaosbpGlVjnhqicAEQEAAYkCJQQY
AQIADwUCUnui4AIbDAUJXfwPAAAKCRAxgTcoDlyI1kAND/sGnXTbMvfHd9AOzv7i
hDX15SSeMDBMWC+8jH/XZASQF/zuHk0jZNTJ01VAdpIxHIVb9dxRrZ3bl56BByyI
8m5DKJiIQWVai+pfjKj6C7p44My3KLodjEeR1oOODXXripGzqJTJNqpW5eCrCxTM
yz1rzO1H1wziJrRNc+ACjVBE3eqcxsZkDZhWN1m8StlX40YgmQmID1CC+kRlV+hg
LUlZLWQIFCGo2UJYoIL/xvUT3Sx4uKD4lpOjyApWzU40mGDaM5+SOsYYrT8rdwvk
nd/efspff64meT9PddX1hi7Cdqbq9woQRu6YhGoCtrHyi/kklGF3EZiw0zWehGAR
2pUeCTD28vsMfJ3ZL1mUGiwlFREUZAcjIlwWDG1RjZDJeZ0NV07KH1N1U8L8aFcu
+CObnlwiavZxOR2yKvwkqmu9c7iXi/R7SVcGQlNao5CWINdzCLHj6/6drPQfGoBS
K/w4JPe7fqmIonMR6O1Gmgkq3Bwl3rz6MWIBN6z+LuUF/b3ODY9rODsJGp21dl2q
xCedf//PAyFnxBNf5NSjyEoPQajKfplfVS3mG8USkS2pafyq6RK9M5wpBR9I1Smm
gon60uMJRIZbxUjQMPLOViGNXbPIilny3FdqbUgMieTBDxrJkE7mtkHfuYw8bERy
vI1sAEeV6ZM/uc4CDI3E2TxEbQ==
```

**Key fingerprint**

```
pub   4096R/0E5C88D6 2013-11-07 [expires: 2063-10-26]
      Key fingerprint = E5F3 DA80 35F0 2EC1 47F9  020F 3181 3728 0E5C 88D6
      uid                  Salvatore Sanfilippo <antirez@gmail.com>
      sub   4096R/3B34D15F 2013-11-07 [expires: 2063-10-26]
