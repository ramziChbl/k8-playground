# -*- mode: ruby -*-
# vi: set ft=ruby :

MASTER_NODES_NUMBER = 1
WORKER_NODES_NUMBER = 2

Vagrant.configure("2") do |config|

  config.vm.provider "virtualbox" do |vb|
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
end
