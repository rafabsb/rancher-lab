Vagrant.configure("2") do |config|
  
  config.vm.box = "centos/7"
  config.vm.box_version = "1710.01"

  N = 3
  (1..N).each do |machine_id|
    config.vm.define "machine#{machine_id}" do |machine|
      machine.vm.hostname = "machine#{machine_id}"
      machine.vm.network "private_network", ip: "192.168.56.10#{machine_id}"


      # customize vm #1 to have the necessary Rancher Master specs
      if machine_id == 1
        machine.vm.provider :virtualbox do |v|
          v.customize ["modifyvm", :id, "--natdnshostresolver1", "on"]
          v.customize ["modifyvm", :id, "--memory", 2048]
          v.customize ["modifyvm", :id, "--cpus", 2]
          v.customize ["modifyvm", :id, "--name", "machine#{machine_id}"]
        end
      else
        machine.vm.provider :virtualbox do |v|
          v.customize ["modifyvm", :id, "--natdnshostresolver1", "on"]
          v.customize ["modifyvm", :id, "--memory", 12288]
          v.customize ["modifyvm", :id, "--cpus", 12]
          v.customize ["modifyvm", :id, "--name", "machine#{machine_id}"]
        end
      end


      # Only execute once the Ansible provisioner,
      # when all the machines are up and ready.
      if machine_id == N
        machine.vm.provision :ansible do |ansible|
          # Disable default limit to connect to all the machines
          ansible.limit = "all"
          ansible.playbook = "playbook.yml"
          ansible.groups = {
            "master" => ["machine1"],
            "worker" => ["machine2","machine3"],
            "all_groups:children" => ["master", "worker"]
          }
        end
      end
    end
  end
end