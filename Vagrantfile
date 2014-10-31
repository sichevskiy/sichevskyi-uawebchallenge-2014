# -*- mode: ruby -*-
# vi: set ft=ruby :
# All Vagrant configuration is done here. For a complete reference,
# please see the online documentation at vagrantup.com.

VAGRANTFILE_API_VERSION = "2"

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|

  config.vm.boot_timeout = 300

  config.vm.define "balancer" do |balancer|
	balancer.vm.box = "balancer"
	# Configure some Virtual Box params
	balancer.vm.provider :virtualbox do |balancer|
		balancer.customize ["modifyvm", :id, "--name", "UWCBalancer"]
		balancer.customize ["modifyvm", :id, "--ostype", "Ubuntu_64"]
		balancer.customize ["modifyvm", :id, "--memory", "1536"]
		balancer.customize ["modifyvm", :id, "--cpuexecutioncap", "80"]
		# Set VirtualBox guest CPU count to the number of host cores
		# balancer.customize ["modifyvm", :id, "--cpus", `grep "^processor" /proc/cpuinfo | wc -l`.chomp ]
	end
	balancer.vm.box_url = "http://cloud-images.ubuntu.com/vagrant/precise/current/precise-server-cloudimg-amd64-vagrant-disk1.box"
        balancer.vm.network "private_network", ip: "192.168.42.20"
        balancer.vm.network "forwarded_port", guest: 80, host: 8090
        balancer.vm.synced_folder "c:/sichevskyi-uawebchallenge-2014", "/var/www/sichevskyi-uawebchallenge-2014", owner: "www-data", group: "www-data", :mount_options => ["dmode=777","fmode=666"]
        balancer.vm.synced_folder "c:/xdebug", "/var/www/xdebug", owner: "www-data", group: "www-data", :mount_options => ["dmode=777","fmode=666"]

        # PLUGINS
        # Set entries in hosts file
        # https://github.com/cogitatio/vagrant-hostsupdater
        if Vagrant.has_plugin?("vagrant-hostsupdater")
          balancer.hostsupdater.remove_on_suspend = true
          balancer.vm.hostname = "192.168.42.20.xip.io"
        end
        if Vagrant.has_plugin?("vagrant-cachier")
          balancer.cache.auto_detect = true
        end

        # PROVISIONING
		$script = <<SCRIPT
                sudo apt-add-repository ppa:rquillo/ansible -y
                sudo apt-get update -y
                sudo apt-get install ansible -y
SCRIPT

        config.vm.provision "shell", inline: $script
        config.vm.provision "shell" do |sh|
                sh.inline = "ansible-playbook /var/www/sichevskyi-uawebchallenge-2014/build/ansible/dev.yml --inventory-file=/var/www/sichevskyi-uawebchallenge-2014/build/ansible/dev --connection=local"
        end

  end

end