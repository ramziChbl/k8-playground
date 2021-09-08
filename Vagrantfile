# -*- mode: ruby -*-
# vi: set ft=ruby :

MASTER_NODES_NUMBER = 1
WORKER_NODES_NUMBER = 1

Vagrant.configure("2") do |config|

  config.vm.provider "virtualbox" do |vb|
  #   # Display the VirtualBox GUI when booting the machine
  #   vb.gui = true

    # Customize the amount of memory on the VM:
    vb.memory = "2048"
  end

  nodes = []

  for i in 1..MASTER_NODES_NUMBER
    nodes.append("master#{i}")
  end

  for i in 1..WORKER_NODES_NUMBER
    nodes.append("worker#{i}")
  end

  nodes.each_with_index do |nodeName, index|
    if nodeName[0..5] == 'master'
      config.vm.define "master#{nodeName[6]}" do |machine|
        machine.vm.box = "ubuntu/xenial64"
        machine.vm.hostname = nodeName
        machine.vm.network "private_network", ip: "192.168.77.10#{nodeName[6]}"
      end
    else
      config.vm.define "worker#{nodeName[6]}" do |machine|
        machine.vm.box = "ubuntu/xenial64"
        machine.vm.hostname = nodeName
        machine.vm.network "private_network", ip: "192.168.77.20#{nodeName[6]}"
        if index == MASTER_NODES_NUMBER + WORKER_NODES_NUMBER - 1
          machine.vm.provision :ansible do |ansible|
            # Disable default limit to connect to all the machines
            ansible.limit = "all"
            ansible.playbook = "provisioning/kubeadm_install.yml"
            ansible.config_file = "provisioning/ansible.cfg"
            ansible.groups = {
              "master" => nodes[0..MASTER_NODES_NUMBER-1],
              "worker" => nodes[MASTER_NODES_NUMBER..-1],
            }
          end
        end
      end
    end
  end
  
#  config.vm.define 'master' do |master|
#    master.vm.box = "ubuntu/xenial64"
#    master.vm.hostname = "master"
#    master.vm.network "private_network", ip: "192.168.77.15"#, mac: "080027xxxxxx"
#  end

#  config.vm.define 'worker1' do |worker1|
#    worker1.vm.box = "ubuntu/xenial64"
#    worker1.vm.hostname = "worker1"
#    worker1.vm.network "private_network", ip: "192.168.77.16"
#  end

  # Disable automatic box update checking. If you disable this, then
  # boxes will only be checked for updates when the user runs
  # `vagrant box outdated`. This is not recommended.
  # config.vm.box_check_update = false

  # Create a forwarded port mapping which allows access to a specific port
  # within the machine from a port on the host machine. In the example below,
  # accessing "localhost:8080" will access port 80 on the guest machine.
  # NOTE: This will enable public access to the opened port
  # config.vm.network "forwarded_port", guest: 80, host: 8080

  # Create a forwarded port mapping which allows access to a specific port
  # within the machine from a port on the host machine and only allow access
  # via 127.0.0.1 to disable public access
  # config.vm.network "forwarded_port", guest: 80, host: 8080, host_ip: "127.0.0.1"

  # Create a private network, which allows host-only access to the machine
  # using a specific IP.
  # config.vm.network "private_network", ip: "192.168.33.10"

  # Create a public network, which generally matched to bridged network.
  # Bridged networks make the machine appear as another physical device on
  # your network.
  # config.vm.network "public_network"

  # Share an additional folder to the guest VM. The first argument is
  # the path on the host to the actual folder. The second argument is
  # the path on the guest to mount the folder. And the optional third
  # argument is a set of non-required options.
  # config.vm.synced_folder "../data", "/vagrant_data"

  # Provider-specific configuration so you can fine-tune various
  # backing providers for Vagrant. These expose provider-specific options.
  # Example for VirtualBox:
  #
  

#  config.vm.provision "ansible" do |ansible|
#    ansible.playbook = "provisioning/kubeadm_install.yml"
#    ansible.config_file = "provisioning/ansible.cfg"
#  end


  #
  # View the documentation for the provider you are using for more
  # information on available options.

  # Enable provisioning with a shell script. Additional provisioners such as
  # Ansible, Chef, Docker, Puppet and Salt are also available. Please see the
  # documentation for more information about their specific syntax and use.
  # config.vm.provision "shell", inline: <<-SHELL
  #   apt-get update
  #   apt-get install -y apache2
  # SHELL
end
