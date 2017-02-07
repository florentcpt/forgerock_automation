# -*- mode: ruby -*-
# vi: set ft=ruby :

# Path to Ansible inventory file to dynamically generate nodes
ansible_inventory = 'ansible/inventory'
# Regular expression to select only valid lines to create nodes
# valid line is : node_name ansible_host="node.domain.local" ip_addr="192.168.99.10"
ansible_regex = '^(\w+) ansible_host="([\w\.]+)" ip_addr="([\d\.]+)"$'

Vagrant.configure("2") do |config|

  config.vm.box = "centos/7"

  File.open(ansible_inventory, 'r') do |f|
    f.each_line do |line|
      /#{ansible_regex}/.match(line) do |m|
        config.vm.define m[1] do |node|
          node.vm.hostname = m[2]
          node.vm.network :private_network, ip: m[3]
        end
      end
    end
  end

  config.vm.provider "virtualbox" do |vb|
    vb.gui = false
    vb.memory = "512"
  end

  # Fix an issue with Vagrant and CentOS => https://github.com/mitchellh/vagrant/issues/5590
  config.vm.provision "shell", run: 'always', inline: "nmcli connection reload; systemctl restart network.service"

  config.vm.provision :ansible, run: 'always' do |ansible|
    ansible.inventory_path = ansible_inventory
    ansible.playbook = 'ansible/provision.yml'
    ansible.playbook_command = './venv/bin/ansible-playbook'
    ansible.sudo = true
  end
end
