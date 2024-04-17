Vagrant.configure("2") do |config|

  config.hostmanager.enabled = true
  config.hostmanager.manage_host = true
  config.hostmanager.manage_guest = true
  config.hostmanager.ignore_private_ip = false
  config.hostmanager.include_offline = true

  config.vm.define "proxy" do |proxy|
  proxy.vm.box = "generic/ubuntu1804"
  proxy.vm.hostname = "proxy"
  proxy.vm.network "private_network", ip: "192.168.55.10"

  proxy.vm.network "forwarded_port", guest: 22, host: 2222, id: "ssh"
  proxy.vm.network "forwarded_port", guest: 80, host: 8080, id: "nginx"
  proxy.vm.provider "virtualbox" do |v|
  v.name = "reverse-proxy"
  end
  proxy.vm.provision "shell", inline: <<-SHELL
  apt-get update
  apt-get install -y nginx
  echo 'server {
  listen 80;
  location / {
    proxy_pass http://192.168.33.11/;
  }
}' | sudo tee /etc/nginx/sites-available/default > /dev/null
  sudo service nginx restart
SHELL
 end

config.vm.define "front" do |front|
front.vm.box = "generic/ubuntu1804"
front.vm.network "private_network", ip: "192.168.55.11"
front.vm.provider "virtualbox" do |v|
  v.name = "front"
  end
front.vm.provision "shell", inline: <<-SHELL
  sudo apt-get update
  sudo apt-get install -y git
    git config --global user.name "avoevoda433"
    git config --global user.email "avoevoda433@gmail.com"
    sudo git clone https://github.com/avoevoda433/devops-training-frontend /front
  sudo apt-get install -y nginx
  curl -sL https://deb.nodesource.com/setup_14.x | sudo -E bash -
  sudo apt-get install -y nodejs
  SHELL
end

 config.vm.define "back" do |back|
  back.vm.box = "generic/ubuntu1804"
  back.vm.hostname = "back"
  back.vm.network "private_network", ip: "192.168.55.12"
  back.vm.provider "virtualbox" do |v|
  v.name = "back"
  end
  back.vm.provision "shell", inline: <<-SHELL
    echo "export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64" >> ~/.bashrc
    echo "export JRE_HOME=/usr/lib/jvm/java-8-openjdk-amd64/jre" >> ~/.bashrc
    source ~/.bashrc
    sudo apt-get update
    sudo apt-get install -y git
    git config --global user.name "avoevoda433"
    git config --global user.email "avoevoda433@gmail.com"
    sudo git clone https://github.com/avoevoda433/devops-training-backend/front
    sudo apt-get install -y openjdk-8-jdk
  SHELL
   end

  config.vm.define "db" do |db|
  db.vm.box = "generic/ubuntu1804"
  db.vm.hostname = "db"
  db.vm.network "private_network", ip: "192.168.55.13"
  db.vm.provider "virtualbox" do |v|
    v.name = "db"
    end
  db.vm.provision "shell", inline: <<-SHELL
  sudo apt-get update
  sudo debconf-set-selections <<< 'mysql-server mysql-server/root_password password password'
  sudo debconf-set-selections <<< 'mysql-server mysql-server/root_password_again password password'
  sudo apt-get install -y mysql-server
  SHELL
 end
end
