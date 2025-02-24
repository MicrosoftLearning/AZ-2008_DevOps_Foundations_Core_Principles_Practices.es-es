---
lab:
  title: Mejora de la resistencia de la carga de trabajo mediante Traffic Manager y prueba de resistencia mediante Azure Chaos Studio
  module: Operate with DevOps
---

# Laboratorio 04: Mejora de la resistencia de la carga de trabajo mediante Traffic Manager y prueba de resistencia mediante Azure Chaos Studio

## Tiempo estimado: 35 minutos

## Escenario

Recuerda el escenario de este módulo en el que trabajas para una empresa de desarrollo de software en el sector minorista que experimenta problemas frecuentes de tiempo de inactividad y rendimiento con su nueva aplicación de tienda en línea. Dado que has decidido mejorar la carga de trabajo y probar la resistencia mediante Traffic Manager y Azure Chaos Studio respectivamente, este laboratorio te ofrece la oportunidad de implementar un perfil de Traffic Manager y validar la función de Traffic Manager, configurar el entorno de Azure Chaos Studio, así como implementar y validar un experimento.

## Objetivos

En este laboratorio, aprenderás a:

- Preparar la suscripción a Azure para el laboratorio
- Mejorar la resistencia de la carga de trabajo mediante Traffic Manager
- Probar la resistencia de la carga de trabajo mediante Azure Chaos Studio
- Eliminar los recursos utilizados en los laboratorios

> **Nota:** en el laboratorio anterior, implementaste dos instancias de una aplicación web de .NET en dos regiones de Azure. En este laboratorio, primero crearás una configuración resistente que implementa la función de equilibrio de carga de Azure Traffic Manager entre las dos instancias de aplicación web. A continuación, usarás Azure Chaos Studio para desencadenar un error de una de las aplicaciones a fin de probar la resistencia de la implementación del equilibrio de carga.

> **Nota:** para este laboratorio, usa la misma cuenta de GitHub que has estado usando en todos los laboratorios anteriores.

## Requisitos previos

- [Laboratorio 01: Planificación y gestión ágiles mediante GitHub](01-agile-planning-management-using-github.md) completado
- [Laboratorio 02: Implementación del flujo de trabajo con GitHub](02-implement-manage-repositories-using-github.md) completado
- [Laboratorio 03: Implementación de CI/CD con Acciones de GitHub e IaC con Bicep](03-implement-ci-cd-with-github-actions-and-iac-with-bicep.md) completado
- Una suscripción a Azure en la que tengas al menos el acceso de nivel de propietario

## Ejercicio 0: Preparación de la suscripción a Azure para el laboratorio

> **Nota:** si usas una nueva suscripción a Azure, es posible que algunos de los proveedores de recursos de Azure no estén registrados. Dado que el proceso de registro puede tardar unos minutos, para minimizar el tiempo de espera, los registrarás al principio de este laboratorio.

> **Nota:** en este laboratorio, usarás Azure Chaos Studio. Si es la primera vez que implementa Azure Chaos Studio en tu suscripción, primero debes registrar el proveedor de recursos correspondiente.

1. Inicia un explorador web y ve a Azure Portal en `https://portal.azure.com`.
1. Si se te solicita, inicia sesión con tu cuenta de Microsoft Entra ID con el acceso de propietario a la suscripción a Azure que usaste en el laboratorio anterior.
1. En la pestaña del navegador web que muestra Azure Portal, en el cuadro de texto de búsqueda de la parte superior de la página, escribe **`Subscriptions`** y, en la lista de resultados, selecciona **Suscripciones**.
1. En la página suscripciones, en el menú vertical del lado izquierdo, selecciona **Proveedores de recursos**.
1. En la lista de proveedores de recursos, busca y selecciona **Microsoft.Chaos**.
1. Con el proveedor de recursos **Microsoft.Chaos** seleccionado, en la barra de herramientas, selecciona **Registrar**.

   > **Nota:** no esperes hasta que se complete el registro, continúa directamente con el ejercicio siguiente. El registro puede tardar un par de minutos.

