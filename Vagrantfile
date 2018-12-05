Vagrant.configure("2") do |config|
  config.vm.define :chef_server do |server_config|
    server_config.vm.box = "bento/ubuntu-16.04"
    server_config.vm.hostname = "chefserver"
    server_config.vm.network :private_network, ip: "10.0.1.15"
    server_config.vm.network :forwarded_port, guest: 22, host: 2222
    server_config.ssh.guest_port = 9194
    server_config.ssh.username = "vagrant"
    server_config.ssh.password = "vagrant"
    server_config.vm.network "forwarded_port", guest: 22, host: 2222
    server_config.vm.provision "shell", inline: <<-EOC
      sudo dpkg -l selinux*
      sudo apt-get update
      sudo apt-get install -y autoconf gcc libc6 make wget unzip apache2 php libapache2-mod-php7.0 libgd2-xpm-dev
      cd /tmp
      wget -O nagioscore.tar.gz https://github.com/NagiosEnterprises/nagioscore/archive/nagios-4.4.1.tar.gz
      tar xzf nagioscore.tar.gz
      cd /tmp/nagioscore-nagios-4.4.1/
      sudo ./configure --with-httpd-conf=/etc/apache2/sites-enabled
      sudo make all
      sudo make install-groups-users
      sudo usermod -a -G nagios www-data
      sudo make install
      sudo make install-daemoninit
      sudo make install-commandmode
      sudo make install-config
      sudo make install-webconf
      sudo a2enmod rewrite
      sudo a2enmod cgi
      sudo ufw allow Apache
      sudo ufw reload
      sudo htpasswd -c /usr/local/nagios/etc/htpasswd.users nagiosadmin -p '*******'
      sudo systemctl restart apache2.service
      sudo systemctl start nagios.service
      sudo apt-get install -y autoconf gcc libc6 libmcrypt-dev make libssl-dev wget bc gawk dc build-essential snmp libnet-snmp-perl gettext
      cd /tmp
      wget --no-check-certificate -O nagios-plugins.tar.gz https://github.com/nagios-plugins/nagios-plugins/archive/release-2.2.1.tar.gz
      tar zxf nagios-plugins.tar.gz
      cd /tmp/nagios-plugins-release-2.2.1/
      sudo ./tools/setup
      sudo ./configure
      sudo make
      sudo make install
      sudo systemctl start nagios.service
      sudo apt-get update
      sudo apt-get install ssmtp
    EOC
end
  config.vm.define :chef_node do |node_config|
  	node_config.vm.box = "bento/ubuntu-16.04"
  	node_config.vm.hostname = "chefnode"
    node_config.vm.network :private_network, ip: "10.0.1.16"
    node_config.ssh.guest_port = 9195
    node_config.ssh.username = "vagrant"
    node_config.ssh.password = "vagrant"
    node_config.vm.provision "shell", inline: <<-EOC
      sudo apt-get update
      sudo apt-get install nagios-plugins nagios-nrpe-server
      sudo vi /etc/nagios/nrpe.cfg
      sudo service nagios-nrpe-server restart
      sudo /usr/local/Nagios/bin/Nagios -v /usr/local/Nagios/etc/ubuntu_host.cfg
      sudo service Nagios-nrpe-server restart
    EOC

end
end
