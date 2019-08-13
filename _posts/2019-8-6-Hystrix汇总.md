---
layout:     post
title:      hystrix汇总
subtitle:   hystrix使用技巧
date:       2019-08-06
author:     showface
header-img: 
catalog: true
tags:
    - hystrix
    - spring cloud
---

>

spring-cloud-starter-hystrix      Hystrix，Feign是基于Hystrix的  
spring-cloud-starter-feign   Feign依赖，声明式开发  
spring-cloud-starter-eureka  
spring-boot-starter-web  
  
服务降级（Fallback）  
资源隔离  
主要通过线程池来实现资源隔离。通常在使用的时候我们会根据调用的远程服务划分出多个线程池。  
不影响应用功能的情况下通过实时的动态属性刷新：通过 Spring Cloud Config 与 Spring Cloud Bus 的联合使用  
对于大多数需求来说这样的消耗是微乎其微的，对于大部分的请求我们可以忽略线程池的额外开销  
Hystrix 也为此设计了另外的一个解决方案：信号量（Semaphores）。
使用信号量来控制单个依赖服务的并发度，信号量的开销要远比线程池的开销小得多，但是它不能设置超时和实现异步访问。所以，只有在依赖服务是足够可靠的情况下才使用信号量。  
HystrixCommand 和 HystrixObservableCommand 中 2 处支持信号量的使用  
  
断路器模式  
阈值涉及到三个重要参数：快照时间窗、请求总数下限、错误百分比下限。  
快照时间窗：统计默认最近的 10 秒的一些请求和错误数据  
请求总数下限：在快照时间窗内，必须满足请求总数下限才有资格进行熔断。  
错误百分比下限：当请求总数在快照时间窗内超过了下限，超过限定的错误百分比  

断路器保持在开路状态一段时间后 (默认 5 秒)，自动切换到半开路状态 (HALF-OPEN)。这时会判断下一次请求的返回情况，
如果请求成功，断路器切回闭路状态 (CLOSED)，否则重新切换到开路状态 (OPEN)。  

熔断只是作用在服务调用这一端  

THREAD（线程隔离）——HystrixCommand会在单独线程上执行，并发请求受线程池中线程数量限制  
SEMAPHORE（信号量隔离）——HystrixCommand将会在调用线程上执行，开销小，受信号量个数限制  

Feign：  
Feign是Netflix开发的声明式、模板化的HTTP客户端， Feign可以帮助我们更快捷、优雅地调用HTTP API。  
使用Feign，创建一个接口，并在接口上添加相应注解。  
支持了Spring MVC注解，并整合了Ribbon和Eureka，整合了Spring Cloud Ribbon和Spring Cloud Hystrix，还提供了一种声明式的Web服务客户端定义的方式。  
创建一个接口并用注解方式配置它，即可完成服务提供方的接口绑定。  


具体实现：  
Feign使用Hystrix  

yml：
>feign:
  hystrix:
    enabled: true


feign接口里加
@FeignClient(name="provider-user",fallback = HelloFallback.class)   		
fallback指定用什么类在断路后的处理。  
创建fallback的处理类。  

Hystrix Dashboard  
三种不同的监控方式  
默认的集群监控：http://turbine-hostname:port/turbine.stream   
指定的集群监控：http://turbine-hostname:port/turbine.stream?cluster=[clusterName]   
单体应用的监控：http://hystrix-app:port/actuator/ hystrix.stream  

Delay：控制服务器上轮询监控信息的延迟时间。  
Title：该参数可以展示合适的标题。  

application.yml ：
>management:
  endpoints:
    web:
      exposure:
        include: hystrix.stream


http://localhost:8030/actuator/hystrix.stream  

actuator/hystrix.stream  
监控之前要记得先访问一下服务中的任意一个接口  
  
Turbine  
Turbine真正做的，就是将每一个（指定）服务的 Hystrix/stream中的状态信息取出，并集中处理（计算与展示），应该说，它是具有自己独立的调度的，服务（实例）发现，服务连接，数据聚合，数据输出，共四个过程。  
Turbine首先通过 InstanceDiscovery 模块获取所有的实例信息（定期更新获取），ConnectionManager 负责连接到实例，连接上实例后，便会有源源不断的数据流发送给聚合器 Aggregator，之后，再传送给需要的地方。  

数据聚合主要包含3个部分：  
TurbineDataMonitor：数据监听，从实例处获取指标  
TurbineDataDispatcher：派发器，将数据聚合后输出到客户端或者下游的数据监听  
TurbineDataHandler：数据处理  

turbine.stream  

