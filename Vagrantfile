#### Dev Environment ####
# Setups Ubuntu/Docker/Docker Compose/Git
#### 

Vagrant.configure(2) do |config|
	config.vm.box = "ubuntu/trusty64"
	config.vm.box_check_update = 'false'
	config.ssh.forward_agent = true
	config.vm.network "private_network", ip: "172.16.10.25"
	config.vm.network "forwarded_port", guest: 80, host: 8885
	config.vm.provision "file", source: "./your_repository_id_rsa", destination: "/home/vagrant/"
	config.vm.provision "file", source: "~/.ssh/config", destination: "/home/vagrant/.ssh/config"
	config.vm.synced_folder ".", "/home/vagrant"
	config.vm.provision "shell", inline:<<-SHELL
		# Prevent out of memory by setting up swap space
		sudo /bin/dd if=/dev/zero of=/var/swap.1 bs=1M count=1024
		sudo /sbin/mkswap /var/swap.1
		sudo  /sbin/swapon /var/swap.1
		# Update RSA key Permission 
		chmod 0600 /home/vagrant/.ssh/your_repository_id_rsa
        	# Setting up docker
		sudo apt-get remove --purge docker docker-engine -y
        	sudo apt-get update
        	sudo apt-get -y install \
        		linux-image-extra-$(uname -r) \
        		linux-image-extra-virtual

        	sudo apt-get install apt-transport-https ca-certificates curl software-properties-common -y
        	sudo wget -qO - https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
        	sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
        	sudo apt-get update
        	sudo apt-get install git docker-ce -y
		# Setting up Docker Compose
        	sudo apt-get -y install python-pip
        	sudo pip install docker-compose
		# Laravel Project setup
        	mkdir -p /home/vagrant/proyectos
		chown -R vagrant.vagrant /home/vagrant/proyectos
		touch /home/vagrant/.ssh/known_hosts
		mv /home/vagrant/your_repository_id_rsa /home/vagrant/.ssh/
		chown -R vagrant.vagrant /home/vagrant/.ssh
		if [[ ! -n "$(grep "^bitbucket.org " /home/vagrant/.ssh/known_hosts)" ]]
		then
			su - vagrant -c "ssh-keyscan your_git_provider.com >> /home/vagrant/.ssh/known_hosts 2>/dev/null"
		fi
		# Set up sudo
		#sudo echo vagrant ALL=NOPASSWD:ALL > /etc/sudoers.d/vagrant
		#sudo chmod 0440 /etc/sudoers.d/vagrant
		echo %vagrant ALL=NOPASSWD:ALL > /etc/sudoers.d/vagrant
		chmod 0440 /etc/sudoers.d/vagrant
		usermod -a -G sudo vagrant
		#
		echo Host GITHOSTSAMPLE > /home/vagrant/.ssh/config
        	echo HostName bitbucket.org >> /home/vagrant/.ssh/config
        	echo User git >> /home/vagrant/.ssh/config
        	echo IdentityFile /home/vagrant/.ssh/your_repository_id_rsa >> /home/vagrant/.ssh/config
		# Setup sudo to allow no-password sudo for "sudo"
		sudo usermod -a -G sudo vagrant
		su -c "eval $(ssh-agent -s)" vagrant
		su -c "ssh-add /home/vagrant/.ssh/your_repository_id_rsa" vagrant
		su -c "git clone git@your_git_provider:your_user/your_project.git /home/vagrant/proyectos" vagrant
		chown -R vagrant.vagrant /home/vagrant/proyectos
		# Change directory automatically on ssh login
		if ! grep -qF "cd /home/vagrant/proyectos" /home/vagrant/.bash_profile; then
			echo "cd /home/vagrant/proyectos" >> /home/vagrant/.bash_profile
		fi
		chown vagrant.vagrant /home/vagrant/.bash_profile
		
	SHELL
end
