## Práctica DHCP

## Entorno utilizado

- Host: Windows 11
- Virtualización: VirtualBox 7.2.4
- Gestión de máquinas: Vagrant 2.4.9
- Editor: Visual Studio Code
- Servidor Linux: Debian

## Objetivo

Configurar un servidor DHCP conectado a una red interna
192.168.57.0/24, proporcionar direcciones dinámicas a clientes,
crear una reserva basada en MAC y configurar posteriormente
enrutamiento y NAT.

## Checkpoint 1 - Configuración inicial del servidor DHCP

Se ha creado una máquina virtual Debian llamada "server"

El servidor dispone de una interfaz conectada a la red pública de casa
y una segunda interfaz conectada a la red interna 192.168.57.0/24

Por lo que la interfaz interna del servidor tiene configurada la dirección:
192.168.57.10/24

Tambien se utilizó el comando "ip a" confirmando que una de las interfaces del
servidor tiene asignada la dirección 192.168.57.10/24

Se instaló el servidor DHCP mediante:
sudo apt update
sudo apt install isc-dhcp-server -y

Y se configuró la interfaz en el archivo:
/etc/default/isc-dhcp-server
Para que el servicio DHCP escuche únicamente en la red interna,
también se realizó una copia de seguridad:
sudo cp /etc/dhcp/dhcpd.conf /etc/dhcp/dhcpd.conf.bak

Por último se comprobó el estado mediante:
sudo systemctl status isc-dhcp-server.service
pero todavía da error porque todavía no se ha configurado la subred DHCP

Y ahora utilizamos:
git add .
git commit -m "Feat: Install DHCP service and configure network interfaces"


## Checkpoint 2 - Configuración y comprobación del servidor DHCP

Se configuró el servicio DHCP para la red interna 192.168.57.0/24.

Se establecieron los siguientes parámetros globales:

- Tiempo de concesión por defecto: 1 día (86400 segundos)
- Tiempo máximo de concesión: 8 días (691200 segundos)
- Dominio: jorge.test
- Servidores DNS: 10.0.0.2 y 4.4.4.4

Para la subred interna se configuró el rango dinámico:
192.168.57.20 - 192.168.57.50

La configuración se realizó en el archivo:
/etc/dhcp/dhcpd.conf

Antes de reiniciar el servicio se comprobó la sintaxis mediante:
sudo dhcpd -t

Al no detectarse errores, se reinició el servidor DHCP:
sudo systemctl restart isc-dhcp-server.service

El resultado mostró que el servicio se encontraba correctamente iniciado:
Active: active (running)

Finalmente, se comprobaron los puertos UDP en escucha mediante:
sudo ss -lun


## Checkpoint 3 - Comprobación de la asignación dinámica

Se creó la máquina cliente "c1" y se conectó a la misma red interna
intnet que el servidor DHCP.

Se comprobó la configuración de sus interfaces mediante:
ip a

se comprobó que el servidor estaba proporcionando
correctamente direcciones dentro del rango:
192.168.57.20 - 192.168.57.50

También se realizó una liberación y renovación manual de la concesión
DHCP mediante:
sudo dhclient eth1 -r
sudo dhclient eth1

Finalmente, desde el servidor se comprobó la base de datos de
concesiones mediante:
sudo tail -n 20 /var/lib/dhcp/dhcpd.leases

En ella apareció una concesión activa correspondiente al cliente c1,
identificada por su dirección MAC y por:
client-hostname "c1" y binding state active