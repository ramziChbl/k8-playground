# -*- mode: ruby -*-
# vi: set ft=ruby :

MASTER_NODES_NUMBER = 1
WORKER_NODES_NUMBER = 1

# Kubernetes distribution used to create the cluster
# Accepted values : kubernetes, k3s
K8_DISTRIBUTION = "kubernetes"
CNI = "calico"


Vagrant.configure("2") do |config|

  config.vm.provider "virtualbox" do |vb|
    vb.memory = "2048"
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
      config.vm.define "master#{nodeName[6]}" do |machine|
       
        machine.vm.box = "ubuntu/focal64"
        machine.vm.hostname = nodeName
        machine.vm.network "private_network", ip: "192.168.77.10#{nodeName[6]}"
      end
    else
      config.vm.define "worker#{nodeName[6]}" do |machine|
        machine.vm.box = "ubuntu/focal64"
        machine.vm.hostname = nodeName
        machine.vm.network "private_network", ip: "192.168.77.20#{nodeName[6]}"
        if index == MASTER_NODES_NUMBER + WORKER_NODES_NUMBER - 1
          machine.vm.provision :ansible do |ansible|
            ansible.verbose = "v"
            ansible.extra_vars = {
              cni: CNI
              }
            # Disable default limit to connect to all the machines
            ansible.limit = "all"
            if K8_DISTRIBUTION == "kubernetes"
              ansible.playbook = "provisioning/kubernetes/ansible/kubeadm_install.yml"
              ansible.config_file = "provisioning/kubernetes/ansible/ansible.cfg"
            elsif K8_DISTRIBUTION == "k3s"
              ansible.playbook = "provisioning/k3s/ansible/k3s_install.yml"
              ansible.config_file = "provisioning/k3s/ansible/ansible.cfg"
            end

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
