# Apache Kafka with python examples

- Author Wenxiao Jeremy Gu, FEB 2016

- Modified for OSX 10.11.3 and AWS EC2 machine (RHEL 6.5). 

- Based on class notes and Google search.

 
#### Introduction

This document talks about the following topics:

- What's Apache Kafka?

- How to use Kafka (on mac)?

- How to use Kafka with python?

- How to use Docker Container with Kafka (on mac)?

- Solution of Homework 2 in the class

- Troubleshooting and lesson learned

- Next steps

- References

- Appendix


### Part 1: What's Apache Kafka

This is the second big data class in a certification from UW. The class talks about the data pipeline with focus on spark, kafka and storm. So before we go into the details, intuitively, Kafka would do something like a post office.

We here talk about the high-level definition, terminology and the use cases.

#### Official definition

_**Kafka** is a distributed, partitioned, replicated commit log service. It provides the functionality of a messaging system, but with a unique design._ 

#### Terminology on messaging

- **Topics:** _Kafka maintains feeds of messages in categories called topics._   

- **Producers:** _We'll call processes that publish messages to a Kafka topic producers._   

- **Consumers:** _We'll call processes that subscribe to topics and process the feed of published messages consumers._   

- **Broker:** _Kafka is run as a cluster comprised of one or more servers each of which is called a broker._   

#### Use Cases

Here are some popular use cases from the Kafka official documentation:

1. **Metrics:**  Kafka is often used for operational monitoring data. This involves aggregating statistics from distributed applications to produce centralized feeds of operational data. 

2. **Website Activity Tracking:**  The original use case for Kafka was to be able to rebuild a user activity tracking pipeline as a set of real-time publish-subscribe feeds. This means site activity (page views, searches, or other actions users may take) is published to central topics with one topic per activity type

3. **Log Aggregation**: Many people use Kafka as a replacement for a log aggregation solution. Log aggregation typically collects physical log files off servers and puts them in a central place (a file server or HDFS perhaps) for processing. 

4. **Stream Processing**: Many users end up doing stage-wise processing of data where data is consumed from topics of raw data and then aggregated, enriched, or otherwise transformed into new Kafka topics for further consumption. For example a processing flow for article recommendation might crawl article content from RSS feeds and publish it to an "articles" topic.

5. **Event Sourcing**: Event sourcing is a style of application design where state changes are logged as a time-ordered sequence of records. Kafka's support for very large stored log data makes it an excellent backend for an application built in this style. 

