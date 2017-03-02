# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure(2) do |config|

  config.vm.box = "geerlingguy/centos7"

  config.vm.provider :virtualbox do |vb|
    vb.memory = 4096
    vb.cpus = 2
    vb.customize ["modifyvm", :id, "--natdnshostresolver1", "on"]
  end

  config.vm.define :hass2 do |host|
    host.vm.hostname = "hass2"
    # NOTE: You can override the boxtype here
    host.vm.synced_folder "./src", "/home-assistant", create: true
    host.vm.synced_folder "./config", "/root/.homeassistant", create: true
    host.vm.network "forwarded_port", guest: 8123, host: 8223
    host.vm.network "forwarded_port", guest: 22, host: 22002
    host.vm.provider :virtualbox do |v|
      v.customize ["modifyvm", :id, "--name", host.vm.hostname]
    end
  end

  config.vm.provision :ansible do |ansible|
    ansible.groups = {
        "app" => ["hass2"]
    }
    ansible.playbook = "playbook.yml"
    ansible.galaxy_role_file = "roles/requirements.yml"
    ansible.galaxy_command = "ansible-galaxy install --role-file=%{role_file} --roles-path=roles/ --ignore-errors"
    ansible.extra_vars = { ansible_winrm_scheme: "http" }
  end
end

