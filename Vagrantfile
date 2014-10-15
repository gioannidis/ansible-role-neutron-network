# -*- mode: ruby -*-
# vi: set ft=ruby :

require 'fileutils'

def local_cache(basebox_name)
  cache_dir = Vagrant::Environment.new.home_path.join('cache', 'apt', basebox_name)
  partial_dir = cache_dir.join('partial')
  FileUtils.mkpath partial_dir unless partial_dir.exist?
  cache_dir
end

VAGRANTFILE_API_VERSION = "2"
Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|

  config.vm.box = "ubuntu/trusty64"
  config.vm.synced_folder local_cache(config.vm.box), "/var/cache/apt/archives/"

  config.vm.define "network" do |machine|
    machine.vm.network :private_network, ip: "10.1.0.2",
                       :netmask => "255.255.0.0"
    machine.vm.provider :virtualbox do |v|
      v.customize ["modifyvm", :id, "--memory", 2048]
      v.customize ["modifyvm", :id, "--nicpromisc2", "allow-vms"]
    end
  end

  config.vm.provision "ansible" do |ansible|
    ansible.playbook = "getreqs.yml"
  end

  config.vm.provision "ansible" do |ansible|
    ansible.playbook = "prep.yml"
    #ansible.extra_vars = {
    #  keystone_identity_host: "{{ ansible_docker0['ipv4']['address'] }}"
    #  nova_identity_host: "{{ ansible_docker0['ipv4']['address'] }}"
    #  nova_compute_host: "{{ ansible_docker0['ipv4']['address'] }}"
    #  neutron_identity_host: "{{ ansible_docker0['ipv4']['address'] }}"
    #  neutron_compute_host: "{{ ansible_docker0['ipv4']['address'] }}"
    #  neutron_network_host: "{{ ansible_docker0['ipv4']['address'] }}"
    #}
  end

  config.vm.provision "ansible" do |ansible|
    ansible.playbook = "deploy.yml"
    ansible.extra_vars = {
      #neutron_network_dockerized_deployment: true,
      #neutron_rabbitmq_host: "{{ ansible_docker0['ipv4']['address'] }}",
      #neutron_identity_host: "{{ ansible_docker0['ipv4']['address'] }}"
      #neutron_compute_host: "{{ ansible_docker0['ipv4']['address'] }}"
      #neutron_network_host: "{{ ansible_docker0['ipv4']['address'] }}"
      neutron_network_external_name: "public",
      neutron_network_external_device: "eth1",
      neutron_network_external_ip: "10.1.0.2",
      neutron_network_external_netmask: "16",
      neutron_network_external_allocation_pool_start: "10.1.0.100",
      neutron_network_external_allocation_pool_end: "10.1.0.200",
      neutron_network_external_dns_servers: "8.8.8.8"
    }
  end

  config.vm.provision "ansible" do |ansible|
    ansible.playbook = "test.yml"
    ansible.extra_vars = {
      neutron_network_external_router_ip: "10.1.0.100",
    }
  end

end
