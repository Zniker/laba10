# -*- mode: ruby -*-
# vi: set ft=ruby :
Vagrant.configure("2") do |config|
# Образ виртуальной машины с Vagrant Cloud
config.vm.box = "bento/ubuntu-20.04"
# Настройки виртуальной машины и выбор провайдера
config.vm.provider "virtualbox" do |vb|
vb.name = "VagrantVM"
# Отключаем интерфейс, он не понадобится
vb.gui = false
# 2 Гб оперативной памяти
vb.memory = "2048"
# Одноядерный процессор
vb.cpus = 1
end
config.vm.hostname = "VagrantVM"
config.vm.synced_folder ".", "/home/vagrant/code",
owner: "www-data", group: "www-data"
# Переброс портов
config.vm.network "forwarded_port", guest: 80, host: 8000
config.vm.network "forwarded_port", guest: 3306, host: 33060
# Команда для настройки сети
config.vm.network "private_network", ip: "192.168.10.100"
# Команда, которая выполнится после создания машины
config.vm.provision "shell", path: "provision.sh"
end