Vagrant.require_version ">= 1.4.3"
VAGRANTFILE_API_VERSION = "2"

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
    numNodes = 4
    r = numNodes..1
    (r.first).downto(r.last).each do |i|
        config.vm.define "node#{i}" do |node|
            node.vm.box = "chef/centos-6.5"
            node.vm.provider "virtualbox" do |v|
              v.name = "node#{i}"
              v.customize ["modifyvm", :id, "--memory", "1024"]
            end
            node.vm.network :private_network, ip: "10.0.0.10#{i}"

            # base setup
            node.vm.hostname = "node#{i}"
            
            node.vm.provision "shell" do |s|
                s.path = "scripts/setup-os.sh"
                s.args = "-t #{numNodes}"
            end

            node.vm.provision "shell", path: "scripts/setup-java.sh"

            node.vm.provision "shell" do |s|
                s.path = "scripts/setup-hadoop.sh"
                s.args = "-t #{numNodes}"
            end

            if i == 1
                # namenode
                node.vm.provision "shell" do |s|
                    s.path = "scripts/init-hadoop.sh"
                    s.args = "-r namenode"
                end
                node.vm.network "forwarded_port", guest: 50070, host: 50070
                # storm nimbus
                # storm ui
                # hbase master
            else
                # zookeeper
                node.vm.provision "shell" do |s|
                    s.path = "scripts/setup-zookeeper.sh"
                    s.args = "-t #{numNodes}"
                end
                # datanode
                node.vm.provision "shell" do |s|
                    s.path = "scripts/init-hadoop.sh"
                    s.args = "-r datanode"
                end
                # hbase regionserver
                # kafka broker
                node.vm.provision "shell" do |s|
                    s.path = "scripts/setup-kafka.sh"
                    s.args = "-t #{numNodes}"
                end
                # storm supervisor
                # elasticsearch
                # solr
            end
        end
    end
end