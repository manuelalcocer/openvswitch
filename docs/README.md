# Openvswitch y kvm
![alt_tag](images/diagrama1.png?raw_true "Esquema de trabajo")
## Creación de bridges y patchs

### Crear el bridge __br0__:
~~~
# ovs-vsctl add-br br0
# ip link set br0 up
~~~

### Añadir el interfaz __eth0__ al bridge __br0__:
~~~
# ovs-vsctl add-port br0 eth0
# ip link set eth0 up
~~~

### Ahora se puede asignar una ip a __br0__:

- Por DHCP (esta es la que yo seguiré):
~~~
# dhclient br0
~~~

- Manualmente:
~~~
# ip address add 192.168.1.10/24 dev br0
~~~

### Crear los patchs para unir los bridges:
~~~
# ovs-vsctl add-port br0 patch0
# ovs-vsctl set interface patch0 type=patch
# ovs-vsctl add-port br1 patch1
# ovs-vsctl set interface patch1 type=patch
~~~

### Unir los puertos de los patch, con esto ya hay comunicación entre los bridges:
~~~
# ovs-vsctl set interface patch0 options:peer=patch1 
# ovs-vsctl set interface patch1 options:peer=patch0 
~~~