## Ejercicio 1: Mejora de la resistencia de la carga de trabajo mediante Traffic Manager

En este ejercicio, implementarás una configuración resistente que distribuye las solicitudes entre las dos instancias de aplicación web de .NET en dos regiones de Azure diferentes mediante Azure Traffic Manager.

> **Nota:** para el propósito de nuestro laboratorio, consideraremos la implementación de la aplicación web de eShopOnWeb basada en .NET en la región Este de EE. UU. como instancia principal. Aunque, en este caso, esta consideración es puramente arbitraria (y solo se usa con fines de demostración), puede haber escenarios en los que podría ser beneficioso priorizar uno de los puntos de conexión.

El ejercicio consta de las tareas siguientes:

- Tarea 1: Implementación de un perfil de Traffic Manager
- Tarea 2: Validación de la funcionalidad de Traffic Manager

### Tarea 1: Implementación de un perfil de Traffic Manager

1. En la pestaña del explorador web que muestra Azure Portal, en el cuadro de texto de búsqueda de la parte superior de la página, escribe **`Traffic Manager profiles`** y, en la lista de resultados, selecciona **perfiles de Traffic Manager**.
1. En la página **Servicios de equilibrio de carga \| Traffic Manager**, selecciona **+ Crear**.
1. En la página **Crear perfil de Traffic Manager**, realiza las siguientes acciones:

   - En el cuadro de texto **Nombre**, escribe **`devopsfoundationstmprofile`**.

       > **Nota:** el nombre del perfil de Traffic Manager debe ser único. Si recibes un mensaje de error que indica que el nombre ya está en uso, prueba otro nombre y asegúrate de registrarlo. Lo necesitarás a lo largo de este laboratorio.

   - En la lista desplegable **Método de enrutamiento**, selecciona **Prioridad**.

   > **Nota:** elegimos el método de enrutamiento de prioridad para reflejar la suposición algo arbitraria de que la aplicación web de Azure App Service debe controlar todas las solicitudes en el Este de EE. UU.

   - Comprueba que la suscripción a Azure aparece en la lista desplegable **Suscripción**
   - Selecciona el vínculo **Crear nuevo** debajo de la lista desplegable **Grupo de recursos**, en el cuadro de texto **Nombre**, escribe **`rg-devops-foundations`** y, después, selecciona **Aceptar**.
   - En la lista desplegable **ubicación del grupo de recursos**, selecciona la misma región de Azure que elegiste en los laboratorios anteriores de este curso.

1. Selecciona **Crear** para iniciar el proceso de aprovisionamiento.

   > **Nota**: espera a que la implementación se complete. Esto debería completarse en un minuto.

1. En la página **Servicios de equilibrio de carga \| Traffic Manager**, si es necesario, selecciona **Actualizar** y, a continuación, selecciona **devopsfoundationstmprofile**.
1. En la página **devopsfoundationstmprofile**, en la sección **Essentials**, copia el valor de la configuración **Nombre DNS** y guárdalo. Lo necesitarás a lo largo de este laboratorio.
1. En la página **devopsfoundationstmprofile**, en el menú de navegación izquierdo, en la sección **Valores**, selecciona **Configuración**.
1. Revisa el contenido de la página de **devopsfoundationstmprofile \| Configuración**. Ten en cuenta que, de forma predeterminada, el **tiempo de vida de DNS (TTL)** está establecido en **60** segundos. Cambia el valor a **5** segundos.

   > **Nota:** al reducir el valor de TTL, se acelerará el tiempo de conmutación por error. 

   > **Nota:** además del valor de TTL, el intervalo de sondeo, el tiempo de espera del sondeo y el número tolerado de errores afectan a la cantidad de tiempo que se detecta un error de punto de conexión.

1. En la página **devopsfoundationstmprofile \| Configuración**, en la lista desplegable **Protocolo**, selecciona **HTTPS** y, en el cuadro de texto **Puerto**, escribe **443**.

   > **Nota:** ambas aplicaciones web de Azure App Service que se configurarán como puntos de conexión son accesibles a través de HTTPS en el puerto TCP 443 predeterminado.

