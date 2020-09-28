## 个人简历

### 个人信息
* * *

- 姓名： __宋延辉__&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;年龄： __25__
- 手机： __17621953855__&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;邮箱： __huihuisyh@aliyun.com__
- 专业： __软件工程云计算专业__&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;岗位： __Python后端工程师__

### 专业技能：
* * *
- 熟练使用python有良好的编程习惯，遵循编程规范；了解go语言。
- 熟练使用Flask框架，使用RESTFul风格借口开发；了解Django。
- 熟悉MySQL、PostgreSQL、Redis等数据库，具备数据库调优能力；了解MongoDB、ElasticSearch。
- 熟练使用Supervisor、Docker、Docker-compose进行项目部署。
- 熟悉异步框架Celery和消息队列RabbitMQ的使用。
- 熟悉团队协作工具Git和Gitlab的日常使用。
- 熟悉常用数据结构，有一定的算法基础。

### 工作经历
* * *
#### __郑州赛欧思科技有限公司__ &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; __研发部门__  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; __Python工程师__ &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`2018.6 - 至今`
- 参与公司内部基础服务建设
- 参与公司核心产品的研发
- 参与落地项目的部署和维护

#### __上海青悦信息科技有限公司__  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; __研发部门__ &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; __Python工程师__ &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`2017.6 - 2018.5`
- 参与公司内部基础数据的采集
- 参与公司核心业务系统的开发与维护
- 参与线上系统的部署与维护

### 项目经验
* * *
#### __安全态势感知与通报预警平台__ &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;   __项目负责人__  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; `2018.6 - 2019-10`
- __项目描述__：该项目是公司核心产品中的聚合层服务，主要功能为资产探查、安全事件和漏洞监测、开源社区监控、安全通报和威胁情报等。探测客户网络资产，对网络资产进行持续轮循监测，发现安全漏洞或安全通报进行预警通知，并且还包括近实时威胁情报通知。主要对各个子服务做权限验证和接口转发，告警信息通知，动态设置功能编排等。系统已经为国庆七十周年，少数民族运动会和全国网络安全宣传周，金鸡百花奖等提供河南省网络安保。
- __负责功能__: 通报预警子服务的设计与实现，对聚合层平台权限管理，聚合统计数据，报表自动生成，预警推送服务的设计与实现。
- __技术实现__: 数据存储Postgres与Redis缓存;RabbitMQ各服务模块消息推送;Celery异步任务调度;Flask做后端框架提供API;supervisor配合Gunicorn部署;日志对接ELK系统，异常信息对接sentry实现异常第一时间邮件通知，对接OSS等。

#### __河南省数据中心平台__ &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;   __项目负责人__  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; `2019.1 - 至今`
- __项目描述__: 该项目服务于郑州市公安局，该项目分底层和上层服务,底层有网站资产发现服务、域名资产发现服务、单位资产发现服务、IP资产发现服务、端口资产发现服务、子域名发现服务等,底层的数据会推送到上层,对上层的业务服务提供支撑作用,上层对郑州单位、IP、域名等信息的聚合关联,并同时将位置信息集成至郑州3D地势信息中。为日常警务管理、威胁域名IP快速定位、指挥决策等警务工作提供重要支撑。
- __负责功能__: 实现对网络资产的扫描发现，网站状态的持续监控，分析网站指纹信息，分析网站whois信息，数据更新采用订阅发布模式，提供网站信息ES检索功能。
- __技术实现__: 底层数据采集，数据分析服务使用Docker-compose进行Docker容器集群编排，采集和分析任务采用celery异步任务实现，底层源数据生层json文件采用logstash同步到ElasticSearch，上层服务通过es进行数据的同步。上层服务获取es中的数据，数据更新通过celery + RabbitMQ实现发布订阅模式实现，日志信息对接ELK，异常日志对接sentry进行第一时间通知等。

#### __威胁情报服务系统__ &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;   __项目负责人__  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; `2018.6 - 2019-5`
- __项目描述__: 该项目是公司核心产品的子服务之一,主要功能是为政府部门和公司安全组人员提供威胁信息。该系统分为服务端和客户端,服务端通过爬虫技术广泛采集互联网的公开被黑资域名、被黑IP、漏洞信息、安全咨询和监控黑客成果展示网站等,客户端作为落地项目的子服务部署在河南省网信办、河南省信息中心、郑州市教育局等诸多线上生产环境。
- __负责功能__: 实现对互联网公开的黑域名和黑IP的数据采集；对发布漏洞信息进行数据采集，监控某一黑客成果展示网站，将数据通过github进行中转，同步到内部服务中，并进行被黑邮件通知。
- __技术实现__：celery 实现爬取任务周期调度;PostgreSQL、ElasticSearch做存储、Flask做后端框架提供API;服务端Docker配合Gunicorn部署、客户端使用Supervisor部署;日志对接ELK系统等。