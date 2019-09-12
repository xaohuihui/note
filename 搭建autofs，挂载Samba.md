## 搭建autofs，挂载Samba

- 安装软件
```bash
apt-get install samba -y
apt-get install autofs -y
```

- 创建共享目录
```bash
mkdir -p data/share
```

- 配置/etc/samba/smb.conf文件
```vim
[mnt]
      comment = Driver Soft Share
      path = /root/data/share
      browseable = yes
      writeable = yes
      user = root
```

- 启动samba服务
```bash
service smbd restart
```

- 然后配置/etc/auto.master 文件
添加
```bash
/misc    /etc/auto.cifs    -timeout =60
```

- 配置/etc/auto.cifs(如果没有的话就建一个)或者把auto.misc 文件重命名
```bash
mv /etc/auto.misc /etc/auto.cifs
```

- 在文件中添加一句
```bash
samba       -fstype=cifs,username=root,password=root    :/127.0.0.1/mnt
```

> 注意：mnt这个目录为共享名就是在smb.conf中配置的中括号中的。

- 重启服务 ```server autofs restart```
- 查看```cd /msic/samba```
就可以看到自动挂载了
- 创建共享目录
```bash
mkdir -p data/share
```

- 配置/etc/samba/smb.conf文件
```bash
[mnt]
      comment = Driver Soft Share
      path = /root/data/share
      browseable = yes
      writeable = yes
      user = root

```

- 启动samba服务
```bash
service smbd restart
```

- 然后配置/etc/auto.master 文件
添加
```bash
/misc    /etc/auto.cifs    -timeout =60
```

- 配置/etc/auto.cifs(如果没有的话就建一个)或者把auto.misc 文件重命名
```bash
mv /etc/auto.misc /etc/auto.cifs
```

- 在文件中添加一句
```bash
samba       -fstype=cifs,username=root,password=root    :/127.0.0.1/mnt
```

>注意：mnt这个目录为共享名就是在smb.conf中配置的中括号中的。

- 重启服务 ```server autofs restart```
- 查看```cd /msic/samba```
就可以看到自动挂载了
