# bsg-net
run them all

## Initial setup

- install VirtualBox + Vagrant
- create a directory: `mkdir the-plan`
- `cd the-plan`
- `git clone https://github.com/the-plan/basestar.git`
- `git clone https://github.com/the-plan/bsg-monitor.git`
- `git clone https://github.com/the-plan/bsg-map.git`
- `git clone https://github.com/the-plan/k-raider.git`
- `git clone https://github.com/the-plan/fake-raider.git`
- `git clone https://github.com/the-plan/bsg-net.git`
- `cd bsg-net`

## Update your hosts file

```
192.168.99.21 redis.local
192.168.99.22 bsg-monitor.local
192.168.99.23 bsg-map.local
192.168.99.101 bs01.local 
192.168.99.102 bs02.local 
192.168.99.103 bs03.local 
192.168.99.104 bs04.local 

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

### BaseStars

#### Initialize BaseStars microservices

> first time

```shell
vagrant up bs01; vagrant up bs02; vagrant up bs03; vagrant up bs04
```

### Display WebApp

#### Initialize Scala Backend and NodeJS Frontend

> first time

```shell
vagrant up bsg-monitor; vagrant up bsg-map
```
