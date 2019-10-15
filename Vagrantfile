IMAGE_NAME = "ubuntu/bionic64"
K8S_NAME = "microk8s"
CLUSTER_NAME = "ditwl-k8s-01"
MASTERS_NUM = 1
MEM = 2048
CPU = 2
IP_BASE = "192.168.50."

Vagrant.configure("2") do |config|
    config.ssh.insert_key = false

    config.vm.provider "virtualbox" do |v|
        v.memory = MEM
        v.cpus = CPU
    end

    (1..MASTERS_NUM).each do |i|      
        config.vm.define "#{K8S_NAME}-#{i}" do |master|
            master.vm.box = IMAGE_NAME
            master.vm.network "private_network", ip: "#{IP_BASE}#{i + 10}"
            master.vm.hostname = "#{K8S_NAME}-#{i}"
            master.vm.provision "ansible" do |ansible|
                ansible.playbook = "ansible/microk8s.yml"
                #Generate Ansible Groups for inventory
                ansible.groups = {
                    "k8s_master" => ["#{K8S_NAME}-[1:#{MASTERS_NUM}]"]
                }
                #Redefine defaults
                ansible.extra_vars = {
                    k8s_cluster_name:       CLUSTER_NAME,                    
                    k8s_master_admin_user:  "vagrant",
                    k8s_master_admin_group: "vagrant",
                    k8s_master_apiserver_advertise_address: "#{IP_BASE}#{i + 10}",
                    k8s_master_node_name: "k8s-m-#{i}",
                    k8s_node_public_ip: "#{IP_BASE}#{i + 10}"
                }                
            end
        end
    end
end    