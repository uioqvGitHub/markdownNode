[TOC]

### 两种模式： Queue与 Topic

1. Topic
   1. 一对多
   2. 无状态
   3. 不安全
   4. 数据容易丢失的
2. Queue
   1. 一对一
   2. 有状态
   3. 安全
   4. 数据不会丢失的


### 简单示例

#### producter

```java
package com.uioqv.producter;

import javax.jms.Connection;
import javax.jms.ConnectionFactory;
import javax.jms.Destination;
import javax.jms.JMSException;
import javax.jms.Message;
import javax.jms.MessageProducer;
import javax.jms.Session;

import org.apache.activemq.ActiveMQConnectionFactory;

/**
 * 建立发送MQ池连接
 * 组装消息的内容
 * 发送消息到MQ消息池
 * @author uioqv
 *
 */
public class Producter {
	private static final String USERNAME = ActiveMQConnectionFactory.DEFAULT_USER;
	private static final String PASSWORD = ActiveMQConnectionFactory.DEFAULT_PASSWORD;
	private static final String BROKER_URL = ActiveMQConnectionFactory.DEFAULT_BROKER_URL;

	
	public static void main(String[] args) {
		ConnectionFactory connectionFactory; 	//连接工厂
		Connection connection;					//连接
		Session session;						//会话
		Destination destination;				//目标接口
		MessageProducer producer;				//生产者
		connectionFactory = new ActiveMQConnectionFactory(USERNAME, PASSWORD, BROKER_URL);
		
		try {
			connection = connectionFactory.createConnection();
			connection.start();
			
			/**
			 * 参数1：boolean: 	true---开启事务处理
			 * 					false--不开启事务处理
			 * 参数2: int: Session
			 * 			Session.AUTO_ACKNOWLEDGE: 	代表自动装配， 客户端无需做任何操作，就可以直接获取消息
			 * 			Session.CLIENT_ACKNOWLEDGE	则代表需要客户端手工去点击确认，才能收到消息
			 * 			Session.DUPS_OK_ACKNOWLEDGE	代表需要客户端进行备份，才能获取数据消息
			 * 			Session.SESSION_TRANSACTED	使用这个属性，第一个参数必需是false
			 */
			session = connection.createSession(true, Session.AUTO_ACKNOWLEDGE);
			destination = session.createQueue("uioqv");			//消息安全模式， 参数是名称
			producer = session.createProducer(destination);		//初始化生产者
			for (int i = 0; i < 10; i++) {
				Message message = session.createTextMessage("哈哈哈哈哈哈哈哈");
				producer.send(message );
			}

			session.commit();	//如果没有commit，则MQ平台服务器， 是无法获取消息内容，做监控的
			session.close();	//必须关闭session会话， 节省资源
			connection.close();
		} catch (JMSException e) {
			// TODO 自动生成的，异常未处理
			e.printStackTrace();
		}
		
	}
}
```

#### consumer

