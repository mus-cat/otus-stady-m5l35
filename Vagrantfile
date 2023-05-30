# -*- mode: ruby -*-
# vim: set ft=ruby :

MACHINES = {
:ns01 => {
        :box_name => "generic/debian11",
        :net => [
                   {ip: '192.168.10.2', adapter: 2, netmask: "255.255.255.0", virtualbox__intnet: "dns"}
                ],
	:config => "dnssrv1.yml",
	:type => "primary",
	:peer => "192.168.10.3"
  },
:ns02 => {
        :box_name => "generic/debian11",
        :net => [
                   {ip: '192.168.10.3', adapter: 2, netmask: "255.255.255.0", virtualbox__intnet: "dns"},
                   {ip: '192.168.10.6', adapter: 3, netmask: "255.255.255.0", virtualbox__intnet: "dns"}
                ],
	:config => "router.yml",
	:type => "secondary",
	:peer => "192.168.10.2"
  },
:client1 => {
        :box_name => "generic/debian11",
        :net => [
                   {ip: '192.168.10.4', adapter: 2, netmask: "255.255.255.0", virtualbox__intnet: "dns"}
                ],
	:config => "router.yml"
  },
:client2 => {
        :box_name => "generic/debian11",
        :net => [
                   {ip: '192.168.10.5', adapter: 2, netmask: "255.255.255.0", virtualbox__intnet: "dns"}
                ],
	:config => "router.yml"
  }
}

Vagrant.configure("2") do |config|

  MACHINES.each do |boxname, boxconfig|

    config.vm.define boxname do |box|

        box.vm.box = boxconfig[:box_name]
        box.vm.host_name = boxname.to_s
	
	box.vm.provider "virtualbox" do |v|
        	# Set VM RAM size and CPU count
        	v.memory = "512"
        	v.cpus = "1"
     	 end

        boxconfig[:net].each do |ipconf|
          box.vm.network "private_network", ipconf
        end

#	if boxconfig[:config].length > 0
	if MACHINES.keys.last == boxname
      		box.vm.provision "ansible" do |ansible|
#	          ansible.playbook = boxconfig[:config]
	          ansible.playbook = "dnssrv.yml"
       		  ansible.verbose = "v"
		  ansible.limit = "all"
#		  if boxname.to_s.include? "ns0"
#		    	  ansible.extra_vars = { ip_nsprim: MACHINES[:ns01][:net][0][:ip], 
#		    	  			 ip_nssec: MACHINES[:ns02][:net][0][:ip], 
#		    	  			 ip_nscl1: MACHINES[:client1][:net][0][:ip], 
#		    	  			 ip_nscl2: MACHINES[:client2][:net][0][:ip] }
#		  end
		end
	end
      end
  end
end

