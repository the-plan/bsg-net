# BSG

BOX_IMAGE = "bento/ubuntu-17.04"
# https://www.vagrantup.com/docs/provisioning/basic_usage.html

# sudo nano /etc/hosts 
# Update your hosts file
# 192.168.99.21 redis.local
# connect with medis with ip address 192.168.99.21 (port 7777)



Vagrant.configure("2") do |config|
  config.vm.box = BOX_IMAGE
  
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

  end # end of config


end