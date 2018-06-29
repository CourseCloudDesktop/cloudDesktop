# Nova整体架构

>Nova是openstack最核心的服务，是openstack中的计算组织控制器

## 功能
1. 实例生命周期管理
2. 管理计算资源
3. 网络和认证管理
4. 异步的一致性通信

## 体系结构

### nova整体架构图
+ Nova主要由API、Compute、Conductor、Scheduler四个核心服务组成，这些服务之间通过AMQP消息队列来进行通信
![](../assets/houhxpictures/final.nova.1.PNG)

### nova中各服务介绍

+ nova-api：接收和响应客户的 API 调用
+ nova-scheduler：虚机调度服务，负责决定在哪个计算节点上运行虚机 
+ nova-compute：管理虚机的核心服务，通过调用 Hypervisor API 实现虚机生命周期管理
+ nova-conductor：nova-compute 经常需要更新数据库，比如更新虚机的状态。 出于安全性和伸缩性的考虑，nova-compute 并不会直接访问数据库，而是将这个任务委托给 nova-conductor
+ DB：Nova 会有一些数据需要存放到数据库中，一般使用 MySQL。 数据库安装在控制节点上。 Nova 使用命名为 “nova” 的数据库。 
+ queue： Nova 包含众多的子服务，这些子服务之间需要相互协调和通信。 为解耦各个子服务，Nova 通过 Message Queue 作为子服务的信息中转站。 所以在架构图上我们看到了子服务之间没有直接的连线，它们都通过 Message Queue 联系。 

## 总结

1. Nova是openstack中最核心的组件。openstack的其他组件归根结底是为Nova组件服务的。
2. Nova服务是由多个子服务构成，子服务是通过RPC实现通信。服务之间有很松的耦合性。
