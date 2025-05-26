# Proyecto Transversal - ASIXcD1

**Grupo:** 2
**Alumnos:** Alex Jiménez, Miquel Serra, Javier Vericat, Raúl Juárez, Aleix Tomás.

## Índice
1. [Introducción](#introducción)
2. [Propuesta de CPD](#propuesta-de-cpd)
3. [Implementación de servicios de audio y vídeo](#implementación-de-servicios-de-audio-y-vídeo)
4. [Implementación y diseño de una base de datos](#impacto-ambiental)
5. [Sostenibilidad](#propuestas-para-minimizar-los-impactos-ambientales)
6. [Conclusión¿?](#conclusión)

## Introducción
lorem  lorem  
fds  

## Propuesta de CPD
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






Diagrames, planells i fotografies de tota la seguretat física 
incorporada. 
En el plano de la planta subterranea se mostraran las siguientes cosas.
Zonas de videovigilancia
Punto de acceso y control
Ruta de evacuación
Posición de sensores y extintores






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

### Implementació del CPD al núvol AWS amb els serveis utilitzats (mínim de 4 - el serveis d'àudio, vídeo i bases de dades es valoren en els altres blocs).  

Montaremos estos servicios por cada MV:  
- MV1 → Servidor de vídeo + audio (Miquel)
- MV2 → Servidor web (Javier)
- MV3 → Base de datos (Raul)
- MV4 → DNS y Autenticación y gestión de usuarios “LDAP” (Alex)
- MV5 → Backups (Aleix)




## Implementación de servicios de audio y vídeo
