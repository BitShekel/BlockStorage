---

copyright:
  years: 2014, 2018
lastupdated: "2018-03-15"

---
{:new_window: target="_blank"}
{:shortdesc: .shortdesc}

# Conexión a los LUN de iSCSI de MPIO en Microsoft Windows

Antes de empezar, asegúrese de que el host que accede al volumen de {{site.data.keyword.blockstoragefull}} se haya autorizado a través del [{{site.data.keyword.slportal}}](https://control.softlayer.com/){:new_window}:

1. Desde la página de listado de {{site.data.keyword.blockstorageshort}}, pulse el botón **Acciones** asociado al volumen recién suministrado y pulse **Autorizar host**.
2. Seleccione el host(s) deseado de la lista y pulse **Enviar**; esta acción autoriza al host(s) el acceso al volumen.

## Cómo montar volúmenes de {{site.data.keyword.blockstorageshort}}

A continuación se describen los pasos necesarios para conectar una instancia de cálculo de {{site.data.keyword.BluSoftlayer_full}} basada en Windows a un número de unidad lógica (LUN) de interfaz para pequeños sistemas (iSCSI) de E/S de multivía de acceso (MPIO). El ejemplo se basa en Windows Server 2012. Los pasos pueden ajustarse para otras versiones de Windows de acuerdo con la documentación del proveedor del sistema operativo.

### Configurar la característica MPIO

1. Inicie Server Manager y vaya a **Gestionar**, **Añadir roles y características**.
2. Pulse **Siguiente** para ir al menú de características.
3. Desplácese y marque el recuadro de selección **E/S de multivía de acceso**.
4. Pulse **Instalar** para instalar MPIO en el servidor del host.
![Añadir roles y características](/images/Roles_Features.png)

### Añadir soporte de iSCSI para MPIO

1. Abra las propiedades de MPIO. Para abrir las propiedades de MPIO, pulse **Inicio**, vaya a Herramientas administrativas y pulse **MPIO**.
2. Pulse el separador **Descubrir multivías de acceso**
3. Marque el recuadro de selección **Añadir soporte para dispositivos iSCSI** y, a continuación, pulse **Añadir**. Cuando se le solicite reiniciar el sistema, pulse **Sí**.

**Nota**: en el caso de Windows Server 2008, añadir soporte para iSCSI permite que Microsoft Device Specific Module (MSDSM) reclame todos los dispositivos iSCSI para MPIO, lo que primero requiere una conexión a un destino de iSCSI.

### Configurar el iniciador iSCSI

1. Inicie el iniciador iSCSI desde Server Manager y seleccione **Herramientas**, **Iniciador iSCSI**.
2. Pulse el separador **Configuración**.
    - Puede que el campo de nombre de iniciador ya esté rellenado con una entrada similar a iqn.1991-05.com.microsoft:.
    - Pulse **Cambiar** para sustituir los valores existentes por su nombre calificado iSCSI (IQN). El nombre IQN se puede obtener en la pantalla Detalles de {{site.data.keyword.blockstorageshort}} en el Portal.
    ![Propiedades del Iniciador iSCSI](/images/iSCSI.png)
    - Pulse el separador **Descubrir** y pulse **Descubrir portal**.
    - Especifique la dirección IP del destino iSCSI y deje el puerto en el valor predeterminado de 3260. 
    - Pulse **Avanzado** para abrir la ventana Configuración avanzada.
    - Seleccione **Habilitar inicio de sesión CHAP** para activar la autenticación CHAP.
    ![Habilitar inicio de sesión CHAP](/images/Advanced_0.png)
    **Nota:** los campos Nombre y destino secretos distinguen entre mayúsculas y minúsculas.
         - Suprima las entradas existentes en el campo Nombre y escriba el nombre de usuario del portal.
         - Escriba la contraseña del portal en el campo Destino secreto.<br/>
         **Nota:** los campos Nombre y destino secretos se pueden obtener en la pantalla Detalles de {{site.data.keyword.blockstorageshort}} en el Portal como Nombre de usuario y Contraseña respectivamente.
    - Pulse **Aceptar** en las ventanas de configuración avanzada y descubrir portal de destino para volver a la pantalla principal Propiedades del iniciador iSCSI. Si recibe errores de autenticación, vuelva a comprobar las entradas de nombre de usuario y contraseña. ![Destino inactivo](/images/Inactive_0.png)
    Tenga en cuenta que el nombre de su destino aparece en la sección de destinos descubiertos con un estado inactivo. 
    
    Los siguientes pasos muestran cómo activar el destino.
    
### Activar destino

1. Pulse **Conectar** para conectarse al destino.
2. Marque el recuadro de selección **Habilitar multivía de acceso** para habilitar la E/S de multivía de acceso en el destino. ![Habilitar multivía de acceso](/images/Connect_0.png)
3. Pulse **Avanzado** y seleccione **Habilitar inicio de sesión CHAP**.
![Habilitar CHAP](/images/chap_0.png)
4. Especifique el nombre en el campo Nombre y especifique la contraseña en el campo secreto de destino.<br/>
**Nota:** los valores de los campos Nombre y destino secretos se pueden obtener en la pantalla Detalles de {{site.data.keyword.blockstorageshort}} en el portal como Nombre de usuario y Contraseña respectivamente
5. Pulse **Aceptar** hasta que se abra la ventana Propiedades del iniciador iSCSI. El estado del destino en la sección de destinos descubiertos cambia de Inactivo a Conectado.
![Estado conectado](/images/Connected.png) 


### Configure MPIO en el iniciador iSCSI

1. Inicie el iniciador iSCSI y, en el separador Destinos, pulse **Propiedades**.
2. Pulse **Añadir sesión** en la ventana Propiedades para iniciar la ventana Conectar a destino.
3. Marque el recuadro de selección **Habilitar multivía de acceso** y pulse **Avanzado...**.
  ![Destino](/images/Target.png) 
  
4. En la ventana Configuración avanzada
   - Deje el valor predeterminado en los campos Adaptador local e IP de iniciador. Para los servidores de host con varias interfaces de iSCSI, deberá elegir el valor adecuado para el campo IP del iniciador.
   - Seleccione la dirección IP del almacenamiento iSCSI en la lista desplegable de IP de portal de destino.
   - Marque el recuadro de selección **Habilitar inicio de sesión CHAP**
   - Escriba los valores secretos Nombre y Destino obtenidos en el portal y pulse **Aceptar**.
   - Pulse **Aceptar** en la ventana Conectar a destino para volver a la ventana Propiedades. La ventana Propiedades ahora debería mostrar más de una sesión en la ventana Identificador. Ahora tiene más de una sesión en el almacenamiento de iSCSI.
   ![Valores](/images/Settings.png) 
   
5. En la ventana Propiedades, pulse **Dispositivos** e inicie la ventana Dispositivos. El nombre de la interfaz del dispositivo debería incluir mpio al principio del nombre del dispositivo. <br/>
  ![Dispositivos](/images/Devices.png) 
  
6. Pulse **MPIO** para iniciar la ventana Detalles del dispositivo. Esta ventana le permite elegir las políticas de equilibrio de carga para MPIO y también le muestra las vías de acceso a iSCSI. En este ejemplo, dos vías de acceso se muestran como disponibles para MPIO con una política de equilibrio de carga Round Robin con subconjunto.   ![Detalles del dispositivo](/images/DeviceDetails.png) 
  
7. Pulse **Aceptar** varias veces para salir del iniciador iSCSI.



## Cómo verificar si MPIO se ha configurado correctamente en sistemas operativos Windows

Para verificar si MPIO de Windows está configurado, primero debe asegurarse de que el complemento MPIO esté habilitado y que se haya completado el requisito de rearrancar:

![Características_Roles_0](/images/Roles_Features_0.png)

Una vez completado el rearranque y añadido el dispositivo de almacenamiento de rendimiento, podemos verificar si MPIO se ha configurado y funciona. Para ello, vaya a **Detalles del dispositivo de destino** y pulse **MPIO**:
![DetallesDispositivo_0](/images/DeviceDetails_0.png)

Si MPIO no se ha configurado en el dispositivo de almacenamiento de rendimiento y los equipos de {{site.data.keyword.BluSoftlayer_full}} realizan mantenimiento o se produce una interrupción de red, el dispositivo de almacenamiento se desconectará y no estará disponible. MPIO garantiza un nivel adicional de conectividad durante estos sucesos, y mantiene una sesión establecida con lecturas/escrituras activas en el LUN.

## Cómo desmontar volúmenes de {{site.data.keyword.blockstorageshort}}

A continuación se describen los pasos necesarios para desconectar una instancia de cálculo de Bluemix basada en Windows a un LUN de iSCSI de MPIO. El ejemplo se basa en Windows Server 2012. Los pasos pueden ajustarse para otras versiones de Windows de acuerdo con la documentación del proveedor del sistema operativo.

### Iniciar el iniciador de iSCSI.

1. Pulse el separador **Destinos**.
2. Seleccione los destinos que desee eliminar y pulse **Desconectar**.

### Opcional si ya no necesita acceder a los destinos de iSCSI:

1. Pulse el separador **Descubrimiento** en el iniciador iSCSI.
2. Marque el portal de destino asociado a su volumen de almacenamiento y pulse **Eliminar**.
