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
Sistema de free cooling indirecto (usando el aire exterior filtrado cuando las condiciones lo permitan).
CRAC con refrigeración líquida para mantener la temperatura entre 18-22ºC y la humedad entre 45%-55%.
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
○ Servidors: Número i tipus de model.  
Cantidad: 2 servidores físicos  
Modelo: Dell PowerEdge R750 (2U)  
○ Patch panels.  
Cantidad: 2 patch panels (1 por rack)  
Modelo: Aiten Data Patch Panel 24 Puertos FTP Cat 6 (1U)  
○ Switches.  
Cantidad: 2 switches  
Modelo: Cisco Business CBS350-24T-4G (1U)  
○ Enrutador.  
Cantidad: 1 router  
Modelo: Cisco C1131-8PWE (1U)  
○ Planells i diagrames de com estan distribuïts els racks amb els servidors, patch panels i switches.  
![racks](https://github.com/MiquelSerra-ITB2425/pro-ASIXcD1-g2/blob/main/images/racks.png)

Infraestructura elèctrica: 
○ Sistemes d’alimentació redundant. 
La instalación eléctrica se haría con una configuración N+1 para garantizar que siempre hay suministro eléctrico.
Esta configuración consiste en que toda fuente de alimentación, transformadores etc tengan otra fuente de alimentación de respaldo para reducir drásticamente las posibilidades de quedarse sin corriente.
○ SAIS. Càlcul de quantes bateries o components per tenir els servidors operatius sense corrent elèctric i temps que voleu de funcionament sense senyal elèctric en els servidors.
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




## Implementación de servicios de audio y vídeo
