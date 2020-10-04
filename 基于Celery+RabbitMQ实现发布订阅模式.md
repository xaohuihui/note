# Celery + RabbitMQ实现订阅发布模式

###  使用RabbitMQ的扇形交换机(fanout exchange)实现订阅发布模式

> 消息(Message)由Client发送，RabbitMQ接收到消息之后通过交换机转发到对应的队列上面。Worker会从队列中获取未被读取的数据处理。
> RabbitMQ消息模式的核心理念是：生产者没有直接发送任何消费到队列。实际上，生产者都不知道这个消费是发送给哪个队列的。
> 相反，生产者只能发送消息给交换机，交换机是非常简单的。一方面它接受生产者的消息，另一方面向队列推送消息。

#### 扇形交换机介绍
扇形交换机是最基本的交换机类型，它所能做的事情非常简单———广播消息。扇形交换机会把能接收到的消息全部发送给绑定在自己身上的队列。因为广播不需要“思考”，所以扇形交换机处理消息的速度也是所有的交换机类型里面最快的。

##### 实现方式
在Celery中，扇形交换机被定义为广播路由的形式进行分发消息，下面两张图诠释了RabbitMQ本身的扇形交换机工作方式与Celery广播路由的工作方式。
![RabbitMQ扇形交换机](image/diff_rabbitmq.png)

![celery中使用fanout](image/diff_fanout.png)
在RabbitMq中，你可以绑定多个队列到同一个扇形交换机来实现消息广播。

而celery使用rabbitmq做broker时，自己又实现的一套广播机制：通过一个创建一个广播Queue，来广播消息至订阅此队列的所有消费者。

实现具体代码如下：
> 消费者代码实现：__值得注意：__ 在task_queues中Broadcast方法中需要指定一个固定的队列（多个消费者一定要不同）而且auto_delete需要指定为False
> 这样才能保证消费者在重新启动的时候不会删除fanout exchange中bind的队列，这样就不会丢失fanout exchange中的消息
```python
# fanout_celery_worker.py

from celery import Celery as Create_celery
from project import create_app


def init_publish_celery(app=None):
    app = app or create_app()
    celery_app = Create_celery(__name__, broker="amqp://admin:admin@192.168.2.229/pubsub-stage", include=[
        "project.task.fanout_task"])

    # 发布广播的队列
    from kombu.common import Broadcast
    celery_app.conf.task_queues = (Broadcast(name="mange", queue='mange.consumer1', auto_delete=False),)

    # 忽略消息队列未注册的任务
    from celery.worker.consumer import Consumer
    def on_unknown_task(self, body, message, exc):
        message.ack()

    Consumer.on_unknown_task = on_unknown_task

    class ContextTask(celery_app.Task):
        """Make celery tasks work with Flask app context"""

        def __call__(self, *args, **kwargs):
            with app.app_context():
                return self.run(*args, **kwargs)

    def publish(task_name, queue, **kwargs):
        celery_app.send_task(task_name, queue=queue, kwargs=kwargs)

    # 给Celery导入flask的上下文
    celery_app.Task = ContextTask
    # 发布广播才会用到
    celery_app.publish2broadcast = publish
    return celery_app


celery_publish = init_publish_celery()


# fanout_task.py

from project.task.fanout_celery_worker import celery_publish


@celery_publish.task(name='unit_update', queue='mange')
def unit_update(**kwargs):
    print("********************************")
    for _k, _v in kwargs.items():
        print("{}: {}".format(_k, _v))
```
启动之后监听队列如图：

![fanout_celery_worker](image/fanout_celery_worker.png)

rabbitmq中mange exchange中绑定队列：(消费者服务关闭，该bind队列也不会自动删除)

![fanout_exchange_rabbitmq](image/fanout_exchange_rabbitmq.png)

发送任务代码：
> 值得注意的是在第二个发送的异步任务是消费者未注册的任务，将不会被接收

```python
from project.task.fanout_celery_worker import celery_publish

celery_publish.send_task('unit_update', queue='mange', kwargs={"user": "developer", "remark": "测试订阅发布模式"})

celery_publish.send_task('unit_delete', queue='mange', kwargs={"user": "developer", "remark": "未注册的任务将被忽略"})

```

执行结果如下：
> 仅有第一个任务执行

![fanout_celery_result](image/fanout_celery_result.png)


***
* From: xaohuihui
* 手搓不易，记得star哦