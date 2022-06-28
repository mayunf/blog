---
title: springboot-rabbitmq
date: 2022-06-28 21:34:32
tags:
---
## 引入依赖

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-amqp</artifactId>
    <!-- 依据具体版本 -->
    <version>2.3.2.RELEASE</version> 
</dependency>
```

## 填写配置文件

```yaml
spring:
  rabbitmq:
    host: 127.0.0.1  	#mq服务器ip,默认为localhost
    port: 5672          #mq服务器port,默认为5672
    username: guest     #mq服务器username,默认为gust
    password: guest     #mq服务器password,默认为guest
```

## 自动注入Bean

```java
import org.springframework.amqp.core.*;
import org.springframework.amqp.support.converter.Jackson2JsonMessageConverter;
import org.springframework.amqp.support.converter.MessageConverter;
import org.springframework.beans.factory.annotation.Qualifier;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

/**
 * RabbitMQ
 *
 * @author mayunfeng
 * @date 2022/6/17 11:24
 */
@Configuration
public class RabbitMqConfiguration {

    public static final String QUEUE_TRANSIT_CLUE = "queue.transit.clue";
    public static final String QUEUE_TRANSIT_ORDER = "queue.transit.order";

    public static final String EXCHANGE_NAME = "exchange.transit";

    public static final String ROUTE_TRANSIT_CLUE = "route.transit.clue";
    public static final String ROUTE_TRANSIT_ORDER = "route.transit.order";

    @Bean(EXCHANGE_NAME)
    public Exchange exchange() {
        return ExchangeBuilder.topicExchange(EXCHANGE_NAME).durable(true).build();
    }

    @Bean(QUEUE_TRANSIT_CLUE)
    public Queue queueTransitClue() {
        return new Queue(QUEUE_TRANSIT_CLUE);
    }

    @Bean(QUEUE_TRANSIT_ORDER)
    public Queue queueTransitOrder() {
        return new Queue(QUEUE_TRANSIT_ORDER);
    }

    @Bean
    public Binding bindClue(@Qualifier(QUEUE_TRANSIT_CLUE) Queue queueTransitClue, @Qualifier(EXCHANGE_NAME) Exchange exchange) {
        return BindingBuilder.bind(queueTransitClue).to(exchange).with(ROUTE_TRANSIT_CLUE).noargs();
    }

    @Bean
    public Binding bindOrder(@Qualifier(QUEUE_TRANSIT_ORDER) Queue queueTransitOrder, @Qualifier(EXCHANGE_NAME) Exchange exchange) {
        return BindingBuilder.bind(queueTransitOrder).to(exchange).with(ROUTE_TRANSIT_ORDER).noargs();
    }

    /**
     * 序列化方式为json
     */
    @Bean
    public MessageConverter messageConverter() {
        return new Jackson2JsonMessageConverter();
    }
}
```

## 消费者-监听MQ队列

```java
import lombok.extern.slf4j.Slf4j;
import org.springframework.amqp.core.ExchangeTypes;
import org.springframework.amqp.rabbit.annotation.Exchange;
import org.springframework.amqp.rabbit.annotation.Queue;
import org.springframework.amqp.rabbit.annotation.QueueBinding;
import org.springframework.amqp.rabbit.annotation.RabbitListener;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.context.annotation.Configuration;

import java.text.SimpleDateFormat;
import java.util.Calendar;
import java.util.Objects;

/**
 * description
 *
 * @author mayunfeng
 * @date 2022/6/28 17:42
 */
@Configuration
@Slf4j
public class QueueTransitClueListener {

    @RabbitListener(bindings = @QueueBinding(
        value = @Queue(value = RabbitMqConfiguration.QUEUE_TRANSIT_CLUE, durable = "true"),
        exchange = @Exchange(value = RabbitMqConfiguration.EXCHANGE_NAME, type = ExchangeTypes.TOPIC, ignoreDeclarationExceptions = "true"),
        key = RabbitMqConfiguration.ROUTE_TRANSIT_CLUE
    ))
    public void receiveMessage(Long clueId) {
        log.info("QueueTransitClueListener:{}", clueId);
    }
}
```

## 启动Springboot应用

> 登录RabbitMQ后台查看，此时已经创建好交换机、队列，并且已经存在绑定关系。

![image-20220628211643233](https://raw.githubusercontent.com/mayunf/picgo/main/202206282116631.png)

![image-20220628211705984](https://raw.githubusercontent.com/mayunf/picgo/main/202206282117039.png)



## 生产者测试（PHP）

### 安装AMQP扩展

1. 下载扩展 [http://pecl.php.net/package/amqp](http://pecl.php.net/package/amqp)

2. 将`php_amqp.dll`复制到`"C:\phpstudy_pro\Extensions\php\php7.3.4nts\ext`，

3. 在php.ini中添加如下代码：

   ```
   [amqp]
   
   extension=php_amqp.dll 
   ```

4. 将`rabbitmq.4.dll`复制到php根目录 `C:\phpstudy_pro\Extensions\php\php7.3.4nts\`

5. 修改[apache](https://so.csdn.net/so/search?q=apache&spm=1001.2101.3001.7020)配置文件 `httpd.conf`，添加如下代码：

   ```
   # rabbitmq
   
   LoadFile  "C:\phpstudy_pro\Extensions\php\php7.3.4nts\rabbitmq.1.dll"
   ```

6. 重启看看是否已经加载了amqp模块：

### 编写消费者

#### 代码 `publish.php`

```php
<?php
//配置信息
$conn_args = array(
    'host' => '127.0.0.1',
    'port' => '5672',
    'login' => 'guest',
    'password' => 'guest',
    'vhost'=>'/'
);
$e_name = 'exchange.transit'; //交换机名
//$q_name = 'q_linvo'; //无需队列名
$k_route = 'route.transit.clue'; //路由key
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
$message="137821";
echo "Send Message:".$ex->publish($message, $k_route,AMQP_NOPARAM,['content_type' => 'application/json'])."\n";
$channel->commitTransaction(); //提交事务
$conn->disconnect();
?>
```

#### 执行测试

```
C:\phpstudy_pro\WWW\crm-v3>php publish.php
Send Message:1
```

#### 查看结果

![image-20220628212724025](https://raw.githubusercontent.com/mayunf/picgo/main/202206282127078.png)

