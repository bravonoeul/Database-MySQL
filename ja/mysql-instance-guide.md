## Database > MySQL Instance >使用ガイド

## MySQL Instance作成

MySQLを使用するために、先にインスタンスを作成する必要があります。

![mysqlinstance_01_201812](https://static.toastoven.net/prod_mysql/mysqlinstance_01_201812_en.png)

MySQL Instance作成 **ショートカット**ボタンをクリックすると **Compute > Instance >インスタンス作成** に進みます。

MySQLバージョンは、次の2種類が提供されます。

![mysqlinstance_02_201812](https://static.toastoven.net/prod_mysql/mysqlinstance_02_201812_en.png)

* MySQL Community Server 5.6.38
    * mysql-community-server-5.6.38-2.el6.x86_64
* MySQL Community Server 5.7.20
    * mysql-community-server-5.7.20-1.el6.x86_64

MySQLイメージを選択し、追加設定完了後にインスタンスを作成します。
インスタンス作成については、[Instance概要](http://docs.toast.com/ja/Compute/Instance/ja/overview/)を参照してください。

インスタンス作成完了後にSSHを使用して、インスタンスにアクセスします。
インスタンスにFloating IPが接続されていて、セキュリティーグループでTCPポート22(SSH)が許可されている必要があります。

![mysqlinstance_03_201812](https://static.toastoven.net/prod_mysql/mysqlinstance_03_201812_en.png)

SSHクライアントと設定したキーペアを利用してインスタンスに接続します。
SSH接続の詳細ガイドは[SSH接続ガイド](https://docs.toast.com/ja/Compute/Instance/ja/overview/#linux)を参照してください。

## MySQL起動/停止方法

```
#mysqlサービス起動
shell> service mysqld start

#mysqlサービス停止
shell> service mysqld stop

#mysqlサービス再起動
shell> service mysqld restart
```

## MySQL接続

イメージ作成後、最初は下記のように接続します。

```
shell> mysql -uroot
```

## MySQLインスタンス作成後の初期設定

### 1\.パスワード設定

初期インストール後、MySQL ROOTアカウントパスワードは指定されていません。したがってインストール後、すぐにパスワードを設定する必要があります。

* MySQL 5.6バージョンパスワード設定

```
SET PASSWORD [FOR user] = password_option

mysql> set password=password('パスワード');
```

* MySQL 5.7バージョンパスワード設定

```
ALTER USER USER() IDENTIFIED BY 'auth_string';

mysql> ALTER USER 'root'@'localhost' IDENTIFIED BY '新しいパスワード';
```

MySQL基本validate\_password\_policyは下記の通りです。

* validate\_password\_policy=MEDIUM
* 基**本8文字以上、数字、大文字、小文字、特殊文字**を含める必要がある

### 2\.ポート(port)変更

提供されるイメージポートはMySQL基本ポートの3306です。セキュリティー上、ポートの変更を推奨します。

```
shell> vi /etc/my.cnf


#my.cnfファイルに使用するポートを明示します。

port =使用するポート名


#vi エディタ保存


#mysqlサービス再起動


shell> service mysqld restart


#変更されたポートに下記のように接続


shell> mysql -uroot -P[変更されたポート番号]
```

## my.cnf説明

my.cnfのデフォルトのパスは /etc/my.cnf で、TOAST推奨変数(variable)が設定されています。内容は下記の通りです。

| 名前 | 説明 |
| --- | --- |
| default\_storage\_engine | 基本ストレージエンジン(stroage engine)を指定します。InnoDBが指定され、Online-DDLとトランザクション(transaction)を使用できます。 |
| expire\_logs\_days | binlog設定で、 ログを保存する日数を設定します。デフォルトで3日に指定されています。 |
| innodb\_log\_file\_size | トランザクション(transaction)のredo logを保存するログファイルのサイズを指定します。<br><br>実際の運営環境では256MB以上を推奨しており、現在512MBに設定されています。設定値を修正した時は、DBの再起動が必要です。 |
| innodb\_file\_per\_table | テーブルが削除されたりTRUNCATEされる時、テーブルスペースがOSにすぐに返却されます。 |
| innodb\_log\_files\_in\_group | innodb\_log\_fileファイルの個数を設定し、循環的\(circular\)に使用されます。最小2個以上で構成されます。 |
| log_timestamps | MySQL 5.7の基本log時間はUTCで表示されます。したがってログ時間をSYSTEMローカル時間に変更します。 |
| slow\_query\_log | slow\_query logオプションを使用します。 long\_query\_timeによる基本10秒以上のクエリーはslow\_query\_logに記録されます。 |
| sysdate-is-now | sysdateの場合、replicationでsysdate()を使用したSQL文は、複製時にマスターとスレーブの間の時間が異なる問題があり、sysdate()とnow()の関数を同一に適用します。 |

## MySQLディレクトリ説明

MySQLディレクトリおよびファイル説明は下記の通りです。

| 名前 | 説明 |
| --- | --- |
| my.cnf | /etc/my.cnf |
| DATADIR | MySQLデータファイルのパス  - /var/lib/mysql/ |
| ERROR_LOG | MySQL error_logファイルのパス  - /var/log/mysqld.log |
| SLOW_LOG | MySQL Slow Queryファイルのパス -  <span style="color:#333333">/var/lib/mysql/*slow.log</span> |


> MySQL Instanceのリリース状況は[インスタンスリリースノート](/Compute/Compute/ja/release-notes/)を参照してください。
