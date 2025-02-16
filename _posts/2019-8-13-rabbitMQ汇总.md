---
layout:     post
title:      rabbitMQ汇总
subtitle:   rabbitMQ使用技巧
date:       2019-08-13
author:     showface
header-img: 
catalog: true
tags:
    - rabbitMQ
    - spring cloud
---

消息队列（Message Queue，简称MQ），从字面意思上看，本质是个队列，FIFO先入先出，只不过队列中存放的内容是message而已。  
其主要用途：不同进程Process/线程Thread之间通信。  

Erlang – 面向并发的编程语言。  

AMQP是消息队列的一个协议。  

五种队列

一、简单队列：  
P：消息的生产者  
C：消息的消费者  
红色：队列  
生产者将消息发送到队列，消费者从队列中获取消息。  

二、Work模式：  
一个生产者、2个消费者。  
一个消息只能被一个消费者获取。  

轮询分发 ：使用任务队列的优点之一就是可以轻易的并行工作。如果我们积压了好多工作，我们可以通过增加工作者（消费者）来解决这一问题，使得系统的伸缩性更加容易。在默认情况下，
RabbitMQ将逐个发送消息到在序列中的下一个消费者(而不考虑每个任务的时长等等，且是提前一次性分配，并非一个一个分配)。平均每个消费者获得相同数量的消息。这种方式分发消息机制称为Round-Robin（轮询）。

公平分发 ：虽然上面的分配法方式也还行，但是有个问题就是：比如：现在有2个消费者，所有的奇数的消息都是繁忙的，而偶数则是轻松的。按照轮询的方式，奇数的任务交给了第一个消费者，
所以一直在忙个不停。偶数的任务交给另一个消费者，则立即完成任务，然后闲得不行。而RabbitMQ则是不了解这些的。这是因为当消息进入队列，RabbitMQ就会分派消息。它不看消费者为应答的数目，只是盲目的将消息发给轮询指定的消费者。

能者多劳  

 三、订阅模式：  
1、1个生产者，多个消费者  
2、每一个消费者都有自己的一个队列  
3、生产者没有将消息直接发送到队列，而是发送到了交换机  
4、每个队列都要绑定到交换机  
5、生产者发送的消息，经过交换机，到达队列，实现，一个消息被多个消费者获取的目的  
注意：一个消费者队列可以有多个消费者实例，只有其中一个消费者实例会消费  

四、路由模式：  

五、主题模式（通配符模式）  




