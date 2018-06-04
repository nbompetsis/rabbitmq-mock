# RabbitMQ-mock
[![Build Status](https://travis-ci.com/fridujo/rabbitmq-mock.svg?branch=master)](https://travis-ci.com/fridujo/rabbitmq-mock)
[![Coverage Status](https://codecov.io/gh/fridujo/rabbitmq-mock/branch/master/graph/badge.svg)](https://codecov.io/gh/fridujo/rabbitmq-mock/)
[![Maven Central](https://img.shields.io/maven-central/v/com.github.fridujo/rabbitmq-mock.svg)](https://search.maven.org/#search|ga|1|a:"rabbitmq-mock")
[![License](https://img.shields.io/github/license/fridujo/spring-automocker.svg)](https://opensource.org/licenses/Apache-2.0)

Mock for RabbitMQ Java [amqp-client](https://github.com/rabbitmq/rabbitmq-java-client).

### Motivation

This project aims to emulate RabbitMQ behavior for test purposes, through:
* `com.rabbitmq.client.ConnectionFactory` with [`MockConnectionFactory`](src/main/java/com/github/fridujo/rabbitmq/mock/MockConnectionFactory.java)
* `org.springframework.amqp.rabbit.connection.ConnectionFactory` with [`MockConnectionFactory`](src/main/java/com/github/fridujo/rabbitmq/mock/spring/MockConnectionFactory.java)

## Example Use

### Without Spring
Replace the use of `com.rabbitmq.client.ConnectionFactory` by [`MockConnectionFactory`](src/main/java/com/github/fridujo/rabbitmq/mock/MockConnectionFactory.java)

```java
ConnectionFactory factory = new MockConnectionFactory();
try (Connection conn = factory.newConnection()) {
    try (Channel channel = conn.createChannel()) {
        GetResponse response = channel.basicGet(queueName, autoAck);
        byte[] body = response.getBody();
        long deliveryTag = response.getEnvelope().getDeliveryTag();

        // Do what you need with the body

        channel.basicAck(deliveryTag, false);
    }
}
```

More details in [integration-test](src/test/java/com/github/fridujo/rabbitmq/mock/IntegrationTest.java)

### With Spring
Replace the use of `org.springframework.amqp.rabbit.connection.ConnectionFactory` by [`MockConnectionFactory`](src/main/java/com/github/fridujo/rabbitmq/mock/spring/MockConnectionFactory.java)

```java

@Configuration
@Import(AppConfiguration.class)
class TestConfiguration {
    @Bean
    ConnectionFactory connectionFactory() {
        return new MockConnectionFactory();
    }
}
```

More details in [integration-test](src/test/java/com/github/fridujo/rabbitmq/mock/spring/SpringIntegrationTest.java)

## Getting Started

### Maven
Add the following dependency to your **pom.xml**
```xml
<dependency>
    <groupId>com.github.fridujo</groupId>
    <artifactId>rabbitmq-mock</artifactId>
    <version>1.0.1</version>
    <scope>test</scope>
</dependency>
```

### Gradle
Add the following dependency to your **build.gradle**
```groovy
repositories {
	mavenCentral()
}

// ...

dependencies {
	// ...
	testCompile('com.github.fridujo:rabbitmq-mock:1.0.0')
	// ...
}
```

### Building from Source

You need [JDK-8](http://jdk.java.net/8/) to build RabbitMQ-Mock. The project can be built with Maven using the following command.
```
mvn clean package
```

All integration tests can be launched with Maven using the following command.
```
mvn clean test
```

Since Maven has incremental build support, you can usually omit executing the clean goal.

### Installing in the Local Maven Repository

The project can be installed in a local Maven Repository for usage in other projects via the following command.
```
mvn clean install
```
