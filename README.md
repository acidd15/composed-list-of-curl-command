# Frequently Used Snippets

This is my composed list of daily using snippets.

# System

Adding a route.

```shell
sudo route -n add -net {Target IP} {Gateway IP}
```

Checking swap spaces.

```shell
cat /proc/swaps
```

Checking hw info.

```shell
lshw
```

Setting apt proxy.

```shell
sudo vi /etc/apt/apt.conf.d/01http-proxy

# Enter following config.
Acquire::http::Proxy "{PROXY SERVER}";
```

Setting alias to lo interface.

```shell
sudo ifconfig lo0 alias 10.200.10.1/24
```

Sending a file to multiple hosts using scp.

```shell
#!/bin/bash

set -e

USER=$1
FILE=$2

HOSTS=(
  # Put your hosts here
)

read -s -p "Password For $USER: " PASSWORD

for HOST in "${HOSTS[@]}"
do
    expect -c "
    spawn /usr/bin/scp $FILE $USER@$HOST:~/
    expect {
    "*password:*" { send $PASSWORD\r;interact }
    }
    exit
    "
done
```

SSH Tunelling. Local forwarding.

```shell
ssh -L 8080:target_host:8080 user@gateway_host
```

Customizing shell prompt.

```
# Put this line to .bash_profile in your home directory.
# Result: 736 [토  5 30 11:43:47] m0rph2us:git $ 
export PS1="\[\033[1;34m\]\!\[\033[0m\] \[\033[0;101m\][\d \t]\[\033[0;35m\] \u\[\033[0m\]:\W $ "
```

Seeing using ports of PID.

```
lsof -Pan -p PID -i
```

Finding process which uses specific port.

```
lsof -i tcp:8000
```

Checking network connection without telnet.

```
echo > "/dev/tcp/{HOST IP}/{PORT}"
```

# Docker

Installing docker in Ubuntu.

```shell
sudo apt update
 
sudo apt-get install \
    apt-transport-https \
    ca-certificates \
    curl \
    gnupg-agent \
    software-properties-common
 
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
 
sudo apt-key fingerprint 0EBFCD88
 
sudo add-apt-repository \
   "deb [arch=amd64] https://download.docker.com/linux/ubuntu \
   $(lsb_release -cs) \
   stable"
 
sudo apt-get update
 
sudo apt-get install docker-ce docker-ce-cli containerd.io
 
sudo apt-get install docker-compose
 
# Manual installation in case of docker-compose version too low.
sudo curl -L https://github.com/docker/compose/releases/download/1.25.4/docker-compose-`uname -s`-`uname -m` -o /usr/bin/docker-compose
 
docker -v
 
docker-compose -v
```

Changing default address pool of docker. You have to restart docker daemon.

```shell
sudo vi /etc/docker/daemon.json
# Enter following config.
{
  "default-address-pools":
  [
    {"base":"10.17.0.0/16","size":24}
  ]
}
```

Setting proxy for docker.

```shell
sudo mkdir /etc/systemd/system/docker.service.d
sudo vi /etc/systemd/system/docker.service.d/http-proxy.conf
# Enther following config.
[Service]
Environment="HTTP_PROXY={PROXY SERVER}"

sudo systemctl daemon-reload

# Confirm the environment value.
sudo systemctl show --property Environment docker
```

Creating docker network.

```shell
docker network create --gateway 10.255.0.1 --subnet 10.255.0.0/16 debezium-docker-net
```

Running a simple http server.

```shell
docker run --name simple-http-server -v $PWD:/usr/share/nginx/html:ro -d -p 8080:80 nginx
```

Commit stopped cotainer.

```
docker commit {stopped container_id} {new tag name} 
```

Start container with a different entrypoint.

```shell
docker run -it --entrypoint=sh {tag name}
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

Inspecting Binlog(SUPER or REPLICATION_CLIENT grant is required).

```shell
sudo docker run -it --rm mysql mysqlbinlog -h{Server host} -u{User ID} -p --read-from-remote-server --start-position=40000 --stop-position=50000 mysql-bin.000636
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

Producing a message without a key.

```
docker-compose -f docker-compose-something.yaml exec {Service name of kafka container} bash -c "echo 'test' | /kafka/bin/kafka-console-producer.sh \\
    --broker-list kafka-1:9092 \\
    --topic test_topic"
```

Consumming a topic.

```shell
docker-compose -f docker-compose-something.yaml exec {Service name of kafka container} /kafka/bin/kafka-console-consumer.sh \
    --bootstrap-server {Server list. e.g. kafka-1:9092} \
    --from-beginning \
    --property print.key=true \
    --formatter io.confluent.kafka.formatter.AvroMessageFormatter \
    --property schema.registry.url={Schema registry server if you using any} \
    --topic {Topic name}
```

Creating topic.

```shell
docker-compose -f docker-compose-something.yaml exec {Service name of kafka container} /kafka/bin/kafka-topics.sh \
    --create --bootstrap-server {Server list. e.g. kafka-1:9092} \
    --replication-factor {Replication factor. e.g. 1} --partitions {Partition size} --topic {Topic name}
```

Monitoring partition of topic.

```shell
docker-compose -f docker-compose-something.yaml exec {Service name of kafka container} /kafka/bin/kafka-topics.sh \
    --describe --zookeeper {Zookeeper host/node. e.g. zookeeper-1:2181/kafka} --topic {Topic name}
```

Inspecting debezium offset.

```shell
docker run -it --rm edenhill/kafkacat:1.5.0 -b {Server list. e.g. kafka-1:9092} -C -t cdc_connect_offsets -f 'Partition(%p) %k %s\n'
```

Changing debezium offset. See https://debezium.io/documentation/faq/#how_to_change_the_offsets_of_the_source_database.

```shell
echo '["mysql-connector-db",{"server":"cdc_db"}]|{"ts_sec":1590117090,"file":"mysql-bin.000636","pos":57737,"row":1,"server_id":1000241,"event":2}' | docker run -i -a stdin --rm edenhill/kafkacat:1.5.0 -P -b {Server list. e.g. kafka-1:9092} -t cdc_connect_offsets -K \| -p {Partition Number}
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

# ETC

Analyzing MySQL slow log.

```
pt-query-digest slow7.log > report-slow7.txt
```
