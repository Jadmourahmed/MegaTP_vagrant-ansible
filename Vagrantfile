# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|
  config.vm.box_check_update = false
  config.vm.boot_timeout = 600

  ADMIN_IP  = "192.168.56.10"
  NODE01_IP = "192.168.56.11"
  NODE02_IP = "192.168.56.12"
  WINSRV_IP = "192.168.56.20"

  # Admin – Ubuntu (Ansible + Zabbix)
  config.vm.define "admin" do |admin|
    admin.vm.box = "ubuntu/jammy64"
    admin.vm.hostname = "admin"
    admin.vm.network "private_network", ip: ADMIN_IP
    admin.vm.provider "virtualbox" do |vb|
      vb.memory = 2048
      vb.cpus   = 2
    end
    admin.vm.provision "shell", inline: <<-SHELL
      apt-get update
      apt-get install -y software-properties-common curl gnupg2 \\
                         ansible sshpass python3-pip nmap
      pip3 install pywinrm jmespath zabbix-api
      systemctl restart ssh
    SHELL
  end

  # Node01 – RHEL8 (PRIMARY HA)
  config.vm.define "node01" do |node01|
    node01.vm.box = "generic/rhel8"
    node01.vm.hostname = "node01"
    node01.vm.network "private_network", ip: NODE01_IP
    node01.vm.provider "virtualbox" do |vb|
      vb.memory = 1024
      vb.cpus   = 1
    end
    node01.vm.provision "shell", inline: <<-SHELL
      dnf install -y epel-release
      dnf update -y
      sed -i 's/#PermitRootLogin.*/PermitRootLogin no/' /etc/ssh/sshd_config
      sed -i 's/PasswordAuthentication no/PasswordAuthentication yes/' /etc/ssh/sshd_config
      systemctl restart sshd
    SHELL
  end

  # Node02 – RHEL8 (BACKUP HA)
  config.vm.define "node02" do |node02|
    node02.vm.box = "generic/rhel8"
    node02.vm.hostname = "node02"
    node02.vm.network "private_network", ip: NODE02_IP
    node02.vm.provider "virtualbox" do |vb|
      vb.memory = 1024
      vb.cpus   = 1
    end
    node02.vm.provision "shell", inline: <<-SHELL
      dnf install -y epel-release
      dnf update -y
      sed -i 's/#PermitRootLogin.*/PermitRootLogin no/' /etc/ssh/sshd_config
      sed -i 's/PasswordAuthentication no/PasswordAuthentication yes/' /etc/ssh/sshd_config
      systemctl restart sshd
    SHELL
  end

  # WinSrv – Windows Server 2019 (WinRM auto)
  config.vm.define "winsrv" do |winsrv|
    winsrv.vm.box = "gusztavvargadr/windows-server-2019-standard"
    winsrv.vm.hostname = "winsrv"
    winsrv.vm.network "private_network", ip: WINSRV_IP
    winsrv.vm.provider "virtualbox" do |vb|
      vb.gui = false
      vb.memory = 2048
      vb.cpus = 2
    end
  end
end
