# BSG

BOX_IMAGE = "bento/ubuntu-17.04"
# https://www.vagrantup.com/docs/provisioning/basic_usage.html

# sudo nano /etc/hosts 
# Update your hosts file
# 192.168.99.21 redis.local
# connect with medis with ip address 192.168.99.21 (port 7777)
# 192.168.99.22 bsg-monitor.local
# 192.168.99.23 bsg-map.local
# 192.168.99.101 bs01.local 
# 192.168.99.102 bs02.local 
# 192.168.99.103 bs03.local 
# 192.168.99.104 bs04.local 
# 192.168.99.105 bs05.local 
# 192.168.99.106 bs06.local 
# 192.168.99.107 bs07.local 
# 192.168.99.201 r01.local
# 192.168.99.202 r02.local
# 192.168.99.203 r03.local
# 192.168.99.204 r04.local
# 192.168.99.205 r05.local
# 192.168.99.206 r06.local
# 192.168.99.207 r07.local
# 192.168.99.208 r08.local
# 192.168.99.209 r09.local

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
    },
    {
      :name => "bs05",
      :hostname => "bs05.local",
      :ip => "192.168.99.105",
      :box => BOX_IMAGE,
      :ram => 256,
      :cpu => 1,
      :guest_port => 8080,
      :host_port => 8085,
      :color => "edab38"
    },
    {
      :name => "bs06",
      :hostname => "bs06.local",
      :ip => "192.168.99.106",
      :box => BOX_IMAGE,
      :ram => 256,
      :cpu => 1,
      :guest_port => 8080,
      :host_port => 8086,
      :color => "9e37dd"
    },
    {
      :name => "bs07",
      :hostname => "bs07.local",
      :ip => "192.168.99.107",
      :box => BOX_IMAGE,
      :ram => 256,
      :cpu => 1,
      :guest_port => 8080,
      :host_port => 8087,
      :color => "3dedea"
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
        apt-get install openjdk-8-jdk -y
        apt-get install maven -y
      SHELL

      # vagrant up bs02 --provision-with file
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
      vb.memory = 1024
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
      sbt compile 
    SHELL
    
    node.vm.provision "run", type: "shell", inline: <<-SHELL
      echo "👋 Building and running BSG Monitor..."


      export PORT=8080
      export SERVICE_PORT=8080
      export REDIS_RECORDS_KEY="bsg-the-plan"
      export REDIS_PORT=7777
      export REDIS_HOST="192.168.99.21" # add to a variable
      #export REDIS_PASSWORD=

      cd bsg-monitor
      nohup sbt run &

      echo "🌏 Bsg-Monitor listening on http://bsg-monitor.local:8080 ..."
      
    SHELL

  end # end of config - BSG Monitor

  # ---------------------------
  #   BSG Map
  # ---------------------------

  config.vm.define "bsg-map" do |node| 
    
    node.vm.hostname = "bsg-map.local"
    node.vm.network "private_network", ip: "192.168.99.23"

    node.vm.network :forwarded_port, guest: 8080, host: 9091

    node.vm.provider "virtualbox" do |vb|
      vb.memory = 256
      vb.cpus = 1
      vb.name = "bsg-map"
    end
    
    node.vm.provision "setup", type: "shell", inline: <<-SHELL
      echo "👋 Installing nodejs..."
      apt-get update -y
      curl -sL https://deb.nodesource.com/setup_7.x | sudo bash -
      apt-get install nodejs -y

    SHELL

    node.vm.provision "file", source: "../bsg-map", destination: "$HOME/bsg-map"

    node.vm.provision "build", type: "shell", inline: <<-SHELL
      echo "👋 Building BSG MAp..."
      cd bsg-map
      npm install
    SHELL
    
    node.vm.provision "run", type: "shell", inline: <<-SHELL
      echo "👋 Building and running BSG Map..."
      cd bsg-map

      export PORT=8080
      #export RAIDERS_SERVICE=http://bsg-monitor.local:8080/api/raiders
      export RAIDERS_SERVICE=http://192.168.99.22:8080/api/raiders

      
      nohup npm start > /dev/null 2>&1 &

      echo "🌏 Bsg-Map listening on http://bsg-map.local:8080 ..."
      
    SHELL

  end # end of config - BSG Map


  # ===========================
  #   Raiders microservices
  # ===========================

  raiders=[
    {
      :name => "r01",
      :hostname => "r01.local",
      :ip => "192.168.99.201",
      :box => BOX_IMAGE,
      :ram => 256,
      :cpu => 1,
      :guest_port => 8080,
      :host_port => 7071
    },
    {
      :name => "r02",
      :hostname => "r02.local",
      :ip => "192.168.99.202",
      :box => BOX_IMAGE,
      :ram => 256,
      :cpu => 1,
      :guest_port => 8080,
      :host_port => 7072
    },
    {
      :name => "r03",
      :hostname => "r03.local",
      :ip => "192.168.99.203",
      :box => BOX_IMAGE,
      :ram => 256,
      :cpu => 1,
      :guest_port => 8080,
      :host_port => 7073
    },  
    {
      :name => "r04",
      :hostname => "r04.local",
      :ip => "192.168.99.204",
      :box => BOX_IMAGE,
      :ram => 256,
      :cpu => 1,
      :guest_port => 8080,
      :host_port => 7074
    },  
    {
      :name => "r05",
      :hostname => "r05.local",
      :ip => "192.168.99.205",
      :box => BOX_IMAGE,
      :ram => 256,
      :cpu => 1,
      :guest_port => 8080,
      :host_port => 7075
    },
    {
      :name => "r06",
      :hostname => "r06.local",
      :ip => "192.168.99.206",
      :box => BOX_IMAGE,
      :ram => 256,
      :cpu => 1,
      :guest_port => 8080,
      :host_port => 7076
    },
    {
      :name => "r07",
      :hostname => "r07.local",
      :ip => "192.168.99.207",
      :box => BOX_IMAGE,
      :ram => 256,
      :cpu => 1,
      :guest_port => 8080,
      :host_port => 7077
    }, 
    {
      :name => "r08",
      :hostname => "r08.local",
      :ip => "192.168.99.208",
      :box => BOX_IMAGE,
      :ram => 256,
      :cpu => 1,
      :guest_port => 8080,
      :host_port => 7078
    },   
    {
      :name => "r09",
      :hostname => "r09.local",
      :ip => "192.168.99.209",
      :box => BOX_IMAGE,
      :ram => 256,
      :cpu => 1,
      :guest_port => 8080,
      :host_port => 7079
    },                     
  ]

  raiders.each do |raider| 
    config.vm.define raider[:name] do |node|
      node.vm.box = raider[:box]
      node.vm.hostname = raider[:hostname]
      node.vm.network "private_network", ip: raider[:ip]
      node.vm.network :forwarded_port, guest: raider[:guest_port], host: raider[:host_port]

      node.vm.provider "virtualbox" do |vb|
        vb.memory = raider[:ram]
        vb.cpus =  raider[:cpu]
        vb.name =  raider[:name]
      end

      node.vm.provision "setup", type: "shell", inline: <<-SHELL
        echo "👋 Installing Java..."
        apt-get update -y
        apt-get install openjdk-8-jdk -y
        apt-get install maven -y
      SHELL

      node.vm.provision "file", source: "../k-raider", destination: "$HOME/k-raider"
      # do the same thing with a git clone

      node.vm.provision "build", type: "shell", inline: <<-SHELL
        echo "👋 Building Raider..."
        cd k-raider
        mvn clean package

      SHELL

      node.vm.provision "run", type: "shell", inline: <<-SHELL
        echo "👋 running Raider..."
        export PORT=#{raider[:guest_port]}
        export SERVICE_PORT=#{raider[:guest_port]}
        export COLOR=#{raider[:color]}
        export REDIS_RECORDS_KEY="bsg-the-plan"
        export SERVICE_NAME=#{raider[:name]}
        #export SERVICE_HOST=#{raider[:hostname]}
        export SERVICE_HOST=#{raider[:ip]}
        export SERVICE_ROOT="/api"
        export REDIS_PORT=7777
        export REDIS_HOST="192.168.99.21"
        #export REDIS_PASSWORD=

        cd k-raider
        nohup java -jar target/k-raider-1.0-SNAPSHOT-fat.jar &

        echo "🌏 Raider listening on http://#{raider[:hostname]}:#{raider[:guest_port]}  ..."
      SHELL

    end # end config
  end # end raiders

end
