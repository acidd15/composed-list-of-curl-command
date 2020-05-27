# Frequently Used Command

This is my composed list of daily using command.

# Routing IP

```shell
sudo route -n add -net {Target IP} {Gateway IP}
```

# CURL

Request with json body.

```shell
sudo curl -X PUT http://localhost:8081/config -d '{"compatibility": "NONE"}' -H "Content-Type:application/json"
```

Download file through proxy server.

```shell
sudo curl --proxy {Proxy Server} -L {Location to download} -o {Path to save}
```
