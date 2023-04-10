java -version

tar xvf kafka 2.12-3.4.0.tgz

bin/kafka-topics.sh

nano config/zookeeper.properties 
nano config/server.properties 

bin/zookeper-server-start.sh config/zookeper.pproperties
bin/kafka-server-start.sh config/server.properties 

bin/kafka-topics.sh --bootstrap-server localhost:9092 --partitions 2 --replication-factor 1 --topic firsttopic --create
bin/kafka-topics.sh --bootstrap-server localhost:9092 --list


bin/kafka-console-producer.sh --bootstrap-server localhost:9092 --topic firsttopic
bin/kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic firsttopic

bin/kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic firsttopic --from-beginning

bin/kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic firsttopic --group A

bin/kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic firsttopic --group B --from-beginning
