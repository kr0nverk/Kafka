# APACHE KAFKA
## STEP 1: GET KAFKA

Загрузите последнюю версию Kafka [https://www.apache.org/kafka](https://www.apache.org/dyn/closer.cgi?path=/kafka/3.4.0/kafka_2.13-3.4.0.tgz) и извлеките ее:

```
tar xvf kafka 2.12-3.4.0.tgz
```

## STEP 2: START THE KAFKA ENVIRONMENT

*ПРИМЕЧАНИЕ: В вашей локальной среде должна быть установлена Java 8+.*
```
java -version
```

Apache Kafka можно запустить с помощью ZooKeeper. Чтобы начать работу с любой конфигурацией, следуйте разделу ниже,

### Kafka with ZooKeeper

Загрузите последнюю версию ZooKeeper [https://www.apache.org/zookeeper](https://www.apache.org/dyn/closer.lua/zookeeper/zookeeper-3.8.1/apache-zookeeper-3.8.1-bin.tar.gz) и извлеките ее:

```
tar xvf kafka 2.12-3.4.0.tgz
```
Создадим в нашей папке папку data и в ней еще две папки zookeper и server:
Настроить конфигурацию ZooKeeper:
```
# DataDir=/<PATH>/kafka/data/zookeeper
nano config/zookeeper.properties 
```
![Иллюстрация к проекту](https://github.com/kr0nverk/Kafka/blob/master/Images/2.png)

Настроить конфигурацию сервера:
```
# log.dirs=/<PATH>/kafka/data/server
nano config/server.properties 
```
![Иллюстрация к проекту](https://github.com/kr0nverk/Kafka/blob/master/Images/3.png)

Выполните следующие команды, чтобы запустить все службы в правильном порядке:
```
# Start the ZooKeeper service
bin/zookeper-server-start.sh config/zookeper.pproperties
```
Откройте другой сеанс терминала и запустите:
```
# Start the Kafka broker service
bin/kafka-server-start.sh config/server.properties 
```
![Иллюстрация к проекту](https://github.com/kr0nverk/Kafka/blob/master/Images/4.png)

## STEP 3: CREATE A TOPIC TO STORE YOUR EVENTS

Kafka - это платформа распределенной потоковой передачи событий, которая позволяет вам читать, записывать, хранить и обрабатывать события (в документации также называемые записями или сообщениями) на многих компьютерах.

Примерами событий являются платежные транзакции, обновления геолокации с мобильных телефонов, заказы на доставку, измерения датчиков с устройств Интернета вещей или медицинского оборудования и многое другое. Эти события организованы и хранятся в разделах. В упрощенном виде тема похожа на папку в файловой системе, а события - это файлы в этой папке.

Итак, прежде чем вы сможете написать свои первые события, настроиv репликацию и создадим топик. Откройте другой сеанс терминала и запустите:
```
bin/kafka-topics.sh --bootstrap-server localhost:9092 --partitions 2 --replication-factor 1 --topic firsttopic --create
```
![Иллюстрация к проекту](https://github.com/kr0nverk/Kafka/blob/master/Images/5.png)

Все инструменты командной строки Kafka имеют дополнительные опции: запустите kafka-topics.sh команда без каких-либо аргументов для отображения информации об использовании. Например, посмотреть созданные топики можно командой:
```
bin/kafka-topics.sh --bootstrap-server localhost:9092 --list
```
![Иллюстрация к проекту](https://github.com/kr0nverk/Kafka/blob/master/Images/6.png)

## STEP 4: WRITE SOME EVENTS INTO THE TOPIC
Клиент Kafka взаимодействует с брокерами Kafka через сеть для записи (или чтения) событий. После получения брокеры будут хранить события надежным и отказоустойчивым образом столько, сколько вам нужно, — даже вечно.

Запустите producer, чтобы записать несколько событий в вашу тему. По умолчанию каждая введенная вами строка приводит к записи отдельного события в тему.
```
bin/kafka-console-producer.sh --bootstrap-server localhost:9092 --topic firsttopic
```
![Иллюстрация к проекту](https://github.com/kr0nverk/Kafka/blob/master/Images/7.png)

## STEP 5: READ THE EVENTS
Откройте другой сеанс терминала и запустите consumer, чтобы прочитать только что созданные вами события:
```
bin/kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic firsttopic
```

![Иллюстрация к проекту](https://github.com/kr0nverk/Kafka/blob/master/Images/8.png)
![Иллюстрация к проекту](https://github.com/kr0nverk/Kafka/blob/master/Images/9.png)

Но как можно заметить мы не увидили не одного сообщения, чтобы подписаться на все вышедшие сообщения нам нужно чуть изменить команду:
```
bin/kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic firsttopic --from-beginning
```
![Иллюстрация к проекту](https://github.com/kr0nverk/Kafka/blob/master/Images/10.png)

Поскольку события надежно хранятся в Kafka, они могут быть прочитаны столько раз и таким количеством потребителей, сколько вы захотите. Вы можете легко убедиться в этом, открыв еще один сеанс терминала и повторно запустив предыдущую команду.

## STEP 5: CREATE GROUPS
Воспользуемся этой же командой, но воспользуемся еще группой:
```
bin/kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic firsttopic --group A
```
Откроем второй окно и напишем точно такуюже команду.

![Иллюстрация к проекту](https://github.com/kr0nverk/Kafka/blob/master/Images/11.png)

Теперь начнем отправлять сообщения в продюсере, и увидим что сообщения придут только на одного получателя, у нас они пришли на одного, но вообще могут прийдти и на другого. Брокер проверяет какой из разделов свободен и отправляет на него сообщение, если он занят то отправляет на другой. В этом и заключается преимущество разделов - в балансировке. Если повторно вызвать группу A то она ничего не получит, тк уже все получила, но если создать группу B, то она получит все сообщения:

```
bin/kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic firsttopic --group B --from-beginning
```
![Иллюстрация к проекту](https://github.com/kr0nverk/Kafka/blob/master/Images/12.png)

## STEP 8: TERMINATE THE KAFKA ENVIRONMENT
Теперь, когда вы дошли до конца, не бойтесь удалять среду Kafka — разрушьте все побыстрее.
+ Остановите клиенты producer и consumer с помощью Ctrl-C, если вы еще этого не сделали.
+ Остановите брокер Kafka с помощью Ctrl-C.
+ Наконец, остановите сервер ZooKeeper с помощью Ctrl-C.

Если вы также хотите удалить любые данные вашей локальной среды Kafka, включая любые события, которые вы создали по пути, выполните команду:
```
rm -rf /<PATH>/kafka/data/server /<PATH>/kafka/data/zookeeper /tmp/kraft-combined-logs
```

## CONGRATULATIONS!
Вы успешно запустили Apache Kafka.
Чтобы узнать больше, можно воспользоваться:
- Прочтите краткое [Введение](https://kafka.apache.org/intro), чтобы узнать, как Kafka работает на высоком уровне, ее основные концепции и как она сравнивается с другими технологиями. Чтобы разобраться в Kafka более подробно, ознакомьтесь с [Документацией](https://kafka.apache.org/documentation/).
- Просмотрите [Примеры](https://kafka.apache.org/powered-by) использования, чтобы узнать, как другие пользователи нашего мирового сообщества извлекают пользу из Kafka.
