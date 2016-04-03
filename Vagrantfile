
Vagrant.configure(2) do |config|
  config.vm.box = "ubuntu/wily64"
  config.vm.box_check_update = false

  config.ssh.forward_agent = true

  config.vm.define "dev-dbcluster" do |dbcluster|
    dbcluster.vm.network "private_network", ip: "10.0.0.100"
    dbcluster.vm.network "forwarded_port", guest: 3306, host: 3330
    dbcluster.vm.hostname = "dev-dbcluster"
  end

  CLUSTER_SIZE = 3

  (1..CLUSTER_SIZE).each do |machine_id|
    config.vm.define "dev-db#{machine_id}" do |machine|
      machine.vm.hostname = "dev-db#{machine_id}"
      machine.vm.network "private_network", ip: "10.0.0.#{100+machine_id}"
      # Only execute once the Ansible provisioner,
      # when all the machines are up and ready.
      if machine_id == CLUSTER_SIZE 
        machine.vm.provision :ansible do |ansible|
          ansible.playbook = "playbooks/provision.yml"
          # Disable default limit to connect to all the machines
          ansible.limit = "all"
          ansible.groups = {
            "cluster_nodes" => ["dev-db[1:#{CLUSTER_SIZE}]"],
            "cluster_proxy" => ["dev-dbcluster"],
            "dev_env:children" => ["cluster_nodes", "cluster_proxy"],
            "dev_env:vars" => {
              "galera_cluster_members" => "10.0.0.101,10.0.0.102,10.0.0.103",
              "galera_maxscale_user" => "maxscale",
              "galera_maxscale_password" => "defaultmaxscalepassword"
            }
          }

        end
      end
    end
  end

  config.vm.provider "virtualbox" do |vb|
    vb.memory = 1024
    vb.cpus = 1
  end

end
