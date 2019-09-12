## docker安装chrome用来自动化测试

清除docker产生的编译文件的信息:
```bash
sudo docker system prune -a -f
```
构建环境
```bash
$ sudo apt-get update
$ sudo apt-get -y upgrade
$ sudo apt-get -y install unzip python3-pip python3-dev build-essential libssl-dev libffi-dev xvfb
$ sudo pip3 install --upgrade pip
$ export LANGUAGE=en_US.UTF-8
$ export LANG=en_US.UTF-8
$ export LC_ALL=en_US.UTF-8
$ locale-gen en_US.UTF-8
$ sudo dpkg-reconfigure locales
$ pip3 install --upgrade pip
```
#### Chrome-stable
```bash
$ cd ~
$ wget "https://dl.google.com/linux/direct/google-chrome-stable_current_amd64.deb"
$ sudo dpkg -i google-chrome-stable_current_amd64.deb
$ sudo apt-get install -y -f
$ sudo rm google-chrome-stable_current_amd64.deb
```

#### InstaPy
```bash
$ git clone https://github.com/timgrossmann/InstaPy.git
$ wget "https://chromedriver.storage.googleapis.com/2.34/chromedriver_linux64.zip"
$ unzip chromedriver_linux64
$ mv chromedriver InstaPy/assets/chromedriver
$ chmod +x InstaPy/assets/chromedriver
$ chmod 755 InstaPy/assets/chromedriver
$ cd InstaPy
$ pip install .
```

#### docker配置如下 dockerfile

```python
FROM ubuntu:14.04
MAINTAINER <Sparks Li>
# use 163 ubuntu soures
COPY source.list /etc/apt/sources.list

RUN apt-get update
RUN apt-get -y upgrade
RUN apt-get -y install unzip python-pip python-dev build-essential libssl-dev libffi-dev xvfb
RUN pip install --upgrade pip
RUN export LANGUAGE=en_US.UTF-8
RUN export LANG=en_US.UTF-8
RUN export LC_ALL=en_US.UTF-8
RUN locale-gen en_US.UTF-8
RUN dpkg-reconfigure locales
RUN pip install --upgrade pip
WORKDIR /root
RUN apt-get install -y wget
RUN apt-get install -y libcurl3 fonts-liberation libappindicator1 libasound2 libatk-bridge2.0-0 libatk1.0-0 libcairo2 libcups2 libgdk-pixbuf2.0-0 libglib2.0-0
RUN apt-get install -y libnspr4 libnss3 libxss1 xdg-utils
RUN wget "https://dl.google.com/linux/direct/google-chrome-stable_current_amd64.deb"
RUN dpkg -i google-chrome-stable_current_amd64.deb
RUN apt-get install -y -f
RUN rm google-chrome-stable_current_amd64.deb
RUN apt-get install -y git
RUN git clone https://github.com/timgrossmann/InstaPy.git
RUN wget "https://chromedriver.storage.googleapis.com/2.34/chromedriver_linux64.zip"
RUN unzip chromedriver_linux64
RUN mv chromedriver InstaPy/assets/chromedriver
RUN chmod +x InstaPy/assets/chromedriver
RUN apt-get install -y libpq-dev python-dev
RUN chmod 755 InstaPy/assets/chromedriver
WORKDIR InstaPy
RUN pip install .

WORKDIR /root
RUN apt-get install -y sqlite sqlite3
RUN apt-get install -y libsqlite3-dev
RUN apt-get install -y libgconf-2-4
#
# env
#ENV LANG=zh_CN.UTF8
ENV PYTHONIOENCODING=UTF-8
ENV PROJECT_NAME=tyc_api
ENV TZ=Asia/Shanghai
RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone
COPY . /data
WORKDIR /data
## install requirements
RUN pip install --upgrade pip
RUN pip install -r requirements.txt
RUN ["cp", ".env_sample", ".env"]
EXPOSE 8888
RUN find /usr/bin -name google-chrome-stable | xargs sed -i 's/exec -a "$0" "$HERE\/chrome" "$@"/exec -a "$0" "$HERE\/chrome" "$@" --user-data-dir --no-sandbox/g'
CMD ["python", "api_server.py"]
```
驱动与chrome对应链接
http://blog.csdn.net/huilan_same/article/details/51896672
