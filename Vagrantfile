# -*- mode: ruby -*-
# vi: set ft=ruby :


domain = 'example.com'

puppet_nodes = [
  {:hostname => 'win2008',  :ip => '10.0.0.10', :box => 'puppetlabs/centos-7.0-64-puppet', :box_version => '1.0.2', :fwdhost => 8140, :fwdguest => 8140, :ram => 512},
  {:hostname => 'win2008r2', :ip => '10.0.0.11', :box => 'puppetlabs/centos-7.0-64-puppet', :box_version => '1.0.2'},
  {:hostname => 'win2012', :ip => '10.0.0.12', :box => 'puppetlabs/centos-7.0-64-puppet', :box_version => '1.0.2'},
]

# Vagrant r10k plugin configuration. Gather the needed Puppet Modules in Vagrant Project

Vagrant.configure("2") do |config|
 
 if Vagrant.has_plugin?("vagrant-r10k")
   config.r10k.puppet_dir = "puppet"
   config.r10k.puppetfile_path = "puppet/Puppetfile"
   config.r10k.module_path = "puppet/modules"
 end

 puppet_nodes.each do |node|
    config.vm.define node[:hostname] do |node_config|
      node_config.vm.box = node[:box]
      node_config.vm.box_version = node[:box_version]
      node_config.vm.hostname = node[:hostname] + '.' + domain
      node_config.vm.network "private_network", ip: node[:ip]
      
      memory = node[:ram] ? node[:ram] : 2048;
      node_config.vm.provider "virtualbox" do |vm|
        vm.name = node[:hostname]
        vm.memory = memory 
        vm.linked_clone = true
      end

      if node[:fwdhost]
        node_config.vm.network :forwarded_port, guest: node[:fwdguest], host: node[:fwdhost]
      end
      
      node_config.vm.provision :shell do |shell|
        shell.inline = "mkdir -p /etc/puppet/modules;"
      end

      node_config.vm.provision "puppet" do |puppet|
        puppet.manifests_path = "puppet/manifests"
        puppet.manifest_file = "site.pp"
        puppet.module_path = "puppet/modules"
      end
    end
  end
end
