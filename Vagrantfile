# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|
	config.vm.define :firewall do |firewall|
		firewall.vm.box = "bento/centos-7.9"
		firewall.vm.network :public_network, ip: "192.168.1.100"
		firewall.vm.hostname = "firewall"
		firewall.vm.provision "shell", inline:<<-SHELL
		  setenforce 0
		  service firewalld start
		  chkconfig firewalld on
		  firewall-cmd --zone=public --add-masquerade --permanent
		  firewall-cmd --zone=public --add-port=80/tcp --permanent
		  firewall-cmd --zone=public --add-forward-port=port=80:proto=tcp:toport=8080:toaddr=192.168.1.101 --permanent
		  firewall-cmd --reload
		SHELL
	end
	
	config.vm.define :servidor do |servidor|
		servidor.vm.box = "bento/centos-7.9"
		servidor.vm.network :public_network, ip: "192.168.1.101"
		servidor.vm.hostname = "servidor"
		servidor.vm.provision "shell", inline:<<-SHELL
		  setenforce 0
		  yum -y update
		  yum -y install wget
		  wget --no-cookies --no-check-certificate --header "Cookie:oraclelicense=accept-securebackup-cookie" "http://download.oracle.com/otn-pub/java/jdk/8u131-b11/d54c1d3a095b4ff2b6607d096fa80163/jdk-8u131-linux-x64.rpm"
		  yum -y localinstall jdk-8u131-linux-x64.rpm
		  wget -P /home/vagrant/ https://github.com/dularion/streama/releases/download/v1.1/streama-1.1.war
		  mkdir /opt/streama
		  mv /home/vagrant/streama-1.1.war /opt/streama/streama.war
		  mkdir /opt/streama/media
		  chmod 664 /opt/streama/media
		  printf "[Unit]\nDescription=Streama Server\nAfter=syslog.target\nAfter=network.target\n\n[Service]\nUser=root\nType=simple\nExecStart=/bin/java -jar /opt/streama/streama.war\nRestart=always\nStandardOutput=syslog\nStandardError=syslog\nSyslogIdentifier=Streama\n\n[Install]\nWantedBy=multi-user.target" >> /etc/systemd/system/streama.service
		  systemctl daemon-reload
		  systemctl start streama
		  systemctl enable streama
		SHELL
	end
end