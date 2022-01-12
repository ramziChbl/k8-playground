# -*- mode: ruby -*-
# vi: set ft=ruby :

MASTER_NODES_NUMBER = 1
WORKER_NODES_NUMBER = 1

# Kubernetes distribution used to create the cluster
# Accepted values : kubernetes, k3s
K8_DISTRIBUTION = "kubernetes"
CNI = "calico"

MASTER_VM_MEMORY = "2048"
WORKER_VM_MEMORY = "1024"


Vagrant.configure("2") do |config|
  config.vm.box = "ubuntu/focal64"
  config.vm.provider "virtualbox" do |vb|
    vb.memory = "1024"
  end
  config.vm.box_check_update = false
  config.vbguest.auto_update = false

  nodes = []

  for i in 1..MASTER_NODES_NUMBER
    nodes.append("master#{i}")
  end

  for i in 1..WORKER_NODES_NUMBER
    nodes.append("worker#{i}")
  end

  nodes.each_with_index do |nodeName, index|
    if nodeName[0..5] == 'master'
      config.vm.define "master#{nodeName[6]}" do |master|       
        master.vm.hostname = nodeName
        master.vm.network "private_network", ip: "192.168.77.10#{nodeName[6]}"
        master.vm.provider "virtualbox" do |vb|
          vb.memory = MASTER_VM_MEMORY
        end
      end
    else
      config.vm.define "worker#{nodeName[6]}" do |worker|
        worker.vm.hostname = nodeName
        worker.vm.network "private_network", ip: "192.168.77.20#{nodeName[6]}"
        worker.vm.provider "virtualbox" do |vb|
          vb.memory = WORKER_VM_MEMORY
        end

        if index == MASTER_NODES_NUMBER + WORKER_NODES_NUMBER - 1
          worker.vm.provision :ansible do |ansible|
            ansible.verbose = "v"
            ansible.extra_vars = {
              k8_distribution: K8_DISTRIBUTION,
              cni: CNI
              }
            # Disable default limit to connect to all the machines
            ansible.limit = "all"
            ansible.config_file = "provisioning/ansible/ansible.cfg"
            ansible.playbook = "provisioning/ansible/main.yml"
        
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
