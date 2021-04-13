## postgresql 数据简单备份恢复

![postgresql数据库简单备份](image/postgres数据简单备份.jpg)


sudo -u postgres pg_dump -U 用户名 -d 数据库名 -h 服务器IP > 备份sql名

sudo -u postgres psql 将被恢复的数据库名 < 已备份的sql名
