# Frequently Used Snippet

This is my composed list of daily using snippets.

# System

```shell
sudo route -n add -net {Target IP} {Gateway IP}
```

# CURL

Requesting with json body.

```shell
sudo curl -X PUT http://localhost:8081/config -d '{"compatibility": "NONE"}' -H "Content-Type:application/json"
```

Downloading a file through proxy server.

```shell
sudo curl --proxy {Proxy Server} -L {Location to download} -o {Path to save}
```

# Docker - MySQL

Using MySQL client with MySQL docker.

```shell
sudo docker run -it --rm mysql mysql -h{Server host} -P{Server port} -u{User ID}  -p
```

Dumping out specific table data.

```shell
sudo docker run -i mysql sh -c "exec mysqldump -h{Server host} -u{User ID} -p --lock-tables=false --column-statistics=0 --single-transaction --no-create-info {DB name} {Space separated table list} --where {Condition if you need}" > output_file
```

# Docker - Kafka

Resetting offset of the topic of the specific consummer group.

```shell
docker-compose -f docker-compose-something.yaml exec {Service name of kafka container} /kafka/bin/kafka-consumer-groups.sh --bootstrap-server {Server list. e.g. kafka-1:9092} --group {Consummer group name} --topic {Topic name} --reset-offsets --to-earliest —execute
```

Describing status of all consummer group.

```shell
docker-compose -f docker-compose-something.yaml exec {Service name of kafka container} /kafka/bin/kafka-consumer-groups.sh --bootstrap-server {Server list. e.g. kafka-1:9092} --all-groups --describe
```

Monitoring topic.

```shell
docker-compose exec connect /kafka/bin/kafka-console-consumer.sh \
    --bootstrap-server {Server list. e.g. kafka-1:9092} \
    --from-beginning \
    --property print.key=true \
    --formatter io.confluent.kafka.formatter.AvroMessageFormatter \
    --property schema.registry.url={Schema registry server if you using any} \
    --topic {Topic name}
```

# ETC

Analyzing MySQL slow log.

```
pt-query-digest slow7.log > report-slow7.txt
```
