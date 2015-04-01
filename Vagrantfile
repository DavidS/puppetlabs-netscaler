# Download PE tarball, extract to the same location as the Vagrantfile and mv
# to a directory called 'pe37'. Place a PE3 answers file called 'answers.txt'
# in the same location as the Vagrantfile (you can steal the
# all-in-one.answers.txt from the installer's answers/ directory). Run `vagrant
# up` and wait for PE to install. Visit https://localhost:4443 to view the PE
# console.
Vagrant.configure("2") do |config|
  config.vm.define 'master' do |node|
    node.vm.box = "puppetlabs/ubuntu-14.04-64-nocm"
    node.vm.hostname = 'master.localdomain'
    node.vm.network :forwarded_port, guest: 443, host: 4443
    # For some reason setting private IPs doesn't work for me and I have to ssh
    # in and update /etc/hosts by hand
    node.vm.network :private_network, :ip => '192.168.23.3'
    node.vm.provision :hosts
    #node.vm.provision :shell, :inline => "systemctl stop firewalld"
    #node.vm.provision :shell, :inline => "systemctl disable firewalld"
    node.vm.provision :shell, :inline => "/vagrant/pe37/puppet-enterprise-installer -a /vagrant/master.txt"
    node.vm.provision :shell, :inline => "ln -s /vagrant/puppetlabs-netscaler /etc/puppetlabs/puppet/environments/production/modules/netscaler"
    node.vm.provider :virtualbox do |vb|
      vb.customize ['modifyvm', :id, '--memory', '4096']
    end

  end
  config.vm.define 'agent' do |node|
    node.vm.box = "puppetlabs/ubuntu-14.04-64-nocm"
    node.vm.hostname = 'agent'
    node.vm.network :private_network, :ip => '192.168.23.4'
    node.vm.provision :hosts
    node.vm.provision :shell, :inline => "curl -k https://master.localdomain:8140/packages/current/install.bash | sudo bash"
    node.vm.provision :shell, :inline => "ln -s /vagrant/device.conf /etc/puppetlabs/puppet/device.conf"
    node.vm.provider :virtualbox do |vb|
      vb.customize ['modifyvm', :id, '--memory', '1024']
    end

  end
  # I can't actually package this, because vagrant *HAS* to be able to log in
  # as root & vagrant and netscalers don't like that
  #config.vm.define 'netscaler' do |node|
  #  node.vm.box = "NSVPX-ESX-10.1-119.7_nc"
  #  node.vm.hostname = 'netscaler.localdomain'
  #  node.vm.network :private_network, :ip => '192.168.23.4'
  #  node.vm.provision :hosts
  #end
end
