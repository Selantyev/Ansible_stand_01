# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure(2) do |config|
  config.vm.box = "centos/7"

#  config.vm.provision "ansible" do |ansible|
    #ansible.verbose = "vvv"
    #ansible.playbook = "provisioning/playbook.yml"
    #ansible.become = "true"
#  end

  config.vm.provider "virtualbox" do |v|
	  v.memory = 512
  end

  config.vm.define "nginx" do |nginx|
    nginx.vm.network "private_network", ip: "192.168.50.15", virtualbox__intnet: "dns"
    nginx.vm.hostname = "nginx"
  end

  config.vm.define "ansible" do |ansible|
    ansible.vm.network "private_network", ip: "192.168.50.20", virtualbox__intnet: "dns"
    ansible.vm.hostname = "ansible"
  end
  
end
