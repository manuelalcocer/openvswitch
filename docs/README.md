# Openvswitch y kvm
<div align="center">
<img align=center src=images/diagrama1.png border=0>
</div>
## Creación de bridges y patchs
<ol>
<li>Crear el bridge <b>br0</b> y <b>br1</b>:</li>

<pre>
# ovs-vsctl add-br br0
# ovs-vsctl add-br br1
# ip link set br0 up
# ip link set br1 up
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
<li>Editamos una de las máquinas (repetir este paso con la/s demás máquinas):
<pre>
virsh # edit debian8
</pre>
</li>
<li>En la zona de interfaz de red configuramos dejando estos dos valores:
<pre>
&ltinterface type='bridge'/&gt
..
&ltsource bridge='br1'/&gt
&ltvirtualport type='openvswitch'/&gt
</pre>
</li>
<li>Guardamos.</li>
<li>Si entramos de nuevo en la configuración se verá que se ha añadido automáticamente una línea como esta:
<pre>
...
&ltparameters interfaceid='c7e2363e8.....' /&gt
&lt/virtualport&gt
...
</pre>
</li>
<li>Si miramos el estado de <b>ovs</b> con el comando:
<pre>
# ovs-vsctl show
</pre>
Se puede ver como al ejecutar las máquinas se han creado 2 interfaces en el bridge <b>br1</b>
<pre>
 Bridge "br0"
        Port "eth0"
            Interface "eth0"
        Port "patch0"
            Interface "patch0"
                type: patch
                options: {peer="patch1"}
        Port "br0"
            Interface "br0"
                type: internal
    Bridge "br1"
        Port "vnet1"
            Interface "vnet1"
        Port "vnet0"
            Interface "vnet0"
        Port "br1"
            Interface "br1"
                type: internal
        Port "patch1"
            Interface "patch1"
                type: patch
                options: {peer="patch0"}
</pre>
</li>
</ol>
## FIN
## TODO:
<ol>
<li>Conexión de MVs a <b>VLANS</b> con <b>OVS</b> y <b>KVM</b></li>
</ol>
