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
192.168.99.105 bs05.local 
192.168.99.106 bs06.local 
192.168.99.107 bs07.local 
192.168.99.201 r01.local
192.168.99.202 r02.local
192.168.99.203 r03.local
192.168.99.204 r04.local
192.168.99.205 r05.local
192.168.99.206 r06.local
192.168.99.207 r07.local
192.168.99.208 r08.local
192.168.99.209 r09.local
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
vagrant up bs05; vagrant up bs06; vagrant up bs06
```

### Display WebApp

#### Initialize Scala Backend and NodeJS Frontend

> first time

```shell
vagrant up bsg-monitor; vagrant up bsg-map
```

### Run raiders

> first time

```shell
vagrant up r01; vagrant up r02; vagrant up r03; vagrant up r04
vagrant up r05; vagrant up r06; vagrant up r07; vagrant up r08; vagrant up r09
```

## Run when all vms are provisionned


> if you change starting parameters

- `vagrant up redis --provision-with start`
- `vagrant up bs01 --provision-with run`
- `vagrant up bs02 --provision-with run`
- `vagrant up bs03 --provision-with run`
- `vagrant up bs04 --provision-with run`
- `vagrant up bs05 --provision-with run`
- `vagrant up bs06 --provision-with run`
- `vagrant up bs07 --provision-with run`
- `vagrant up bsg-monitor --provision-with run`
- `vagrant up bsg-map --provision-with run`
- `vagrant up r01 --provision-with run`
- `vagrant up r02 --provision-with run`
- `vagrant up r03 --provision-with run`
- `vagrant up r04 --provision-with run`
- `vagrant up r05 --provision-with run`
- `vagrant up r06 --provision-with run`
- `vagrant up r07 --provision-with run`
- `vagrant up r08 --provision-with run`
- `vagrant up r09 --provision-with run`