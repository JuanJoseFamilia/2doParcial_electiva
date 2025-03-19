Vagrant.configure("2") do |config|
  # Configuración de los servidores Apache
  (1..2).each do |i|
    config.vm.define "web#{i}" do |web|
      web.vm.box = "ubuntu/bionic64"
      web.vm.hostname = "web#{i}"
      web.vm.network "private_network", ip: "192.168.56.1#{i}"
      web.vm.provider "virtualbox" do |vb|
        vb.memory = "512"
        vb.cpus = 1
      end
      web.vm.synced_folder "./src/web#{i}", "/var/www/html"
      web.vm.provision "shell", inline: <<-SHELL
        sudo apt update
        sudo apt install -y apache2
        sudo systemctl enable apache2
        sudo systemctl start apache2
      SHELL
    end
  end
  

  # Configuración del servidor Nginx
  config.vm.define "nginx" do |nginx|
    nginx.vm.box = "ubuntu/bionic64"
    nginx.vm.hostname = "nginx"
    nginx.vm.network "private_network", ip: "192.168.56.13"
    nginx.vm.provider "virtualbox" do |vb|
      vb.memory = "512"
      vb.cpus = 1
    end
    nginx.vm.provision "shell", inline: <<-SHELL
      sudo apt update
      sudo apt install -y nginx
      cat <<EOT | sudo tee /etc/nginx/sites-available/default
server {
    listen 80;
    location / {
        proxy_pass http://backend;
    }
}

upstream backend {
    server 192.168.56.11;
    server 192.168.56.12;
}
EOT
      sudo systemctl restart nginx
    SHELL
  end
end
