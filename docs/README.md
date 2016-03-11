# Openvswitch y kvm
<div align="center">
<img align=center src=images/diagrama1.png border=0>
</div>
## Creación de bridges y patchs
<ol>
<li>Crear el bridge <b>br0</b>:</li>

<pre>
# ovs-vsctl add-br br0
# ip link set br0 up
</pre>
<li>Añadir el interfaz <b>eth0</b> al bridge <b>br0</b>:
<pre>
# ovs-vsctl add-port br0 eth0
# ip link set eth0 up
</pre>
<li>Ahora se puede asignar una ip a <b>br0</b>:</li>
- Por DHCP (esta es la que yo seguiré):
<pre>
# dhclient br0
</pre>
- Manualmente:
<pre>
# ip address add 192.168.1.10/24 dev br0
</pre>
<li>Crear los patchs para unir los bridges:</li>
<pre>
# ovs-vsctl add-port br0 patch0
# ovs-vsctl set interface patch0 type=patch
# ovs-vsctl add-port br1 patch1
# ovs-vsctl set interface patch1 type=patch
</pre>
<li>Unir los puertos de los patch, con esto ya hay comunicación entre los bridges:</li>
<pre>
# ovs-vsctl set interface patch0 options:peer=patch1 
# ovs-vsctl set interface patch1 options:peer=patch0 
</pre>
</ol>

## Conectar las MVs al bridge br1
Parto desde el punto donde las MVs ya están creadas y tienen una interfaz.
<ol>
<li>Accedemos a <b>virsh</b>:
<pre>
$ virsh -c qemu:///system
</pre>
</li>
<li>Editamos una de las máquinas:
<pre>
virsh # edit debian8
</pre>
</li>
<li>En la zona de interfaz de red configuramos dejando estos dos valores:
<pre>
\\<interface type='bridge'\\>
..
..
\\<virtualport type='openvswitch'/\\>
</pre>
</li>
<li>Guardamos.</li>
<li>Si entramos de nuevo en la configuración se verá que se ha añadido automaticamente una línea como esta:
<pre>
...
\<parameters interfaceid='c7e2363e8.....' /\>
\</virtualport\>
...
</pre>
</li>
</ol>
