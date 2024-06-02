IMAGE_NAME = "ubuntu/jammy64" # ubuntu 22.04
QTD_NODES = 2

Vagrant.configure("2") do |config|

    config.vm.provider "virtualbox" do |v|
        v.memory = 2048
        v.cpus = 2
    end
      
    config.vm.define "k8s-master" do |master|
        master.vm.box = IMAGE_NAME
        # master.vm.network "public_network", ip: "192.168.56.10", :primary => true # Choose the bridge on startup
        master.vm.network "public_network", ip: "192.168.56.10", :primary => true, bridge: "wlp5s0" # Bridge configuration
        master.vm.network "private_network", ip: "192.168.56.10"
        master.vm.hostname = "k8s-master"
        privileged = true
        master.vm.network "forwarded_port", guest: 6443, host: 6443
    end

    (1..QTD_NODES).each do |i|
        config.vm.define "node-#{i}" do |worker|
            worker.vm.box = IMAGE_NAME
            # worker.vm.network "public_network", ip: "192.168.56.#{i + 10}", :primary => true # Choose the bridge on startup
            worker.vm.network "public_network", ip: "192.168.56.#{i + 10}", :primary => true, bridge: "wlp5s0"# Bridge configuration
            worker.vm.network "private_network", ip: "192.168.56.#{i + 10}"
            worker.vm.hostname = "node-#{i}"
            privileged = true
        end
    end
end
