## 更改postgresql的自增id

使用语句: select setval('your_table_id_seq',(select max(id) from 表名));

如何查看 your_table_id_seq？

使用命令：\d 表名

得出以下结果，看图中红色方框圈住的：

![asdasdasd](./image/更新postgresql自增id.jpeg)
