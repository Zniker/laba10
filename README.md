# laba10

1 Скачивание vagrant
```
set PATH=%PATH%;C:\Vagrant\bin
vagrant -v
```
2 Создание виртуальной машины в Vagrant
```
vagrant version
mkdir VagrantVM
cd VagrantVM
vagrant login
vagrant init -m bento/ubuntu-20.04
```
3 Команда  vagrant init создает файл Vagrantfile в текущей директории. В нем на
языке Ruby описывается конфигурация виртуальной машины.
```
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
```
4* некоторые пометки
```
config.vm.box  — базовый образ. В нашем случае Ubuntu 20.04
config.vm.provider  — система виртуализации. Доступные провайдеры:
VirtualBox, VMware, Hyper-V, Docker и др.
config.vm.hostname  — имя хоста.
config.vm.synced_folder  — синхронизация папок. Первым параметром
передается путь хостовой (основной) машины, вторым параметром передается
путь гостевой (виртуальной машины)
```
5 Настройка системы вынесена в отдельный файл provision.sh
```
apt-get update
apt-get -y upgrade
apt-add-repository ppa:ondrej/php -y
apt-get update
apt-get install -y software-properties-common curl zip
apt-get install -y php7.2-cli php7.2-fpm \
php7.2-pgsql php7.2-sqlite3 php7.2-gd \
php7.2-curl php7.2-memcached \
php7.2-imap php7.2-mysql php7.2-mbstring \
php7.2-xml php7.2-json php7.2-zip php7.2-bcmath php7.2-soap \
php7.2-intl php7.2-readline php7.2-ldap
apt-get install -y nginx
rm /etc/nginx/sites-enabled/default
rm /etc/nginx/sites-available/default
cat > /etc/nginx/sites-available/vagrantvm <<EOF
server {
listen 80;
server_name .vagrantvm.loc;
root "/home/vagrant/code";
index index.html index.htm index.php;
charset utf-8;
location / {
try_files \$uri \$uri/ /index.php?\$query_string;
}
location = /favicon.ico { access_log off; log_not_found off; }
location = /robots.txt { access_log off; log_not_found off; }
access_log off;
error_log /var/log/nginx/vagrantvm-error.log error;
location ~ \.php$ {
fastcgi_split_path_info ^(.+\.php)(/.+)$;
fastcgi_pass unix:/var/run/php/php7.2-fpm.sock;
fastcgi_index index.php;
include fastcgi_params;
fastcgi_param SCRIPT_FILENAME \$document_root\$fastcgi_script_name;
}
}
EOF
ln -s /etc/nginx/sites-available/vagrantvm /etc/nginx/sites-enabled/vagrantvm
service nginx restart
```
6 Запуск
```
vagrant up
http://localhost:8000
```
![image](https://user-images.githubusercontent.com/46539072/172272927-7f91bf6d-0ced-4e35-9593-6fad22c091e4.png)

6* пример запуска ubuntu-trusty-64

![image](https://user-images.githubusercontent.com/46539072/172271050-b11f907e-58b3-4081-aa9e-3fc04322a0d6.png)

7 Основные команды
```
vagrant up 
vagrant reload 
vagrant halt 
vagrant destroy  
vagrant suspend  
vagrant status 
vagrant ssh  
vagrant 
```