1. En la página **devopsfoundationstmprofile \| Configuración**, selecciona **Guardar**.
1. En la página **devopsfoundationstmprofile**, en el menú de navegación izquierdo, en la sección **Valores**, selecciona **Puntos de conexión**.
1. En la página **devopsfoundationstmprofile \| Puntos de conexión***, selecciona **+ Agregar**.

   > **Nota:** primero agregarás un punto de conexión que represente la aplicación web de Azure App Service.

1. En el panel **Agregar punto de conexión**, realiza las siguientes acciones:

   - Asegúrate de que **Punto de conexión de Azure** aparece en la lista desplegable **Tipo**.
   - En el cuadro de texto **Nombre**, escribe **`DevOps Foundations web app - East US`**.
   - Asegúrate de que la casilla **Habilitar punto de conexión** esté activada.
   - En la lista desplegable **Tipo de recurso de destino**, selecciona **App Service**.
   - En la lista desplegable **Recurso de destino**, en la sección **rg-eshoponweb-eastus**, selecciona el nombre de la aplicación web de App Service en la región Este de EE. UU. de Azure.
   - En el cuadro de texto **Prioridad**, escribe **1**.

   > **Nota:** un valor de prioridad inferior designa una prioridad más alta. En este caso, todas las solicitudes se enviarán a la instancia de la aplicación web en la región Este de EE. UU., siempre y cuando esa instancia permanezca disponible.

   - Asegúrate de que las **Comprobaciones de estado** estén habilitadas.

1. Selecciona **Agregar**.

1. De nuevo en la página **devopsfoundationstmprofile \| Puntos de conexión***, selecciona **+ Agregar**.

   > **Nota:** a continuación, agregarás un punto de conexión que representa la otra aplicación web de Azure App Service implementada en la región Oeste de Europa de Azure.

1. En el panel **Agregar punto de conexión**, realiza las siguientes acciones:

   - Asegúrate de que **Punto de conexión de Azure** aparece en la lista desplegable **Tipo**.
   - En el cuadro de texto **Nombre**, escribe **`DevOps Foundations web app - West Europe`**.
   - Asegúrate de que la casilla **Habilitar punto de conexión** esté activada.
   - En la lista desplegable **Tipo de recurso de destino**, selecciona **App Service**.
   - En la lista desplegable **Recurso de destino**, en la sección **rg-eshoponweb-westeurope**, selecciona el nombre de la aplicación web de App Service en la región Oeste de Europa de Azure.
   - En el cuadro de texto **Prioridad**, escribe **2**.
   - Asegúrate de que las **Comprobaciones de estado** estén habilitadas.

1. Selecciona **Agregar**.
1. De nuevo en la página **devopsfoundationstmprofile \| Puntos de conexión**, comprueba que ambos puntos de conexión aparecen con la entrada **En línea** en la columna **Estado del monitor**.

   > **Nota:** es posible que tengas que esperar un par de minutos y seleccionar **Actualizar** antes de que la entrada de estado esté actualizada.

### Tarea 2: Validación de la funcionalidad de Traffic Manager

1. Inicia una ventana del explorador web y ve al nombre DNS asociado al perfil de Traffic Manager.
1. Comprueba que el explorador web muestra la página familiar del sitio web hospedado por la aplicación web de Azure App Service que implementaste en el laboratorio anterior.
1. Cierra la ventana del explorador web recién abierta.
1. Cambie a la ventana del explorador web en la que se muestra Azure Portal.
1. En Azure Portal, seleccione el icono **Azure Cloud Shell** a la derecha del cuadro de texto de búsqueda.
1. Si es necesario, seleccione **Bash** en el menú desplegable de la esquina superior izquierda del panel de Cloud Shell.
1. En la sesión de Bash en el panel de Cloud Shell, ejecute el siguiente comando para resolver el nombre DNS del perfil de Traffic Manager que creó en la tarea anterior en uno de los dos puntos de conexión correspondientes (reemplace el marcador de posición `<tm_profile>` por el nombre DNS del perfil de Traffic Manager, por ejemplo, `devopsfoundationstmprofile.trafficmanager.net`), pero asegúrese de quitar el `https:\\` inicial:

   ```cli
   nslookup <tm_profile>
   ```

