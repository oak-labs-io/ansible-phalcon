# -*- mode: ruby -*-
# vi: set ft=ruby :
# vi: set tabstop=2 :
# vi: set shiftwidth=2 :

Vagrant.configure("2") do |config|

  vms_debian = [
    { :name => "debian-stretch-php7", :box => "debian/stretch64", :vars => { "phalcon_version": '3.3.0' }, :groups => ['sury'] },
    { :name => "ubuntu-xenial-php7", :box => "kmklabs/xenial64", :vars => { "phalcon_version": '3.3.0' }, :groups => ['sury'] }
  ]

  config.vm.network "private_network", type: "dhcp"

  vms_debian.each do |opts|
    config.vm.define opts[:name] do |m|
      m.vm.box = opts[:box]
      m.vm.provider "virtualbox" do |v|
        v.cpus = 1
        v.memory = 256
      end
      m.vm.provision "ansible" do |ansible|
        ansible.playbook = "tests/test.yml"
        ansible.verbose = 'vv'
        ansible.sudo = true
        ansible.extra_vars = opts[:vars]
        ansible.groups = { opts[:groups][0] => [opts[:name]] }
      end
    end
  end
end
