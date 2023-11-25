# -*- mode: ruby -*- 
# vi: set ft=ruby : vsa
Vagrant.configure(2) do |config| 
 config.vm.box = "centos/7" 
 config.vm.box_version = "2004.01" 
 config.vm.provider "virtualbox" do |v| 
 v.memory = 256 
 v.cpus = 1 
 end 
 config.vm.define "nfss" do |nfss| 
 nfss.vm.network "private_network", ip: "192.168.50.10",  virtualbox__intnet: "net1" 
 nfss.vm.hostname = "nfss" 
 config.vm.provision "shell", inline: <<-SHELL
 yum install nfs-utils -y
 systemctl enable firewalld --now
 firewall-cmd --add-service="nfs3" --add-service="rpc-bind" --add-service="mountd" --permanent
 firewall-cmd --reload
 systemctl enable nfs --now
 mkdir -p /srv/share/upload 
 chown -R nfsnobody:nfsnobody /srv/share
 chmod 0777 /srv/share/upload
 echo "/srv/share 192.168.50.11/32(rw,sync,root_squash)" > /etc/exports
 exportfs -r 
 SHELL
 end 
 config.vm.define "nfsc" do |nfsc| 
 nfsc.vm.network "private_network", ip: "192.168.50.11",  virtualbox__intnet: "net1" 
 nfsc.vm.hostname = "nfsc" 
 config.vm.provision "shell", inline: <<-SHELL
 yum install nfs-utils -y
 systemctl enable firewalld --now
 echo "192.168.50.10:/srv/share/ /mnt/nfs nfs x-systemd.automount 0 0" >> /etc/fstab
 systemctl daemon-reload 
 systemctl restart remote-fs.target 
 SHELL
 end
end 