1. Vuelva a ejecutar el mismo comando varias veces, esperando un poco más de 5 segundos entre cada invocación (para eliminar la posibilidad de almacenamiento en caché de DNS) y tenga en cuenta que la salida en cada caso hace referencia al nombre DNS de la aplicación web en la región este de EE. UU. de Azure.

## Ejercicio 2: Prueba de la resistencia de la carga de trabajo mediante Azure Chaos Studio

En este ejercicio, probarás la resistencia de la carga de trabajo mediante Azure Chaos Studio.

> **Nota:** En este ejercicio se muestra el uso de Azure Chaos Studio. El objetivo de Azure Chaos Studio es ayudar a medir, comprender y crear resistencia de aplicaciones y servicios. Esto se logra mediante la interrupción intencionada de las cargas de trabajo con el fin de identificar brechas de resistencia e implementar la mitigación correspondiente de forma proactiva, en lugar de reactiva.

El ejercicio consta de las tareas siguientes:

- Tarea 1: Configuración del entorno de Azure Chaos Studio
- Tarea 2: Implementación de un experimento
- Tarea 3: Validación del experimento

### Tarea 1: Configuración del entorno de Azure Chaos Studio

1. En la pestaña explorador web que muestra Azure Portal, en el cuadro de texto de búsqueda de la parte superior de la página, escribe **`Chaos Studio`** y, en la lista de resultados, selecciona **Chaos Studio**.
1. En la página **Chaos Studio**, seleccione **Destinos**.
1. En la página **Chaos Studio \| Destinos**, seleccione la instancia de aplicación web de Azure App Service en el grupo de recursos **rg-eshoponweb-eastus** en la región Este de EE. UU. que implementó en el laboratorio anterior.
1. En la barra de herramientas, seleccione el encabezado de lista desplegable **Habilitar destinos** y, en la lista desplegable, seleccione **Habilitar destinos directos de servicio (Todos los recursos)**.
1. En la página **Habilitar destinos directos del servicio**, asegúrese de que aparece la instancia de aplicación web correcta de App Service, seleccione **Revisar y habilitar** y, a continuación, seleccione **Habilitar**.

   > **Nota:** Espere a que el destino esté habilitado. Debería tardar menos de un minuto.

### Tarea 2: Implementación de un experimento

1. En Azure Portal, en el cuadro de texto de búsqueda de la parte superior de la página, escribe **`Chaos Studio`** y, en la lista de resultados, selecciona **Chaos Studio**.
1. En la página **Chaos Studio**, seleccione **Experimentos**.
1. En la página **Experimentos**, seleccione **+ Crear** y, a continuación, en la lista desplegable, seleccione **Nuevo experimento**.
1. En la pestaña **Conceptos básicos** de la página **Crear un experimento**, realice las siguientes acciones:

   - Compruebe que la suscripción de Azure aparece en la lista desplegable **Suscripción**.
   - Selecciona el vínculo **Crear nuevo** debajo de la lista desplegable **Grupo de recursos**, en el cuadro de texto **Nombre**, escribe **`rg-devops-foundations`** y, después, selecciona **Aceptar**.
   - En la sección **Detalles del experimento**, en el cuadro de texto **Nombre**, escribe **`DevOps_Foundations_Labs_Experiment_01`**.
   - En la lista desplegable **Región**, seleccione la región de Azure**Oeste de Europa**.

   > **Nota:** Podría elegir cualquier región de Azure, pero teniendo en cuenta que está probando errores en un recurso de la región de Oeste de Europa, cualquier región que no sea la del Este de EE. UU. parece más apropiada.

