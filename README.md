# Frequently Used Command

This is my composed list of daily using command.

# Routing IP

```shell
sudo route -n add -net {Target IP} {Gateway IP}
```

# CURL

Request with body.

```shell
curl -X PUT http://localhost:8081/config -d '{"compatibility": "NONE"}' -H "Content-Type:application/json"
```
