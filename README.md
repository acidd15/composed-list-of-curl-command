# Frequently Used Snippet

This is my composed list of daily using snippets.

# System

Adding a route.

```shell
sudo route -n add -net {Target IP} {Gateway IP}
```

# Docker

Install docker in Ubuntu.

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
