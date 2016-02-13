# Kafka with python examples
## Modified from the class notes

### Download the client
Here: https://www.apache.org/dyn/closer.cgi?path=/kafka/0.8.2.2/kafka_2.10-0.8.2.2.tgz

wget http://mirror.tcpdiag.net/apache/kafka/0.8.2.2/kafka_2.10-0.8.2.2.tgz
cp kafka_2.10-0.8.2.2.tgz kafka.tgz
rm kafka_2.10-0.8.2.2.tgz
tar xzf "kafka.tgz"
rm 	"kafka.tgz"	
cp -r kafka_2.10-0.8.2.2 kafka
rm -rf kafka_2.10-0.8.2.2

### Environment

export KAFKA_HOME="/Users/wenxiaogu/Dropbox/2-Bigdata/bigdata\ 220/big\ summary\ 2016\ -\ interview/bigdata2/0203_kafka_python/kafka"

export DOCKER_IP=$(docker-machine ip vm0)







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