1. En la pestaña **Aspectos básicos** de la página **Crear un experimento**, seleccione **Siguiente: Permisos >**.
1. En la pestaña **Permisos**, acepta la opción predeterminada **Identidad asignada por el sistema** y, después, selecciona **Siguiente: Diseñador de experimentos >**.
1. En la pestaña **Diseñador de experimentos**, realiza las siguientes acciones:

   - En el cuadro de texto **Paso**, escribe **`Step 1: Failover an App Service web app`**.
   - En el cuadro de texto **Rama**, escribe **`Branch 1: Emulate an App Service failure`**.
   - Seleccione **+ Agregar acción** y, en la lista desplegable, seleccione **Agregar error**.

1. En la pestaña **Detalles del error** del panel **Agregar error**, en la lista desplegable **Errores**, seleccione **Detener App Service** y establezca el valor de **Duración (minutos)** en 10.
1. En la pestaña **Detalles del error** del panel **Agregar errores**, seleccione **Siguiente: Recursos de destino >**.
1. En la pestaña **Recursos de destino** del panel **Agregar errores**, asegúrese de que la opción **Seleccionar manualmente en una lista** está seleccionada, seleccione la aplicación web de App Service que agregó como destino al entorno de Chaos Studio y, a continuación, seleccione **Agregar**.
1. De nuevo en la pestaña **Diseñador de experimentos** de la página **Crear un experimento**, seleccione **Revisar y crear**.
1. En la pestaña **Revisar y crear** de la página **Crear un experimento**, seleccione **Crear**.

   > **Nota:** Espere a que se cree el experimento. Debería tardar menos de un minuto.

   > **Nota:** Para que el experimento se realice correctamente, también debe conceder permisos suficientes a la cuenta de servicio administrada recién creada para detener la aplicación web de Azure App Service. Aprovecharemos para este propósito el rol integrado Colaborador de Azure, pero podría crear un rol personalizado si desea seguir el principio de privilegios mínimos.

1. En Azure Portal, en el cuadro de texto de búsqueda de la parte superior de la página, escribe **`App Services`** y selecciona **App Services** en la lista de resultados.
1. En la página **App Services**, seleccione la aplicación web de Azure App Service en la región Este de EE. UU. que implementó en el laboratorio anterior.
1. En la página de la aplicación web, en el menú vertical del lado izquierdo, seleccione **Control de acceso (IAM)**.
1. En la página **Control de acceso (IAM)** de la aplicación web, seleccione **+ Agregar** y, en la lista desplegable, seleccione **Agregar asignación de roles**.
1. En la pestaña **Rol** de la página **Agregar asignación de roles**, seleccione **Roles de administrador con privilegios**, en la lista de roles, seleccione **Colaborador** y, a continuación, seleccione **Siguiente**.
1. En la pestaña **Miembros** de la página **Agregar asignación de roles**, establezca la opción **Acceso asignado a** para **Identidad administrada** y, a continuación, seleccione **+ Seleccionar miembros**
1. En el panel **Seleccionar identidades administradas**, en la lista desplegable **Identidades administradas**, seleccione **Chaos Experiment**, en la lista de identidades administradas, seleccione la que creó anteriormente en esta tarea y, a continuación, seleccione **Seleccionar**.
1. De nuevo en la pestaña **Miembros** de la página **Agregar asignación de roles**, seleccione **Revisar y asignar** y, a continuación, vuelva a seleccionar **Revisar y asignar**.

### Tarea 3: Validación del experimento

1. En Azure Portal, vuelva a la página **Chaos Studio** y, en el menú vertical del lado izquierdo, seleccione **Experimentos**.
1. En la lista de experimentos, seleccione **DevOps_Foundations_Labs_Experiment_01**.
1. En la página **DevOps_Foundations_Labs_Experiment_01**, seleccione **Inicio** y, cuando se le pida confirmación, seleccione **Aceptar**.
1. Espere hasta que el estado del experimento cambie a **En ejecución**.
1. En la página **DevOps_Foundations_Labs_Experiment_01**, en la sección **Historial**, seleccione el vínculo **Detalles** de la entrada que representa la ejecución del experimento actual.
1. En la página de detalles del experimento **DevOps_Foundations_Labs_Experiment_01**, revise la lista que contiene el paso, la rama y la acción asociadas al experimento.

   > **Nota:** Es posible que tenga que seleccionar **Actualizar** entrada de la barra de herramientas para actualizar el estado de la lista.

