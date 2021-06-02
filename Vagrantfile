# -*- mode: ruby -*-
# vi: set ft=ruby :

# All Vagrant configuration is done below. The "2" in Vagrant.configure
# configures the configuration version (we support older styles for
# backwards compatibility). Please don't change it unless you know what
# you're doing.
Vagrant.configure("2") do |config|
  # The most common configuration options are documented and commented below.
  # For a complete reference, please see the online documentation at
  # https://docs.vagrantup.com.

  # Every Vagrant development environment requires a box. You can search for
  # boxes at https://vagrantcloud.com/search.
  config.vm.box = "bento/ubuntu-20.04"
  config.vm.synced_folder "data/", "/vagrant/vagrant_data", disabled: true
        
  config.vm.network "forwarded_port", guest: 8200, host: 8200
  
   config.vm.provider :virtualbox do |vb|
      vb.memory = "1024"
  end
  
  config.vm.define "client" do |client|
    client.vm.hostname = "client.local"
  end

end
