---
lab:
  title: Implementación de CI/CD con Acciones de GitHub e IaC con Bicep
  module: Deliver with DevOps
---

# Laboratorio 03: implementación de CI/CD con Acciones de GitHub e IaC con Bicep

## Tiempo estimado: 40 minutos

## Escenario

Recuerde el escenario de este módulo, en el que trabaja para una empresa de desarrollo de software del sector minorista que quiere asegurarse de que el proceso de lanzamiento de una nueva versión de la aplicación de su tienda en línea es eficaz y fiable, a la vez que minimiza el riesgo de errores. Dado que ha decidido usar GitHub para facilitar la administración del ciclo de vida de las aplicaciones, este laboratorio le ofrece la oportunidad de bifurcar y revisar el repositorio de GitHub que contiene el código fuente de una aplicación web, un flujo de trabajo de Acciones de GitHub y una plantilla de Bicep. Además, podrá configurar el entorno de destino y validar la funcionalidad Infraestructura como código (IaC) y CI/CD.

## Objetivos

En este laboratorio, aprenderá a:

- Preparación de la suscripción de Azure para el laboratorio
- Implementación de Infraestructura como código (IaC) y CI/CD con Acciones de GitHub y una plantilla de Bicep

> **Nota:** En el laboratorio anterior, configuró GitHub Pages, lo que significa que ya ha implementado la implementación continua (posiblemente sin siquiera darse cuenta). GitHub Pages usa (en segundo plano) Acciones de GitHub para realizar la implementación automatizada después de las confirmaciones en la rama principal. Para comprobarlo, vaya a la pestaña **Acciones** de la página principal del repositorio bifurcado, **Spoon-Knife**. Ahora mejorará esta funcionalidad. En concreto, usará un flujo de trabajo de Acciones de GitHub desarrollado a medida con una plantilla de Bicep para aprovisionar un par de aplicaciones web de Azure App Service en diferentes regiones de Azure e implementar una aplicación web .NET personalizada en ambas.

> **Nota:** Para este y los laboratorios posteriores, use la misma cuenta de GitHub que creó para el primer laboratorio.

## Requisitos previos

