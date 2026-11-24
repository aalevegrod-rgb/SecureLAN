
# 🔐 SECURELAN
## Diseño e implantación de una red empresarial segura

**Anteproyecto – Proyecto Intermodular de Administración de Sistemas Informáticos en Red**

| Información | Datos |
|---|---|
| Ciclo Formativo | Técnico Superior en Administración de Sistemas Informáticos en Red |
| Centro educativo | IES Triana |
| Autor | Alejandro Vega |
| Curso académico | 2026/2027 |

---

# Índice

1. [Introducción](#1-introducción)
2. [Título](#2-título)
3. [Descripción](#3-descripción)
4. [Justificación](#4-justificación)
5. [Objetivos](#5-objetivos)
6. [Lenguajes de programación y tecnologías](#6-lenguajes-de-programación-y-tecnologías)
7. [Prototipo de interfaz de usuario](#7-prototipo-de-interfaz-de-usuario)
8. [Gestor de base de datos](#8-gestor-de-base-de-datos)
9. [Entornos de aplicación del proyecto](#9-entornos-de-aplicación-del-proyecto)
10. [Fases de desarrollo](#10-fases-de-desarrollo)
11. [Módulos del ciclo formativo relacionados](#11-módulos-del-ciclo-formativo-relacionados)
12. [Anexos y Bibliografía](#12-anexos-y-bibliografía)

---

# 1. Introducción

En este anteproyecto, estará documentado como tengo pensado crear una red segura para una pequeña empresa (unos 50 trabajadores). Existirá DHCP, para poder darle IPs automáticas a los dispositivos, crearé VLANs para cada departamento, utilizaré DNS para poder acceder a dispositivos a través de nombres y no de IPs, usaré un firewall seguro con monitorización para poder ver en todo momento cómo funciona, y por ultimo control de acceso, para que ver a que accede cada trabajador, cuando y desde donde, esto se guardar todo en logs.

Para el DHCP, DNS utilizaré OPNsense. Para VLAN utilizaŕe también OPNsense. Para el Firewall utilizaré OPNsense con reglas por VLAN. Para el control de acceso utilizaré Samba. Y para logs y Monitorización utilizare Grafana.

# 2. Título

**SecureLAN: diseño e implantación de una red empresarial segura**

# 3. Descripción

En este proyecto vamos a diseñar y montar desde cero una red informática para una empresa pequeña (unos 50 trabajadores) y además estará vigilada. La red se dividirá en zonas separadas según el departamento (dirección, administración, comercial, informática, servidores e invitados) con VLAN (red lógica independiente, similar a una subred, en la que solo se ven entre sí los dispositivos que pertenecen a esa VLAN).

Encima de esto se montan los servicios que hacen funcionar la red: DHCP (reparte IPs de manera automática), DNS (traduce nombres a direcciones), un Firewall (el que decide qué tráfico pasa) y el Control de Acceso (cada persona tendrá distintos permisos con los usuarios y grupos).

Una de las funciones que sabía que iba a añadir es la monitorización del Firewall, con ayuda de Grafana. El Control de Acceso también lo mostrará Grafana y como mínimo todo queda guardado en logs en un servidor aparte.

# 4. Justificación

Muchas pequeñas empresas montan su red en plano, es decir, todos los dispositivos conectados a la red sin ninguna configuración. Esto hace que cada dispositivo pueda verse con otros dentro de la red, o puedan ver al servidor, esto hace que pueda acceder a información confidencial o simplemente expandir un virus por toda la red. Además de no tener un control de acceso, no sabríamos ni identificar al culpable.

Con SecureLAN esto no sucedería ya que:

- Separamos la red para que cada departamento llegue solo a lo configurado.
- Controlamos el tráfico con un firewall configurado por reglas.
- Tenemos todo registrado y a la vista, para en cualquier momento, identificar qué está pasando.

He buscado información sobre que suelen utilizar las empresas actualmente para proteger y segmentar la red, y he encontrado varios inconvenientes:

- **Soluciones de fabricantes:** Son equipos y plataformas muy completas que combinan switches, firewall y gestión en la nube. Su desventaja es que obligan a comprar hardware de la marca, pagar licencias o suscripciones y quedarse atado al fabricante, lo que resulta caro para una empresa pequeña.

- **Firewall de software libre:** Son programas gratuitos que convierten un ordenador normal en un firewall profesional. Su desventaja es que, por sí solos, no resuelven el diseño completo de la red ni muestra de forma visual lo que está pasando.

- **Entorno Microsoft:** Es una solución muy extendida en empresas para controlar usuarios y permisos. Su desventaja es que requiere licencias de Windows Server.

Mi idea no es inventar algo nuevo, sino juntar herramientas gratuitas y de código abierto para que una empresa pequeña, tenga una red segura sin pagar licencias.

Elijo OPENsense porque en un mismo router puedo utilizar DHCP, DNS, VLANs y Firewall y tiene una interfaz web. También elijo Samba porque permite tener usuarios y control de acceso parecido al de Windows pero gratis. Por último elijo Grafana porque sus paneles son muy intuitivos y visuales.

Lo que se diferencia SecureLAN de las demás, es que lo conecto todo para que los logs del firewall y el control de acceso acaben en el mismo panel y así se pueda ver toda la red en una única pantalla.

# 5. Objetivos

El objetivo principal se basa en diseñar, montar y probar una red segura para una pequeña empresa, con la red separada por departamentos, un firewall con monitorización y un control de acceso con registros. Otros puntos a tener en cuenta son:

- Diseñar el direccionamiento IP.
- Crear la VLAN de cada departamento con OPNsense.
- Configurar un servidor DHCP en OPNsense para que cada VLAN reparta sus propias IPs automáticamente, dejando alguna IP fija para impresoras y servidores.
- Configurar un servidor DNS en OPNsense para entrar a los equipos por nombre.
- Configurar el firewall con reglas distintas para cada VLAN, siguiendo la idea de que solo se permite lo necesario y todo lo demás se bloquea.
- Montar un dominio con Samba con usuarios y grupos por departamento, carpetas compartidas con permisos distintos.
- Guardar en logs quién se conecta, a qué accede, a qué hora y desde qué equipo accede.
- Crear paneles en Grafana para ver en tiempo real el estado del firewall y los accesos de los trabajadores.
- Configurar alertas básicas, como por ejemplo cuando haya muchos intentos fallidos de login.

# 6. Lenguajes de programación y tecnologías

En general, este proyecto no se basa generalmente en nada de programación aunque si usaré en algunos momentos scripts para automatizar tareas.

## 6.1. Lenguajes de scripting

- **Bash (Linux):** Para automatizar tareas en los servidores Linux, como hacer copias de seguridad de configuraciones o comprobar que los servicios funcionan. Viene en todos los Linux y ya lo he usado en el grado.

- **PowerShell (Windows):** Para tareas en Windows Server, como crear muchos usuarios a la vez, comprobar permisos o activar la auditoría de inicios de sesión.

- **YAML (Docker Compose):** No es programar como tal, es un fichero de texto donde escribo qué programas quiero arrancar y como se conectan.

- **LogQL y Flux:** Lenguajes de consulta de Grafana Loki e InfluxDB respectivamente, empleados para filtrar registros de eventos y construir gráficas de rendimiento y seguridad.

## 6.2. Tecnologías y herramientas

- **VirtualBox:** Para la creación de máquinas virtuales, lo elijo por su fluidez, lo bien que funciona y porque lo he utilizado en clase. Soporta todo SO pudiendo configurar hardware específico.

- **Cisco Packet Tracer:** Permite crear un esquema provisional de la red, probando que toda la red funcionan bien, incluyendo procesos como VLAN, DHCP, DNS…

- **OPNsense:** Junta todos los servicios de la red en una sola máquina virtual con un interfaz web limpia y envío de logs.

- **Samba (Ubuntu Server):** Implementa un controlador de dominio compatible con Active Directory (usuarios, grupos y carpetas) a coste cero en licencias.

- **Telegraf + InfluxDB:** Telegraf se instala como plugin oficial en OPNsense y envía estadísticas de CPU, RAM y ancho de banda directo a InfluxDB.

- **Grafana Allow + Loki:** Recogen y almacenan los registros syslog de OPNsense y los eventos de samba añadiendolo por etiquetas con mínimo consumo de RAM.

- **Grafana OSS:** Ofrece paneles visuales en tiempo real y motor de alertas conectando InlfuxDB y Loki en una interfaz web.

- **Docker + Docker Compose:** Permite encender, reiniciar, o replicar toda la pila de monitorización (InfluxDB, Loki y Grafana) mediante un único fichero YAML.

- **Wireshark, Nmap y Ping:** Herramientas estándar para verificar el aislamiento entre VLANs, habilitar puertos abiertos y analizar paquetes en tiempo real.

# 7. Prototipo de interfaz de usuario

El proyecto tiene dos interfaces principales: el panel de OPNsense y el panel de Grafana.

El prototipo contempla las siguientes pantallas:

## 7.1. Seguridad de la Red

Panel general donde se puede consultar el estado de la infraestructura, el tráfico de red, los bloqueos por interfaz y el estado de los dispositivos.

## 7.2. Firewall en Tiempo Real

Interfaz para consultar el tráfico de las distintas VLAN, las conexiones denegadas, el estado de la red y las últimas conexiones registradas.

## 7.3. Control de accesos

Panel para consultar la actividad de los usuarios, los accesos permitidos y denegados, y los intentos fallidos de inicio de sesión.

## 7.4. Alerta de seguridad

Interfaz para visualizar las alertas activas, su gravedad, los eventos detectados y las posibles incidencias de seguridad.

# 8. Gestor de base de datos

Este proyecto no necesita una base de datos tradicional tipo MySQL, porque casi todo lo que guarda son registros de lo que va pasando. Por eso he elegido:

- **Loki:** para guardar los logs de texto.
- **Prometheus:** para guardar datos a lo largo del tiempo.
- **Samba:** que guarda en su propio directorio los usuarios, grupos, y equipos de dominio.
- **Grafana:** usa una base de datos pequeña interna para guardar sus paneles y usuarios, y no hace falta tocarla.

He hecho un modelo entidad-relación en caso de ser necesario posteriormente:

## 8.1. Modelo entidad-relación

```text
DEPARTAMENTO (id_departamento, nombre, id_vlan)

VLAN (id_vlan, numero, nombre, red, mascara, puerta_enlace)

USUARIO (id_usuario, nombre_usuario, nombre_completo, id_departamento)

DISPOSITIVO (id_dispositivo, nombre, MAC, IP, tipo, id_vlan)

EVENTO_ACCESO (id_evento, fecha_hora, id_usuario, id_dispositivo, recurso, resultado)

EVENTO_FIREWALL (id_evento, fecha_hora, IP_origen, IP_destino, puerto, protocolo, accion, id_vlan)

ALERTA (id_alerta, fecha_hora, tipo, gravedad, descripcion)
```

# 9. Entornos de aplicación del proyecto

Mi dispositivo que uso en casa, tiene 16GB de RAM y unos 500GB de disco HDD libre para su uso.

Esta es la idea que tengo para la organización de máquinas virtuales.

## 9.1. Organización de máquinas virtuales

| Máquina Virtual | Sistema Operativo | Función Principal | RAM |
|---|---|---|---|
| FW-OPNSENSE | OPNsense (FreeBSD) | Firewall, Router, VLAN, DHCP, DNS. | 2GB |
| SRV-AD | Windows Server 2019 | Controlador de Dominio y servidor de archivos. | 4GB |
| SRV-MONITOR | Ubuntu Server 24.04 | Host Docker con Grafana, Loki, Prometheus y receptor Syslog. | 4GB |
| PC-ADMIN | Windows 10 | Puesto cliente unido al dominio en la VLAN de Administración. | 4GB |
| PC-COMERCIAL | Ubuntu Desktop | Puesto cliente en la VLAN del departamento Comercial. | 2GB |
| PC-INVITADO | Ubuntu Desktop | Equipo externo conectado a la red aislada de invitados. | 2GB |

## 9.2. Plan de direccionamiento IP y segmentación de VLAN

| VLAN | Departamento | Subred | Gateway | Rango DHCP | Reservas Fijas |
|---|---|---|---|---|---|
| 10 | Dirección | 192.168.10.0/24 | 192.168.10.1 | 192.168.10.100 - 192.168.10.150 | 192.168.10.2 - 192.168.10.99 |
| 20 | Administración | 192.168.20.0/24 | 192.168.20.1 | 192.168.20.100 - 192.168.20.200 | 192.168.20.2 - 192.168.20.99 |
| 30 | Comercial | 192.168.30.0/24 | 192.168.30.1 | 192.168.30.100 - 192.168.30.200 | 192.168.30.2 - 192.168.30.99 |
| 40 | Informática | 192.168.40.0/24 | 192.168.40.1 | 192.168.40.100 - 192.168.40.150 | 192.168.40.2 - 192.168.40.99 |
| 50 | Servidores | 192.168.50.0/24 | 192.168.50.1 | Desactivado | 192.168.50.10 - 192.168.50.20 |
| 60 | Invitados | 192.168.60.0/24 | 192.168.60.1 | 192.168.60.50 - 192.168.60.250 | Ninguna |
| 99 | Gestión | 192.168.99.0/24 | 192.168.99.1 | Desactivado | 192.168.99.1 |

## 9.3. Configuración de seguridad y reglas del cortafuegos

El cortafuegos se configura bajo un modelo de seguridad por defecto: Todo tráfico entre VLANs queda bloqueado salvo las reglas permitidas específicas:

- **VLAN 99 (Gestión) y VLAN 40 (Informática):** Acceso administrativo completo (HTTPS, SSH y visualización de Grafana) hacia OPNsense y los servidores de la VLAN 50.

- **VLANs 10, 20 y 30 (Dirección, Administración y Comercial):** Acceso permitido únicamente al servidor DNS (192.168.50.1), al controlador de dominio y carpetas compartidas en SRV-SAMBA (192.168.50.10) según permisos de grupo, y salida a Internet (HTTP/HTTPS). Queda prohibida la comunicación directa entre equipos distintos departamentos.

- **VLAN 50 (Servidores):** Aloja SRV-SAMBA (192.168.50.10) y SRV-MONITOR (192.168.50.20). Solo responde a peticiones legítimas procedentes de redes internas autorizadas.

- **VLAN 60 (Invitados):** Acceso exclusivo a internet (puertos 80 y 443) mediante NAT. Bloqueo estricto hacia cualquier dirección IP privada (192.168.0.0/16), impidiendo alcanzar tanto los departamentos como los servidores.

# 10. Fases de desarrollo

El desarrollo de SecureLAN se ejecutará siguiendo seis etapas planificadas hasta su entrega:

- **Planificación inicial (Noviembre):** Definir objetivos, elegir herramientas, preparar el entorno y hacer el calendario. Entregar el anteproyecto.

- **Análisis y diseño (Diciembre-Enero):** Diseñar la tabla de VLANs y IPs, el esquema de la red, las reglas del firewall y los prototipos en Figma.

- **Desarrollo e implementación (Febrero-Abril):** Instalar OPNsense, crear VLANs, DHCP y DNS, montar Samba, configurar el firewall y montar Grafana, Loki y Prometheus.

- **Pruebas y validación (Abril-Mayo):** Comprobar que las reglas funcionan, que los logs se guardan y que las alertas saltan.

- **Despliegue y puesta en marcha (Mayo):** Dejar todo funcionando como en una empresa real, con copias de seguridad de las configuraciones.

- **Mantenimiento y documentación (Mayo-Junio):** Terminar la memoria, preparar la presentación y la demostración, y revisar fallos.

# 11. Módulos del ciclo formativo relacionados

En este apartado escribiré todos los módulos utilizados para este proyecto.

- **Planificación y Administración de Redes (PAR):** Diseño del esquema de direccionamiento ipv4, cálculo de subredes y configuración de segmentación lógica mediante VLANs.

- **Servicios de Red e Internet (SRI):** Implantación y administración de servidores DHCP con múltiples ámbitos y reservas fijas, configuración DNS interna y enrutamiento entre subredes.

- **Seguridad y Alta Disponibilidad (SAD):** Configuración de cortafuegos basado en reglas, aplicación del principio de mínimo privilegio, aislamiento de redes de invitados y auditoría de seguridad mediante logs y alertas.

- **Administración de Sistemas Operativos (ASO):** Despliegue y administración de servidores Ubuntu, gestión centralizada de identidades, grupos y permisos de recursos compartidos mediante Samba y automatización con scripts.

- **Implantación de Sistemas Operativos (ISO):** Instalación, optimización de recursos y gestión de redes virtuales con VirtualBox e integración de clientes Windows y Linux en red corporativa.

# 12. Anexos y Bibliografía

- Prototipo de interfaz de usuario.
- Grafana, Loki y Alloy.
- OPNsense.
- Ubuntu Server.
- Docker y Docker Compose.

---

**SECURELAN | Proyecto Intermodular ASIR | Curso 2026/2027**

*Diseño e implantación de una red empresarial segura.*