```java
package com.uioqv.consumer;

import javax.jms.Connection;
import javax.jms.ConnectionFactory;
import javax.jms.Destination;
import javax.jms.JMSException;
import javax.jms.Message;
import javax.jms.MessageConsumer;
import javax.jms.MessageProducer;
import javax.jms.Session;
import javax.jms.TextMessage;

import org.apache.activemq.ActiveMQConnectionFactory;

public class Consumer {
	private static final String USERNAME = ActiveMQConnectionFactory.DEFAULT_USER;
	private static final String PASSWORD = ActiveMQConnectionFactory.DEFAULT_PASSWORD;
	private static final String BROKER_URL = ActiveMQConnectionFactory.DEFAULT_BROKER_URL;

	
	public static void main(String[] args) {
		ConnectionFactory connectionFactory; 	//连接工厂
		Connection connection;					//连接
		Session session;						//会话
		Destination destination;				//目标接口
		MessageConsumer consumer;				//消费者
	
		connectionFactory = new ActiveMQConnectionFactory(USERNAME, PASSWORD, BROKER_URL);
		
		try {
			connection = connectionFactory.createConnection();
			connection.start();
			
			/**
			 * 参数1：boolean: 	true---开启事务处理
			 * 					false--不开启事务处理
			 * 参数2: int: Session
			 * 			Session.AUTO_ACKNOWLEDGE: 	代表自动装配， 客户端无需做任何操作，就可以直接获取消息
			 * 			Session.CLIENT_ACKNOWLEDGE	则代表需要客户端手工去点击确认，才能收到消息
			 * 			Session.DUPS_OK_ACKNOWLEDGE	代表需要客户端进行备份，才能获取数据消息
			 * 			Session.SESSION_TRANSACTED	使用这个属性，第一个参数必需是false
			 */
			session = connection.createSession(true, Session.AUTO_ACKNOWLEDGE);
			destination = session.createQueue("uioqv");			//消息安全模式， 参数是名称
			consumer = session.createConsumer(destination);
			
			while(true) {
				TextMessage message = (TextMessage)consumer.receive(10000);
				if(message!= null) {
					message.acknowledge();
					String text = message.getText();
					System.out.println(text);
				} else {
					break;
				}
			}

			session.commit();	//如果没有commit，则MQ平台服务器， 是无法获取消息内容，做监控的
			session.close();	//必须关闭session会话， 节省资源
			connection.close();
		} catch (JMSException e) {
			// TODO 自动生成的，异常未处理
			e.printStackTrace();
		}
	}
}
```

### web示例

#### activemq xml config

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:context="http://www.springframework.org/schema/context"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:amq="http://activemq.apache.org/schema/core"
	xmlns:jms="http://www.springframework.org/schema/jms"
	xsi:schemaLocation="http://www.springframework.org/schema/beans   
        http://www.springframework.org/schema/beans/spring-beans-4.0.xsd   
        http://www.springframework.org/schema/context   
        http://www.springframework.org/schema/context/spring-context-4.0.xsd
        http://www.springframework.org/schema/jms
        http://www.springframework.org/schema/jms/spring-jms-4.0.xsd
        http://activemq.apache.org/schema/core
        http://activemq.apache.org/schema/core/activemq-core-5.8.0.xsd">

	<!-- ActiveMQ 连接工厂 -->
 	<!-- 真正可以产生Connection的ConnectionFactory，由对应的 JMS服务厂商提供-->
	<!-- 如果连接网络：tcp://ip:61616；未连接网络：tcp://localhost:61616 以及用户名，密码-->
	<amq:connectionFactory id="amqConnectionFactory" brokerURL="tcp://localhost:61616" userName="admin" password="admin"  />

	<!-- Spring Caching连接工厂 -->
 	<!-- Spring用于管理真正的ConnectionFactory的ConnectionFactory -->  
	<bean id="connectionFactory" class="org.springframework.jms.connection.CachingConnectionFactory">
		<!-- 目标ConnectionFactory对应真实的可以产生JMS Connection的ConnectionFactory -->  
  		<property name="targetConnectionFactory" ref="amqConnectionFactory"></property>
		<!-- Session缓存数量 -->
		<property name="sessionCacheSize" value="100" />
	</bean>
	
	
	
	<!-- Spring JmsTemplate 的消息生产者 start-->
	<!-- 定义JmsTemplate的Queue类型 -->
	<bean id="jmsQueueTemplate" class="org.springframework.jms.core.JmsTemplate">
		<!-- 这个connectionFactory对应的是我们定义的Spring提供的那个ConnectionFactory对象 -->  
		<constructor-arg ref="connectionFactory" />
		<!-- 非pub/sub模型（发布/订阅），即队列模式 -->
		<property name="pubSubDomain" value="false" />
	</bean>
	<!-- 定义JmsTemplate的Topic类型 -->
	<bean id="jmsTopicTemplate" class="org.springframework.jms.core.JmsTemplate">
		 <!-- 这个connectionFactory对应的是我们定义的Spring提供的那个ConnectionFactory对象 -->  
		<constructor-arg ref="connectionFactory" />
		<!-- pub/sub模型（发布/订阅） -->
		<property name="pubSubDomain" value="true" />
	</bean>
	<!--Spring JmsTemplate 的消息生产者 end-->
	
	
	
	<!-- 消息消费者 start-->
	<!-- 定义Queue监听器 -->
	<jms:listener-container destination-type="queue" container-type="default" 
		connection-factory="connectionFactory" acknowledge="auto">
		<jms:listener destination="test.queue" ref="queueReceiver1"/>
		<jms:listener destination="test.queue" ref="queueReceiver2"/>
	</jms:listener-container>
	<!-- 定义Topic监听器 -->
	<jms:listener-container destination-type="topic" container-type="default" 
		connection-factory="connectionFactory" acknowledge="auto">
		<jms:listener destination="test.topic" ref="topicReceiver1"/>
		<jms:listener destination="test.topic" ref="topicReceiver2"/>
	</jms:listener-container>
	
	<!-- 消息消费者 end -->
