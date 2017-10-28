# BSG

BOX_IMAGE = "bento/ubuntu-17.04"
# https://www.vagrantup.com/docs/provisioning/basic_usage.html

# sudo nano /etc/hosts 
# Update your hosts file
# 192.168.99.21 redis.local
# connect with medis with ip address 192.168.99.21 (port 7777)
# 192.168.99.101 bs01.local 
# http://bs01.local:8080
# 192.168.99.102 bs02.local 
# http://bs02.local:8080
# 192.168.99.103 bs03.local 
# http://bs03.local:8080
# 192.168.99.104 bs04.local 
# http://bs04.local:8080
# 192.168.99.22 bsg-monitor.local


Vagrant.configure("2") do |config|
  config.vm.box = BOX_IMAGE
  
  # ===========================
  #   Redis server
  # ===========================
  config.vm.define "redis" do |node| 
    
    node.vm.hostname = "redis.local"
    node.vm.network "private_network", ip: "192.168.99.21"

    node.vm.network :forwarded_port, guest: 7777, host: 7777

    node.vm.provider "virtualbox" do |vb|
      vb.memory = 256
      vb.cpus = 1
      vb.name = "redis"
    end
    config.vm.provision "shell", inline: "echo hello"
    

    node.vm.provision "setup", type: "shell", inline: <<-SHELL
      echo "👋 Installing Redis..."
      apt-get install redis-server -y
    SHELL
    
    # vagrant up redis --provision-with start
    node.vm.provision "start", type: "shell", inline: <<-SHELL
      rm dump.rdb
      nohup redis-server --port 7777 --protected-mode no > /dev/null 2>&1 &
      echo "😜 bye! 👋👋👋"
    SHELL

  end # end of config - Redis Server

  # ===========================
  #   BaseStars microservices
  # ===========================

  basestars=[
    {
      :name => "bs01",
      :hostname => "bs01.local",
      :ip => "192.168.99.101",
      :box => BOX_IMAGE,
      :ram => 256,
      :cpu => 1,
      :guest_port => 8080,
      :host_port => 8081,
      :color => "FF5733"
    },
    {
      :name => "bs02",
      :hostname => "bs02.local",
      :ip => "192.168.99.102",
      :box => BOX_IMAGE,
      :ram => 256,
      :cpu => 1,
      :guest_port => 8080,
      :host_port => 8082,
      :color => "339FFF"
    },
    {
      :name => "bs03",
      :hostname => "bs03.local",
      :ip => "192.168.99.103",
      :box => BOX_IMAGE,
      :ram => 256,
      :cpu => 1,
      :guest_port => 8080,
      :host_port => 8083,
      :color => "46FF33"
    },
    {
      :name => "bs04",
      :hostname => "bs04.local",
      :ip => "192.168.99.104",
      :box => BOX_IMAGE,
      :ram => 256,
      :cpu => 1,
      :guest_port => 8080,
      :host_port => 8084,
      :color => "FF33F0"
    }
  ]
  # vagrant up bs01 --provision
  # vagrant up bs02 --provision
  # vagrant up bs03 --provision
  # vagrant up bs04 --provision
  basestars.each do |basestar| 
    config.vm.define basestar[:name] do |node|
      node.vm.box = basestar[:box]
      node.vm.hostname = basestar[:hostname]
      node.vm.network "private_network", ip: basestar[:ip]
      node.vm.network :forwarded_port, guest: basestar[:guest_port], host: basestar[:host_port]

      node.vm.provider "virtualbox" do |vb|
        vb.memory = basestar[:ram]
        vb.cpus =  basestar[:cpu]
        vb.name =  basestar[:name]
      end

      node.vm.provision "setup", type: "shell", inline: <<-SHELL
        echo "👋 Installing Java..."
        apt-get update -y
        #apt-get install curl python-software-properties -y
        apt-get install openjdk-8-jdk -y
        apt-get install maven -y
      SHELL

      # vagrant up bs02 --provision-with file
      #node.vm.provision "file", source: "./gitbucket-#{GIT_BUCKET_VERSION}", destination: "basestar"
      node.vm.provision "file", source: "../basestar", destination: "$HOME/basestar"
      # do the same thing with a git clone

      node.vm.provision "build", type: "shell", inline: <<-SHELL
        echo "👋 Building BaseStar..."
        cd basestar
        mvn clean package

      SHELL

      node.vm.provision "run", type: "shell", inline: <<-SHELL
        echo "👋 running BaseStar..."
        export PORT=#{basestar[:guest_port]}
        export SERVICE_PORT=#{basestar[:guest_port]}
        export COLOR=#{basestar[:color]}
        export REDIS_RECORDS_KEY="bsg-the-plan"
        export SERVICE_NAME=#{basestar[:name]}
        #export SERVICE_HOST=#{basestar[:hostname]}
        export SERVICE_HOST=#{basestar[:ip]}
        export SERVICE_ROOT="/api"
        export REDIS_PORT=7777
        export REDIS_HOST="192.168.99.21"
        #export REDIS_PASSWORD=

        cd basestar
        nohup java  -jar target/basestar-1.0-SNAPSHOT-fat.jar &
        # nohup java  -jar target/basestar-1.0-SNAPSHOT-fat.jar > /dev/null 2>&1 &

        echo "🌏 BaseStar listening on http://#{basestar[:hostname]}:#{basestar[:guest_port]}  ..."
      SHELL


    end # end config
  end # end basestars

  # ===========================
  #   WebApp
  # ===========================

  # ---------------------------
  #   BSG Monitor
  # ---------------------------
  config.vm.define "bsg-monitor" do |node| 
    
    node.vm.hostname = "bsg-monitor.local"
    node.vm.network "private_network", ip: "192.168.99.22"

    node.vm.network :forwarded_port, guest: 8080, host: 9090

    node.vm.provider "virtualbox" do |vb|
      vb.memory = 256
      vb.cpus = 1
      vb.name = "bsg-monitor"
    end
    
    node.vm.provision "setup", type: "shell", inline: <<-SHELL
      echo "👋 Installing Java..."
      apt-get update -y
      apt-get install openjdk-8-jdk -y
      #apt-get install maven -y

      ## Scala
      wget https://downloads.lightbend.com/scala/2.12.4/scala-2.12.4.deb
      dpkg -i scala-2.12.4.deb
      apt-get update
      apt-get install scala

      ## SBT
      wget https://dl.bintray.com/sbt/debian/sbt-0.13.16.deb
      dpkg -i sbt-0.13.16.deb
      apt-get update
      apt-get install sbt

    SHELL

    node.vm.provision "file", source: "../bsg-monitor", destination: "$HOME/bsg-monitor"

    node.vm.provision "build", type: "shell", inline: <<-SHELL
      echo "👋 Building BSG Monitor..."
      cd bsg-monitor

    SHELL
    
    node.vm.provision "run", type: "shell", inline: <<-SHELL
      echo "👋 running BSG Monitor..."

    SHELL

  end # end of config - BSG Monitor

  # ---------------------------
  #   BSG Map
  # ---------------------------


end
