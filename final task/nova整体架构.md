# Nova整体架构

>Nova是openstack最核心的服务，是openstack中的计算组织控制器

## 功能
1. 实例生命周期管理
2. 管理计算资源
3. 网络和认证管理
4. 异步的一致性通信

## 体系结构

+ Nova主要有API、Compute、Conductor、Scheduler四个核心服务组成，这些服务之间通过AMQP消息队列来进行通信
![](../assets/houhxpictures/final.nova.1.PNG)