</beans>  
```

#### queueSender

```java
package com.xdl.mq.producer.queue;

import javax.jms.JMSException;
import javax.jms.Message;
import javax.jms.Session;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.factory.annotation.Qualifier;
import org.springframework.jms.core.JmsTemplate;
import org.springframework.jms.core.MessageCreator;
import org.springframework.stereotype.Component;
import org.springframework.stereotype.Controller;
import org.springframework.stereotype.Service;

/**
 * 
 * @author liang
 * @description  队列消息生产者，发送消息到队列
 * 
 */
@Component("queueSender")   
public class QueueSender {
	
	@Autowired
	@Qualifier("jmsQueueTemplate")
	private JmsTemplate jmsTemplate;//通过@Qualifier修饰符来注入对应的bean
	
	/**
	 * 发送一条消息到指定的队列（目标）
	 * @param queueName 队列名称
	 * @param message 消息内容
	 */
	public void send(String queueName,final String message){
			jmsTemplate.send(queueName, new MessageCreator() {
			@Override
			public Message createMessage(Session session) throws JMSException {
				return session.createTextMessage(message);
			}
		});
	}
	
}

```

####topicSender

```java
package com.xdl.mq.producer.topic;

import javax.jms.JMSException;
import javax.jms.Message;
import javax.jms.Session;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.factory.annotation.Qualifier;
import org.springframework.jms.core.JmsTemplate;
import org.springframework.jms.core.MessageCreator;
import org.springframework.stereotype.Component;

/**
 * 
 * @author liang
 * @description   Topic生产者发送消息到Topic
 * 
 */

@Component("topicSender")
public class TopicSender {
	
	@Autowired
	@Qualifier("jmsTopicTemplate")
	private JmsTemplate jmsTemplate;
	
	/**
	 * 发送一条消息到指定的队列（目标）
	 * @param queueName 队列名称
	 * @param message 消息内容
	 */
	public void send(String topicName,final String message){
		jmsTemplate.send(topicName, new MessageCreator() {
			@Override
			public Message createMessage(Session session) throws JMSException {
				return session.createTextMessage(message);
			}
		});
	}

}
```

#### Receiver

```java

package com.xdl.mq.consumer.queue;

import javax.jms.JMSException;
import javax.jms.Message;
import javax.jms.MessageListener;
import javax.jms.TextMessage;

import org.springframework.stereotype.Component;

/**
 * 
 * @author liang
 * @description  队列消息监听器
 * 
 */
@Component
public class Receiver1 implements MessageListener {

	@Override
	public void onMessage(Message message) {
		try {
			System.out.println("Receiver1接收到消息:"+((TextMessage)message).getText());
		} catch (JMSException e) {
			e.printStackTrace();
		}
	}
}
```

