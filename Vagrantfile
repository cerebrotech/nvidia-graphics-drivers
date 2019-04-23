# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|
  config.vm.box = "ubuntu/xenial64"
  config.vm.network "private_network", ip: "192.168.33.10"
  config.vm.synced_folder ".", "/home/vagrant/nvidia-graphics-drivers"

  config.vm.provider "virtualbox" do |vb|
    vb.cpus = 2
    vb.memory = "1024"
    vb.customize ["modifyvm", :id, "--uartmode1", "disconnected"]
    vb.customize ["modifyvm", :id, "--natdnshostresolver1", "on"]
  end

  config.vm.provision :shell, inline: <<-SHELL
    apt-get update
    apt-get install -y pbuilder ubuntu-dev-tools apt-file dh-make cdbs
  SHELL


  config.vm.provision :shell, privileged: false, inline: <<-SHELL
    test -f /home/vagrant/pbuilder/xenial-base.tgz || pbuilder-dist xenial create
    cd /home/vagrant/nvidia-graphics-drivers
    test -f NVIDIA-Linux-x86_64-384.111-no-compat32.run || debian/rules get-orig-source
    debuild --no-tgz-check -S -d -us -uc
    cd ..
    pbuilder-dist xenial build --buildresult /home/vagrant/nvidia-graphics-drivers/build \
      --architecture x86_64 nvidia-graphics-drivers-384_384.111-0ubuntu0.16.04.1.dsc
  SHELL
end
