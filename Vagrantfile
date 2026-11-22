# -*- mode: ruby -*-
# vi: set ft=ruby :

MACHINES = {
  :ansible => {
        :box_name => "ubuntu/jammy64",
        :vm_name => "ansible",
        :net => [
                    ["192.168.50.50", 2, "255.255.255.0", "dns"]
                ]
  },
  :ns01 => {
        :box_name => "ubuntu/jammy64",
        :vm_name => "ns01",
        :net => [
                    ["192.168.50.10", 2, "255.255.255.0", "dns"]
                ]
  },
  :ns02 => {
        :box_name => "ubuntu/jammy64",
        :vm_name => "ns02",
        :net => [
                    ["192.168.50.11", 2, "255.255.255.0", "dns"]
                ]
  },
  :client => {
        :box_name => "ubuntu/jammy64",
        :vm_name => "client",
        :net => [
                    ["192.168.50.15", 2, "255.255.255.0", "dns"]
                ]
  },
  :client2 => {
        :box_name => "ubuntu/jammy64",
        :vm_name => "client2",
        :net => [
                    ["192.168.50.16", 2, "255.255.255.0", "dns"]
                ]
  },
}
Vagrant.configure(2) do |config|

  MACHINES.each do |boxname, boxconfig|
    config.vm.define boxname do |box|
      box.vm.box = boxconfig[:box_name]
      box.vm.hostname = boxconfig[:vm_name]
      box.vm.synced_folder "./shared", "/home/vagrant/shared", # пробрасываем в ВМ каталог с хоста
          owner: "vagrant", 
          group: "vagrant"
      box.vm.provider "virtualbox" do |v|
        v.memory = 768
        v.cpus = 1
       end
	  if boxconfig[:vm_name] == "ansible"
        box.vm.provision "shell", inline: <<-SHELL
          apt-get update
          apt-get install -y software-properties-common
          add-apt-repository --yes --update ppa:ansible/ansible
          apt-get install -y ansible openssh-client git
        SHELL
      end
      boxconfig[:net].each do |ipconf|
		box.vm.network("private_network", ip: ipconf[0], adapter: ipconf[1], netmask: ipconf[2], virtualbox__intnet: ipconf[3])
      end

      if boxconfig.key?(:public)
        box.vm.network "public_network", boxconfig[:public]
      end
		box.vm.provision "shell", inline: <<-SHELL
        mkdir -p ~root/.ssh
        cp ~vagrant/.ssh/auth* ~root/.ssh
      SHELL
    end
  end

end
