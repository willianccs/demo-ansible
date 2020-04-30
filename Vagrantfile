# -*- mode: ruby -*-
# vi: set ft=ruby :

VAGRANTFILE_API_VERSION = "2"

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  #config.vm.box = "geerlingguy/debian10"
  config.vm.box = "ubuntu/xenial64"

  # Bootstrap
  config.ssh.insert_key = false
  config.vm.provider "virtualbox"
  config.vm.synced_folder ".", "/vagrant", disabled: true

  config.vm.provider "virtualbox" do |vb|

     vb.memory = 2048
     vb.cpus = 2
     vb.linked_clone = true
     vb.customize ['modifyvm', :id, '--audio', 'none']
  end

  # Define three VMs with static private IP addresses
  boxes = [
      { :name => "kube2", :ip => "192.168.20.2" },
      { :name => "kube3", :ip => "192.168.20.3" },
      { :name => "kubemaster", :ip => "192.168.20.4" }
  ]

  # Configure each of the VMs
  boxes.each_with_index do |opts,index|
    config.vm.define opts[:name] do |config|
      config.vm.hostname = opts[:name] + ".cluster.lab"
      config.vm.network :private_network, ip:opts[:ip]
      #config.vm.network "public_network", bridge: "enp3s0"

    end
  end

end
