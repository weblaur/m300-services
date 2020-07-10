# -*- mode: ruby -*-
# vi: set ft=ruby :

# All Vagrant configuration is done below. The "2" in Vagrant.configure
# configures the configuration version (we support older styles for
# backwards compatibility). Please don't change it unless you know what
# you're doing.

Vagrant.configure("2") do |config|
  config.vm.define "web" do |web|
    web.vm.box = "centos/8"
    web.vm.hostname = "cent8-web01"
    web.vm.network "public_network", ip: "192.168.1.10"
      web.vm.network "forwarded_port", guest: 80, host:8080, auto_correct:true
      web.vm.provider "virtualbox" do |vb|
        vb.memory = "1024"
        vb.gui = false
      end
      #web.vm.synced_folder ".", "/var/www/html"
      web.vm.provision "shell", inline: <<-SHELL
            dnf install -y httpd mariadb-server
            systemctl enable httpd
            systemctl enable mariadb
            systemctl enable firewalld
            systemctl start firewalld
            systemctl start httpd
            systemctl start mariadb
            systemctl status httpd
            firewall-cmd --zone=public --permanent --add-port=80/tcp 
            firewall-cmd --reload
SHELL
        end
  config.vm.define "db" do |db|
    db.vm.box = "centos/8"
    db.vm.hostname = "cent8-db01"
    db.vm.network "public_network", ip:"192.168.1.11"
      db.vm.network "forwarded_port", guest: 3306, host:3306, auto_correct:true
      db.vm.provider "virtualbox" do |vb|
        vb.memory = "1024"
        vb.gui = false
      end
      db.vm.provision "shell", inline: <<-SHELL
            dnf install -y httpd mariadb-server
            systemctl enable mariadb
            systemctl enable firewalld
            systemctl start firewalld
            systemctl start mariadb
            firewall-cmd --zone=public --permanent --add-port=3306/tcp 
            firewall-cmd --reload
SHELL
        end
end