---
title: rabbitmq-lazy-queue
date: 2022-06-30 22:33:02
tags:
- java
- rabbitmq
- springboot
- queue
---
# RabbitMQ 延迟队列

[toc]

## 消息延迟推送的实现

### 背景

在 `RabbitMQ3.6.x` 之前我们一般采用死信队列+TTL过期时间来实现延迟队列，我们这里不做过多介绍，网上很多文章都有过介绍。在 `RabbitMQ 3.6.x` 开始，`RabbitMQ` 官方提供了延迟队列的插件，可以下载放置到 `RabbitMQ` 根目录下的 `plugins` 下

### 实现原理

原始的DLX + TTL 的模式，消息首先会路由到一个正常的队列，根据设置的 TTL 进入死信队列，与之不同的是通过 `x-delayed-message` 声明的交换机（具体代码请看下面config下的配置类交换机定义参数），它的消息在发布之后不会立即进入队列，先将消息保存至 Mnesia（一个分布式数据库管理系统，适合于电信和其它需要持续运行和具备软实时特性的 Erlang 应用。目前资料介绍的不是很多）。

这个插件将会尝试确认消息是否过期，首先要确保消息的延迟范围是 Delay > 0, Delay =< ?ERL_MAX_T（在 Erlang 中可以被设置的范围为 (2^32)-1 毫秒），如果消息过期通过 `x-delayed-type` 类型标记的交换机投递至目标队列，整个消息的投递过程也就完成了。

![https://img-blog.csdnimg.cn/img_convert/8111eb3f4e0f26cadd025e6322dbae0f.png](https://img-blog.csdnimg.cn/img_convert/8111eb3f4e0f26cadd025e6322dbae0f.png)

## 一、安装插件

### 插件地址

下载地址[https://github.com/rabbitmq/rabbitmq-delayed-message-exchange/releases](https://github.com/rabbitmq/rabbitmq-delayed-message-exchange/releases)

### 安装插件

```shell
# 下载插件
wget https://github.91chi.fun/https://github.com//rabbitmq/rabbitmq-delayed-message-exchange/releases/download/3.9.0/rabbitmq_delayed_message_exchange-3.9.0.ez
# 将下载的文件copy到docker容器内
docker cp ./rabbitmq_delayed_message_exchange-3.9.0.ez some-rabbit:/plugins
# 进入docker
docker exec -it some-rabbit /bin/bash
# 执行安装命令
root@myrabbit:/# rabbitmq-plugins enable rabbitmq_delayed_message_exchange
# 退出docker容器
root@myrabbit:/# exit
```

## Springboot 实现

### 引入pom

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-amqp</artifactId>
</dependency>
```
### 编写yaml

```yaml
rabbitmq:
  host: 192.168.1.50
  port: 5673
  username: guest
  password: guest
```

### 编写Bean

```java
import org.springframework.amqp.core.Binding;
import org.springframework.amqp.core.BindingBuilder;
import org.springframework.amqp.core.CustomExchange;
import org.springframework.amqp.core.Queue;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

import java.util.HashMap;

/**
 * description
 *
 * @author mayunfeng
 * @date 2022/6/30 20:49
 */
@Configuration
public class LazyExchangeConfiguration {

    public static final String LAZY_EX_CHANGE_NAME = "lazy.exchange";

    public static final String LAZY_QUEUE_NAME = "lazy.queue";

    public static final String LAZY_ROUTER_NAME = "lazy.route";

    @Bean
    public CustomExchange lazyExchange() {
        HashMap<String, Object> map = new HashMap<>();
        map.put("x-delayed-type", "direct");
        // 五个参数分别对应：名称，类型，持久化，自动删除，参数。CustomExchange类型必须是x-delayed-message
        return new CustomExchange(LAZY_EX_CHANGE_NAME, "x-delayed-message", true, false, map);
    }

    @Bean
    public Queue lazyQueue() {
        return new Queue(LAZY_QUEUE_NAME,true);
    }

    @Bean
    public Binding lazyQueueBinding() {
        return BindingBuilder.bind(lazyQueue()).to(lazyExchange()).with(LAZY_ROUTER_NAME).noargs();
    }

}
```

### 编写消费端

```java
import lombok.extern.slf4j.Slf4j;
import org.springframework.amqp.rabbit.annotation.RabbitListener;
import org.springframework.stereotype.Component;

/**
 * description
 *
 * @author mayunfeng
 * @date 2022/6/30 21:01
 * 监听 打印接受到的延迟后的消息 从channel中获取queue数据
 */
@Component
@Slf4j
public class LazyTopicListener {


    @RabbitListener(queues = LazyExchangeConfiguration.LAZY_QUEUE_NAME)
    public void onMessage(String message) {
        log.info(message);
    }

}

```

### 编写生产者

```java
import com.mayunfeng.psc.config.LazyExchangeConfiguration;
import org.springframework.amqp.rabbit.core.RabbitTemplate;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

import javax.annotation.Resource;

/**
 * description
 *
 * @author mayunfeng
 * @date 2022/6/30 22:19
 */
@Service
public class CustomSender {

    @Resource
    RabbitTemplate rabbitTemplate;

    public void send(String message){
        rabbitTemplate.convertAndSend(LazyExchangeConfiguration.LAZY_EX_CHANGE_NAME,LazyExchangeConfiguration.LAZY_ROUTER_NAME,message, messagePostProcessor -> {
            messagePostProcessor.getMessageProperties().setHeader("x-delay", 10000);
            return messagePostProcessor;
        });
    }
}
```

### 测试一下

```java
package com.mayunfeng.psc;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import com.mayunfeng.psc.util.CustomSender;
import org.junit.jupiter.api.Test;

/**
 * description
 *
 * @author mayunfeng
 * @date 2022/6/17 11:11
 */
@SpringBootTest
public class RabbitMqTest {

    @Autowired
    CustomSender customSender;

    @Test
    void sendDelayTopic () {
        String msg = "this is a message";
        customSender.send(msg);
    }

}
```

### php作为生产者

```php
<?php
//配置信息
$conn_args = array(
    'host' => '192.168.1.50',
    'port' => '5673',
    'login' => 'guest',
    'password' => 'guest',
    'vhost'=>'/'
);
$e_name = 'lazy.exchange'; //交换机名
$k_route = 'lazy.route'; //路由key

//创建连接和channel
$conn = new AMQPConnection($conn_args);
if (!$conn->connect()) {
    die("Cannot connect to the broker!\n");
}
$channel = new AMQPChannel($conn);

//创建交换机对象
$ex = new AMQPExchange($channel);
$ex->setName($e_name);
date_default_timezone_set("Asia/Shanghai");
//发送消息
$channel->startTransaction(); //开始事务
$message="这是一条延迟消息";

$ex->setType('x-delayed-message');
$ex->setArguments($arguments);
echo "Send Message:".$ex->publish($message, $k_route,AMQP_NOPARAM,[
    'content_type' => 'application/json',
    'headers' => [
        'x-delay' => 10000
        ]
    ])."\n";
$channel->commitTransaction(); //提交事务

$conn->disconnect();
?>
```

#### 关键点说明

- 交换机的arguments设置项里需要加`x-delayed-type`
- 消息在publish的时候需要在headers中加`x-delay`项，表示延迟多长时间从交换机中发送到队列中