1. Seleccione la entrada **Acción** para mostrar el panel **Detalles del error**.
1. En la sección **Destinos en ejecución**, seleccione la entrada que representa la aplicación web de Azure App Service.
1. En la página de la aplicación web, en la sección ** Essential**, tenga en cuenta que el estado de la aplicación web aparece como **Detenido**.

   > **Nota:** Es posible que tenga que seleccionar **Actualizar** entrada de la barra de herramientas para actualizar el estado de la aplicación web.

   > **Nota:** Ahora vamos a comprobar si Traffic Manager redirige correctamente las solicitudes destinadas a su perfil al punto de conexión que representa la aplicación web de App Service en la región Oeste de Europa.

1. Inicie un explorador web y vaya al nombre DNS asociado al perfil de Traffic Manager.
1. Compruebe que el explorador web muestra la página familiar del sitio web hospedado por la aplicación web de Azure App Service que implementó en el laboratorio anterior.
1. Cambie a la ventana del explorador web en la que se muestra Azure Portal.
1. En Azure Portal, seleccione el icono **Azure Cloud Shell** a la derecha del cuadro de texto de búsqueda.
1. En la sesión de Bash en el panel de Cloud Shell, ejecute el siguiente comando para resolver el nombre DNS del perfil de Traffic Manager que creó en el ejercicio anterior a uno de los dos puntos de conexión correspondientes (reemplace el marcador de posición `<tm_profile>` por el nombre DNS del perfil de Traffic Manager, pero asegúrese de quitar el `https:\\` inicial):

   ```cli
   nslookup <tm_profile>
   ```

1. Vuelva a ejecutar el mismo comando varias veces, esperando un poco más de 5 segundos entre cada invocación (para eliminar la posibilidad de almacenamiento en caché de DNS) y compruebe que la salida en cada caso hace referencia al nombre DNS de la aplicación web en la región de Azure Oeste de Europa.

> **Nota:** Aunque este ejemplo podría parecer un poco simple, el objetivo principal del ejercicio era ilustrar el proceso de implementación de pruebas basadas en Azure Chaos Studio y sus componentes principales. Usaría el enfoque equivalente si una aplicación web de Azure App Service formara parte de un entorno más complejo, donde el impacto de su error podría ser mucho más difícil de predecir.

### Ejercicio 3: Eliminación de los recursos usados en los laboratorios

En este ejercicio, quitarás los recursos usados en los laboratorios.

1. En la pestaña explorador web que muestra Azure Portal, en el cuadro de texto de búsqueda de la parte superior de la página, escribe **`Resource groups`** y, en la lista de resultados, selecciona **Grupos de recursos**.
1. En la página **Grupos de recursos**, en la lista de grupos de recursos, seleccione el grupo de recursos que creó en el laboratorio actual.
1. En la página del grupo de recursos, seleccione **Eliminar grupo de recursos**.
1. En el cuadro de texto **Escribir el nombre del grupo de recursos para confirmar la eliminación**, escribe el nombre del grupo de recursos que vas a eliminar y, a continuación, selecciona **Eliminar**.

   > **Nota:** Espere a que se elimine el grupo de recursos. Debería tardar menos de un minuto.

1. Repita el paso anterior para el grupo de recursos que creó en los laboratorios anteriores de este curso.

    > **Nota:** Al eliminar el grupo de recursos, se quitarán todos los recursos asociados, incluido el perfil de Traffic Manager y el entorno de Azure Chaos Studio.

    > **Nota:** No es necesario eliminar la cuenta de GitHub y los repositorios que creó en los laboratorios anteriores de este curso. Puede seguir utilizándolos como muestrario de su trabajo.
