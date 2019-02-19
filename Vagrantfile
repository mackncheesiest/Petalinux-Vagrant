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

  config.vm.box = "ubuntu/xenial64"

  # Relies on the vagrant-disksize plugin added above
  config.disksize.size = "50GB"

  config.vm.synced_folder "/localhome/jmack2545/vagrant/vagrant_share", "/vagrant_data"
  config.vm.synced_folder ".", "/vagrant", disabled: true

  config.vm.provider "virtualbox" do |vb|
      vb.gui = false
  
      vb.memory = "8192"
      vb.name = "Ubuntu-Vagrant"
      vb.cpus = 6

      #vb.customize ["modifyvm", :id, "--usb", "on"]
      #vb.customize ["modifyvm", :id, "--usbxhci", "on"]
      vb.customize ["modifyvm", :id, "--vram", "128"]
      vb.customize ["modifyvm", :id, "--clipboard", "hosttoguest"]
  end
  
  config.vm.provision "shell", privileged: false, inline: <<-SHELL
    sudo userdel ubuntu
    sudo sed -i "s/archive.ubuntu.com/us.archive.ubuntu.com/g" /etc/apt/sources.list
    
    # Needed for apt to find zlib1g:i386
    sudo dpkg --add-architecture i386
	
    sudo apt update
    # Optionally, install a desktop environment
    # sudo apt -y install ubuntu-desktop virtualbox-guest-dkms virtualbox-guest-utils virtualbox-guest-x11

    # Petalinux dependencies
    sudo apt -y install gcc git make net-tools libncurses5-dev tftpd zlib1g-dev libssl-dev flex bison libselinux1 gnupg wget diffstat chrpath socat xterm autoconf libtool tar unzip texinfo zlib1g-dev gcc-multilib build-essential libsdl1.2-dev libglib2.0-dev zlib1g:i386 screen pax gzip python xvfb

    sudo mkdir /opt/petalinux
    sudo chown vagrant /opt/petalinux
    sudo chgrp vagrant /opt/petalinux
    # Assumes that the petalinux installer is available on the shared folder
    # Copying gives the freedom to change permissions and such to allow the vagrant user to install (since petalinux doesn't support install as root)
    sudo cp /vagrant_data/petalinux-v2018.2-final-installer.run /tmp
    sudo chown vagrant /tmp/petalinux-v2018.2-final-installer.run
    chmod u+x /tmp/petalinux-v2018.2-final-installer.run

    # https://github.com/mitre-cyber-academy/2019-ectf-vagrant/blob/master/provision/scripts/petalinux_install.sh#L12
    yes | /tmp/petalinux-v2018.2-final-installer.run /opt/petalinux > /dev/null

    # Set default /bin/sh to bash instead of dash https://superuser.com/a/1064247/613921
    echo "dash dash/sh boolean false" | sudo debconf-set-selections
    sudo DEBIAN_FRONTEND=noninteractive dpkg-reconfigure dash
    
    echo "source /opt/petalinux/settings.sh" >> ~/.bashrc
  SHELL
end
