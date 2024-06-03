IMAGE_NAME = "ubuntu/jammy64" # ubuntu 22.04
QTD_CONTROL_PLANES = 2
QTD_WORKER_NODES = 2

Vagrant.configure("2") do |config|

    config.vm.define "haproxy" do |haproxy|
        haproxy.vm.box = IMAGE_NAME
        haproxy.vm.network "private_network", ip: "192.168.56.9"
        haproxy.vm.hostname = "haproxy"
        haproxy.vm.provider "virtualbox" do |v|
            v.memory = 512
            v.cpus = 1
        end
    end

    config.vm.provider "virtualbox" do |v|
        v.memory = 2048
        v.cpus = 2
    end
    
    (1..QTD_CONTROL_PLANES).each do |i|
        config.vm.define "controlplane-#{i}" do |master|
            master.vm.box = IMAGE_NAME
            master.vm.network "private_network", ip: "192.168.56.#{i + 10}"
            master.vm.hostname = "controlplane-#{i}"
            privileged = true
        end
    end

    (1..QTD_WORKER_NODES).each do |i|
        config.vm.define "node-#{i}" do |worker|
            worker.vm.box = IMAGE_NAME
            worker.vm.network "private_network", ip: "192.168.56.#{i + 20}"
            worker.vm.hostname = "node-#{i}"
            privileged = true
        end
    end

end
