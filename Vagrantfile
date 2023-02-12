# -*- mode: ruby -*-
# vi: set ft=ruby :

# see vagrant documentation for details

Vagrant.configure("2") do |config|

  (1..2).each do |i|

    config.vm.define "node#{i}" do |node|

      node.vm.box = "generic/ubuntu2204"
      node.vm.hostname = "node#{i}"
      node.vm.network "public_network", bridge: "Lan 98"

      node.vm.provider "hyperv" do |h|
        h.cpus = 2
        h.memory = 3072
        h.maxmemory = 3072
      end

      node.ssh.insert_key = false
      node.ssh.private_key_path = ["~/.ssh/id_ed25519","~/.vagrant.d/insecure_private_key"]

      node.vm.provision "file", source: "./netplan.yaml", destination: "/tmp/netplan.yaml"
      node.vm.provision "file", source: "~/.ssh/id_ed25519.pub", destination: "/tmp/key.pub"
      node.vm.provision "shell", inline: <<-SHELL
        cat /tmp/key.pub | tee -a /home/vagrant/.ssh/authorized_keys
        sed -e "s/xx/#{10+i}/g" /tmp/netplan.yaml | tee /etc/netplan/01-netcfg.yaml
        chmod 0644 /etc/netplan/01-netcfg.yaml
        netplan apply
      SHELL

    end
  end
end
