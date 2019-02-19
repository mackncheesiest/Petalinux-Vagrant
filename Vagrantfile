# -*- mode: ruby -*-
# vi: set ft=ruby :

# All Vagrant configuration is done below. The "2" in Vagrant.configure
# configures the configuration version (we support older styles for
# backwards compatibility). Please don't change it unless you know what
# you're doing.
Vagrant.configure("2") do |config|

  # https://unix.stackexchange.com/a/379634  
  required_plugins = %w( vagrant-disksize )
  _retry = false
  required_plugins.each do |plugin|
      unless Vagrant.has_plugin? plugin
          system "vagrant plugin install #{plugin}"
          _retry=true
      end
  end

  if (_retry)
      exec "vagrant " + ARGV.join(' ')
  end

  # The most common configuration options are documented and commented below.
  # For a complete reference, please see the online documentation at
  # https://docs.vagrantup.com.

  # Every Vagrant development environment requires a box. You can search for
  # boxes at https://vagrantcloud.com/search.
  config.vm.box = "ubuntu/xenial64"

  # Relies on the vagrant-disksize plugin added above
  config.disksize.size = "50GB"

  # Create a forwarded port mapping which allows access to a specific port
  # within the machine from a port on the host machine. In the example below,
  # accessing "localhost:8080" will access port 80 on the guest machine.
  # NOTE: This will enable public access to the opened port
  # config.vm.network "forwarded_port", guest: 80, host: 8080

  # Create a forwarded port mapping which allows access to a specific port
  # within the machine from a port on the host machine and only allow access
  # via 127.0.0.1 to disable public access
  # config.vm.network "forwarded_port", guest: 80, host: 8080, host_ip: "127.0.0.1"

  # Create a private network, which allows host-only access to the machine
  # using a specific IP.
  # config.vm.network "private_network", ip: "192.168.33.10"

  # Create a public network, which generally matched to bridged network.
  # Bridged networks make the machine appear as another physical device on
  # your network.
  # config.vm.network "public_network"

  # Share an additional folder to the guest VM. The first argument is
  # the path on the host to the actual folder. The second argument is
  # the path on the guest to mount the folder. And the optional third
  # argument is a set of non-required options.
  config.vm.synced_folder "C:/MySharedFolder", "/vagrant_data"

  # Provider-specific configuration so you can fine-tune various
  # backing providers for Vagrant. These expose provider-specific options.
  # Example for VirtualBox:
  #
  config.vm.provider "virtualbox" do |vb|
      # Display the VirtualBox GUI when booting the machine
      vb.gui = true
  
      # Customize the amount of memory on the VM:
      vb.memory = "4096"
      vb.name = "Ubuntu-Vagrant"
      vb.cpus = 4

      vb.customize ["modifyvm", :id, "--usb", "on"]
      vb.customize ["modifyvm", :id, "--usbxhci", "on"]
      vb.customize ["modifyvm", :id, "--vram", "128"]
      vb.customize ["modifyvm", :id, "--clipboard", "hosttoguest"]
  end
  #
  # View the documentation for the provider you are using for more
  # information on available options.

  # Enable provisioning with a shell script. Additional provisioners such as
  # Puppet, Chef, Ansible, Salt, and Docker are also available. Please see the
  # documentation for more information about their specific syntax and use.
  config.vm.provision "shell", inline: <<-SHELL
    # Helps speed up package fetching and installation by explicitly pointing to US sources
    sudo sed -i "s/archive.ubuntu.com/us.archive.ubuntu.com/g" /etc/apt/sources.list
    
    # Needed for apt to find zlib1g:i386
    sudo dpkg --add-architecture i386
	
    sudo apt update
    # sudo apt -y install ubuntu-desktop virtualbox-guest-dkms virtualbox-guest-utils virtualbox-guest-x11 firefox

    # Petalinux dependencies
    sudo apt -y install gcc git make net-tools libncurses5-dev tftpd zlib1g-dev libssl-dev flex bison libselinux1 gnupg wget diffstat chrpath socat xterm autoconf libtool tar unzip texinfo zlib1g-dev gcc-multilib build-essential libsdl1.2-dev libglib2.0-dev zlib1g:i386 screen pax gzip python

    sudo mkdir /opt/petalinux
    sudo chown vagrant /opt/petalinux
    sudo chgrp vagrant /opt/petalinux
    # Assumes that the petalinux installer is available on the shared folder
    # Didn't want to run from the shared folder in case there is overhead associated with that
    cp /vagrant_data/petalinux-v2018.3-final-installer.run /tmp
    # https://github.com/mitre-cyber-academy/2019-ectf-vagrant/blob/master/provision/scripts/petalinux_install.sh#L12
    yes | /tmp/petalinux-v2018.3-final-installer.run /opt/petalinux > /dev/null
     
    echo "source /opt/petalinux/settings.sh" >> ~/.bashrc
    sudo userdel ubuntu

    sudo reboot now
  SHELL
end
