# -*- mode: ruby -*-
# vi: set ft=ruby :
#
VAGRANTFILE_API_VERSION = "2"

ENV['VAGRANT_DEFAULT_PROVIDER'] ||= 'docker'

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|

  DOCKER_IMAGE_REPO = "mtbvang"
  DOCKER_IMAGE_NAME = "ubuntu-vagrant"
  DOCKER_IMAGE_TAG = "14.04"

  DOCKER_SYNC_FOLDER_HOST = "/home/dev/code"
  DOCKER_SYNC_FOLDERL_GUEST = "/vagrant_data"
  DOCKER_CMD = ["/usr/sbin/sshd", "-D", "-e"]

  DOCKER_NAMESPACE_PREFIX = "reactisostarterkit"

  if Vagrant.has_plugin?("vagrant-cachier")
    config.cache.scope = :box
  end

  # Create symlinks to access graph files
  config.vm.provision "shell", inline: "mkdir -p /var/lib/puppet/state/graphs && ln -sf /vagrant/build /var/lib/puppet/state/graphs"

  # Boostrap docker containers with shell provisioner.
  config.vm.provision "bootstrap", type: "shell" do |s|
    s.path = "vagrant/bootstrap.sh"
    s.args = "3.7.5-1"
  end

  config.vm.provision "file", source: "~/.gitconfig", destination: ".gitconfig"
  config.vm.provision "file", source: "~/.ssh/config", destination: ".ssh/config"
  config.vm.provision "file", source: "~/.ssh/founders", destination: ".ssh/founders"
  config.vm.provision "file", source: "~/.ssh/founders.pub", destination: ".ssh/founders.pub"
  config.vm.provision "file", source: "vagrant/.bash_aliases", destination: ".bash_aliases"
  config.vm.provision "file", source: "vagrant/.bashrc", destination: ".bashrc"

  config.vm.define "#{DOCKER_NAMESPACE_PREFIX}" do |d|
    d.vm.hostname = "#{DOCKER_NAMESPACE_PREFIX}.local"
    d.vm.network "forwarded_port", guest: 8080, host: 8084

    d.vm.provision "provision", type: "shell" do |s|
      s.path = "vagrant/provision.sh"
      s.args = "0.12.2"
    end

    d.vm.provision "dotfiles", type: "shell" do |s|
      s.path = "vagrant/provision-dotfiles.sh"
    end

    d.vm.provider "docker" do |d|
      d.cmd     = DOCKER_CMD
      #d.cmd = ["/bin/bash", "/vagrant/vagrant/startConsul.sh"]
      d.image   = "#{DOCKER_IMAGE_REPO}/#{DOCKER_IMAGE_NAME}:#{DOCKER_IMAGE_TAG}"
      d.has_ssh = true
      d.privileged = true
      d.name = "#{DOCKER_NAMESPACE_PREFIX}-dev"
    end
  end

end
