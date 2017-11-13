# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure(2) do |config|

  config.vm.box = "ubuntu/trusty64"

  config.vm.network "forwarded_port", guest: 80,   host: 8000 # apache
  config.vm.network "forwarded_port", guest: 8080, host: 8080 # tomcat
  config.vm.network "forwarded_port", guest: 3306, host: 3306 # mysql
  config.vm.network "forwarded_port", guest: 8888, host: 8888 # djatoka

  # config.vm.network "private_network", ip: "192.168.111.111"
  config.vm.post_up_message = "Done. enjoy..."

  config.vm.provider "virtualbox" do |vb|
    vb.memory = "4000"
    vb.cpus = 2
  end

  # Use rbconfig to determine if we're on a windows host or not.

  require 'rbconfig'
  is_windows = (RbConfig::CONFIG['host_os'] =~ /mswin|mingw|cygwin/)
  if is_windows
    config.vm.provision "ansible_local" do |ansible|
      ansible.playbook = "build.yml"
      ansible.verbose = 'vv'
      ansible.install = true
      ansible.extra_vars = {
        mysql_local_installation: "true",
        attach_mounts: false,
        drupal_reverse_proxy: false,
        fqdn_suffix: 'library.local'
      }
    end
  else
    config.vm.provision "ansible" do |ansible|
      ansible.playbook = "build.yml"
      ansible.verbose = 'vv'
      ansible.extra_vars = {
        mysql_local_installation: "true",
        attach_mounts: false,
        drupal_reverse_proxy: false,
        fqdn_suffix: 'library.local'
      }
    end
  end
end
