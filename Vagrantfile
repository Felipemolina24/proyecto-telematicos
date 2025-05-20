# -*- mode: ruby -*-
# vi: set ft=ruby :
Vagrant.configure("2") do |config|
  
  # Configuración del servidor
  config.vm.define :servidor do |servidor|
    servidor.vm.box = "bento/ubuntu-22.04"
    servidor.vm.network :private_network, ip: "192.168.50.3"
    servidor.vm.hostname = "servidor"
    
    servidor.vm.boot_timeout = 600

    # Directorio sincronizado para el servidor
    servidor.vm.synced_folder "./compartido_servidor", "/home/vagrant/compartido"
  end

  # Configuración del cliente
  config.vm.define :cliente do |cliente|
    cliente.vm.box = "bento/ubuntu-22.04"
    cliente.vm.network :private_network, ip: "192.168.50.2"
    cliente.vm.hostname = "cliente"

    cliente.vm.boot_timeout = 600
    
    # Directorio sincronizado para el cliente
    cliente.vm.synced_folder "./compartido_cliente", "/home/vagrant/compartido"
  end

  # Configuración del servidor Nagios
  config.vm.define :nagios do |nagios|
    nagios.vm.box = "bento/ubuntu-22.04"
    nagios.vm.network :private_network, ip: "192.168.50.10"
    nagios.vm.hostname = "nagios"
    
    nagios.vm.boot_timeout = 600

  end

 # Configuración del servidor Firewall
  config.vm.define :firewall do |firewall|
    firewall.vm.box = "bento/ubuntu-22.04"
    firewall.vm.network :private_network, ip: "192.168.50.4"
    firewall.vm.hostname = "firewall"

    firewall.vm.boot_timeout = 600

  end

end
