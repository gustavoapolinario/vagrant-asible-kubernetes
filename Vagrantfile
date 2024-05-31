IMAGE_NAME = "ubuntu/jammy64" # ubuntu 22.04
QTD_NODES = 2
# K8S_VERSION = "1.30" # You can use this way to install the latest minor version
K8S_VERSION = "1.30.1-1.1" # You can use this way to install exacly version
K8S_MAJOR_VERSION = "1.30"
CALICO_VERSION = "3.28.0"

Vagrant.configure("2") do |config|
    config.ssh.insert_key = false

    config.vm.provider "virtualbox" do |v|
        v.memory = 2048
        v.cpus = 2
    end
      
    config.vm.define "k8s-master" do |master|
        master.vm.box = IMAGE_NAME
        master.vm.network "private_network", ip: "192.168.56.10"
        master.vm.hostname = "k8s-master"
        privileged = true
        master.vm.provision "ansible" do |ansible|
            ansible.playbook = "kubernetes-setup/main_control_plane.yml"
            ansible.extra_vars = {
                role: "master",
                k8s_version: K8S_VERSION,
                k8s_major_version: K8S_MAJOR_VERSION,
                calico_version: CALICO_VERSION,
            }
        end
        master.vm.network "forwarded_port", guest: 6443, host: 6443
    end

    (1..QTD_NODES).each do |i|
        config.vm.define "node-#{i}" do |worker|
            worker.vm.box = IMAGE_NAME
            worker.vm.network "private_network", ip: "192.168.56.#{i + 10}"
            worker.vm.hostname = "node-#{i}"
            worker.vm.provision "ansible" do |ansible|
                ansible.playbook = "kubernetes-setup/main_worker_node.yml"
                ansible.extra_vars = {
                    role: "worker",
                    k8s_version: K8S_VERSION,
                    k8s_major_version: K8S_MAJOR_VERSION,
                    calico_version: CALICO_VERSION,
                }
            end
        end
    end
end