[Reference: Kafka Homepage](http://kafka.apache.org/documentation.html#quickstart)


### Part 2: How to use Kafka with python?

We use Kafka here to do the homework, _ _, 

#### Download the client

Note that we use scala 2.10 in this example. We download _kafka 0.8.2.2_ version under _scala 2.10_.

[Reference: Kafka Downloading page](https://www.apache.org/dyn/closer.cgi?path=/kafka/0.8.2.2/kafka_2.10-0.8.2.2.tgz)

```
    wget http://mirror.tcpdiag.net/apache/kafka/0.8.2.2/kafka_2.10-0.8.2.2.tgz
	cp kafka_2.10-0.8.2.2.tgz kafka.tgz
	rm kafka_2.10-0.8.2.2.tgz
	tar xzf "kafka.tgz"
	rm 	"kafka.tgz"	
	cp -r kafka_2.10-0.8.2.2 kafka
	rm -rf kafka_2.10-0.8.2.2
```

#### Environment

We also downloaded Kafka 0.9 but will not be shown as example here in this document. May the force be with you. 

```
    ## KAFKA 0.8.2
    export KAFKA_HOME="/Users/wenxiaogu/Dropbox/2-Bigdata/bigdata220/big_summary_2016/BigData2/0203_kafka_python/kafka"
    
    ## KAFKA 0.9.0
    export KAFKA_HOME2="/Users/wenxiaogu/Dropbox/2-Bigdata/bigdata220/big_summary_2016/BigData2/0203_kafka_python/kafka"

```

### Start Kafka

#### localhost (works)

See http://kafka.apache.org/07/quickstart.html

bin/kafka-topics.sh --create --zookeeper localhost:2181 --replication-factor 1 --partitions 1 --topic test
bin/kafka-topics.sh --list --zookeeper localhost:2181

head -n5 gfa25.csv| bin/kafka-console-producer.sh --broker-list localhost:9092 --topic test 

#### Howtownworks AWS (fails)

http://hortonworks.com/hadoop-tutorial/simulating-transporting-realtime-events-stream-apache-kafka/#section_3
ip: 54.191.159.137
export DOCKER_IP=54.191.159.137

Local ssh connecting to remote

$KAFKA_HOME/bin/kafka-topics.sh --zookeeper $DOCKER_IP:2181 --list
head -n5 gfa25.csv | $KAFKA_HOME/bin/kafka-console-producer.sh --topic topicNew --broker-list $DOCKER_IP:2181 

$KAFKA_HOME/bin/kafka-topics.sh --create --zookeeper $DOCKER_IP:2181 --replication-factor 1 --partitions 1 --topic truckevent  


On the remote machine
```
export PATH=$PATH:/usr/hdp/current/kafka-broker/bin

kafka-topics.sh --create --zookeeper localhost:2181 --replication-factor 1 --partitions 1 --topic test
kafka-topics.sh --list --zookeeper localhost:2181

bin/kafka-console-producer.sh --broker-list localhost:9092 --topic test
bin/kafka-console-consumer.sh --zookeeper localhost:2181 --topic test --from-beginning

head -n5 gfa25.csv| kafka-console-producer.sh --broker-list 54.191.159.137:9092 --topic test
```



If you reboot your cluster, you must restart the Ambari Server and all the Ambari Agents manually.

Log in to each machine in your cluster separately

On the Ambari Server host machine:

    sudo ambari-server restart

On each host in your cluster:

    sudo ambari-agent restart


### Set up Docker 
```
    export DOCKER_IP=$(docker-machine ip vm0)
```





<html> // Tab开头
<title>Markdown</title>
</html> // 四个空格开头


```
<p>code here</p>
```

```js
window.addEventListener('load', function() {
console.log('window loaded');
});
```

## Some Kafka Commands

### Obtain the code

pushd [dir]/uwbd-examples

dir: where you put git clones

git pull
git submodule init
git submodule update

### download client
wget https://www.apache.org/dyn/closer.cgi?path=/kafka/0.8.2.2/kafka_2.10-0.8.2.2.tgz

### environment
export KAFKA_HOME=[dir]/kafka_2.10-0.8.2.2
dir: where you unzipped kafka

export DOCKER_IP=$(docker-machine ip vm0)
vm0: the name of your vm

### install/start/restart kafka in docker

bash restart-kafka-in-docker.sh

<b>Note: you must have initialized your git submodules to get the uwbd-util directory</b>

### list topics

$KAFKA_HOME/bin/kafka-topics.sh --zookeeper $DOCKER_IP:2181 --list

### create topic

$KAFKA_HOME/bin/kafka-topics.sh --zookeeper $DOCKER_IP:2181 --create --partitions 1 --replication-factor 1 --topic topicNew

### get some data
curl http://www.ers.usda.gov/datafiles/International_Food_Security/AllCountries/gfa25.csv -o gfa25.csv

##### aside: head command

head gfa25.csv
# defaults to 10 lines

head -n5 gfa25.csv
# how you get 5 lines

### produce (aka send, aka publish) 5 messages to topic

head -n5 /Users/jehenrik/srchome/src/uwbd/uwbd-examples/data-cleaning-spark_python/gfa25.csv | $KAFKA_HOME/bin/kafka-console-producer.sh --topic topicNew --broker-list $DOCKER_IP:9092

Each line is interpreted as a message

### install python-kafka:

pip install -r requirements.txt

### produce from python:

python hello-kafka-producer.py

Excerpt:

client = KafkaClient(['{}:9092'.format(docker_ip)])
producer = SimpleProducer(client)

producer.send_messages('topicNew', b'a message')

### consume (aka receive, aka subscribe) from topic

python hello-kafka-consumer.py

Excerpt:

consumer = KafkaConsumer('topicNew',
group_id='my-group',
bootstrap_servers=['{}:9092'.format(docker_ip)])
for message in consumer:
print ("%s:%d:%d: key=%s value=%s" % (message.topic, message.partition,
message.offset, message.key,
message.value))
### create topic

The way that the kafka-python connector allows creation of a topic with a method called ensure_topic_exists.  In other words, create if it has not been created.

python hello-kafka-ensure_topic_exists.py

Excerpt:

client = KafkaClient(['{}:9092'.format(docker_ip)])
client.ensure_topic_exists("topic2")







