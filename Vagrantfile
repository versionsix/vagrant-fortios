Vagrant.configure("2") do |config|
  config.vm.define "fortios" do |subconfig|
    subconfig.vm.box = "file://packer_virtualbox-ovf_virtualbox.box"
    subconfig.ssh.insert_key = false
    subconfig.vbguest.auto_update = false
    subconfig.vm.synced_folder ".", "/vagrant", disabled: true
    subconfig.vm.network "forwarded_port", guest: 80, host: 8080
    subconfig.vm.network "forwarded_port", guest: 8443, host: 8443
    subconfig.vm.network "private_network", virtualbox__intnet: "mynetwork", auto_config: false
    subconfig.vm.network "private_network", virtualbox__intnet: "mysecondnetwork", auto_config: false
    # VirtualBox.
    subconfig.vm.define "virtualbox" do |virtualbox|
      virtualbox.gui = false
      virtualbox.memory = 1024
      virtualbox.cpus = 1
    end
    subconfig.vm.provision :ansible do |ansible|
      ansible.limit = "all"
      ansible.playbook = "fortios-config.yml"
    end
  end
  config.vm.define "client" do |subconfig|
    subconfig.vm.box = "ubuntu/bionic64"
    subconfig.vm.synced_folder ".", "/vagrant", disabled: true
    subconfig.vm.network "private_network", virtualbox__intnet: "mynetwork", type: "dhcp"
    subconfig.vm.provision "shell", run: "always",
      inline: "sudo route add default gw 192.168.198.1"
  end
end
