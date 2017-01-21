## MQTT and/or AMQP
MQTT (Message Queue Telemetry Transport) is designed to provide publish-and-subscribe messaging (no queues, in spite of the name) and `focused on simple than AMQP`. AMQP (Advanced Message Queuing Protocol) provides a wide range of features related to messaging, including reliable queuing, topic-based publish-and-subscribe messaging, flexible routing, transactions, and security.

>###### RabbitMQ supports major of messaging protocols including both.

```
ㅁ Author: suktae.choi
ㅁ Date: 2016.03.01
ㅁ References:
 - https://blog.codepath.com/2012/11/15/asynchronous-processing-in-web-applications-part-1-a-database-is-not-a-queue/
 - http://blog.codepath.com/2013/01/06/asynchronous-processing-in-web-applications-part-2-developers-need-to-understand-message-queues/
 - https://pamlesleylu.wordpress.com/2013/02/02/hello-world-for-spring-amqp-and-rabbitmq/
```

**Fundamental**

Servlet Container or WAS handles http request in front and bypasses it to java application logic.<br>
MQ Server handles enqueued message in MQ in front and bypasses it to java application logic.<br>

**Asynchronous Processing in Web Application**

<img src="https://github.com/agongi/study/blob/master/mqtt-amqp/images/Screen%20Shot%202016-03-01%20at%2014.33.01.png" width="75%">
