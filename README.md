# vagrant-fortios

I created this vagrant box for playing with fortigate because it has it's own DPI engine and I had to test some stuff on it.(finding out which vpn's their engine is able to block and which not) That's why traffic logging is enabled by default. However, if you woud like to do something else with it, changing it is very easy.

Example packer file for fortios + virtualbox.
Based on `FortiOS 6.0.0 build 0076`. Since the fortios eula does not
allow for sharing the image, please create the ova yourself by getting the
`fortios.qcow2` image and then running following commands.
```
VM='fortios-600build0076'
qemu-img convert -f qcow2 fortios.qcow2 -O vmdk $VM.vmdk
VBoxManage createvm --name $VM --ostype Linux_64 --register
VBoxManage storagectl $VM --name "SATA Controller" --add sata --controller IntelAHCI
VBoxManage storageattach $VM --storagectl "SATA Controller" --port 0 --device 0 --type hdd --medium fortios.vmdk
VBoxManage modifyvm $VM --ioapic on
VBoxManage modifyvm $VM --memory 1024 --vram 128
VBoxManage modifyvm $VM --nic1 nat
vboxmanage export $VM -o $VM.ova
```

Depending on your machine you might have to change `"boot_wait": "50s",` to a higher value

It will also setup DHCP on the `port2` interface with the 192.168.198.0/24 subnet. Change this if needed. Because the vagrant ssh provisioner does not work inside fortios, we do all default config in packer. Doing this using ansible provisioner [might be possible](https://docs.ansible.com/ansible/2.6/modules/fortios_config_module.html) , but I didn't test it.

Build the packer box:
```bash
packer build fortios-600build0076.json
```
when the box is built we can start playing `vagrant up`

The vagrant image also includes a ubuntu bionic64 client to test the fortigate. Connect to the client using `vagrant ssh client` Access the fortigate by going to http://localhost:8080 and login using user: vagrant pass: vagrant
