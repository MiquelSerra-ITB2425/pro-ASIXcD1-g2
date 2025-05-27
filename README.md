# Proyecto Transversal - ASIXcD1

**Grupo:** 2  
**Alumnos:** Alex Jiménez, Miquel Serra, Javier Vericat, Raúl Juárez, Aleix Tomás.

## Índice
1. [Introducción](#introducción)
2. [Propuesta de CPD](#propuesta-de-cpd)
3. [Implementación de servicios de audio y vídeo](#implementación-de-servicios-de-audio-y-vídeo)
4. [Implementación y diseño de una base de datos](#diseño-e-implementación-de-una-base-de-datos)
5. [Sostenibilidad](#sostenibilidad)
6. [Conclusión¿?](#conclusión)

## Introducción
Este proyecto simula la instalación de un pequeño CPD en el cual hemos decidido montar servicios de gestión centralizada de usuarios (LDAP), DNS, backups y web; además de los 3 servicios obligatorios (vídeo, audio y base de datos).

Cada miembro del grupo se ha encargado de montar una instancia de AWS en la que operan los servicios mencionados, puedes ver [aquí](#Implementació-del-CPD-al-núvol-AWS-amb-els-serveis-utilitzats-(mínim-de-4---el-serveis-d'àudio,-vídeo-i-bases-de-dades-es-valoren-en-els-altres-blocs).) la distribución de servicios por cada miembro del grupo.

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


### Investigar i comparar eficiència energètica amb altres proveïdors del núvol. Com els diferents proveïdors ofereixen solucions de CPD administrats per aquestes empreses i com donen cobertura als requeriments exposats anteriorment.  



## Implementación de servicios de audio y vídeo



## Diseño e implementación de una base de datos

**Diseño entidad-relación:**

![er](https://github.com/MiquelSerra-ITB2425/pro-ASIXcD1-g2/blob/main/images/er.jpg)  

**Modelo relacional:**  

DEPARTAMENTO (id, nombre, teléfono)  

EMPLEADO (DNI, nombre, ap1, ap2, dirección, teléfono, idDep, idGrupo)  
*idDep referencia a DEPARTAMENTO;*  
*idGrupo referencia a GRUPO-NIVEL;*  

GRUPO-NIVEL (id, salario, periodoPrueba, diasVacaciones)  

**Implementación en mysql:**  

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

## Sostenibilidad  

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
