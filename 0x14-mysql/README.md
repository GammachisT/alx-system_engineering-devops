# Installn mysql on server

## task #0 Install MySQL
To do this task 
	* you should complete SSH project task #3 for web-01 and web-02 correctly
	* "that means you should copy the provided ssh public key into your web servers ~/.ssh/authorized_keys" by using echo command
	* next installing MySQL provided version on both of your servers step by step.

## task #1 Let us in!
## task #2 If only you could see what I've seen with your eyes
## task #3 Quite an experience to live in fear, isn't it?
## task #5  MySQL backup
	* Navigate to your web-01 && web-02
	* login to `mysql`
	* do the following line by line
	```
	CREATE USER holberton_user@localhost IDENTIFIED BY "projectcorrection280hbtn";
	GRANT REPLICATION CLIENT ON *.* TO 'holberton_user'@'localhost';
	CREATE DATABASE tyrell_corp;
	USE tyrell_corp;
	CREATE TABLE nexus6(id INTEGER, name TEXT);
	INSERT INTO nexus6 VALUES (0, "Jarvis");
	GRANT SELECT ON tyrell_corp.nexus6 TO holberton_user@localhost;
	CREATE USER replica_user@'%' IDENTIFIED BY "replica_user";
	GRANT REPLICATION SLAVE ON *.* TO 'replica_user'@'%';
	GRANT SELECT ON mysql.user TO holberton_user@localhost;
	CREATE USER web02@IP IDENTIFIED BY "web02";
	GRANT REPLICATION SLAVE ON *.* TO web02@IP;
	```


## task #4 Setup a Primary-Replica infrastructure using MySQL

Navigate to your web-01 which is the ` primary host.`

* Then open your editor by using nano or vi or any athor, add the name of the file `example: vi master` 
* add the following to your file

```
sudo ufw disable
config_data=\
"
# The MySQL  Server configuration file.
#
# For explanations see
# http://dev.mysql.com/doc/mysql/en/server-system-variables.html

[mysqld]
binlog_do_db    = tyrell_corp
log_bin         = /var/log/mysql/mysql-bin.log
server-id       = 1
pid-file        = /var/run/mysqld/mysqld.pid
socket          = /var/run/mysqld/mysqld.sock
datadir         = /var/lib/mysql
log-error       = /var/log/mysql/error.log
# By default we only accept connections from localhost
bind-address    = 0.0.0.0
# Disabling symbolic-links is recommended to prevent assorted security risks
symbolic-links=0
"

echo "$config_data" | sudo dd status=none of=/etc/mysql/mysql.conf.d/mysqld.cnf
sudo service mysql restart

echo "Done!!!"
```

* save and exit your editor
* then chmod u+x `filename`
* Run `sudo ./filename`

** After you have fished the above now `navigate to web-02` **

* Then open your editor by using nano or vi or any athor, add the name of the file `example: vi slave`
* add the following to your file
```
sudo ufw disable
config_data=\
"
# The MySQL  Server configuration file.
#
# For explanations see
# http://dev.mysql.com/doc/mysql/en/server-system-variables.html

# The MySQL  Server configuration file.
#
# For explanations see
# http://dev.mysql.com/doc/mysql/en/server-system-variables.html

[mysqld]
binlog_do_db    = tyrell_corp
relay-log       = /var/log/mysql/mysql-relay-bin.log
log_bin         = /var/log/mysql/mysql-bin.log
server-id       = 2
pid-file        = /var/run/mysqld/mysqld.pid
socket          = /var/run/mysqld/mysqld.sock
datadir         = /var/lib/mysql
log-error       = /var/log/mysql/error.log
# By default we only accept connections from localhost
bind-address    = 127.0.0.1
# Disabling symbolic-links is recommended to prevent assorted security risks
symbolic-links=0
"
echo "$config_data" | sudo dd status=none of=/etc/mysql/mysql.conf.d/mysqld.cnf

sudo service mysql restart
echo "Done!!"
```
* save and exit your editor
* then chmod u+x `filename`
* Run `sudo ./filename`

** Next `login to your MySQL` on both your web servers **
	* on web-01 do the following one by one.
	```
	CREATE USER slave@'%' IDENTIFIED BY 'password';
	GRANT REPLICATION SLAVE ON *.* TO slave@'%';
	FLUSH PRIVILEGES;
	SHOW MASTER STATUS;
	```
	* on web-02 do the following one by one.
	* First to login to you mysql on web 02 use the following code
	`mysql -u `CREATEd USER in web 01` -h web-01IP -p
	password="IDENTIFIED BY=`the password you create during CREATED USER`
	```
	CHANGE MASTER TO MASTER_HOST=`web-01 IP`, MASTER_USER=`repl`, MASTER_PASSWORD=`slavepass`, MASTER_LOG_FILE=`recorded_log_file_name`, MASTER_LOG_POS=`log_position`;
	START SLAVE;
	```
sudo service mysql restart

> [!IMPORTANT]
> Replace `web-01 IP` by your web 01 ip address
> `repl` = created user
> `recorded_log_file_name`= file name 
> `log_position`= digits under position in table craeted
