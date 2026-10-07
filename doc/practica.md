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