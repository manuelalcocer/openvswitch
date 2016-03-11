# Openvswitch y kvm
![alt_tag](images/diagrama1.png?raw_true "Esquema de trabajo")
## Creación de bridges y patchs
<ol>
<li>Crear el bridge <i>br0</i>:</li>

~~~
# ovs-vsctl add-br br0
# ip link set br0 up
~~~
<li>Añadir el interfaz <i>eth0</i> al bridge <i>br0</i>:
~~~
# ovs-vsctl add-port br0 eth0
# ip link set eth0 up
~~~
<li>Ahora se puede asignar una ip a <i>br0</i>:</li>
- Por DHCP (esta es la que yo seguiré):
~~~
# dhclient br0
~~~
- Manualmente:
~~~
# ip address add 192.168.1.10/24 dev br0
~~~
<li>Crear los patchs para unir los bridges:</li>
~~~
# ovs-vsctl add-port br0 patch0
# ovs-vsctl set interface patch0 type=patch
# ovs-vsctl add-port br1 patch1
# ovs-vsctl set interface patch1 type=patch
~~~
<li>Unir los puertos de los patch, con esto ya hay comunicación entre los bridges:</li>
~~~
# ovs-vsctl set interface patch0 options:peer=patch1 
# ovs-vsctl set interface patch1 options:peer=patch0 
~~~
</ol>
