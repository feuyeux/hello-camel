# hello-camel
## 0 Kafka without zookeeper

> ```bash
>lsof -i tcp:9092 |grep LISTEN |awk '{ print $2 }' |xargs kill
  
  ps aux |grep kafka |grep java |awk '{ print $2 }' |xargs kill
  ```

[install kafka without zookeeper](How to easily install kafka without zookeeper.md)

```
./bin/kafka-server-start.sh -daemon ./config/kraft/server1.properties
./bin/kafka-server-start.sh -daemon ./config/kraft/server2.properties
./bin/kafka-server-start.sh -daemon ./config/kraft/server3.properties
```

## 1 Camel Springboot

### Springboot

#### 1 kafka-offsetrepository[example]

https://github.com/apache/camel-spring-boot-examples

```bash
./bin/kafka-topics.sh --create --topic my-topic --partitions 1 --replication-factor 1 --bootstrap-server localhost:9092

bin/kafka-topics.sh --bootstrap-server localhost:9093 --list
```

```bash
$ cd camel-spring-boot-examples/kafka-offsetrepository
$ mvn spring-boot:run
```

```bash
2021-08-16 01:37:06.501  INFO 44458 --- [ducer[my-topic]] o.a.c.e.k.o.KafkaProcessor               : producer partition is:0
2021-08-16 01:37:06.503  INFO 44458 --- [sumer[my-topic]] consumer-route1                          : consumer >>> This is first producer: 86A76814921F073-0000000000000014
2021-08-16 01:37:07.502  INFO 44458 --- [ducer[my-topic]] producer-route-1                         : producer >>> This is first producer: 86A76814921F073-0000000000000015
2021-08-16 01:37:07.503  INFO 44458 --- [ducer[my-topic]] o.a.c.e.k.o.KafkaProcessor               : producer partition is:0
2021-08-16 01:37:07.504  INFO 44458 --- [sumer[my-topic]] consumer-route1                          : consumer >>> This is first producer: 86A76814921F073-0000000000000015
2021-08-16 01:37:08.507  INFO 44458 --- [ducer[my-topic]] producer-route-1                         : producer >>> This is first producer: 86A76814921F073-0000000000000016
```
#### 2 rabbitmq[example]

```bash
docker run -it -p 5672:5672 --hostname rabbit3Camel --name rabbit3Camel rabbitmq:3
```

```bash
$ cd camel-spring-boot-examples/rabbitmq
$ mvn spring-boot:run
```

```bash
2021-08-16 01:21:38.467  INFO 40987 --- [pool-1-thread-4] route3                                   : From RabbitMQ: Random number 12
2021-08-16 01:21:38.468  INFO 40987 --- [pool-1-thread-5] route3                                   : From RabbitMQ: Bigger random number 161
2021-08-16 01:21:39.456  INFO 40987 --- [pool-1-thread-6] route3                                   : From RabbitMQ: Random number 75
2021-08-16 01:21:40.457  INFO 40987 --- [pool-1-thread-7] route3                                   : From RabbitMQ: Random number 88
2021-08-16 01:21:40.463  INFO 40987 --- [pool-1-thread-8] route3                                   : From RabbitMQ: Bigger random number 126
2021-08-16 01:21:41.461  INFO 40987 --- [pool-1-thread-9] route3                                   : From RabbitMQ: Random number 80
2021-08-16 01:21:42.466  INFO 40987 --- [ool-1-thread-10] route3                                   : From RabbitMQ: Random number 62
```

## 2 Camel Quarkus

### 1 GraalVM

https://www.graalvm.org/docs/getting-started/macos

https://github.com/graalvm/graalvm-ce-builds/releases

```bash
sudo xattr -r -d com.apple.quarantine /Library/Java/JavaVirtualMachines/graalvm-ce-java16-21.2.0
```

```bash
GRAALVM_HOME=/Library/Java/JavaVirtualMachines/graalvm-ce-java16-21.2.0
export PATH=$GRAALVM_HOME/Contents/Home/bin:$PATH
export JAVA_HOME=$GRAALVM_HOME/Contents/Home
```

### 2 Quarkus

https://quarkus.io/guides

调试

```bash
./mvnw compile quarkus:dev
```

JVM构建和启动
```bash
./mvnw clean package
java -jar target/quarkus-app/quarkus-run.jar
```

Native构建和启动
```bash
./mvnw clean package -Pnative
./target/*-runner
```

### 3 Camel-Quarkus

#### first-steps
https://camel.apache.org/camel-quarkus/latest/user-guide/first-steps.html
```bash
mvn io.quarkus:quarkus-maven-plugin:2.1.1.Final:create \
    -DprojectGroupId=org.feuyeux.camel \
    -DprojectArtifactId=camel-quarkus \
    -Dextensions=camel-quarkus-log,camel-quarkus-timer
```
```bash
cd camel-quarkus
./mvnw compile quarkus:dev
```

```bash
curl http://localhost:8080/hello
```

#### rest-json[example]
https://camel.apache.org/camel-quarkus/latest/user-guide/examples.html

```bash
$ cd camel-quarkus-examples/rest-json
$ mvn clean compile quarkus:dev
```

```bash
$ curl http://localhost:8080/fruits
[{"name":"Apple","description":"Winter fruit"},{"name":"Pineapple","description":"Tropical fruit"}]

$ curl http://localhost:8080/legumes
[{"name":"Carrot","description":"Root vegetable, usually orange"},{"name":"Zucchini","description":"Summer squash"}]

$ curl http://localhost:8080/fruits -H "Content-Type: application/json" -XPOST -d '{"name": "orange","description": "Nice!"}'
[{"name":"Apple","description":"Winter fruit"},{"name":"Pineapple","description":"Tropical fruit"},{"name":"orange","description":"Nice!"}]

$ curl http://localhost:8080/fruits
[{"name":"Apple","description":"Winter fruit"},{"name":"Pineapple","description":"Tropical fruit"},{"name":"orange","description":"Nice!"}]
```

#### kafka[example]

```bash
$ cd camel-quarkus-examples/kafka
$ mvn clean compile quarkus:dev -Dkafka.bootstrap.servers=localhost:9092,localhost:9093,localhost:9094
```

SEDA(Staged Event Driven Architecture)

```bash
$ curl -X GET http://0.0.0.0:8080/example
Message #5
```

## 3 Camel K

TODO Kubernetes

