## flask 项目运行
http://blog.huangzp.com    #安装环境博客

celery flower --broker=redis://localhost  #启动celery监控工具

celery -A test worker --loglevel=info -Q test_minus -n minus  #启动celery工作指定队列启动

celery -A test worker --loglevel=info  #启动默认celery队列

sqlalchemy 使用方式总结： https://my.oschina.net/freegeek/blog/222725

单独运行项目中的某个脚本： python -m app.crwal.threat.ip.blocklist

后台运行 ： python -m app.tasks.get_domain_info_task > ~/桌面/a.txt 2>&1 &


vpn配置soc_soc.conf(放/etc下)
{
"server":"49.51.35.176",
"server_port":8501,
"local_address": "127.0.0.1",
"local_port":1080,
"password":"soc@lihaijie",
"timeout":300,
"method":"chacha20-ietf"
}

2.ubuntu16.04 shadowsock2.8 不支持chacha20-ietf
解决办法

sudo apt install libsodium-dev

pip install https://github.com/shadowsocks/shadowsocks/archive/master.zip -U
sudo sslocal -c /etc/soc_soc.conf

配置文件在deploy文件下
把配置文件拷贝到/etc/supervisor/supervisord.conf 文件中include设定的路径中。
进supervisor控制台
sudo supervisorctl
update 操作
失败的话看看配置文件中日志路径下的日志记录

创建项目内的虚拟环境
python3 -m venv env

last_time[0].strftime("%Y-%m-%d %H:%M:%S.%f")
'2018-07-06 09:21:06.753369'

pyenv使用说明 http://einverne.github.io/post/2017/04/pyenv.html


python3 -m venv env  生成虚拟环境
