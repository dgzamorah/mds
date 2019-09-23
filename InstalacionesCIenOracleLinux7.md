# ***Instrucciones de instalación herramientas integración***
Septiembre 2019
# Contenido
[Requisitos](#requisitos)

[INSTALACIONES](#instalaciones)

  1. [Jenkins](#jenkinstall)
  2. [Docker](#dockerinstall)
  3. [Sonarqube y postgres](#sonardbinstall)

[CONFIGURACIONES](#CONFIGURACIONES)

  1. [Jenkins y conexiones](#jenkinsconf)

[RESPALDOS](#RESPALDOS)

  1. [Jenkins](#jenkinsbk)
  2. [Sonarqube](#sonarbk)

[DIAGNÓSTICO - COMANDOS](#DIAGNÓSTICO)

  1. [Jenkins](#jenkinsdiag)
  2. [Docker, Sonar y Postgres](#sonardiag)


___

## **Requisitos** <a name="requisitos"></a>

Esta sección describe características aprovisionadas previamente en el servidor donde se instalaron las herramientas de las que trata este instructivo.

| Características ||
|--------|-----------|
|Servidor virtual|**x.x.x.x**|
|S.O|Oracle Linux 7.6|
|CPU Y RAM|--|
|Software|Weblogic|
|        |Maven|
|        |NodeJs|
|        |Git|
|Herramientas Linux|wget|
|                  |nano|

También hay otro servidor, con otra de las herramientas ya instaladas, **Gitlab**
## **INSTALACIONES** <a name="instalaciones"></a>
### **Jenkins** <a name="jenkinstall"></a>
+ https://wiki.jenkins.io/display/JENKINS/Installing+Jenkins+on+Red+Hat+distributions

Según la documentación, se realiza la instalación de los componentes Java necesarios, se agrega el repositorio correspondiente para instalación:
```
$ sudo yum install java-1.8.0-openjdk
$ sudo wget -O /etc/yum.repos.d/jenkins.repo http://pkg.jenkins-ci.org/redhat-stable/jenkins.repo
$ sudo rpm --import https://jenkins-ci.org/redhat/jenkins-ci.org.key
$ sudo yum install jenkins
```
Para el inicio automático del servicio con el SO:
```
$ sudo systemctl enable jenkins
```
>Puede que haya que revisar el firewall por si algo falla
```
$ sudo firewall-cmd --list-all
```
>dado caso, los comandos sugeridos son:

>(no importa si alguno de estos comandos presenta conflicto por configuraciones ya hechas con la instalación de Jenkins)

```
$ sudo firewall-cmd --permanent --new-service=jenkins
$ sudo firewall-cmd --permanent --service=jenkins --set-short="Jenkins Service Ports"
$ sudo firewall-cmd --permanent --service=jenkins --set-description="Jenkins service firewalld port exceptions"
$ sudo firewall-cmd --permanent --service=jenkins --add-port=8080/tcp
$ sudo firewall-cmd --permanent --add-service=jenkins
$ sudo firewall-cmd --zone=public --add-service=http --permanent
$ sudo firewall-cmd --reload
```
Finalmente, para iniciar el programa
```
$ sudo systemctl start jenkins
```
Debe verse en navegador web:
+ **`http://x.x.x.x:8080/`**

Ahora seguir los pasos de instalación inicial:

![imagen muestra inicio Jenkins](https://microsoft.github.io/PartsUnlimitedMRP/assets/azurestack/initial_jenkins_unlock.png)

Seleccionar los plugins sugeridos:

![imagen muestra startup jenkins](https://www.hostingdesire.com/learning/jenkins/customize-jenkins.jpeg)

### **Docker** <a name="dockerinstall"></a>
Comandos para la instalación:
```
$ cd /etc/yum.repos.d/
$ wget http://yum.oracle.com/public-yum-ol7.repo
```
Este archivo debe modificarse, se buscan los campos mostrados y cambiar los valores a **enabled=1**
```
$ nano public-yum-ol7.repo
[ol7_latest]
name=Oracle Linux $releasever Latest ($basearch)
baseurl=http://yum.oracle.com/repo/OracleLinux/OL7/latest/$basearch/
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-oracle
gpgcheck=1
enabled=1

[ol7_UEKR4]
name=Latest Unbreakable Enterprise Kernel Release 4 for Oracle Linux $releasever ($basearch)
baseurl=http://yum.oracle.com/repo/OracleLinux/OL7/UEKR4/$basearch/
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-oracle
gpgcheck=1
enabled=1

[ol7_addons]
name=Oracle Linux $releasever Add ons ($basearch)
baseurl=http://yum.oracle.com/repo/OracleLinux/OL7/addons/$basearch/
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-oracle
gpgcheck=1
enabled=1
```
Luego continua la instalación y se habilita el servicio para inicio con el SO
```
$ sudo yum install docker-engine
$ sudo systemctl enable docker
$ sudo systemctl start docker
```
Docker compose,
```
$ sudo curl -L "https://github.com/docker/compose/releases/download/1.24.1/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
$ sudo chmod +x /usr/local/bin/docker-compose
```
verifica la instalación:
```
$ docker-compose --version
```
Un ajuste más para que pueda funcionar docker en este SO, se debe modificar el siguiente archivo y cambiando el parámetro **SELINUX=disabled**:
```
$ nano /etc/selinux/config
# This file controls the state of SELinux on the system.
# SELINUX= can take one of these three values:
#     enforcing - SELinux security policy is enforced.
#     permissive - SELinux prints warnings instead of enforcing.
#     disabled - No SELinux policy is loaded.
SELINUX=disabled
# SELINUXTYPE= can take one of three two values:
#     targeted - Targeted processes are protected,
#     minimum - Modification of targeted policy. Only selected processes are protected.
#     mls - Multi Level Security protection.
SELINUXTYPE=targeted
```

#### **Sonar y Postgresql** <a name="sonardbinstall"></a>
Para poner en marcha los contenedores se puede hacer de varias formas. En este caso se hará por **docker-compose**, que consiste en un archivo el cual se ejecuta tipo script y este hace que docker ponga en marcha los contenedores con la configuración que se describe dentro de este.
1. se crea un archivo en cualquier directorio y debe llamarse docker-compose.yml
```
$ touch docker-compose.yml
```
2. copiar el contenido al archivo, con un editor como nano o vim (si hay inconvenientes, debería haber adjunta a este documento una copia del archivo)

```
version: "2"

services:
  sonarqube:
    hostname: sonarqube
    image: sonarqube:lts
    ports:
      - "9000:9000"
    networks:
      - sonarnet
    environment:
      - sonar.jdbc.url=jdbc:postgresql://sonardb:5432/sonar
    restart: always
    volumes:
      - sonarqube_conf:/opt/sonarqube/conf
      - sonarqube_data:/opt/sonarqube/data
      - sonarqube_extensions:/opt/sonarqube/extensions

  sonardb:
    hostname: sonardb
    image: postgres:9.6.14
    ports:
      - "5432:5432"
    networks:
      - sonarnet
    environment:
      - POSTGRES_USER=sonar
      - POSTGRES_PASSWORD=sonar
    restart: always
    volumes:
      - postgresql:/var/lib/postgresql
      - postgresql_data:/var/lib/postgresql/data

networks:
  sonarnet:
    driver: bridge

volumes:
  sonarqube_conf:
  sonarqube_data:
  sonarqube_extensions:
  postgresql:
  postgresql_data:
```
3. desde la ubicación donde se creo el archivo se ejecuta:
```
$ docker-compose up -d
```
4. verifica la correcta ejecución con:
```
$ docker ps -a
```
>(5). en caso de error 
```
$ docker logs NOMBRE_CONTENEDOR
```
>si es error por MAX VIRTUAL MEMORY, se debe modificar un archivo del sistema:
```
$ nano /etc/sysctl.conf
```
>agregar al archivo esta línea: 
```
vm.max_map_count = 262144
```
>ejecutar este comando para recargar esas configuraciones
```
$ sudo sysctl -p
```
>reinicia el contenedor
```
$ docker restart NOMBRE_CONTENEDOR_SONAR
```

### Jenkins

Se crea el volumen correspondiente:
`$ docker volume create jenkinsv`

El comando para ejecutarlo por contenedor
```
$ docker run \
   --detach \
   --name miojenkins -p 8080:8080 -p 50000:50000 \
   -u root \
   -v jenkinsv:/var/jenkins_home\
   -v /var/run/docker.sock:/var/run/docker.sock \
   -v "$HOME":/home \
   jenkinsci/blueocean
```
hay que verificar esta propiedad 

`$ chown -R 1000:1000 /var/lib/docker/volumes/jenkinsv`


#### Gitlab
Crear los volúmenes
```
$ docker volume create gitlabdata
$ docker volume create gitlablogs
$ docker volume create gitlabconfig
```
Verificando la versión a la fecha
`$ docker pull gitlab/gitlab-ce:12.1.11-ce.0`

Y ejecuta el comando para iniciar el contenedor
```
$ docker run --detach   --publish 443:443 --publish 80:80 \
--publish 220:22   --name miogitlab \
--volume gitlabconfig:/etc/gitlab \
--volume gitlablogs:/var/log/gitlab \
--volume gitlabdata:/var/opt/gitlab \
gitlab/gitlab-ce:12.1.11-ce.0
```
```diff
- Falta agregar enlaces y configuración para contenedores Gitlab y Jenkins
```

Ahora debería poder acceder a las herramientas por:
+ **`http://x.x.x.x:8080/`**
+ **`http://x.x.x.x:9000/`**

Se deben seguir los pasos de configuración inicial para Sonarqube

Por defecto

| | |
|------|------|
|user :| admin|
|pass :| admin|

![imagen muestra startup Sonarqube](https://miro.medium.com/max/1838/1*frCVcWbMBIlYUxb_xMxWAw.jpeg)

## **CONFIGURACIONES** <a name="CONFIGURACIONES"></a>

Luego de la configuración inicial, se deben conectar los programas **Jenkins - Sonar** y también **Jenkins - Gitlab**, para ello procedemos a la consola web de cada usuario administrador en **Sonar** y **Gitlab** para tomar las claves de los 'Token' y luego configurar con esos datos en Jenkins

**Sonar** - Admin user>Security>Generate Token
+ **`http://vx.x.x.x:9000/account/security/`**

![imagen muestra de sonarqube](https://www.decodingdevops.com/wp-content/uploads/2019/03/token-sonarqube.png)

**Gitlab** - Admin user>profile settings> Access Token

Seleccionando el alcance (scope) pertinente, se requiere permiso de lectura para los repos. de interés.
+ **`http://x.x.x.x/profile/personal_access_tokens/`**

![imagen muestra de Gitlab](https://i.stack.imgur.com/6FR26.png)

### **Jenkins** <a name="jenkinsconf"></a>
Antes de continuar, se deberá verificar que se tengan los plugins necesarios para las conexiones con las otras aplicaciones y capacidades de Jenkins, si es que no se hizo en la parte de la configuración inicial con las recomendadas.
algunos de los plugins son:
>+ authentication-tokens
>+ credentials
>+ database
>+ gitlab, git
>+ nodejs
>+ sonar

Todo esto se encuentra en esta parte:

![configure system jenkins imagen](https://issues.jenkins-ci.org/secure/attachment/32435/jenkins2-no-gears-oops.png)

Para conectar Jenkins con las aplicaciones Gitlab y Sonar por medio de API Token, **Configure System**:
+ **`http://x.x.x.x:8080/configure`**

También se debe configurar **Global Tool Configuration** en 
+ **`http://x.x.x.x:8080/configureTools/`**

En este último se configuran las herramientas de compilación y demás que se necesiten para el funcionamiento del pipeline (maven, NodeJS, Git)
En el mismo menú 'Global Tool Configuration':

**GIT - Instalaciones**
- [ ]se coloca la ruta para la aplicación

**`/usr/bin/git`**

en caso que no se tenga debe validarse la instalación de Git en el S.O:
```
yum install git
```
**JDK - Instalaciones**
- [ ]se coloca la ruta en JAVA_HOME:

 **`/usr/lib/jvm/java-1.8.0-openjdk-1.8.0.222.b10-0.el7_6.x86_64/`**

acá se pueden poner varias versiones dependiendo la necesidad, para cada caso se debe validar esa instalación de JDK

En el SO, para la instalación del JDK 8 por ejemplo:
```
$ sudo yum install java-1.8.0-openjdk-devel
```
**Maven - Instalaciones**

- [ ]Puede especificar la ruta de instalación
**`/usr/local/src/apache-maven`**
- [ ]ó instalar automáticamente, desde apache ver. 3.6.0
En el SO, se verifican las instalaciones:
```
$ cd /usr/local/src
$ wget  http://www-us.apache.org/dist/maven/maven-3/3.6.2/binaries/apache-maven-3.6.2-bin.tar.gz
$ tar -xf apache-maven-3.6.2-bin.tar.gz
$ mv apache-maven-3.6.2/ apache-maven/
$ cd /etc/profile.d/
$ nano maven.sh
# Que el archivo tenga este contenido:
# Apache Maven Environment Variables
# MAVEN_HOME for Maven 1 - M2_HOME for Maven 2
export M2_HOME=/usr/local/src/apache-maven
export PATH=${M2_HOME}/bin:${PATH}
$ chmod +x maven.sh
$ source /etc/profile.d/maven.sh
$ mvn --version
```
**SonarQube Scanner - Instalaciones**
- [ ] instalar automáticamente, 'from maven central' en la última versión

se puede realizar siguiendo las indicaciones en: https://docs.sonarqube.org/latest/analysis/scan/sonarscanner-for-jenkins/
allí se encuentran indicaciones detalladas dependiendo el tipo de proyecto

**NodeJS**
En esta parte debe verificarse la configuración con los comandos según el caso del proyecto a construir, esto es un ejemplo:
- [ ]Global npm packages to install **@angular/cli@latest typescript**
Instalaciones en SO, si lo requiriera:
```
yum install nodejs
```

## **RESPALDOS** <a name="RESPALDOS"></a>

### **Jenkins** <a name="jenkinsbk"></a>

Para este caso se recomienda realizar copia del directorio raíz para jenkins (HOME), el cual puede ser:
```
/var/lib/jenkins
```
Esta copia se puede hacer con el servicio activo, lo único es al restaurar debería estar abajo mientras se reemplaza todo.
También se deben tener en cuenta los detalles del sistema y plugins, dado que es propable que no se encuentren todos los plugin luego de copiarse a una nueva instalación. Esta información puede verse en 
```
http://x.x.x.x:8080/systemInfo
```
Las tareas o jobs, credenciales, herramientas y plugins deberán ser verificados de acuerdo a las indicaciones de configuración anteriores.

### **Sonarqube** <a name="sonarbk"></a>
Una alternativa para backup/restore, es trabajar con la base de datos de la aplicación, la cúal está en el contenedor de postgres, allí se almacena todo:proyectos, usuarios, plantillas, configuraciones.

https://www.pgadmin.org/download/

Se puede hacer una instalación de un cliente pgAdmin en cualquier otro equipo para conectarse a la base de datos de este contenedor postgres por el puerto 5432 con un explorador web:
      - http://x.x.x.x:5432

Para el acceso se tienen en cuenta los datos de DB

      - POSTGRES_USER=sonar
      - POSTGRES_PASSWORD=sonar
      
De allí hacer backup de la DB que se llama 'sonar', con formato **.backup**
De forma similar se puede hacer el proceso de restaurar, 
Ahora, si se necesita **restaurar** esta base de datos en **otro servidor** con instancia de Sonarqube y Postgres, esta debe:
1. iniciarse como si fuera **primera instalación** con usuario clave y demás
2. **detener el servicio de sonarqube**, no el de postgres
3. **restaurar la DB en la instancia nueva**, no deben salir errores
>NOTA: No seleccionar ninguna opción adicional a las que están en el cuadro de diálogo para la restauración.

4. **iniciar el servicio de Sonarqube**, las credenciales deben ser las mismas que el ambiente original al que se le hizo backup.


## **DIAGNÓSTICO - COMANDOS** <a name="DIAGNÓSTICO"></a>
### **Jenkins** <a name="jenkinsdiag"></a>
Tener en cuenta que está instalado sobre el S.O, entonces se debe revisar el proceso 'jenkins' y su rendimiento en relación con las capacidades del servidor.
Comandos:
```
$ sudo systemctl start jenkins
$ sudo systemctl stop jenkins
$ sudo systemctl status jenkins
```
tener en cuenta la ubicación del programa, para revisión de logs si se hace necesario, por defecto estaría en: 
```
/var/lib/jenkins/logs/
```
### **Docker, Sonar y Postgres** <a name="sonardiag"></a>
Para docker, se recuerda que este funciona como proceso en el S.O, debe estar el respectivo servicio activo, lo que se verifica con los comandos:
```
$ sudo systemctl start docker
$ sudo systemctl stop docker
$ sudo systemctl status docker
```
Si el servicio está activo, debe verificarse que los contenedores estén en correcto estado con:
```
$ docker ps -a
```
comandos de inicio/parada de contenedores, también funciona con los ID en vez del nombre:
```
$ docker start NOMBRE_CONTENEDOR 
$ docker stop NOMBRE_CONTENEDOR
```
Otros comandos de diagnóstico:

|comando|uso|
|---|---|
|$ docker ps|    //muestra contenedores activos|
|$ docker images|    //lista las imágenes|
|$ docker volume ls|     //listado de voluenes (data persistente de las app)|
|$ docker inspect NOMBRE_CONTENEDOR|    //detalles del contenedor|
|$ docker logs NOMBRE_CONTENEDOR|    //muestra logs del contenedor|
|$ docker exec -it NOMBRE_CONTENEDOR /bin/bash|    //ejecutar bash del contenedor|
|$ docker network ls|    //listar las redes docker|
|$ docker network NOMBRE_RED_DOCKER inspect|    //detalle de la red docker|

En el caso de la puesta con la herramienta docker-compose, se debe recordar que se iniciaron los contenedores con el comando:
```
$ docker-compose up -d
```
por lo que se "apagarían" con el siguiente comando:
```
$ docker-compose down
```
Si esto se hace se dejarán de ver los contenedores al ejecutar:
```
$ docker ps -a
```
para volver a verlos debe estar ubicado en el direcorio donde está el archivo docker-compose.yml para sonar y postgres, luego ejecutar:
```
$ docker-compose up -d
```
La información importante de las aplicaciones en los contenedores se encuentra en el sistema de archivos del S.O:
```
/var/lib/docker/volumes
```
dicha información persiste para cada caso en la carpeta con el nombre de la aplicación/contenedor, esto es por la configuración que está en el archivo docker-compose.