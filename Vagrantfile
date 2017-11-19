Vagrant.configure("2") do |config|
  
  config.vm.box = "centos/7"
  config.vm.box_version = "1710.01"
  config.vm.provision :shell, path: "bootstrap.sh"

  N = 3
  (1..N).each do |machine_id|

    file_to_disk = "/media/rafael/0615474d-0295-40af-a11e-161981f63972/home/rafael/lab-vm-disks/large_disk#{machine_id}.vdi"
    
    config.vm.define "machine#{machine_id}" do |machine|
      machine.vm.hostname = "machine#{machine_id}"
      machine.vm.network "private_network", ip: "192.168.56.10#{machine_id}"



      # customize vm #1 to have the necessary Rancher Master specs
      if machine_id == 1
        machine.vm.provider :virtualbox do |v|
          v.customize ["modifyvm", :id, "--natdnshostresolver1", "on"]
          v.customize ["modifyvm", :id, "--memory", 1024]
          v.customize ["modifyvm", :id, "--cpus", 1]
          v.customize ["modifyvm", :id, "--name", "machine#{machine_id}"]
          #create aditional IDE disk
          unless File.exist?(file_to_disk)
            v.customize ['createhd', '--filename', file_to_disk, '--size', 20 * 1024]
          end
          v.customize ['storageattach', :id, '--storagectl', 'IDE', '--port', 1, '--device', 0, '--type', 'hdd', '--medium', file_to_disk]

        end 
      else
        machine.vm.provider :virtualbox do |v|
          v.customize ["modifyvm", :id, "--natdnshostresolver1", "on"]
          v.customize ["modifyvm", :id, "--memory", 4096]
          v.customize ["modifyvm", :id, "--cpus", 4]
          v.customize ["modifyvm", :id, "--name", "machine#{machine_id}"]
          #create aditional IDE disk
          unless File.exist?(file_to_disk)
            v.customize ['createhd', '--filename', file_to_disk, '--size', 20 * 1024]
          end
          v.customize ['storageattach', :id, '--storagectl', 'IDE', '--port', 1, '--device', 0, '--type', 'hdd', '--medium', file_to_disk]

        end
      end


      # Only execute once the Ansible provisioner,
      # when all the machines are up and ready.
#     if machine_id == N
#       machine.vm.provision :ansible do |ansible|
#         # Disable default limit to connect to all the machines
#         ansible.limit = "all"
#         ansible.playbook = "provisioning/playbook.yml"
#         ansible.groups = {
#           "master" => ["machine1"],
#           "worker" => ["machine2","machine3"],
#           "all_groups:children" => ["master", "worker"]
#         }
#       end
#     end
    end
  end
end