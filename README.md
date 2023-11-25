### NFS
#### К исходному Vagrantfile добавляем:
 config.vm.provision "shell", inline: <<-SHELL
#### Установка утилит для работы nfs
 yum install nfs-utils -y
#### Включаем фаерволл
 systemctl enable firewalld --now
#### Добавляем сервисы
 firewall-cmd --add-service="nfs3" --add-service="rpc-bind" --add-service="mountd" --permanent
#### Перезапускаем фаерволл
 firewall-cmd --reload
#### Включаем службу nfs
 systemctl enable nfs --now
#### Создаем папку для расшаривания 
 mkdir -p /srv/share/upload
#### Делаем владельцем nfsnobody
 chown -R nfsnobody:nfsnobody /srv/share
#### Делаем полные права для всех 
 chmod 0777 /srv/share/upload
#### Добавляем в конфиг информацию для кого и на каких условиях будет работать шара
 echo "/srv/share 192.168.50.11/32(rw,sync,root_squash)" > /etc/exports
#### Выполняем экспорт 
 exportfs -r
 SHELL
 end
#### Добавляем на клиенте
 yum install nfs-utils -y
 systemctl enable firewalld --now
#### Добавляем инфу по монтированию в fstab 
echo "192.168.50.10:/srv/share/ /mnt/nfs nfs x-systemd.automount 0 0" >> /etc/fstab
#### Перезапускаем systemctl
 systemctl daemon-reload
#### Перезапускаем remote-fs.target т.к. systemd при использовании x-systemd.automount автоматически создает зависимость с данным модулем
 systemctl restart remote-fs.target
 SHELL
 end
end











