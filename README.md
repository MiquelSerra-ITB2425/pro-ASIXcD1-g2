# Proyecto Transversal - ASIXcD1

**Grupo:** 2  
**Equipo:** Alex Jiménez, Miquel Serra, Javier Vericat, Raúl Juárez, Aleix Tomás.

## Índice
0. [Introducción](#0-introducción)  
1. [Propuesta de CPD](#1--propuesta-de-cpd)  
2. [Implementación de servicios de audio y vídeo](#2--implementación-de-servicios-de-audio-y-vídeo)  
3. [Implementación y diseño de una base de datos](#3--diseño-e-implementación-de-una-base-de-datos)  
4. [Otros servicios](#4-otros-servicios)  
   4.1. [Servidor web (NGINX)](#41--servidor-web-nginx)  
   4.2. [Servidor DNS + autenticación y gestión de usuarios (LDAP)](#42--servidor-dns--autenticación-y-gestión-de-usuarios-ldap))  
   4.3. [Servicio de backups](#43--servicio-de-backups)  
6. [Sostenibilidad](#5--sostenibilidad)  
7. [Conclusión](#6-conclusión)  

## 0. Introducción
Este proyecto simula la instalación de un pequeño CPD, en el cual hemos desplegado servicios de gestión centralizada de usuarios (LDAP), DNS, backups y servidor web; además de los tres servicios obligatorios: vídeo, audio y base de datos.

Cada miembro del grupo se ha encargado de configurar una instancia de AWS en la que operan los servicios mencionados. Puedes consultar [aquí](#implementació-del-cpd-al-núvol-aws-amb-els-serveis-utilitzats) la distribución de servicios asignados a cada integrante.

## 1. 🗄 Propuesta de CPD
### Ubicación física
- **Situación física de la sala del edificio.**  
  La ubicación del CPD será en una planta subterránea, ya que tendrá una temperatura constante durante todo el año y permite aprovechar mejor los sistemas de refrigeración natural.
Además, de esta manera se mejora su seguridad física con protección contra intrusos y se disminuyen riesgos ambientales como incendios, tormentas, etc.
También será más fácil minimizar el impacto acústico hacia el entorno.

- **Sistemes de climatització (aire condicionat). Nivells de temperatura, humitat i neteja de l’aire.**  
  Sistema de climatización mixto:  
  - Sistema de free cooling indirecto (usando el aire exterior filtrado cuando las condiciones lo permitan).
  - CRAC con refrigeración líquida para mantener la temperatura entre 18-22ºC y la humedad entre 45%-55%.  
Temperatura operativa: 20-24 °C.  
Sistema de filtrado HEPA para limpiar el aire de partículas que puedan afectar al servidor.  

- **Mesures per dificultar la identificació de la sala.**  
  Que no haya visión de fuera a dentro, señalización discreta para evitar la identificación de la sala.
Acceso a la planta subterránea a través de una puerta interior segura, dentro de la zona de uso técnico restringido.
- **Distribució i gestió del cablejat.**  
  Cableado estructurado en canales, organizado por categorías (cat 6, cat 7, corriente etc…)
- **Terra tècnic i sostre tècnic.**  
Los cables y tubos irán por debajo de un suelo técnico con una elevación de 30 cm para facilitar el paso de cables y aire. También se hará un techo técnico con el mismo fin.
- **Planells, dibuixos, diagrames dels elements anteriorment citats.**  
![plano2d](https://github.com/MiquelSerra-ITB2425/pro-ASIXcD1-g2/blob/main/images/plano2d.png)
![plano3d](https://github.com/MiquelSerra-ITB2425/pro-ASIXcD1-g2/blob/main/images/plano3d.png)  
- **Estructuració dels racks (mínim 2 racks).**

  **Rack #1**
  
  Función: Alberga los servidores físicos y servicios virtualizados (web, vídeo, audio, BBDD, DNS, DHCP, backups).

  U1 – U2: Sistema de alimentación  
U3 – U4: SAI  
U5 – U7: Servidor físico 1 (virtualització principal)  
U8 – U10: Servidor físico 2 (base de datos y backups)  
U11 – U13: Paneles ciegos (espacio reservado a posibles ampliaciones)  
U14: Switch  
U16: Patch panel  
U17 – U24: Espacio tapado con paneles ciegos para mejorar circulación de aire  

  **Rack #2**
  
  Función: Comunicación y acceso a nivel de red.
  
  U1 – U2: Sistema de alimentación  
U3 – U4: SAI  
U5 – U6: Enrutador  
U7 – U8: Switch  
U9: Consola KVM (para poder acceder localmente a los servidores en caso de fallo de red)  
U10 – U11: Patch panel  
U12 – U24: Espacio tapado con paneles ciegos para mejorar circulación de aire y reservado para posibles ampliaciones.  


### Infraestructura IT:
- **Servidors: Número i tipus de model**  
Cantidad: 2 servidores físicos  
Modelo: Dell PowerEdge R750 (2U)
- **Patch panels.**  
Cantidad: 2 patch panels (1 por rack)  
Modelo: Aiten Data Patch Panel 24 Puertos FTP Cat 6 (1U)  
- **Switches.**  
Cantidad: 2 switches  
Modelo: Cisco Business CBS350-24T-4G (1U)  
- **Enrutador.**  
Cantidad: 1 router  
Modelo: Cisco C1131-8PWE (1U)  
- **Planells i diagrames de com estan distribuïts els racks amb els servidors, patch panels i switches.**

![racks](https://github.com/MiquelSerra-ITB2425/pro-ASIXcD1-g2/blob/main/images/racks.png)

### Infraestructura elèctrica: 
- **Sistemes d’alimentació redundant.**  
La instalación eléctrica se haría con una configuración N+1 para garantizar que siempre hay suministro eléctrico.  
Esta configuración consiste en que toda fuente de alimentación, transformadores etc tengan otra fuente de alimentación de respaldo para reducir drásticamente las posibilidades de quedarse sin corriente.  
- **SAIS.**    
Nuestro CPD tendrá que tener SAIS lo suficientemente grandes como para aguantar 1 hora, que se estima que es un tiempo razonable como para transicionar a los generadores de emergencia.  
Tendrá dos SAI (uno por cada rack).  
Además los SAIS tienen la función de regular la tensión de la red, para no estropear el CPD con sobretensión.
Falta saber cuánto consumen los CPDS para calcular la capacidad del SAIS

| Dispositivo                | Modelo                      | Consumo estimado (W)      |
|---------------------------|-----------------------------|---------------------------|
| Servidor Dell PowerEdge R750 | 2 unidades                  | 700 W c/u (1400 W)         |
| Switch Cisco CBS350-24T-4G   | 2 unidades                  | 30 W c/u (60 W)            |
| Patch Panel               | 2 unidades                  | 0 W (pasivo)              |
| Consola KVM               | 1 unidad                    | 40 W                      |
| Enrutador                 | 1 unidad                    | 50 W                      |
| Otros (ventiladores, etc.) | Estimación por rack         | 100 W por rack (200 W)    |
|                           |                             |                           |
| **Total estimado**        |                             | **1750 W**                |

Para 1 hora de funcionamiento sin corriente:  
Energía necesaria = Potencia (W) × Tiempo (h)  
Energía = 1750 W × 1 h = 1750 Wh = 1.75 kWh  

### Seguretat física i lògica:  
- **Física:**  
  - **Elements de control d’accés a incorporar en el CPD.**  
En la planta subterránea pondremos acceso por:  
    - Huella dactilar  
    - Tarjeta de proximidad  
Para tener así una doble seguridad en casa de atracos.  
  - **Videovigilància.**  
Cámaras de videovigilancia ip 24/7 y acceso remoto a las cámaras. Lo pondremos en la entrada y por el interior del CPD.  
  - **Sistemes prevenció, detecció i d’extinció d’incendis.**  
    - Sistemas de humo con sensores térmicos y ópticos.  
    - Sistema d’extinció automàtica amb gas inert (FM-200), ideal para la protección para equipos electricos y electronicos, ademas nada de polvo, aceites ni humedad ni dañino para las personas y equipos.  
    - Alarma sonora y visual.  
  - **Vies d’evacuació.**  
    - Salida de emergencia señalizada con iluminación autónoma.  
    - Mapa de fuga visible y formación al personal con simulacros periódicos.  

  - **Diagrames, planells i fotografies de tota la seguretat física 
incorporada.**  
En el plano de la planta subterranea se mostrará lo siguiente:  
    - Cámaras de videovigilancia.  
    - Acceso mediante huella dactilar.  
    - Salida de emergencia.  
    - Posición de sensores y extintores.  

![seguridad](https://github.com/MiquelSerra-ITB2425/pro-ASIXcD1-g2/blob/main/images/plano-seguridad.png)  

- **Lògica:**  
  - **Restricció d’accés per autorització.**  
Cada sistema tindrà accés per rols, amb autenticació multifactor (MFA). Només el personal tècnic autoritzat podrà accedir a determinades màquines o configuracions crítiques.  
  - **Firewalls.**
Firewall de nueva generación (NGFW), como Fortinet, con inspección profunda de paquetes.
  - **Monitorizació.**
Sistemas como Zabbix para controlar servidores, tráfico y estado de servicios.
Alertas automáticas por correo o mensajería ante anomalías.
    - **Còpies de seguretat/Backups.** 
Backups diarios en discos locales redundantes y en Amazon S3 (CPD en la nube).
Estrategia 3-2-1: 3 copias, 2 formatos distintos, 1 fuera del edificio.
  - **RAIDs.**  
Sistemas configurados con RAID 10, ofreciendo redundancia y altas prestaciones de escritura y lectura.  

- **Prevenció de riscos laborals:**  
  - **Mesures aplicades en matèria de prevenció de RRLL en el CPD**  
    - Señalización adecuada de salidas, materiales peligrosos y normas de emergencia.  
    - Extintores manuales y alfombras antielectrostáticas.  
    - Iluminación de emergencia y sistema de alarma.  
    - EPI (Equipos de Protección Individual): guantes, zapatos antideslizantes, chaleco.  
    - Formación obligatoria para el personal técnico en riesgos eléctricos y protocolos de evacuación.  

### Sostenibilitat:
- **Com optimitzar el consum d’energia.**  
  - Virtualizar servidores para reducir el número de máquinas físicas  
  - Sistemas de monitorización como el Zabbix  
  - Consolidación de cargas para maximizar el uso de los recursos  
- **Ús d’energia verda pel CPD.**  
  El CPD se conectará preferentemente a fuentes de energía renovable, como la fotovoltaica, a través de proveedores verdes o con una instalación propia de placas solares. Esto permitirá alimentar a los sistemas con energía limpia y reducir drásticamente la huella de carbono. 
- **Estalvi en longitud de cablejat.**  
  - Reducir el material necesario.  
  - Minimizar pérdidas energéticas por distancia.  
  - Mejorar la refrigeración pasiva en el espacio técnico.  

- **Sistemes de circulació d’aire que aprofitin condicions naturals.**  
  - Sistemas de free cooling, que utilizan aire exterior cuando es más frío que el interior.  
  - Circulación de aire canalizada y pasiva con sistemas de sobrepresión.  
  - Climatización con sistemas HVAC de alta eficiencia y sensores de temperatura y humedad inteligentes.  
- **Parada d’equips de comunicacions quan no hi ha càrrega.**  
  - Switches, equipos de r	ed y servidores secundarios cuando no haya carga o fuera de horas punta.  
  - Sistemas de comunicación redundantes no esenciales en horas de bajo uso.  
- **Equips de baix consum energètic.**  
  - Servidores con fuentes de alimentación de alto rendimiento. 
  - Discos SSD (más eficientes que HDD).  
  - Racks y componentes con mejor ventilación y menor necesidad de refrigeración activa.  

### Implementació del CPD al núvol AWS amb els serveis utilitzats.

Montaremos estos servicios por cada MV:  
- MV1 → Servidor de vídeo + audio (Miquel)
- MV2 → Servidor web (Javier)
- MV3 → Base de datos (Raúl)
- MV4 → DNS + autenticación y gestión de usuarios “LDAP” (Alex)
- MV5 → Backups (Aleix)  


### Investigar i comparar eficiència energètica amb altres proveïdors del núvol.  

**Amazon Web Services (AWS)**  
AWS ha anunciado su compromiso de utilizar energía 100% renovable antes de 2025. Sus centros de datos utilizan tecnologías como los procesadores Graviton, que ofrecen una mejor eficiencia energética, y sistemas de refrigeración pasiva como el free cooling. Además, permiten gestionar la carga de manera automática con servicios como CloudWatch y Lambda, lo que facilita detener instancias sin uso y optimizar el consumo. También ofrece servicios de almacenamiento como EBS y S3, que ayudan a reducir la dependencia de infraestructura física.  

**Google Cloud Platform (GCP)**  
Google Cloud ya funciona con energía 100% renovable desde 2017 y ha sido neutro en emisiones desde 2007. Utiliza inteligencia artificial para optimizar la climatización de sus centros de datos, reduciendo el consumo energético hasta un 30%. Con un PUE muy bajo (entre 1.1 y 1.2), demuestra una gran eficiencia energética. A nivel funcional, ofrece servicios similares a AWS para la virtualización, monitorización y automatización de recursos, destacando Stackdriver para el control en tiempo real.  

**Microsoft Azure**  
Microsoft Azure también ha marcado objetivos muy ambiciosos en materia de sostenibilidad: quiere ser carbon negative en 2030 y eliminar todas sus emisiones históricas para 2050. Sus centros de datos utilizan refrigeración líquida y energía renovable, con una gran eficiencia en el diseño térmico. Ofrece servicios de apagado automático de instancias mediante Azure Monitor y herramientas como Power Automate para gestionar la carga de manera inteligente.  

| Proveedor        | Energía renovable  | Eficiencia energética        | Refrigeración            | Automatización      | Compromiso ambiental     |
| ---------------- | ------------------ | ---------------------------- | ------------------------ | ------------------- | ------------------------ |
| **AWS**          | 100% (2025)        | Alta (Graviton, PUE <1.2)    | *Free cooling*           | CloudWatch + Lambda | Compensación de CO₂      |
| **Google Cloud** | 100% (actualmente) | Muy alta (IA, PUE <1.1)      | Optimizada por IA        | Stackdriver         | Neutro desde 2007        |
| **Azure**        | 100% (2025)        | Alta (refrigeración líquida) | *Free cooling* + líquida | Azure Monitor       | *Carbon negative* (2030) |


## 2. 🎛 Implementación de servicios de audio y vídeo  

### 2.1. 🎧 Instalación de servidor de audio

*sudo apt update  
sudo apt install -y icecast2 darkice vnstat iftop iperf3 ufw* 

No cambio nada de la config por defecto de icecast 
![str1](https://github.com/MiquelSerra-ITB2425/pro-ASIXcD1-g2/blob/main/images/str01.png)  

Cambio  el kernel  
![kernel1](https://github.com/MiquelSerra-ITB2425/pro-ASIXcD1-g2/blob/main/images/kernel1.png)  
![kernel2](https://github.com/MiquelSerra-ITB2425/pro-ASIXcD1-g2/blob/main/images/kernel2.png)  

#### Activar Icecast2
*sudo sed -i 's/ENABLE=false/ENABLE=true/' /etc/default/icecast2*

#### Configuració DarkIce
*[general]  
duration        = 0  
bufferSecs      = 5  
reconnect       = yes*  

*[input]  
device          = default  
sampleRate      = 44100  
bitsPerSample   = 16  
channel         = 2*  

*[icecast2-0]  
bitrateMode     = vbr  
bitrate         = 128  
server          = localhost  
port            = 8000  
password        = sourcepass  
mountPoint      = live.mp3  
name            = Stream d'àudio*  

Añado quality y format  
![str3](https://github.com/MiquelSerra-ITB2425/pro-ASIXcD1-g2/blob/main/images/str03.png)  

#### Obrim els ports del firewall  
*sudo ufw allow 8000/tcp  
sudo ufw allow 22/tcp*

#### Iniciem els serveis  
*sudo systemctl restart icecast2  
sudo systemctl enable icecast2*


Instalar ffmpeg  
creo un script para que el mp3 funcione en bucle
![str4](https://github.com/MiquelSerra-ITB2425/pro-ASIXcD1-g2/blob/main/images/str04.png)  
![str5](https://github.com/MiquelSerra-ITB2425/pro-ASIXcD1-g2/blob/main/images/str05.png)  

SERVIDOR:
*sudo darkice -c /etc/darkice.cfg*  
uno de los dos: 
- *ffmpeg -re -i tralalerito.mp3 -vn -c:a libmp3lame -b:a 128k -content_type audio/mpeg   -f mp3 icecast://source:hackme@54.236.209.174:8000/live.mp3*  

- ./audio.sh tralalerito.mp3 

CLIENTE:
http://54.236.209.174:8000/live.mp3

### 2.2. 📹 Instalación de servidor de vídeo  

#### Instal·lació GStreamer i eines de vídeo  
*sudo apt update  
sudo apt install -y gstreamer1.0-tools gstreamer1.0-plugins-base \  
  gstreamer1.0-plugins-good gstreamer1.0-plugins-bad \  
  gstreamer1.0-plugins-ugly gstreamer1.0-libav \  
  gstreamer1.0-alsa gstreamer1.0-pulseaudio \  
  vnstat iftop iperf3 v4l-utils*  

#### Instal·lació GStreamer finalitzada  
**Obrint port per RTP**  
*sudo ufw allow 5000/udp  
sudo ufw allow 5004/udp  
sudo ufw allow 22/tcp*  

Cambio también los security groups en aws para abrir estos puertos  
![str6](https://github.com/MiquelSerra-ITB2425/pro-ASIXcD1-g2/blob/main/images/str06.png)  

Cambio config de icecast para añadir un mount point  
![str7](https://github.com/MiquelSerra-ITB2425/pro-ASIXcD1-g2/blob/main/images/str07.png)  

reinicio icecast  
ejecuto:  
*ffmpeg -re -i popeye.mp4   -c:v libvpx -b:v 800k   -c:a libvorbis -b:a 128k   -f webm   -content_type video/webm   icecast://source:hackme@54.236.209.174:8000/stream.webm*  
accedo a la web “http://54.236.209.174:8000/stream.webm”  
![str8](https://github.com/MiquelSerra-ITB2425/pro-ASIXcD1-g2/blob/main/images/str08.png)  

script para vídeo  
![str9](https://github.com/MiquelSerra-ITB2425/pro-ASIXcD1-g2/blob/main/images/str09.png)  

ejecutar *./video.sh popeye.mp4*  

#### 2.3. Comprobación del ancho de banda con iperf3  
1. Instal·lació:  
*sudo apt update  
sudo apt install -y iperf3*  

2. Per fer la prova al servidor (receptor): *iperf3 -s*  
3. Sortida esperada: *Server listening on 5201*  
![str10](https://github.com/MiquelSerra-ITB2425/pro-ASIXcD1-g2/blob/main/images/str10.png)  

4. Al client (emissor): Substitueix IP_SERVIDOR per l’adreça IP del servidor:  
*iperf3 -c IP_SERVIDOR*  
5. Sortida esperada:  
Connecting to host IP_SERVIDOR, port 5201  
*[ ID] Interval           Transfer     Bandwidth  
[  5]   0.00-1.00   sec   112 MBytes   938 Mbits/sec*  
![str11](https://github.com/MiquelSerra-ITB2425/pro-ASIXcD1-g2/blob/main/images/str11.png)  


## 3. 💽 Diseño e implementación de una base de datos

### 3.1. Diseño entidad-relación:  

![er](https://github.com/MiquelSerra-ITB2425/pro-ASIXcD1-g2/blob/main/images/er.jpg)  

---

### 3.2. Modelo relacional:  

DEPARTAMENTO (id, nombre, teléfono)  

EMPLEADO (DNI, nombre, ap1, ap2, dirección, teléfono, idDep, idGrupo)  
*idDep referencia a DEPARTAMENTO;*  
*idGrupo referencia a GRUPO-NIVEL;*  

GRUPO-NIVEL (id, salario, periodoPrueba, diasVacaciones)  

---

### 3.3. Implementación en mysql:  

*(para acceder a la db como root entramos con “mysql -u root -p” y la contraseña es “itb”).*  
![image3](https://github.com/MiquelSerra-ITB2425/pro-ASIXcD1-g2/blob/main/images/image3.png)  

*Los datos de salarios se han introducido en base a las tablas salariales de 2024. Los días de vacaciones y el periodo de prueba en base al “convenio colectivo estatal de empresas de consultoría, tecnologías de la información y estudios de mercado y de la opinión pública” publicado el 16/04/2025.*  

Tablas salariales 2024:  
![tablasalarial](https://github.com/MiquelSerra-ITB2425/pro-ASIXcD1-g2/blob/main/images/tablasalarial.png) 

Periodo de prueba:  
![periodoprueba](https://github.com/MiquelSerra-ITB2425/pro-ASIXcD1-g2/blob/main/images/periodoprueba.png)  

Días vacaciones:  
![vacaciones](https://github.com/MiquelSerra-ITB2425/pro-ASIXcD1-g2/blob/main/images/vacaciones.png)  

Insertamos datos para los departamentos y las categorías profesionales:  
![image9](https://github.com/MiquelSerra-ITB2425/pro-ASIXcD1-g2/blob/main/images/image9.png)  

Insertamos datos para cada empleado:  
![image11](https://github.com/MiquelSerra-ITB2425/pro-ASIXcD1-g2/blob/main/images/image11.png)  

Comprobamos que se han insertado correctamente los datos:  
![image1](https://github.com/MiquelSerra-ITB2425/pro-ASIXcD1-g2/blob/main/images/image1.png)  
![image13](https://github.com/MiquelSerra-ITB2425/pro-ASIXcD1-g2/blob/main/images/image13.png)  

Creamos un usuario para que los clientes puedan acceder a la base de datos con él y administrarla:  
![image17](https://github.com/MiquelSerra-ITB2425/pro-ASIXcD1-g2/blob/main/images/image17.png)  

Habilitamos “bind-address” para que cualquier IP pueda conectarse a la database:  
![image8](https://github.com/MiquelSerra-ITB2425/pro-ASIXcD1-g2/blob/main/images/image8.png)  

Y con esto cualquier cliente dentro del rango de IPs que permita el security group ya podrá conectarse a la base de datos.  
![secgroupDB](https://github.com/MiquelSerra-ITB2425/pro-ASIXcD1-g2/blob/main/images/secgroupDB.png)  

--

Creamos distintos usuarios con sus roles y permisos:
![db21](https://github.com/MiquelSerra-ITB2425/pro-ASIXcD1-g2/blob/main/images/db21.png)  

Hacemos la prueba con el usuario ‘ifarre’ para comprobar que solo puede hacer ‘select’ en la base de datos y no tiene permiso para realizar un ‘insert’, por ejemplo:
![db22](https://github.com/MiquelSerra-ITB2425/pro-ASIXcD1-g2/blob/main/images/db22.png)  


## 4. Otros servicios  

### 4.1. 🌐 Servidor web (NGINX)  

Instalamos Nginx:  
![web1](https://github.com/MiquelSerra-ITB2425/pro-ASIXcD1-g2/blob/main/images/web1.png)  

El objetivo inicial era desplegar un servidor web accesible por HTTPS en una instancia EC2 de AWS, utilizando Nginx como servidor web para alojar una web HTML que incluía audio y vídeo en streaming.  

Durante el desarrollo, detectamos que el servidor de streaming (que emitía audio y vídeo en http://54.236.209.174:8000/) no soportaba HTTPS, lo que generaba bloqueos por contenido mixto en los navegadores modernos al intentar integrarlo en una página servida por HTTPS.  

Para mantener la compatibilidad con los streams y evitar bloqueos, decidimos servir toda la web en HTTP, desactivando el redireccionamiento a HTTPS. De esta forma, tanto el contenido web como el streaming funcionan correctamente. Finalmente, quedó de esta manera la configuración del Nginx:  
![web2](https://github.com/MiquelSerra-ITB2425/pro-ASIXcD1-g2/blob/main/images/web2.png)  

Y este es el resultado de la web después de aplicar el html y el css:  
![web3](https://github.com/MiquelSerra-ITB2425/pro-ASIXcD1-g2/blob/main/images/web3.png)  
![web4](https://github.com/MiquelSerra-ITB2425/pro-ASIXcD1-g2/blob/main/images/web4.png)  
![web5](https://github.com/MiquelSerra-ITB2425/pro-ASIXcD1-g2/blob/main/images/web5.png)  
![web6](https://github.com/MiquelSerra-ITB2425/pro-ASIXcD1-g2/blob/main/images/web6.png)  
![web7](https://github.com/MiquelSerra-ITB2425/pro-ASIXcD1-g2/blob/main/images/web7.png)  
![web8](https://github.com/MiquelSerra-ITB2425/pro-ASIXcD1-g2/blob/main/images/web8.png)  
![web9](https://github.com/MiquelSerra-ITB2425/pro-ASIXcD1-g2/blob/main/images/web9.png)  

### 4.2. 👥 Servidor DNS + autenticación y gestión de usuarios (LDAP)  

#### 🔹 Configuración del Servidor DNS (BIND9)

**Instalación de BIND9**

```bash
sudo apt-get update
sudo apt-get install bind9
```
![dns-image](https://github.com/MiquelSerra-ITB2425/pro-ASIXcD1-g2/blob/main/images/dns01.png)  

**Archivos de configuración clave**

- `/etc/bind/named.conf`: Archivo principal que incluye otros archivos de configuración.
![dns-image](https://github.com/MiquelSerra-ITB2425/pro-ASIXcD1-g2/blob/main/images/dns02.png)  
- `/etc/bind/named.conf.options`: Define opciones globales como los forwarders (reenviadores).
- `/etc/bind/named.conf.local`: Define las zonas DNS locales.
![dns-image](https://github.com/MiquelSerra-ITB2425/pro-ASIXcD1-g2/blob/main/images/dns03.png)  
- Archivos de zona: `db.asixcd-g2` y `db.172.16.3`, que contienen registros directos e inversos.
![dns-image](https://github.com/MiquelSerra-ITB2425/pro-ASIXcD1-g2/blob/main/images/dns04.png)  
![dns-image](https://github.com/MiquelSerra-ITB2425/pro-ASIXcD1-g2/blob/main/images/dns05.png)  
![dns-image](https://github.com/MiquelSerra-ITB2425/pro-ASIXcD1-g2/blob/main/images/dns06.png)  
![dns-image](https://github.com/MiquelSerra-ITB2425/pro-ASIXcD1-g2/blob/main/images/dns07.png)  
![dns-image](https://github.com/MiquelSerra-ITB2425/pro-ASIXcD1-g2/blob/main/images/dns08.png)  
![dns-image](https://github.com/MiquelSerra-ITB2425/pro-ASIXcD1-g2/blob/main/images/dns09.png)  


**Ejemplo de `named.conf.options`**

```
options {
    directory "/var/cache/bind";

    forwarders {
        8.8.8.8;
        1.1.1.1;
    };

    dnssec-validation auto;
    auth-nxdomain no;
    listen-on-v6 { any; };
};
```


**Ejemplo de `named.conf.local`**

```
zone "asixcd-g2.local" {
    type master;
    file "/etc/bind/db.asixcd-g2";
};

zone "3.16.172.in-addr.arpa" {
    type master;
    file "/etc/bind/db.172.16.3";
};
```


**Ejemplo de archivo `db.asixcd-g2`**

```
$TTL    604800
@       IN      SOA     dns.asixcd-g2.local. root.asixcd-g2.local. (
                          2         ; Serial
                     604800         ; Refresh
                      86400         ; Retry
                    2419200         ; Expire
                     604800 )       ; Negative Cache TTL

@       IN      NS      dns.asixcd-g2.local.
dns     IN      A       172.16.3.254
web     IN      A       172.16.4.74
vidaud  IN      A       172.16.1.112
db      IN      A       172.16.2.222
backup  IN      A       172.16.5.77
```


**Ejemplo de archivo `db.172.16.3`**

```
$TTL    604800
@       IN      SOA     dns.asixcd-g2.local. root.asixcd-g2.local. (
                          2         ; Serial
                     604800         ; Refresh
                      86400         ; Retry
                    2419200         ; Expire
                     604800 )       ; Negative Cache TTL

@       IN      NS      dns.asixcd-g2.local.
254     IN      PTR     dns.asixcd-g2.local.
```


**Reiniciar el servicio DNS**

```bash
sudo systemctl restart bind9
```

---

### Configuración de Autenticación Centralizada con LDAP

**Instalación del cliente LDAP**

```bash
sudo apt-get update
sudo apt-get install nslcd libnss-ldap libpam-ldap ldap-utils
```
![dns-image](https://github.com/MiquelSerra-ITB2425/pro-ASIXcD1-g2/blob/main/images/dns10.png)  


**Archivo `/etc/nslcd.conf`**

```
uri ldap://172.16.3.254
base dc=asixcd-g2,dc=local
binddn cn=admin,dc=asixcd-g2,dc=local
bindpw itbitb
```
![dns-image](https://github.com/MiquelSerra-ITB2425/pro-ASIXcD1-g2/blob/main/images/dns11.png)  
![dns-image](https://github.com/MiquelSerra-ITB2425/pro-ASIXcD1-g2/blob/main/images/dns12.png)  


**Reiniciar el servicio `nslcd`**

```bash
sudo systemctl restart nslcd
```
![dns-image](https://github.com/MiquelSerra-ITB2425/pro-ASIXcD1-g2/blob/main/images/dns13.png)  


**Ejemplo de archivo `usuario.ldif`**

```
dn: uid=josep,ou=People,dc=asixcd-g2,dc=local
objectClass: inetOrgPerson
objectClass: posixAccount
objectClass: shadowAccount
uid: josep
sn: Josep
cn: Josep Vidaud
userPassword: {CRYPT}itbitb
uidNumber: 10001
gidNumber: 10000
homeDirectory: /home/josep
loginShell: /bin/bash
```


**Importar el usuario al servidor LDAP**

```bash
ldapadd -x -D "cn=admin,dc=asixcd-g2,dc=local" -W -f usuario.ldif
```
![dns-image](https://github.com/MiquelSerra-ITB2425/pro-ASIXcD1-g2/blob/main/images/dns14.png)  

### 4.3. 🔐 Servicio de backups  

#### Introducción

En este caso nos basamos en hacer copias de seguridad, con lo cual haremos el backup de nuestra base de datos y también algún añadido extra.

#### Instalación del cliente MySQL

Vamos a instalar el cliente de **MySQL**, que nos sirve también para hacer backups, ya que también es más accesible y podremos acceder remotamente a la base de datos.

![backup](https://github.com/MiquelSerra-ITB2425/pro-ASIXcD1-g2/blob/main/images/backup1.png)
```bash
sudo apt install mysql-client
```

Si nos conectamos con el **host**, **usuario** y **password** correspondiente, de nuestra base de datos accederemos de manera exitosa.

![backup](https://github.com/MiquelSerra-ITB2425/pro-ASIXcD1-g2/blob/main/images/backup2.png)
```bash
mysql -h 172.16.X.X -u usuario -p
```
---

#### Crear una copia de seguridad

Ahora vamos a hacer una copia de seguridad de la base de datos `cpd_dg2`.

![backup](https://github.com/MiquelSerra-ITB2425/pro-ASIXcD1-g2/blob/main/images/backup3.png)
```bash
SHOW DATABASES;
```

Primero de todo, tenemos que tener **todos los permisos correspondientes**, porque a la hora de hacer la copia de seguridad nos saldrán errores una tras otra.

![backup](https://github.com/MiquelSerra-ITB2425/pro-ASIXcD1-g2/blob/main/images/backup4.png)
```bash
SHOW GRANTS for usuario;
```

Este es el comando para hacer una copia de seguridad. En resumen quiere decir:

- hacer copia de seguridad
- con el usuario
- con password (que nos la pedirá luego)
- el host o IP
- seleccionar todas las bases de datos o una en concreto
- definir la ruta de guardado
- añadir la fecha en que se hizo la copia

![backup](https://github.com/MiquelSerra-ITB2425/pro-ASIXcD1-g2/blob/main/images/backup5.png)
**Ejemplo de comando:**  

```bash
mysqldump -u usuario -p -h host --basedatos nombre_basedatos > /ruta/backup_$(date +%Y-%m-%d).sql
```

#### Comprobacion y añadidos

Para comprobar que se hizo correctamente, buscamos la ubicación del backup, vemos que esta ahi.
![backup](https://github.com/MiquelSerra-ITB2425/pro-ASIXcD1-g2/blob/main/images/backup6.png)
```bash
ls -la /ruta/
```

Vemos que si hacemos si hacemos un nano o un cat del sql, vemos que se hizo correctamente.
![backup](https://github.com/MiquelSerra-ITB2425/pro-ASIXcD1-g2/blob/main/images/backup7.png)
```bash
nano /ruta/backup_$(date +%Y-%m-%d).sql
```

Ademas hemos automatizado la copia de seguriddad con la comanda crontab -e, que automaticamente podremos hacer automatizaciones de todo tipo. En nuestro caso hicimos de esa manera

- hacer copia diárias a kas 23:59h
- accion correspondiente (mysqldump)
- seleccionar la base de datos
- definir la ruta de guardado
- añadir la fecha en que se hizo la copia

![backup](https://github.com/MiquelSerra-ITB2425/pro-ASIXcD1-g2/blob/main/images/backup8.png)

**Ejemplo de comando:**  

```bash
59 23 * * * mysqldump --databases nommbre_basedatos > /ruta/backup_$(date +\%F_\%H-\%M-\%S).sql
```

Esto es un recurso automático que utiliza el usuario, password y host para poder conectar automáticamente cuando hacemos el comando mysql para conectar nuestra base de datos, esto enlaza con él
![backup](https://github.com/MiquelSerra-ITB2425/pro-ASIXcD1-g2/blob/main/images/backup9.png)
```bash
[client]
user=usuario
password=tucontraseñasegura
host=172.16.X.X
```



## 5. 🌳 Sostenibilidad  

### Identificación de recursos empleados.

- **1. Ubicación sugerida del centro de operaciones**  
  - Lugar: Parque Tecnológico del Vallès (Cerdanyola del Vallès, Barcelona)  
  - Justificación:  
    - Proximidad a Barcelona, con buena conexión de transporte público.  
    - Infraestructuras ya preparadas para empresas tecnológicas.  
    - Acceso a servicios energéticos sostenibles y fibra óptica de alta capacidad.  
    - Presencia de otras empresas tecnológicas y centros de I+D.  

  - Costes aproximados:  
    - Alquiler de oficinas para 100 personas (~800 m²): 9.600 €/mes (12 €/m²)  
    - Costes de electricidad (~0,18 €/kWh × 212 kWh × 30 días): ~1.145 €/mes  
    - Costes operativos varios (internet, mantenimiento, climatización): ~2.000 €/mes  
    - **Total mensual estimado:** 12.745 €  
    - **Total anual estimado:** ~152.940 €  
    
- **Infraestructura IT desplegada**  

| Tipo de servidor       | Función                  | Recursos asignados              |
|------------------------|--------------------------|----------------------------------|
| Servidor de Backups    | Almacenamiento y recuperación | 2 vCPU, 4 GB RAM, 30 GB (máx en cuenta estudiante AWS)   |
| Servidor Vídeo+Audio   | Streaming multimedia      | 4 vCPU, 8 GB RAM, 30 GB      |
| Servidor Base de Datos | MySQL                | 2 vCPU, 4 GB RAM, 30 GB    |
| Servidor Web           | Hosting web y APIs        | 2 vCPU, 4 GB RAM, 30 GB    |
| Servidor DNS y LDAP  | DNS y Autenticación y gestión de usuarios “LDAP”  | 2 vCPU, 4 GB RAM, 30 GB  |

- **Infraestructura física**  

| Dispositivo     | Cantidad | Modelo                             | Rack              |
|------------------|----------|------------------------------------|--------------------|
| Servidor físico  | 2        | Dell PowerEdge R750 (2U)           | Rack #1 (U5–U10)   |
| Switches         | 2        | Cisco CBS350-24T-4G (1U)           | Rack #1 y #2       |
| Patch panels     | 2        | Aiten Cat 6 FTP 24 puertos (1U)    | Rack #1 y #2       |
| Enrutador        | 1        | Cisco C1131-8PWE                   | Rack #2            |
| Consola KVM      | 1        | Genérica                           | Rack #2            |
| SAI (UPS)        | 2        | APC Smart-UPS (~1,2 kWh)           | Ambos racks        |


- **2. Estimar el consumo energético y la huella de carbono**
  - Previsión de uso y tráfico mensual:  
Funcionamiento estimado: 240 horas/mes (12 h/día, 5 días/semana)  
Usuarios previstos: ~100  
Tráfico estimado:  
    - Web y BBDD: 1 TB  
    - Vídeo y Audio: 3 TB  
    - Total tráfico mensual: 4 TB  

  - Cálculo del consumo energético y huella de carbono

    | Componente              | Consumo mensual estimado (kWh) |
    |-------------------------|-------------------------------:|
    | Servidor físico 1       | 90                            |
    | Servidor físico 2       | 75                            |
    | Switches (2x)           | 10                            |
    | Router                  | 5                             |
    | Patch panels + KVM      | 2                             |
    | SAIs (UPS)              | 10                            |
    | Tráfico red (4 TB @5 W/GB) | 20                         |
    | **Total**               | **212 kWh**                   |

  - Emisiones estimadas (España, 2024):
    - Factor conversión: 0,23 kg CO₂ / kWh
    - Emisiones mensuales: 212 × 0,23 = 48,76 kg CO₂
    - Emisiones anuales: 48,76 × 12 = 585,12 kg CO₂/año
   
### Propuesta de medidas de reducción u optimización:

1. **Instalación de placas solares fotovoltaicas**
Instalación recomendada: 12 kWp  
Producción estimada en Barcelona: ~1.700 kWh/kWp/año → 20.400 kWh/año  
Cobertura del consumo estimado (2.544 kWh/año): ~100%  
Emisiones evitadas: 2.544 × 0,23 = 585,12 kg CO₂/año  
Ahorro eléctrico anual: 2.544 × 0,18 €/kWh = 457,92 €

2. **Gestión eficiente del agua**
Grifos con sensores de infrarrojos  
Sistemas de doble descarga en sanitarios  
Reutilización de aguas grises en limpieza y riego

3. **Optimización térmica y energética**
Paneles ciegos en racks  
Flujo de aire optimizado (frontal-trasero)  
Aislamiento térmico en salas técnicas  
Termostatos inteligentes y sensores de CO₂

4. **Buenas prácticas IT**  
Consolidación de máquinas virtuales  
Autoscaling de recursos cloud  
Apagado automático fuera del horario laboral  
Migración de servicios a AWS Irlanda (>90% renovable)  

5. **Sensibilización y formación**  
Talleres y formaciones sobre eficiencia energética y sostenibilidad  
Paneles informativos con consumo energético en tiempo real

**Reparto del consumo energético mensual (kWh)**  

![grafico1](https://github.com/MiquelSerra-ITB2425/pro-ASIXcD1-g2/blob/main/images/grafico1.png)  


## 6. Conclusión  

En este proyecto hemos desplegado un CPD virtual distribuido sobre AWS, en el que hemos implementado servicios de gestión centralizada de usuarios (LDAP), resolución de nombres (DNS), base de datos MySQL con copias de seguridad automatizadas mediante crontab y streaming de vídeo y audio reproducidos en una web configurada con NGINX.  

Inicialmente, para conectar las máquinas virtuales habríamos preferido una topología en estrella, donde una de las instancias actuara como router central. Sin embargo, debido al funcionamiento de las VPC de AWS y las restricciones de sus cuentas educativas eso no ha sido posible y hemos optado por una topología en malla, en la que cada MV está conectada directamente con las demás.  

En definitiva, el proyecto nos ha ayudado a comprender qué elementos son necesarios para que un CPD funcione correctamente, desde la infraestructura física y lógica hasta la gestión de servicios en red. Además, nos ha ayudado a mejorar nuestra capacidad de trabajo en equipo y de toma de decisiones conjunta.
