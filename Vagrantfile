# -*- mode: ruby -*-
# vi: set ft=ruby :

$ubu_setup = <<-SCRIPT
#  cd /etc/apt/sources.list.d; mv -f ansible-ubuntu-ansible-focal.list /home/vagrant
#  DEBIAN_FRONTEND=noninteractive apt-get update -y && apt-get upgrade -y
#  DEBIAN_FRONTEND=noninteractive apt-get install -y ansible make jove
  mkdir -p /etc/ansible
  printf 'localhost\n' > /etc/ansible/hosts
  date > /home/vagrant/vg_provisioned_at.text
SCRIPT

# Vagrantfile API/syntax version. Don't touch unless you know what you're doing!
VAGRANTFILE_API_VERSION = '2'

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  # Ensure we use our vagrant private key
  config.ssh.insert_key = false
  config.ssh.private_key_path = '~/.vagrant.d/insecure_private_key'

  config.vm.define 'ansibleflask.local' do |machine|
    machine.vm.hostname = 'ansibleflask.local'
    machine.cache.enable :apt_cacher
    machine.vm.box = "bento/ubuntu-20.04"

    machine.vm.network :private_network, ip: '10.100.99.11'
    machine.vm.synced_folder '.', '/vagrant' # , disabled: true

    machine.vm.provider "virtualbox" do |vb|
      vb.gui = false
      vb.name = "ansibleflask.local"
      vb.memory = 1280
      vb.cpus = 2
    end

    machine.vm.provision "shell", inline: $ubu_setup

    # better:
    # ansible-playbook -vv -i vagrant-inventory tests/test.yml
    # ... "fails" at 'Checkout the latest source code' ...
    machine.vm.provision "ansible" do |ansible|
      ansible.verbose = 'vv'
      ansible.playbook = 'tests/test.yml'
      ansible.become = true
      ansible.become_user = 'root'
      ansible.inventory_path = 'vagrant-inventory'
      ansible.host_key_checking = false
    end
  end
end
