---
share: "true"
title: centos定时备份mysql数据库
date: 2020-10-09 22:40:01
tags: 
---

## centos定时备份mysql数据库

最重要的两个工具

* mysqldump
*  crontab

<!--more-->

### mysqldump 命令

编写脚本backup.sh

```
#!/bin/bash
mysqldump -h127.0.0.1 -uroot -ppassword --databases test --opt --default-character-set=UTF8 > /home/db-backup/data/sql-$(date "+%Y%m%d%H%M%S").sql
```

### crontab命令

```
crontab -e 
00 01 * * * /home/db-backup/backup.sh    //每天凌晨一点执行脚本
```

### 参考

* [[MySQL定时备份数据库（全库备份）](https://www.cnblogs.com/letcafe/p/mysqlautodump.html)](https://www.cnblogs.com/letcafe/p/mysqlautodump.html)
* [CentOS定时备份mysql数据库和清理过期备份文件](https://blog.csdn.net/Chay_Chan/article/details/85774999)
