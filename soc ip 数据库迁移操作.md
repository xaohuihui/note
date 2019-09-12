###  soc ip 创建

创建数据库之后恢复数据库备份
数据备份与恢复
备份数据库:
sudo -u postgres pg_dump -U 用户名 -d 数据库名 -h 服务器IP > 备份sql名
恢复数据库:
sudo -u postgres psql 将被恢复的数据库名 < 已备份的sql名


创建索引
```bash
CREATE INDEX ip_residence_all_2018w06_single_bd09_wgs84_min_max_ip_idx ON ip_residence_all_2018w06_single_bd09_wgs84 (minip, maxip);
```
