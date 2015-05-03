# -*- mode: ruby -*-
# vi: set ft=ruby :

# Vagrantfile API/syntax version. Don't touch unless you know what you're doing!
VAGRANTFILE_API_VERSION = "2"
Vagrant.require_version ">= 1.5.2"

# Copy files into place
$setupscript = <<END
  # Setup directory for Puppet apply as user
  mkdir -p /home/vagrant/.puppet
  cp /vagrant/etc-puppet/puppet.conf /home/vagrant/.puppet/
  chown -R vagrant:vagrant /home/vagrant/.puppet

  # Enable print of MoTD
  sed -i -e 's/PrintMotd no/PrintMotd yes/' /etc/ssh/sshd_config
  systemctl restart sshd

  # Install example hiera settings in global directory
  mkdir -p /etc/puppetlabs/puppet
  cp -r /vagrant/etc-puppet/* /etc/puppetlabs/puppet/
  mkdir -p /etc/puppetlabs/code
  cp -r /vagrant/puppet-code/* /etc/puppetlabs/code/
  chown -R vagrant:vagrant /etc/puppetlabs

  # Provide the URL to the Puppet Labs yum repo on login
  echo "

You should start by enabling the Puppet Labs Puppet Collection 1 release repo
   sudo yum install http://yum.puppetlabs.com/puppetlabs-release-pc1-el-7.noarch.rpm

Then install Puppet 4 and its companion packages
   sudo yum install -y puppet-agent
   
" > /etc/motd
END

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  config.vm.box = "puppetlabs/centos-7.0-64-nocm"

  # Default client
  config.vm.define "client", primary: true do |client|
    client.vm.hostname = "client.example.com"
    client.vm.network :private_network, ip: "192.168.250.10"
    client.vm.provision "shell", inline: $setupscript
  end

  # A puppetmaster
  config.vm.define "puppetmaster", autostart: false do |puppetmaster|
    puppetmaster.vm.hostname = "puppetmaster.example.com"
    puppetmaster.vm.network :private_network, ip: "192.168.250.5"
    puppetmaster.vm.provision "shell", inline: $setupscript
  end
end