- [Laboratorio 01: planificación y gestión ágiles mediante GitHub](01-agile-planning-management-using-github.md) completado
- [Laboratorio 02: implementación del flujo de trabajo con GitHub](02-implement-manage-repositories-using-github.md) completado
- Una suscripción de Azure en la que tenga al menos el acceso de nivel de colaborador. Si no tiene ninguna suscripción de Azure, puede [registrarse para obtener una evaluación gratuita](https://azure.microsoft.com/free).

## Ejercicio 0: preparación de la suscripción de Azure para el laboratorio

> **Nota:** Si usa una nueva suscripción de Azure, es posible que algunos de los proveedores de recursos de Azure no estén registrados. Dado que el proceso de registro puede tardar unos minutos, para minimizar el tiempo de espera, los registrará al principio de este laboratorio.

> **Nota:** En este laboratorio, usará Azure Cloud Shell. Si es la primera vez que usa Azure Cloud Shell en su suscripción, deberá registrar el proveedor de recursos correspondiente. 

1. Inicie un explorador web y vaya a Azure Portal en `https://portal.azure.com`.
1. Si se le solicita, inicie sesión con su cuenta de Microsoft Entra ID con el acceso de propietario a la suscripción de Azure disponible.
1. En la pestaña explorador web que muestra Azure Portal, en el cuadro de texto de búsqueda de la parte superior de la página, escribe **`Subscriptions`** y, en la lista de resultados, selecciona **Suscripciones**.
1. En la página **Suscripciones**, seleccione la suscripción que desea usar en este laboratorio.
1. En la página suscripciones, en el menú vertical del lado izquierdo, seleccione **Proveedores de recursos**.
1. En la lista de proveedores de recursos, busque y seleccione **Microsoft.CloudShell**.
1. Con el proveedor de recursos **Microsoft.CloudShell** seleccionado, en la barra de herramientas, seleccione **Registrar**.

   > **Nota:** No espere hasta que se complete el registro, continúe directamente con el ejercicio siguiente. El registro puede tardar un par de minutos.

## Ejercicio 1: implementación de CI/CD con Acciones de GitHub e IaC con Bicep

En este ejercicio, implementarás IaC con CI/CD en aplicaciones web de Azure App Service con Acciones de GitHub y una plantilla de Bicep.

> **Nota:** Para simplificar la configuración inicial, usará una bifurcación de un repositorio de GitHub existente que contiene el código fuente de la aplicación .NET, el flujo de trabajo de Acciones de GitHub y la plantilla de Bicep.

El ejercicio consta de las tareas siguientes:

- Tarea 1: Bifurcación y revisión del repositorio de GitHub que contiene el código fuente de una aplicación web, un flujo de trabajo de Acciones de GitHub y una plantilla de Bicep
- Tarea 2: Configurar el entorno de destino
- Tarea 3: Validación de la funcionalidad IaC y CI/CD

### Tarea 1: Bifurcación y revisión del repositorio de GitHub que contiene el código fuente de una aplicación web, un flujo de trabajo de Acciones de GitHub y una plantilla de Bicep

1. Cambia a la ventana del explorador que muestra la cuenta de GitHub y asegúrate de que todavía estás autenticado (si no es así, inicia sesión con tu cuenta de usuario de GitHub).
1. Abra otra pestaña en la misma ventana del explorador y vaya al repositorio de [eShopOnWeb](https://github.com/MicrosoftLearning/eShopOnWeb), que hospeda la versión de .NET de un sitio web de ejemplo, un flujo de trabajo de Acciones de GitHub y una plantilla de Bicep.
1. En la página del repositorio de **eShopOnWeb**, seleccione **Bifurcación**.
1. En la página **Crear una nueva bifurcación**, asegúrese de que la entrada de lista desplegable **Propietario** muestra el nombre de usuario de GitHub, acepte la entrada predeterminada **eShopOnWeb** en el cuadro de texto **Nombre del repositorio** y confirme que **Copia solo de la rama principal** está activado, y, a continuación, seleccione **Crear bifurcación**.

   > **Nota:** La sesión del explorador se redirigirá automáticamente al repositorio recién bifurcado.

1. En la página **eShopOnWeb**, confirme que la lista desplegable muestra la rama actual **principal**.
1. Revise la estructura del repositorio y tenga en cuenta que incluye los siguientes componentes:

   - el directorio **.github/workflows** que contiene varios flujos de trabajo basados en YAML, incluido uno denominado **eshoponweb-cicd.yml**
   - directorio **infra** que contiene la plantilla de Bicep denominada **webapp.bicep**
   - directorio **src/Web** que contiene el código .NET de la aplicación web de origen

1. Abra el directorio **.github/workflows** y seleccione el archivo **eshoponweb-cicd.yml** para ver su contenido. Ten en cuenta que este flujo de trabajo de Acciones de GitHub contiene los trabajos **buildandtest** y **deploy**, que constan de los siguientes pasos:

   - buildandtest
      - comprobar el repositorio
      - preparar el ejecutor para el SDK de versión de .NET deseado
      - compilar, probar y publicar el proyecto de .NET
      - cargar los artefactos de código del sitio web publicados
      - cargar la plantilla de bicep como un artefacto para el siguiente trabajo
   - deploy
      - descargar los artefactos publicados creados en el trabajo anterior
      - descargar la plantilla de bicep cargada en el trabajo anterior
      - iniciar sesión en la suscripción de Azure de destino mediante una entidad de servicio
      - implementar una aplicación web de Azure App Service mediante la plantilla de bicep
      - publicar los artefactos del sitio web en la aplicación web de Azure App Service

   > **Nota:** No se centre en los detalles de cada trabajo. Es mucho más importante comprender simplemente el propósito del flujo de trabajo.

1. Tenga en cuenta que el proceso de aprovisionamiento de recursos de Azure tiene como destino un único grupo de recursos que se define al principio del flujo de trabajo como una variable de entorno `RESOURCE-GROUP`.

   > **Nota:** Deberá crear el grupo de recursos antes de ejecutar el flujo de trabajo.

1. Ten en cuenta que el flujo de trabajo se basa en un secreto para autenticarse en la suscripción a Azure de destino durante el paso Configurar la CLI de Azure, como se muestra en la instrucción `creds: ${{ secrets.AZURE_CREDENTIALS }}`.

   > **Nota:** Tendrá que configurar este secreto antes de ejecutar el flujo de trabajo.

1. Además, tenga en cuenta que este flujo de trabajo se puede configurar para que se desencadene manualmente, como se indica en la sintaxis `on: workflow_dispatch:`.

### Tarea 2: Configurar el entorno de destino

> **Nota:** Para empezar, creará los grupos de recursos. Ejecutará el flujo de trabajo dos veces para implementar dos instancias del sitio web en dos regiones de Azure diferentes.

1. Vaya a la pestaña del explorador web en la que aparece Azure Portal en `https://portal.azure.com`.
1. En Azure Portal, en el cuadro de texto de búsqueda de la parte superior de la página, escribe **`Resource groups`** y selecciona **Grupos de recursos** en la lista de resultados.
1. En la página **Grupos de recursos**, seleccione **+ Crear**.
1. En el cuadro de texto **Grupos de recursos**, escribe **`rg-eshoponweb-westeurope`**.
1. En la lista desplegable **Región**, seleccione **(Europa) Oeste de Europa**.
1. Seleccione **Revisar y crear** y, después, en **Revisar y crear**, seleccione **Crear**.
1. En la página **Grupos de recursos**, seleccione **+ Crear**.
1. En el cuadro de texto **Grupos de recursos**, escribe **`rg-eshoponweb-eastus`**.
1. En la lista desplegable **Región**, seleccione **(EE. UU.) Este de EE. UU.**.
1. Seleccione **Revisar y crear** y, después, en **Revisar y crear**, seleccione **Crear**.

    > **Nota:** A continuación, creará una entidad de servicio que se usará para autenticarse desde el flujo de trabajo de Acciones de GitHub en la suscripción de Azure de destino y le asignará el rol Colaborador en la suscripción.

1. En Azure Portal, seleccione el icono **Cloud Shell** a la derecha del cuadro de texto de búsqueda.
1. Si es necesario, seleccione **Bash** en el menú desplegable de la esquina superior izquierda del panel de Cloud Shell.
1. En la sesión de Bash en el panel de Cloud Shell, ejecute el siguiente comando para almacenar el valor del identificador de suscripción de Azure en una variable:

   ```cli
   SUBSCRIPTION_ID=$(az account show --query id --output tsv) 
   echo $SUBSCRIPTION_ID
   ```

1. Copie el valor del identificador de suscripción devuelto por el segundo comando y regístrelo. La necesitará más adelante en este ejercicio.
1. En la sesión de Bash en el panel de Cloud Shell, ejecute el siguiente comando para crear una entidad de servicio de Microsoft Entra ID y asignarle el rol colaborador en el ámbito de la suscripción:

   ```cli
   az ad sp create-for-rbac --name "devopsfoundationslabsp" --role contributor --scopes /subscriptions/$SUBSCRIPTION_ID --json-auth
   ```

1. Copie toda la salida con formato JSON del comando y regístrela. La necesitará en breve. La salida debe tener el formato que se asemeja al siguiente texto:

   ```json
   {
     "clientId": "cccccccc-cccc-cccc-cccc-cccccccccccc",
     "clientSecret": "xxxxx~xxxxxxxxxxxx-xxxxxxxxxxxxx_xxxxxxx",
     "subscriptionId": "yyyyyyyy-yyyy-yyyy-yyyy-yyyyyyyyyyyy",
     "tenantId": "zzzzzzzz-zzzz-zzzz-zzzz-zzzzzzzzzzzz",
     "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
     "resourceManagerEndpointUrl": "https://management.azure.com/",
     "activeDirectoryGraphResourceId": "https://graph.windows.net/",
     "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
     "galleryEndpointUrl": "https://gallery.azure.com/",
     "managementEndpointUrl": "https://management.core.windows.net/"
   }
   ```

1. Cambie a la ventana del explorador web que muestra la página del repositorio de GitHub de **eShopOnWeb** bifurcado y, en la barra de herramientas, seleccione **Configuración**.
1. En el menú vertical de la izquierda, en la sección **Seguridad**, seleccione **Secretos y variables** y, en la lista desplegable, seleccione **Acciones**.
1. En el panel **Secretos y variables de las acciones**, seleccione **Nuevo secreto de repositorio**.
1. En el panel **Secretos de acciones / Nuevo Secreto**, en el cuadro de texto **Nombre**, escribe **`AZURE_CREDENTIALS`**.
1. En el cuadro de texto **Secreto**, pegue el texto con formato JSON que registró anteriormente en esta tarea y seleccione **Agregar secreto**.
1. Vuelve al explorador web que muestra la sesión de Bash en el panel de Cloud Shell y ejecuta el siguiente comando para generar el nombre de la primera App Service Web Apps que vas a implementar:

   ```cli
   echo devops-webapp-westeurope-$RANDOM$RANDOM
   ```

1. Copie el valor devuelto por el comando y anote el valor. Lo usarás más adelante en este ejercicio.
1. En la sesión de Bash en el panel de Cloud Shell, ejecuta el siguiente comando para generar el nombre de la segunda App Service Web Apps que vas a implementar:

   ```cli
   echo devops-webapp-eastus-$RANDOM$RANDOM
   ```

1. Copie el valor devuelto por el comando y anote el valor. Lo usarás más adelante en este ejercicio.

### Tarea 3: Validación de la funcionalidad IaC y CI/CD

1. Cambie a la ventana del explorador web que muestra la página del repositorio de GitHub **eShopOnWeb** bifurcada y, si es necesario, en la página **eShopOnWeb**, haga clic en la pestaña **Código** y, en la lista desplegable, confirme que la rama actual es la **principal**.
1. En la ventana del navegador web que muestra la rama **principal** de la página del repositorio de GitHub **eShopOnWeb**, vaya a la carpeta **.github/workflows** y seleccione **eshoponweb-cicd.yml**.
1. En el panel **.github/workflows/eshoponweb-cicd.yml**, seleccione el icono de lápiz para editar el flujo de trabajo.
1. En el panel **Editar**, reemplace la línea 4 por el siguiente texto:

   ```yaml
   on: workflow_dispatch
   ```

    >**Nota**: Asegúrese de usar la sangría adecuada.

1. En el panel **Editar**, reemplace la línea 8 por el siguiente texto:

   ```yaml
    RESOURCE-GROUP: rg-eshoponweb-westeurope
   ```

1. En el panel **Editar**, reemplace el marcador de posición `YOUR-SUBS-ID` en la línea 11 por el valor del identificador de suscripción de Azure que registró anteriormente en este ejercicio:
1. En el panel **Editar**, reemplace el marcador de posición `eshoponweb-webapp-NAME` en la línea 11 por el nombre de la **primera** aplicación web de Azure App Service que generó anteriormente en este ejercicio.
1. En el panel **.github/workflows/eshoponweb-cicd.yml**, seleccione **Confirmar cambios** y, a continuación, vuelva a seleccionar **Confirmar cambios**.
1. En la ventana del navegador web que muestra la rama **principal** de la página del repositorio de GitHub **eShopOnWeb**, vaya a la carpeta **infra** y seleccione **webapp.bicep**.
1. En el panel **infra/webapp.bicep**, seleccione el icono de lápiz para editar el flujo de trabajo.
1. En el panel **Editar**, reemplace la línea 2 por el siguiente texto:

   ```yaml
   param sku string = 'S1' // The SKU of App Service Plan
   ```

1. En el panel **infra/webapp.bicep**, seleccione **Confirmar cambios** y, a continuación, vuelva a seleccionar **Confirmar cambios**.
1. En la ventana del explorador web que muestra la página del repositorio de GitHub de **eShopOnWeb** bifurcado, seleccione **Acciones**.
1. Si se te pide que habilites Acciones de GitHub, selecciona **Entiendo mis flujos de trabajo, continúe y habilítelos**.
    >**Nota**: Esto es de esperar, ya que, por defecto GitHub desactivará los flujos de trabajo en un repositorio bifurcado para su propia protección.
1. En la sección **Todos los flujos de trabajo** del lado izquierdo, seleccione **Compilación y prueba de eShopOnWeb**.
1. En el panel **Compilación y prueba de eShopOnWeb**, seleccione **Ejecutar flujo de trabajo**, en la lista desplegable confirme que **Rama: principal** está seleccionada y seleccione **Ejecutar flujo de trabajo** de nuevo.

    >**Nota**: Esto debe desencadenar una ejecución de flujo de trabajo.

1. Seleccione la ejecución del flujo de trabajo **Compilación y prueba de eShopOnWeb**.

    >**Nota**: Si es necesario, actualice la página para ver la ejecución del flujo de trabajo más reciente.

1. En el panel **Compilación y prueba de eShopOnWeb número 1**, seleccione **buildandtest**.
1. Supervise el progreso del flujo de trabajo y compruebe que todos los pasos del trabajo **buildandtest** se completen correctamente.
1. Una vez completado este trabajo, en la sección **Resumen**, seleccione **implementar**.
1. Supervise el progreso del flujo de trabajo y compruebe que todos los pasos del trabajo **deploy** se completen correctamente.

    >**Nota**: En caso de que se produzca un error en cualquiera de los pasos, en la misma página que muestra el progreso del flujo de trabajo, en la esquina superior derecha, seleccione **Volver a ejecutar todos los trabajos** y, a continuación, en el panel **Volver a ejecutar todos los trabajos**, seleccione **Volver a ejecutar trabajos**.

1. Cambie a la ventana del explorador web que muestra la página del repositorio de GitHub **eShopOnWeb** bifurcada y, si es necesario, en la página **eShopOnWeb**, en la lista desplegable, confirme que la rama actual es la **principal**.
1. En la ventana del navegador web que muestra la rama **principal** de la página del repositorio de GitHub **eShopOnWeb**, vaya a la carpeta **.github/workflows** y seleccione **eshoponweb-cicd.yml**.
1. En el panel **.github/workflows/eshoponweb-cicd.yml**, seleccione el icono de lápiz para editar el flujo de trabajo.
1. En el panel **Editar**, reemplace la línea 8 por el siguiente texto:

   ```yaml
    RESOURCE-GROUP: rg-eshoponweb-eastus
   ```

1. En el panel **Editar**, reemplace el marcador de posición `eshoponweb-webapp-NAME` en la línea 11 por el nombre de la **segunda** aplicación web de Azure App Service que generó anteriormente en este ejercicio.
1. En el panel **.github/workflows/eshoponweb-cicd.yml**, seleccione **Confirmar cambios** y, a continuación, vuelva a seleccionar **Confirmar cambios**.
1. En la ventana del explorador web que muestra la página del repositorio de GitHub de **eShopOnWeb** bifurcado, seleccione **Acciones**.
1. En la sección **Todos los flujos de trabajo** del lado izquierdo, seleccione **Compilación y prueba de eShopOnWeb**.
1. En el panel **Compilación y prueba de eShopOnWeb**, seleccione **Ejecutar flujo de trabajo**, en la lista desplegable confirme que **Rama: principal** está seleccionada y seleccione **Ejecutar flujo de trabajo** de nuevo.

    >**Nota**: Esto debe desencadenar una ejecución de flujo de trabajo.

1. Seleccione la ejecución del flujo de trabajo **Compilación y prueba de eShopOnWeb**.
1. En el panel **Compilación y prueba de eShopOnWeb número 2**, seleccione **buildandtest**.
1. Supervise el progreso del flujo de trabajo y compruebe que todos los pasos del trabajo **buildandtest** se completen correctamente.
1. Una vez completado este trabajo, en la sección **Resumen**, seleccione **implementar**.
1. Supervise el progreso del flujo de trabajo y compruebe que todos los pasos del trabajo **deploy** se completen correctamente.

    >**Nota**: En caso de que se produzca un error en cualquiera de los pasos, en la misma página que muestra el progreso del flujo de trabajo, en la esquina superior derecha, seleccione **Volver a ejecutar todos los trabajos** y, a continuación, en el panel **Volver a ejecutar todos los trabajos**, seleccione **Volver a ejecutar trabajos**.

1. En la ventana del explorador web que muestra Azure Portal, en el cuadro de texto de búsqueda de la parte superior de la página, escribe **`App Services`** y selecciona **App Services** en la lista de resultados.
1. En la página **App Services**, en la lista de App Services, seleccione la app service **devops-webapp-westeurope-** que creó anteriormente en este ejercicio.
1. En la página **devops-webapp-westeurope-**, en la sección **Essentials**, compruebe que se muestra el valor de **dominio predeterminado** y selecciónelo para abrir la aplicación web en una nueva pestaña del explorador.
1. En la nueva pestaña del explorador, comprueba que se muestra la aplicación web y que es funcional. También puede comprobar la segunda aplicación web en la región **Este de EE. UU.** de la misma manera.
    >**Nota**: Deje los recursos de Azure implementados en ejecución. Los necesitará en el siguiente laboratorio.
