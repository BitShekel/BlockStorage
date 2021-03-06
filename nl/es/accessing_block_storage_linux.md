---

copyright:
  years: 2014, 2018
lastupdated: "2018-03-09"

---
{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# Conexión a los LUN iSCSI de MPIO en Linux

Estas instrucciones son para RHEL6/Centos6. Si está utilizando otros sistemas operativos Linux, consulte la documentación específica para ver la configuración y asegúrese de que la multivía de soporte a ALUA para la prioridad de vía de acceso.

Antes de empezar, asegúrese de que el host que accede al volumen de {{site.data.keyword.blockstoragefull}} se haya autorizado a través del [{{site.data.keyword.slportal}}](https://control.softlayer.com/){:new_window}:

1. Desde la página de listado de {{site.data.keyword.blockstorageshort}}, pulse las **Acciones** asociadas al volumen recién suministrado.
2. Pulse **Autorizar host**.
3. Seleccione el host(s) deseado de la lista y pulse **Enviar**; esta acción autoriza al host(s) el acceso al volumen.

## Montaje de volúmenes de {{site.data.keyword.blockstorageshort}}

A continuación se describen los pasos necesarios para conectar una instancia de cálculo de {{site.data.keyword.BluSoftlayer_full}} basada en Linux a un número de unidad lógica (LUN) de interfaz para pequeños sistemas (iSCSI) de E/S de multivía de acceso (MPIO).

El ejemplo se basa en **Red Hat Enterprise Linux 6**. Los pasos deben ajustarse para otras distribuciones Linux de acuerdo con la documentación del proveedor del sistema operativo. Hemos añadido notas para otros sistemas operativos, pero la documentación **no** cubre todas las distribuciones Linux. Por ejemplo, en el caso de Ubuntu, pulse [aquí](https://help.ubuntu.com/lts/serverguide/iscsi-initiator.html){:new_window:} para obtener instrucciones sobre la configuración del iniciador iSCSI y pulse [aquí](https://help.ubuntu.com/lts/serverguide/multipath-setting-up-dm-multipath.html){:new_window} para obtener más información sobre la configuración de DM-Multipath.

**Nota:** El nombre calificado iSCSI (IQN) del host, nombre de usuario, contraseña y dirección de destino de referencia en las instrucciones pueden obtenerse en la pantalla **{{site.data.keyword.blockstorageshort}} Detalles** del [{{site.data.keyword.slportal}}](https://control.softlayer.com/){:new_window}.

**Nota:** Recomendamos que ejecute el tráfico de almacenamiento en una red de área local virtual que omita el cortafuegos. La ejecución del tráfico de almacenamiento a través de cortafuegos de software incrementará la latencia e incidirá negativamente sobre el rendimiento del almacenamiento.

1. Instale los programas de utilidad multivía de multivía e iSCSI en el host:
   - RHEL/CentOS:

   ```
   yum install iscsi-initiator-utils device-mapper-multipath
   ```
   {: pre}

   - Ubuntu/Debian:

   ```
   sudo apt-get update
   sudo apt-get install multipath-tools
   ```
   {: pre}

2. Cree o edite el archivo de configuración multivía.
   - Edite **/etc/multipath.conf** con la configuración mínima proporcionada en los siguientes mandatos. <br /><br /> **Nota:** tenga en cuenta que para RHEL7/CentOS7, `multipath.conf` puede estar en blanco porque el sistema operativo tiene configuraciones integradas. Ubuntu no utiliza multipath.conf ya que se basa en multipath-tools.

   ```
   defaults {
   user_friendly_names no
   max_fds max
   flush_on_last_del yes
   queue_without_daemon no
   dev_loss_tmo infinity
   fast_io_fail_tmo 5
   }
   # Todos los datos bajo blacklist deben ser específicos de su sistema.
   blacklist {
   wwid "SAdaptec*"
   devnode "^hd[a-z]"
   devnode "^(ram|raw|loop|fd|md|dm-|sr|scd|st)[0-9]*"
   devnode "^cciss.*"
   }
   devices {
   device {
   vendor "NETAPP"
   product "LUN"
   path_grouping_policy group_by_prio
   features "3 queue_if_no_path pg_init_retries 50"
   prio "alua"
   path_checker tur
   failback immediate
   path_selector "round-robin 0"
   hardware_handler "1 alua"
   rr_weight uniform
   rr_min_io 128
   }
   }
   ```
   {: codeblock}

3. Cargue el módulo multivía, inicie los servicios de multivía y configúrelo para iniciarse al arrancar.
   - RHEL 6:
     ```
     modprobe dm-multipath
     ```
     {: pre}

     ```
     service multipathd start
     ```
     {: pre}

     ```
     chkconfig multipathd on
     ```
     {: pre}

   - CentOS 7:
     ```
     modprobe dm-multipath
     ```
     {: pre}

     ```
     systemctl start multipathd
     ```
     {: pre}

     ```
     systemctl enable multipathd
     ```
     {: pre}

   - Ubuntu:
     ```
     service multipath-tools start
     ```
     {: pre}

   - Para otras distribuciones, consulte la documentación del proveedor del sistema operativo.

4. Verifique que la multivía de acceso esté funcionando.
   - RHEL 6:
     ```
     multipath -l
     ```
     {: pre}

     Si se devuelve en blanco, está funcionando.

   - CentOS 7:
     ```
     multipath -ll
     ```
     {: pre}

     RHEL 7/CentOS 7 puede devolver un dispositivo No fc_host, que puede ignorarse.

5. Actualice el archivo **/etc/iscsi/initiatorname.iscsi** con el nombre calificado iSCSI (IQN) del {{site.data.keyword.slportal}}. Escriba el valor en minúsculas.
   ```
   InitiatorName=<valor-del-Portal>
   ```
   {: pre}
6. Edite los valores de CHAP en **/etc/iscsi/iscsid.conf** con el nombre de usuario y contraseña del {{site.data.keyword.slportal}}. Utilice mayúsculas para los nombres de CHAP.
   ```
    node.session.auth.authmethod = CHAP
    node.session.auth.username = <Valor-nombreUsuario-del-Portal>
    node.session.auth.password = <Valor-contraseña-del-Portal>
    discovery.sendtargets.auth.authmethod = CHAP
    discovery.sendtargets.auth.username = <Valor-nombreUsuario-del-Portal>
    discovery.sendtargets.auth.password = <Valor-contraseña-del-Portal>
   ```
   {: codeblock}

   **Nota:** deje un comentario en los demás valores de CHAP. El almacenamiento de {{site.data.keyword.BluSoftlayer_full}} utiliza únicamente autenticación unidireccional.

7. Establezca iSCSI para que se inicie al arrancar e inícielo ahora.
   - RHEL 6:

      ```
      chkconfig iscsi on
      ```
      {: pre}
      ```
      chkconfig iscsid on
      ```
      {: pre}

      ```
      service iscsi start
      ```
      {: pre}

      ```
      service iscsid start
      ```
      {: pre}

   - CentOS 7:

      ```
      systemctl enable iscsi
      ```
      {: pre}

      ```
      systemctl enable iscsid
      ```
      {: pre}

      ```
      systemctl start iscsi
      ```
      {: pre}

      ```
      systemctl start iscsid
      ```
      {: pre}

   - Otras distribuciones: consulte la documentación del proveedor del sistema operativo.

8. Descubra el dispositivo utilizando la dirección IP de destino obtenida desde el {{site.data.keyword.slportal}}.

     a. Ejecute el descubrimiento en la matriz de iSCSI:
     ```
     iscsiadm -m discovery -t sendtargets -p <valor-ip-del-Portal-SL>
     ```
     {: pre}

     b. Establezca el host para que inicie sesión automáticamente en la matriz de iSCSI:
     ```
     iscsiadm -m node -L automatic
     ```
     {: pre}

9. Verifique que el host ha iniciado sesión en la matriz de iSCSI y ha mantenido sus sesiones.
   ```
   iscsiadm -m session
   ```
   {: pre}

   ```
   multipath -l
   ```
   {: pre}

   Esto debería notificar las vías de acceso en este momento.

10. Verifique que el dispositivo esté conectado.  De forma predeterminada, el dispositivo se conectará a /dev/mapper/mpathX donde X es el ID generado del dispositivo conectado.
    ```
    fdisk -l | grep /dev/mapper
    ```
    {: pre}
  Debería notificar algo similar a lo siguiente,
    ```
    Disk /dev/mapper/3600a0980383030523424457a4a695266: 73.0 GB, 73023881216 byte
    ```
  El volumen ahora está montado y es accesible en el host.

## Creación de un sistema de archivos (opcional)

A continuación se describen los pasos para crear un sistema de archivos sobre el volumen montando recientemente. Un sistema de archivos es necesario para que la mayoría de las aplicaciones utilicen el volumen. Utilice **fdisk** para unidades inferiores a 2 TB y **parted** para discos de más de 2 TB.

### fdisk

1. Obtenga el nombre de disco.
   ```
   fdisk -l | grep /dev/mapper
   ```
   {: pre}
   El nombre de disco devuelto debe ser similar a /dev/mapper/XXX.

2. Cree una partición en el disco mediante fdisk.

   ```
   fdisk /dev/mapper/XXX
   ```
   {: pre}

   XXX representa el nombre del disco devuelto en el Paso 1. <br />

   **Nota**: desplácese por los códigos de mandato listados en la tabla de mandatos de Fdisk de esta sección.

3. Cree un sistema de archivos en la nueva partición.

   ```
   fdisk –l /dev/mapper/XXX
   ```
   {: pre}

   - La nueva partición debería listarse con el disco, similar a XXXlp1, seguido por el tamaño, tipo (83) y Linux.
   - Anote el nombre de partición, lo necesitará en el siguiente paso. (XXXlp1 representa el nombre de partición)
   - Cree el sistema de archivos:

     ```
     mkfs.ext3 /dev/mapper/XXXlp1
     ```
     {: pre}

4. Cree un punto de montaje para el sistema de archivos y móntelo.
   - Cree un nombre de partición PerfDisk o donde quiera montar el sistema de archivos:

     ```
     mkdir /PerfDisk
     ```
     {: pre}

   - Uso del nombre de partición para montar el almacenamiento:
     ```
     mount /dev/mapper/XXXlp1 /PerfDisk
     ```
     {: pre}

   - Compruebe que ve el nuevo sistema de archivos listado:
     ```
     df -h
     ```
     {: pre}

5. Añada el nuevo sistema de archivos al archivo **/etc/fstab** del sistema para habilitar el montaje automático al arrancar.
   - Añada la siguiente línea en la parte inferior de **/etc/fstab** (utilizando el nombre de partición del Paso 3). <br />

     ```
     /dev/mapper/XXXlp1    /PerfDisk    ext3    defaults,_netdev    0    1
     ```
     {: pre}

#### Tabla de mandatos Fdisk
<table border="0" cellpadding="0" cellspacing="0">
 <tbody>
	<tr>
		<td style="width:40%;"><div>Mandato</div></td>
		<td style="width:60%;">Resultado</td>
	</tr>
	<tr>
		<td><li><code>Command: n</code></li>	</td>
		<td>Crea una nueva partición.</td>
	</tr>
	<tr>
		<td><li><code>Command action: p</code></li></td>
		<td>Hace que la partición sea la primaria.</td>
	</tr>
	<tr>
		<td><li><code>Partition number (1-4): 1</code></li></td>
		<td>Se convierte en la partición 1 del disco.</td>
	</tr>
	<tr>
		<td><li><code>First cylinder (1-8877): 1 (default)</code></li></td>
		<td>Inicia en el cilindro 1.</td>
	</tr>
	<tr>
		<td><li><code>Last cylinder, +cylinders or +size {K, M, G}: 8877 (default)</code></li></td>
		<td>Pulse Intro para ir al último cilindro.</td>
	</tr>
	<tr>
		<td><li>*<code>Command: t</code></li></td>
		<td>Configura el tipo de partición.</td>
	</tr>
	<tr>
		<td><li><code>Select partition 1.</code></li></td>
		<td>Selecciona la partición 1 para configurarla como un tipo específico.</td>
	</tr>
	<tr>
		<td><li>*<code>Hex code: 83</code></li></td>
		<td>Selecciona Linux como el Tipo (83 es el código hexadecimal para Linux).</td>
	 </tr>
	<tr>
		<td><li>*<code>Command: w</code></li></td>
		<td>Escribe la información de la nueva partición en el disco.</td>
	</tr>
 </tbody>
</table>

  (`*`)Escriba m para obtener ayuda.

  (`**`)Escriba L para ver la lista de códigos hexadecimales

### parted

En muchas distribuciones Linux, la opción **parted** viene preinstalada. Si no se incluye en su distribución, puede instalarla con:
- Debian/Ubuntu
  ```
  sudo apt-get install parted  
  ```
  {: pre}

- RHEL/CentOS:
  ```
  yum install parted  
  ```
  {: pre}


Para crear un sistema de archivos con **parted**, siga estos pasos:

1. Ejecute parted.

   ```
   parted
   ```
   {: pre}

2. Cree una partición en el disco.
   1. A menos que se especifique lo contrario, parted utilizará la unidad primaria, que la mayoría de las veces es **/dev/sda**. Cambie al disco en el que quiera la partición utilizando el mandato **select**. Sustituya **XXX** por el nuevo nombre de dispositivo.

      ```
      (parted) select /dev/mapper/XXX
      ```
      {: pre}

   2. Ejecute **print** para confirmar que está en el disco correcto.

      ```
      (parted) print
      ```
      {: pre}

   3. Cree una nueva tabla de partición GPT

      ```
      (parted) mklabel gpt
      ```
      {: pre}

   4. Parted se puede utilizar para crear particiones de disco lógicas y primarias, los pasos a seguir son los mismos. Para crear una nueva partición, parted utiliza **mkpart**. Puede especificar parámetros adicionales como **primaria** o **lógica** en función del tipo de partición que quiera crear.
   <br /> **Nota**: el valor predeterminado de las unidades listadas es megabytes (MB), para crear una partición de 10 GB deberá empezar en 1 y acabar en 10000. También puede cambiar las unidades de tamaño a terabytes especificando `(parted) unit TB`, si lo desea.

      ```
      (parted) mkpart
      ```
      {: pre}

   5. Salga de parted con **quit**.

      ```
      (parted) quit
      ```
      {: pre}

3. Cree un sistema de archivos en la nueva partición.

   ```
   mkfs.ext3 /dev/mapper/XXXlp1
   ```
   {: pre}

   **Nota**: es importante seleccionar el disco y la partición adecuados al ejecutar el mandato anterior.
   Compruebe el resultado imprimiendo la tabla de partición. En la columna del sistema de archivos, debería ver ext3.

4. Cree un punto de montaje para el sistema de archivos y móntelo.
   - Cree un nombre de partición PerfDisk o donde quiera montar el sistema de archivos:

     ```
     mkdir /PerfDisk
     ```
     {: pre}

   - Uso del nombre de partición para montar el almacenamiento:

     ```
     mount /dev/mapper/XXXlp1 /PerfDisk
     ```
     {: pre}

   - Compruebe que ve el nuevo sistema de archivos listado:

     ```
     df -h
     ```
     {: pre}

5. Añada el nuevo sistema de archivos al archivo **/etc/fstab** del sistema para habilitar el montaje automático al arrancar.
   - Añada la siguiente línea en la parte inferior de **/etc/fstab** (utilizando el nombre de partición del Paso 3). <br />

     ```
     /dev/mapper/XXXlp1    /PerfDisk    ext3    defaults    0    1
     ```
     {: pre}




## Cómo verificar si MPIO se ha configurado correctamente en sistemas operativos *NIX

Para comprobar si la multivía está recogiendo dispositivos, solo deberían mostrarse los dispositivos NETAPP y debería haber dos de ellos.

```
# multipath -l
```
{: pre}

```
root@server:~# multipath -l
3600a09803830304f3124457a45757067 dm-1 NETAPP,LUN C-Mode size=20G features='1 queue_if_no_path' hwhandler='0' wp=rw
|-+- policy='round-robin 0' prio=-1 status=active`
6:0:0:101 sdd 8:48 active undef running `-+- policy='round-robin 0' prio=-1 status=enabled`
7:0:0:101 sde 8:64 active undef running
```

Compruebe que los discos estén presentes, y debería haber dos discos con el mismo identificador, y un listado /dev/mapper del mismo tamaño con el mismo identificador. El dispositivo /dev/mapper es el que configura la multivía:

```
# fdisk -l | grep Disk
```
{: pre}

```
root@server:~# fdisk -l | grep Disk
Disk /dev/sda: 500.1 GB, 500107862016 bytes Disk identifier: 0x0009170d
Disk /dev/sdc: 21.5 GB, 21474836480 bytes Disk identifier: 0x2b5072d1
Disk /dev/sdb: 21.5 GB, 21474836480 bytes Disk identifier: 0x2b5072d1
Disk /dev/mapper/3600a09803830304f3124457a45757066: 21.5 GB, 21474836480 bytes Disk identifier: 0x2b5072d1
```

Si no se configura correctamente, tendrá el siguiente aspecto:
```
No multipath output root@server:~# multipath -l root@server:~#
```

Se mostrarán los dispositivos en la lista negra:
```
# multipath -l -v 3 | grep sd <date and time>
```
{: pre}

```
root@server:~# multipath -l -v 3 | grep sd Feb 17 19:55:02
| sda: device node name blacklisted Feb 17 19:55:02
| sdb: device node name blacklisted Feb 17 19:55:02
| sdc: device node name blacklisted Feb 17 19:55:02
| sdd: device node name blacklisted Feb 17 19:55:02
| sde: device node name blacklisted Feb 17 19:55:02
```

fdisk solo muestra los dispositivos `sd*`, no los `/dev/mapper`

```
# fdisk -l | grep Disk
```
{: pre}

```
root@server:~# fdisk -l | grep Disk
Disk /dev/sda: 500.1 GB, 500107862016 bytes Disk identifier: 0x0009170d
Disk /dev/sdc: 21.5 GB, 21474836480 bytes Disk identifier: 0x2b5072d1
Disk /dev/sdb: 21.5 GB, 21474836480 bytes Disk identifier: 0x2b5072d1
```
