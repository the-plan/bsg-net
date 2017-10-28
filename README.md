# bsg-net
run them all

## Update your hosts file

```
192.168.99.21 redis.local

```

### Redis server

#### Initialize redis server

> first time

```shell
vagrant up redis
```

#### Stop redis server

```shell
vagrant halt redis
```

#### Run redis server

```shell
vagrant up redis --provision-with start
```
