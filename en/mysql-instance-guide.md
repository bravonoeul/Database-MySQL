## Database > MySQL Instance > Instance Guide

## Creating MySQL Instance

In order to use MySQL Instance service, you need to create an instance first.

![mysqlinstance_01_201812_en](https://static.toastoven.net/prod_mysql/mysqlinstance_01_201812_en.png)

Clicking on the Create MySQL Instance **Shortcut** button will redirect you to **Compute > Instance > Create Instance** page.

There are two versions of MySQL provided.

![mysqlinstance_02_201812_en](https://static.toastoven.net/prod_mysql/mysqlinstance_02_201812_en.png)

* MySQL Community Server 5.6.38
    * mysql-community-server-5.6.38-2.el6.x86_64
* MySQL Community Server 5.7.20
    * mysql-community-server-5.7.20-1.el6.x86_64

Choose MySQL Image and complete additional settings to create an instance.
For additional information on creating an instance, please refer to [Instance Overview](http://docs.toast.com/en/Compute/Instance/en/overview/).

When an instance is created, use SSH to connect to the instance.
A Floating IP must be associated with the instance and TCP port 22(SSH) should be added as a rule in Security Groups.

![mysqlinstance_03_201812_en](https://static.toastoven.net/prod_mysql/mysqlinstance_03_201812_en.png)

Use SSH client along with the key pair provided to access the instance.
For detailed guide on SSH connection, please refer to [SSH Connection Guide](https://docs.toast.com/en/Compute/Instance/en/overview/#linux).

## Starting/Stopping MySQL 

```
#Start mysql Service 
shell> service mysqld start

#Stop mysql Service 
shell> service mysqld stop

#Restart mysql Service 
shell> service mysqld restart
```

## Connecting to MySQL 

For initial connection, connect to MySQL with default user name. 

```
shell> mysql -uroot
```

## Initial Settings for MySQL Instance

### 1\. Setting Password

There's no password on root user on initial installation. Therefore, it is required to set password as soon as possible.  

* Set Password for MySQL Version 5.6  

```
SET PASSWORD [FOR user] = password_option

mysql> set password=password('password');
```

* Set Password for MySQL Version 5.7  

```
ALTER USER USER() IDENTIFIED BY 'auth_string';

mysql> ALTER USER 'root'@'localhost' IDENTIFIED BY 'New Password';
```

Default MySQL validate_password_policy is as below:

* validate\_password\_policy=MEDIUM
* Must be more than 8 characters, and include numbers, lower/upper cases, and special characters.

### 2\. Changing Port Number

The default MySQL port number is 3306. It is recommended to change the port number for security reasons. 

```
shell> vi /etc/my.cnf


# Specify a port to use in the my.cnf file. 

port = Port name to use 


# Save vi editor Save editor 


# Restart mysql service  


shell> service mysqld restart


#Connect with the changed port number


shell> mysql -uroot -P[changed port number]
```

## Description of my.cnf 

The default path of my.cnf is /etc/my.cnf, and ToastCloud recommended variables are set as below: 

| Name | Description |
| --- | --- |
| default\_storage\_engine | Specify a default storage engine: Default is InnoDB with Online-DDL and transactions available. |
| expire\_logs\_days | Set log expiration period for logs provided by binlog settings. Default is three days. |
| innodb\_log\_file\_size | Specify the size of log files which save redo logs of transactions. <br>Recommended size is 256MB or higher in actual environment, and it is set as 512MB by default. In order for the changes to take effect, please restart the database. |
| innodb\_file\_per\_table | When a table is deleted or truncated, the table space is immediately returned to the OS. |
| innodb\_log\_files\_in\_group | Set the number of innodb\_log\_file files and use them in circular fashion: requires at least two. |
| log_timestamps | Default log time of MySQL 5.7 is displayed in UTC time format; therefore, change log time to system local time. |
| slow\_query\_log | Enable the slow\_query log option. Queries taking more than 10 seconds in accordance with long_query_time will be logged to the slow_query_log. |
| sysdate-is-now | For sysdate, SQL with sysdate() used for replication results in discrepant time between Master and Slave, so sysdate() and now() functions will behave the same. |

## Description of MySQL Directory 

Directory and file description of MySQL are as below: 

| Name | Description |
| --- | --- |
| my.cnf | /etc/my.cnf |
| DATADIR | Path for MySQL Data File  - /var/lib/mysql/ |
| ERROR_LOG | Path for MySQL error_log File  - /var/log/mysqld.log |
| SLOW_LOG | Path for MySQL Slow Query File -  <span style="color:#333333">/var/lib/mysql/*slow.log</span> |


> For detailed release status of MySQL Instance, please refer to [Instance Release Notes](http://alpha-docs.toast.com/en/Compute/Compute/en/release-notes/).
