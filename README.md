Instalacion de dependencias:
````

sudo apt update
sudo apt install -y apache2 php libapache2-mod-php \
  build-essential libgd-dev unzip wget \
  openssl libssl-dev daemon apache2-utils
````

Descargar e instalar nagios:
````
wget https://assets.nagios.com/downloads/nagioscore/releases/nagios-4.5.1.tar.gz
tar xzf nagios-4.5.1.tar.gz
cd nagios-4.5.1
sudo ./configure --with-httpd-conf=/etc/apache2/sites-enabled
sudo make all
````
Crear usuario y grupo nagios:
````
sudo useradd nagios
sudo groupadd nagcmd
sudo usermod -a -G nagcmd nagios
sudo usermod -a -G nagcmd www-data
````

Tambien tenemos que instalar esto: (Ejecutar estos comandos dentro del directorio nagios-4.5.1)
````
sudo make install
sudo make install-commandmode
sudo make install-init
sudo make install-config
sudo make install-webconf
````


Crear usuario para interfaz web:
````

sudo htpasswd -c /usr/local/nagios/etc/htpasswd.users nagiosadmin
````

Luego pedira configurar la clave. Para este caso usaremos root
````

Habilitar y arrancar servicios:
````
sudo a2enmod cgi
sudo systemctl restart apache2
sudo systemctl enable apache2
sudo systemctl start nagios
sudo systemctl enable nagios


En esta primera instancia ya podemos acceder al dashboard de nagios desde nuestro navegador. En este caso la maquina donde se realizo la configuracion tiene la ip 192.168.50.10:
````

http://192.168.50.10/nagios
````

Posteriormente procedemos a Configurar hosts a monitorear.
Basado en el vagrantfile tenemos un total de 3 maquinas que vamos a monitorear.
cliente, servidor y firewall
````

Crear definición de hosts en Nagios
````

Primero accedemos al siguiente directorio:
````

cd /usr/local/nagios/etc/objects
````

luego creamos un archivo en el cual se definen los hosts a monitorear:
````

sudo vim hosts.cfg
````

Dentro de este archivo pondremos lo siguiente:
````

define host{
    use                     linux-server
    host_name               cliente
    alias                   Cliente Linux
    address                 192.168.50.2
    max_check_attempts      5
    check_period            24x7
    notification_interval   30
    notification_period     24x7
}

define host{
    use                     linux-server
    host_name               servidor
    alias                   Servidor Linux
    address                 192.168.50.3
    max_check_attempts      5
    check_period            24x7
    notification_interval   30
    notification_period     24x7
}

define host{
    use                     linux-server
    host_name               firewall
    alias                   Firewall Linux
    address                 192.168.50.4
    max_check_attempts      5
    check_period            24x7
    notification_interval   30
    notification_period     24x7
}
````

Luego incluimos el archivo hosts.cfg en la configuración principal de Nagios:
````

sudo vim /usr/local/nagios/etc/nagios.cfg
````

Busca una sección que dice:
````

# Definitions for monitoring localhost
cfg_file=/usr/local/nagios/etc/objects/localhost.cfg
````

Justo debajo de esa línea, agrega:
````

cfg_file=/usr/local/nagios/etc/objects/hosts.cfg
````

Luego Verificamos que no haya errores y recargamos Nagios:
````

sudo /usr/local/nagios/bin/nagios -v /usr/local/nagios/etc/nagios.cfg
````

Si no hay errores recargamos:
````
sudo systemctl restart nagios
````

Podemos instalar los Nagios plugins para que se pueda monitorear los host:
````

sudo apt install -y autoconf gcc libperl-dev make libssl-dev libgd-dev daemon wget unzip build-essential
````

Descargar los Nagios Plugins: 
````

cd ~
wget https://nagios-plugins.org/download/nagios-plugins-2.3.3.tar.gz
````

Descomprimir:
````

tar zxvf nagios-plugins-2.3.3.tar.gz
cd nagios-plugins-2.3.3
````

Configuramos:
````

./configure --with-nagios-user=nagios --with-nagios-group=nagios
````

compialos e instalamos:
````
make
sudo make install
````

Se crearán en /usr/local/nagios/libexec/ todos los comandos como check_ping, check_ssh, check_http, etc.
podemos acceder nuevamente a http://192.168.50.10/nagios y en la parte hosts veremos las maquinas que agregamos para monitorear