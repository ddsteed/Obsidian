# FAQ
## 允许多个 ip 读取数据库
- `vi /etc/mysql/mysql.conf.d/mysql.cnf`
- add 
```
[mysql]

bind-address  = 0.0.0.0
```
- restart mysql
```bash
sudo service mysql restart  
```

## 增加打开一个数据库的数量
- `vi /etc/mysql/my.cnf` or `vi ~/.my.cnf`
- add
```
[mysqld]

max_connections = 2000
```
- restart mysql 

## mysql 死锁问题
- 记住在一个函数中，打开数据库后，一定要 close。否则第一次运行 python 时，会出现死锁问题。

