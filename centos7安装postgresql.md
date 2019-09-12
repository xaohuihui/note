## centos7安装postgresql

```bash
sudo yum install https://download.postgresql.org/pub/repos/yum/9.6/redhat/rhel-7-x86_64/pgdg-centos96-9.6-3.noarch.rpm

sudo yum install postgresql96
sudo yum install postgresql96-server
sudo /usr/pgsql-9.6/bin/postgresql96-setup initdb
systemctl enable postgresql-9.6.service
systemctl start postgresql-9.6.service
sudo yum install postgresql-server

sudo service postgresql status
  568  sudo netstat -tunlp
  569  sudo kill -9 5736
  570  sudo netstat -tunlp
sudo rm /tmp/.s.PGSQL.5432.lock
rm /tmp/.s.PGSQL.5432
sudo postgresql-setup initdb
chkconfig postgresql on
sudo vim /var/lib/pgsql/pg_hba.conf
sudo vim /var/lib/pgsql/data/pg_hba.conf
将local   all    all    trust     # replace ident or peer with trust
修改trust

systemctl restart postgresql
psql -U postgres -d postgres -h 127.0.0.1 -p 5432


如果碰到端口占用的情况
sudo netstat -tunlp | grep 5432
sudo kill -9 1463
重启服务

```
