# -*- mode: ruby -*-
# vi: set ft=ruby :

disk = './secondDisk.vdi'
 
Vagrant.configure(2) do |config|
  config.vm.box = "my-box-centos7-2004-01"
 
  config.vm.provider "virtualbox" do |v|
    v.memory = 512
    v.cpus = 1
  end

  config.vm.define "bs" do |bs|
    bs.vm.provider "virtualbox" do |vb|
      unless File.exist?(disk)
      vb.customize ['createhd', '--filename', disk, '--variant', 'Fixed', '--size', 5 * 1024]
      end
      vb.customize ["storagectl", :id, "--name", "SATA", "--add", "sata" ]
      vb.customize ["storageattach", :id,  "--storagectl", "SATA", "--port", 1, "--device", 0, "--type", "hdd", "--medium", disk]
    end
      bs.vm.network "private_network", ip: "192.168.50.10", virtualbox__intnet: "net1"
      bs.vm.hostname = "backupserver"
      
  end

  config.vm.define "bc" do |bc|
    bc.vm.network "private_network", ip: "192.168.50.11", virtualbox__intnet: "net1"
    bc.vm.hostname = "backupclient"
  end 
end