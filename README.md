# Herramientas de apoyo CD
# ***Instrucciones de configuración***
Septiembre 2019
# Contenido
[RECURSOS](#RECURSOS)

[REPOSITORIO EN GITHUB](#GITHUB)

[CREACIÓN DE PROYECTO EN AZUREDEVOPS](#AZUREDO)

[APROVISIONAMIENTO](#AZURE)

## **RECURSOS** <a name="RECURSOS"></a>

-Cuenta en GitHub.com (free)
-Suscripción en Microsoft Azure
-Suscripción en AzureDevops

## **REPOSITORIO EN GITHUB** <a name="GITHUB"></a>

Creación del repositorio en GitHub, seleccionando la opción desde la vista de repositorios en el perfil de la cuenta

![github profile](https://github.com/dgzamorah/mds/blob/master/pocecop/github%20profile.png)

>  *imagen - github profile*

Seleccionando la opción de visibilidad pública
![Github create](https://github.com/dgzamorah/mds/blob/master/pocecop/Github%20create.png)

> *imagen - Github create*
  
Se puede comprobar desde cualquier navegador
![Github created](https://github.com/dgzamorah/mds/blob/master/pocecop/Github%20create2.png)

> *imagen - Github created*

## **Creación de proyecto en AzureDevOps** <a name="AZUREDO"></a>

Desde la configuración de la organización en AzureDevOps
![Azure DevOps create](https://github.com/dgzamorah/mds/blob/master/pocecop/Azure%20DevOps%20create.png)

>*imagen - Azure DevOps create*
  
Verificando que se tengan los servicios requeridos, para el caso se trabajan principalmente los planes y los diferentes pipelines,
![Azure DevOps create, services](https://github.com/dgzamorah/mds/blob/master/pocecop/Azure%20DevOps%20create%2C%20services.png)

>*imagen - Azure DevOps create, services*
  
Luego se verifica la creación
![Azure DevOps created](https://github.com/dgzamorah/mds/blob/master/pocecop/Azure%20DevOps%20created.png)

>*imagen - Azure DevOps created*

En el proyecto, sección de **pipelines** para crear de construcción y despliegue

![Azure DevOps create pipeline](https://github.com/dgzamorah/mds/blob/master/pocecop/Azure%20DevOps%20create%20pipeline.png)

>*imagen - Azure DevOps create pipeline*

Selecciona la fuente del repositorio, GitHub y para conectarse por medio de **token**

![Azure DevOps select repo](https://github.com/dgzamorah/mds/blob/master/pocecop/Azure%20DevOps%20select%20repo.png)

>*imagen - Azure DevOps select repo*

Desde la cuenta en GitHub en la opción señalada se obtiene el **token** para conexión con AzureDevOps

![Github acces token](https://github.com/dgzamorah/mds/blob/master/pocecop/Github%20acces%20token.png)

>*imagen - Github acces token*

Selecciona el modelo de pipeline a configurar, maven para el caso mostrado

![Azure DevOps create, template](https://github.com/dgzamorah/mds/blob/master/pocecop/Azure%20DevOps%20create%2C%20template.png)

>*imagen - Azure DevOps create, template*

Se configuran los parámetros para la construcción

![Azure DevOps params](https://github.com/dgzamorah/mds/blob/master/pocecop/Azure%20DevOps%20params.png)

>*imagen - Azure DevOps params*

Ahora se habilita la ejecución de tarea para que sea disparada por cambios de los colaboradores en el repositorio origen y de acuerdo a la rama especificada

![Azure DevOps triggers](https://github.com/dgzamorah/mds/blob/master/pocecop/Azure%20DevOps%20triggers.png)

>*imagen - Azure DevOps triggers*

Se puede comprobar ahora el estado de la construcción, 

![Azure DevOps first build](https://github.com/dgzamorah/mds/blob/master/pocecop/Azure%20DevOps%20first%20build.png)
>*imagen - Azure DevOps first build*

Para tener una notificación en forma de etiqueta de color del último estado de ejecución, se obtiene un enlace desde la configuración del pipeline en AzureDevOps, como se señala

![Azure DevOps status badge](https://github.com/dgzamorah/mds/blob/master/pocecop/Azure%20DevOps%20status%20badge.png)

>*imagen - Azure DevOps status badge*

![Azure DevOps status badge MD link](https://github.com/dgzamorah/mds/blob/master/pocecop/Azure%20DevOps%20status%20badge%20MD%20link.png)

>*imagen - Azure DevOps status badge MD link*

Ahora este enlace se puede agregar a la documentación del proyecto, para que el colaborador que efectúa el cambio compruebe la construcción de forma automática desde Github

![Github Readme demo](https://github.com/dgzamorah/mds/blob/master/pocecop/Github%20Readme%20demo.png)

>*imagen - Github Readme demo*

## **APROVISIONAMIENTO** <a name="AZURE"></a>

Para el aprovisionamiento de la infraestructura necesaria como bases de datos y otros servicios se utilizan las capacidades del portal Azure, de forma automática se generan replicas por plantillas de ser necesario

![Azure create service](https://github.com/dgzamorah/mds/blob/master/pocecop/Azure%20create%20service.png)

>*imagen - Azure create service*

Como ejemplo se muestra la configuración con datos básicos para un servicio de base de datos a desplegar, también se puede crear la plantilla asociada

![Azure creating sample*](https://github.com/dgzamorah/mds/blob/master/pocecop/Azure%20creating%20sample.png)

>*imagen - Azure creating sample*

Luego de la creación se obtienen los datos necesarios para la conexión al servicio expuesto públicamente

![Azure created service URL](https://github.com/dgzamorah/mds/blob/master/pocecop/Azure%20created%20service%20URL.png)

>* imagen - Azure created service URL*

De manera automática se puede replicar el servicio requerido para cada fase de los desarrollos, en la siguiente imagen se muestran varios de los utilizados

![Azure provisioning]()

>*imagen - Azure provisioning*



