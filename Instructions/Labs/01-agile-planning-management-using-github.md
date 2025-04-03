---
lab:
  title: Planificación y administración ágiles mediante GitHub
  module: Plan with DevOps
---

# Laboratorio 01: Planificación y administración ágiles mediante GitHub

## Tiempo estimado: 40 minutos

## Escenario

Recuerda el escenario de este módulo en el que trabajas para una empresa de desarrollo de software del sector minorista que planea migrar su tienda en línea a una nueva aplicación, pero tiene dificultades para planificar el proyecto debido a la escasa colaboración y comunicación entre los equipos de desarrollo y operaciones. Dado que has decidido usar GitHub para la planificación y administración de Agile, este laboratorio te ofrece la oportunidad de crear un repositorio de GitHub, hitos y problemas asociados, un proyecto y un panel de proyectos. Además, podrás agregar un borrador al panel del proyecto y un elemento basado en un problema y revisar la configuración de automatización.

## Objetivos

En este laboratorio, aprenderás a:

- Creación de un repositorio, un proyecto y un panel de proyectos de GitHub
- Creación y administración de elementos del panel del proyecto

> **Nota:** para este y los laboratorios posteriores, necesitarás una cuenta de GitHub. Se recomienda encarecidamente crear una cuenta independiente para usarla en los laboratorios. Para crear una cuenta de GitHub, sigue las instrucciones del artículo [Creación de una cuenta en GitHub](https://docs.github.com/get-started/quickstart/creating-an-account-on-github).

## Ejercicio 1: Creación de un repositorio, un proyecto y un panel de proyectos de GitHub

En este ejercicio, crearás un repositorio, un proyecto y un panel de proyectos de GitHub.

> **Nota:** según la documentación de GitHub, Projects ofrece una *herramienta adaptable y flexible para planear y realizar el seguimiento del trabajo en GitHub*. Los proyectos proporcionan acceso a los *paneles*, que desempeñan el rol de los paneles *Kanban*. Kanban es un marco común y ampliamente utilizado en un entorno ágil para representar el estado del trabajo del proyecto.

El ejercicio consta de las tareas siguientes:

- Tarea 1: Creación de un repositorio de GitHub
- Tarea 2: Creación de hitos e incidencias de GitHub
- Tarea 3: Creación de un proyecto de GitHub
- Tarea 4: Creación de un panel de proyecto de GitHub

### Tarea 1: Creación de un repositorio de GitHub

1. Inicia un explorador web y ve a la página principal de [GitHub](https://github.com).
1. Cuando se te pida que te autentiques, inicia sesión con tu cuenta de usuario de GitHub.
1. En la página principal de GitHub, selecciona la pestaña **Repositorios** y, luego, selecciona **Nuevo**.
1. En la página **Crear un nuevo repositorio**, realiza las siguientes acciones:

   - En la lista desplegable **Propietario**, selecciona el nombre de la cuenta de usuario de GitHub.
   - En el cuadro de texto **Nombre del repositorio**, escribe **`DevOpsCoreIntroRepo`**.
   - Cambia la visibilidad del repositorio a **Privado**.
   - Activa la casilla **Agregar un archivo LÉAME**.
   - En la lista desplegable **Agregar .gitignore**, selecciona **Visual Studio**.

   > **Nota:** para obtener más información sobre .gitignore, consulta [Ignorar archivos](https://docs.github.com/get-started/getting-started-with-git/ignoring-files).

   - En la lista desplegable **Elegir una licencia**, selecciona **MIT**.

   > **Nota:** para obtener más información sobre las selecciones de licencias, consulta [Licencias de un repositorio](https://docs.github.com/repositories/managing-your-repositorys-settings-and-features/customizing-your-repository/licensing-a-repository).

1. Selecciona **Crear repositorio**.

### Tarea 2: Creación de hitos e incidencias de GitHub

1. En la página **DevOpsCoreIntroRepo**, selecciona la pestaña **Incidencias**.

   > **Nota:** crearás un hito y una incidencia asociados al repositorio recién creado que usarás posteriormente al trabajar con el panel del proyecto.

1. En la página **Incidencia**, a la izquierda del botón **Nueva incidencia**, selecciona **Hitos**.
1. En la página **Hitos**, selecciona **Nuevo hito**.
1. En la página **Nuevo hito**, realiza las siguientes acciones:

   - En el cuadro de texto **Título**, escribe **`alpha release`**.
   - En el cuadro de texto **Fecha de vencimiento (opcional)**, escribe la fecha una semana antes de la fecha actual.
   - En el cuadro de texto **Descripción**, escribe **`Completion of the alpha release`**.

1. Selecciona **Nuevo hito**.
1. Repite los tres últimos pasos para crear un hito de **versión beta** con la fecha de vencimiento dos semanas antes de la fecha actual. En el cuadro de texto **Descripción**, escribe **`Completion of the beta release`**.
1. Vuelva a la página **Incidencias** y seleccione **Nueva incidencia**.
1. En el cuadro de texto **Agregar un título**, escribe **`Repo README page is empty`**.
1. En el cuadro de texto **Agregar una descripción**, escribe **`Brevity might be a virtue, but this README page can really use some text`**.
1. Seleccione el icono de engranaje situado junto a la entrada **Hito** y, en la lista desplegable, seleccione **versión alfa**.
1. Seleccione el icono de engranaje situado junto a la entrada **Etiquetas** y, en la lista desplegable, seleccione **error**.
1. Seleccione **Crear**. Tenga en cuenta que la incidencia se ha asignado automáticamente **#1**.

### Tarea 3: Creación de un proyecto de GitHub

1. En la página principal de GitHub, seleccione el icono de avatar situado en el lado derecho de la barra de herramientas y, en el menú desplegable, seleccione **Sus proyectos**.
1. Seleccione **New project** (Nuevo proyecto).
1. En el panel **Seleccionar una plantilla**, seleccione **Planeamiento del equipo** y, a continuación, seleccione **Crear proyecto**.  

   > **Nota:** Como alternativa, puede empezar desde cero y mostrar el proyecto en la tabla, el panel o el formato de hoja de ruta.

1. En la página del nuevo proyecto, seleccione el nombre del proyecto generado automáticamente. Esto mostrará automáticamente la página **Configuración del proyecto**.
1. En el cuadro de texto **Nombre del proyecto**, escribe **`DevOps Core Intro Project`**.
1. En el cuadro de texto **Descripción breve**, escribe **`Introduction to GitHub Projects`** y selecciona **Guardar**.
1. En la sección **LÉAME**, escriba el texto siguiente

   > **Nota:** La sección **LÉAME** incluye un editor de Markdown simplificado que le ayuda a crear una página LÉAME visualmente atractiva para el proyecto. Puede usar los iconos de la barra de herramientas para dar formato al texto y usar la pestaña **Vista previa** para revisar los cambios resultantes. Copie y pegue el texto siguiente en la sección Editor LÉAME:

   ```text
   ### Welcome to DevOps Core Intro Project ###

   **Projects are a customizable, flexible tool for planning and tracking your work.**

   To find out more, refer to GitHub documentation [about Projects](https://docs.github.com/issues/planning-and-tracking-with-projects/learning-about-projects/about-projects).
   ```

1. Seleccione **Vista previa** para revisar la página resultante.
1. Seleccione **Guardar**.
1. Desplácese hacia abajo hasta la **Zona de peligro** y **observe** la opción que le permite cambiar la visibilidad del proyecto entre **Privado** y **Público**, cerrar el proyecto y eliminarlo.

   > **Nota:** No cierre ni elimine el proyecto en este momento. Solo tenga en cuenta las opciones disponibles para usted.

1. Desplácese hacia arriba hasta la parte superior de la página y tenga en cuenta que tiene las opciones para **Administrar el acceso** al proyecto y crear campos personalizados en la interfaz del proyecto.
1. Seleccione la flecha hacia la izquierda en la etiqueta **<- Configuración** para salir de la página **Configuración**.

### Tarea 4: Creación de un panel de proyecto de GitHub

1. En la página **Proyecto de introducción principal de DevOps**, seleccione el símbolo de intercalación orientado hacia abajo junto a la pestaña **Trabajo pendiente** y, en la sección **Diseño**, seleccione **Panel**.

   > **Nota:** Puede usar esta opción para cambiar fácilmente entre las vistas de tabla, panel y hoja de ruta.

1. Revise la página resultante que consta de tres columnas predefinidas etiquetadas:

   - **Tareas pendientes**: incluye los elementos que no se han iniciado
   - **En curso**: incluye los elementos en los que se trabaja activamente
   - **Listo**: incluye los elementos que se han completado

   > **Nota:** Este diseño representa un panel Kanban muy básico. Dentro de cada columna, puede agregar elementos individuales. También puede agregar columnas adicionales.

1. Para agregar una columna adicional, seleccione el icono **+** situado a la derecha de la columna **Listo** y, a continuación, seleccione **+ Nueva columna**.
1. En la ventana **Nueva opción**, en el cuadro de texto **Texto de etiqueta**, escribe **`Review In Progress`** y selecciona un color que quieras asignar a la columna. En el cuadro de texto **Descripción**, escribe **`This item is being reviewed`** y, después, selecciona **Guardar**.
1. Seleccione el círculo pequeño situado junto a la etiqueta **Revisión en curso** de la columna recién agregada y úselo para arrastrarla entre la columna **En curso** y **Listo**.

## Ejercicio 2: creación y administración de elementos del panel del proyecto

En este ejercicio, crearás y administrarás elementos del panel del proyecto

> **Nota:** Hay dos maneras básicas de agregar elementos a un panel de proyecto. Puede crear un borrador de elemento o agregar un elemento que represente una incidencia existente en un repositorio de GitHub.

El ejercicio consta de las tareas siguientes:

- Tarea 1: Adición de un elemento de borrador
- Tarea 2: Adición de un elemento basado en una incidencia
- Tarea 3: Revisión de la configuración de automatización

### Tarea 1: Adición de un elemento de borrador

1. En la página **Proyecto de introducción principal de DevOps**, en la columna **Tareas pendientes**, selecciona **+ Agregar elemento**.

   > **Nota:** En el cuadro de texto que se muestra automáticamente, puedes empezar a escribir para crear un borrador o escribe **#** para hacer referencia a un problema existente en cualquiera de los repositorios de GitHub. Comenzaremos con la primera de estas dos técnicas.

1. En el cuadro de texto, escribe **`Missing Wiki`** y, después, presiona **Entrar** en el teclado. Esto agregará un nuevo elemento de borrador a la columna **Tareas pendientes**.
1. En el elemento de borrador recién agregado, selecciona el símbolo de puntos suspensivos y, en el menú desplegable, selecciona **Convertir a incidencia**.
1. En la lista desplegable **Seleccionar un elemento**, selecciona **DevOpsCoreIntroRepo** para agregar el elemento al repositorio que creaste en el ejercicio anterior. Ten en cuenta que la incidencia se ha etiquetado automáticamente con **#2**.
1. Selecciona la incidencia **Falta wiki**.
1. En el panel **Falta wiki #2**, ten en cuenta que tienes otras opciones de configuración disponibles en este momento, incluidas las etiquetas y los hitos.
1. Selecciona **Agregar etiquetas** y, en la lista desplegable **Seleccionar elementos**, selecciona **mejora**.
1. Selecciona **Agregar hito** y, en la lista desplegable **Seleccionar un elemento**, selecciona **versión alfa**.
1. Cierra el panel **Falta wiki #2**.

   > **Nota:** ahora agregarás otro elemento de borrador y lo convertirás en una incidencia.

1. En la página **Proyecto de introducción principal de DevOps**, en la columna **Tareas pendientes**, selecciona **+ Agregar elemento**.
1. En el cuadro de texto, escribe **`Additional collaborators needed`** y presiona **CTRL + Entrar** en el teclado. Esto agregará un nuevo elemento de borrador a la columna **Tareas pendientes**.
1. En el elemento de borrador recién agregado, selecciona el símbolo de puntos suspensivos y, en el menú desplegable, selecciona **Convertir a incidencia**; a continuación, selecciona **DevOpsCoreIntroRepo** para agregar el elemento al respositorio.

### Tarea 2: Adición de un elemento basado en una incidencia

1. En la página **Proyecto de introducción principal de DevOps**, en la columna **Tareas pendientes**, selecciona **+ Agregar elemento**.
1. En el cuadro de texto, escribe **#** para mostrar una lista de repositorios existentes. En la lista, selecciona **DevOpsCoreIntroRepo**.
1. A continuación, en la lista de incidencias existentes, selecciona **La página LÉAME del repositorio está vacía #1**. Esto agregará automáticamente esa incidencia a la columna **Tareas pendientes**.
1. Selecciona **La página LÉAME del repositorio está vacía** para mostrar el panel correspondiente.
1. En el panel **La página LÉAME del repositorio está vacía**, en la sección Persona asignada, selecciona **Agregar persona asignada...**. En la lista desplegable **Seleccionar elementos**, selecciona el nombre de usuario de GitHub y cierra la página **La página LÉAME del repositorio está vacía**.
1. Arrastra el elemento **La página LÉAME del repositorio está vacía** desde la columna **Tareas pendientes** y colócala en la columna **En curso**.

### Tarea 3: Revisión de la configuración de automatización

1. En la página **Proyecto de introducción principal de DevOps**, directamente en el icono de avatar, selecciona el icono de puntos suspensivos.
1. En la lista desplegable, selecciona **Flujos de trabajo**.
1. En la página **Flujos de trabajo**, en el menú de la izquierda, revisa la lista de flujos de trabajo predeterminados. '

   > **Nota:** de manera predeterminada, los flujos de trabajo **Elemento cerrado** y **Solicitud de incorporación de cambios combinada** están activados.

1. Selecciona **Elemento cerrado** y revisa el flujo de trabajo. El flujo de trabajo establece automáticamente el estado de una incidencia en **Listo**, siempre que se cierre ese elemento.
1. Selecciona el icono de avatar en la esquina superior derecha de la página y, en el menú desplegable, selecciona **Sus repositorios**.
1. En la lista de repositorios, selecciona **DevOpsCoreIntroRepo**.
1. En la sección **LÉAME**, selecciona el icono de lápiz para abrir el archivo en el modo de edición.
1. Reemplaza el contenido existente por el texto siguiente:

   ```text
   ### Welcome to DevOps Core Intro Project Repository ###

   **Projects are a customizable, flexible tool for planning and tracking your work.**

   To find out more, refer to GitHub documentation [about Projects](https://docs.github.com/issues/planning-and-tracking-with-projects/learning-about-projects/about-projects).
   ```

1. Selecciona **Confirmar cambios**.
1. En la ventana **Confirmar cambios**, acepta la configuración predeterminada y selecciona **Confirmar cambios** de nuevo.
1. Selecciona la pestaña **Incidencias**.
1. Selecciona **La página LÉAME del repositorio está vacía**.
1. En **La página LÉAME del repositorio está vacía #1**, selecciona **Cerrar incidencia**.
1. Ten en cuenta que cerrar el elemento dio lugar a las siguientes acciones:

   - El estado del elemento se cambió automáticamente a **Listo**, como se indica en un comentario adicional que indica que el bot de **github-project-automation** movió el elemento de **En progreso** a **Listo** en el **Proyecto de introducción principal de DevOps**.
   - El hito de **versión alfa** se ha marcado como completo al 50 %, como se indica en la barra horizontal verde de la sección **Hito** de la página.

   > **Nota:** en caso de que no veas los cambios, actualiza la página.

1. Para comprobarlo, vuelve a la vista de panel del **Proyecto de introducción principal de DevOps** y observa que el elemento **La página LÉAME del repositorio está vacía** aparece en la columna **Listo**.
