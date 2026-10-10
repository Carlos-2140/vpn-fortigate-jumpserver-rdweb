# VPN FortiGate + Jump Server + RD Web

> **Práctica de Infraestructura 2 — Seguridad y acceso remoto**
>
> 🎥 **Video de demostración:** pendiente de agregar antes de la entrega.

## Descripción

Este repositorio documenta la implementación de una infraestructura de acceso remoto seguro en **GNS3**, utilizando **FortiGate**, un **Jump Server con Windows Server**, **Remote Desktop Services (RDS)** y un **Web Server Ubuntu**.

El objetivo principal es centralizar el acceso administrativo a los servidores mediante un Jump Server y aplicar controles de seguridad en FortiGate para limitar la comunicación a los servicios estrictamente necesarios.

## Arquitectura

- **FortiGate-VM64-KVM** como firewall y concentrador VPN.
- **R-ISP** como router de salida.
- **SW-Core** como elemento de conmutación del laboratorio.
- **Jump Server Windows Server** con AD DS, DNS, IIS, RDS, RD Web y RemoteApp.
- **WEB-Server Ubuntu** con HTTPS, SSH y XRDP.
- **FortiClient** para acceso VPN IPsec remoto.

La topología y el diagrama se encuentran en la carpeta `Topologia`.

## Direccionamiento IP

| Equipo / Interfaz | Dirección |
|---|---|
| R-ISP | 198.51.100.1/30 |
| FortiGate WAN-ISP (port1) | 198.51.100.2/30 |
| FortiGate WEB-LAN (port2) | 10.21.40.129/29 |
| WEB-Server Ubuntu | 10.21.40.130/29 |
| FortiGate JUMP-LAN (port3) | 10.21.40.137/29 |
| Jump Server | 10.21.40.138/29 |
| Pool VPN | 10.21.41.1 - 10.21.41.6/29 |

El detalle completo se encuentra en `Direccionamiento/README.md`.

## VPN Remote Access

La VPN configurada en FortiGate se denomina **VPN-RA-01** y utiliza FortiClient mediante IPsec.

Características principales:

- IKEv1.
- Modo Aggressive.
- Autenticación mediante PSK y XAUTH.
- Grupo de usuarios: **VPN-USERS**.
- Pool VPN: **10.21.41.1 - 10.21.41.6/29**.
- Split Tunnel limitado al objeto **JUMP-SERVER**.
- El cliente VPN accede únicamente al Jump Server mediante los servicios autorizados.

Las contraseñas, claves precompartidas y demás secretos no deben publicarse en el repositorio.

## Jump Server

- IP: **10.21.40.138/29**
- Dominio: **INFRA2.LOCAL**
- FQDN: **WIN-LEU177NL1UK.INFRA2.LOCAL**

Roles y servicios implementados:

- Active Directory Domain Services.
- DNS.
- IIS.
- Remote Desktop Services.
- RD Connection Broker.
- RD Web Access.
- RD Session Host.
- RemoteApp.
- RD Web Client HTML5.

### Usuarios y permisos

| Usuario | Grupo | Acceso |
|---|---|---|
| usuario_basico | RDS-BASICOS | Web |
| usuario_priv | RDS-PRIVILEGIOS | Web, PuTTY/SSH y RDP |

RemoteApps publicadas:

- Internet Explorer / acceso Web.
- PuTTY.
- Conexión a Escritorio remoto (`mstsc.exe`).

## WEB-Server Ubuntu

- IP: **10.21.40.130/29**
- Gateway: **10.21.40.129**
- Interfaz: **ens3**

| Servicio | Puerto |
|---|---:|
| HTTPS | TCP/443 |
| SSH | TCP/22 |
| XRDP | TCP/3389 |

La página utilizada durante las pruebas corresponde al **Sistema de Caja**.

## Políticas de seguridad FortiGate

### VPN → Jump Server

La política `vpn_VPN-RA-01_remote_0` permite desde el pool VPN hacia **JUMP-SERVER**:

- HTTPS
- PING
- RDP
- RDS-WEBSOCKET-3392

### Jump Server → WEB-Server

La política `ALLOW-JUMP-TO-WEB` utiliza el grupo `JUMP-TO-WEB-SERVICES`, compuesto por:

- HTTPS
- RDP
- SSH

La política `DENY-JUMP-TO-WEB-OTHER` bloquea cualquier otro servicio entre ambos servidores y registra las violaciones.

## Evidencias

La carpeta `Evidencias` contiene pruebas de:

- VPN activa.
- IP asignada al cliente VPN.
- Conectividad hacia el Jump Server.
- Políticas de firewall.
- Logs de Forward Traffic.
- Acceso HTTPS al servidor Ubuntu.
- Acceso SSH mediante PuTTY.
- Acceso RDP/XRDP.
- RemoteApps por perfil de usuario.
- Certificados RDS.
- Licenciamiento RDS.
- Instalación y publicación del RD Web Client HTML5.

## RD Web Client HTML5

El cliente HTML5 fue instalado y publicado en modo **Production** utilizando **RDWebClientManagement 1.0.4** y **RD Web Client 2.1.85.0**.

La interfaz HTML5 quedó publicada y accesible mediante HTTPS. Durante las pruebas, la autenticación final mediante HTML5 quedó **parcial**, debido a que el listener WebSocket local TCP/3392 no quedó registrado en el Jump Server. **RD Web clásico y RemoteApp sí fueron validados correctamente.**

## Estructura del repositorio

```text
vpn-fortigate-jumpserver-rdweb/
├── Configuracion FortiGate/
│   └── Configuracion_fortigate.pdf
├── Configuracion Jum server/
│   ├── Configuracion_Jump_Server.pdf
│   └── configuracion-jump-server-completa.txt
├── Direccionamiento/
│   └── README.md
├── Evidencias/
│   └── Evidencias.pdf
├── Script/
│   └── configuracion-web-server.txt
├── Show running-config/
│   ├── Show running-config FortiGate.conf
│   ├── Show running-config R-ISP.txt
│   └── Show running-config SW-Core.txt
└── Topologia/
    ├── Diagrama.png
    └── Topologia.png
```

## Seguridad del repositorio

Este repositorio es público. Por esa razón:

- No deben publicarse contraseñas.
- No debe publicarse la PSK real de la VPN.
- No deben publicarse claves privadas.
- Los valores sensibles deben reemplazarse por `<REDACTED>`.

## Estado de la práctica

| Componente | Estado |
|---|---|
| FortiGate y políticas | ✅ |
| VPN IPsec Remote Access | ✅ |
| Acceso VPN → Jump Server | ✅ |
| Active Directory y DNS | ✅ |
| RD Web clásico | ✅ |
| RemoteApps | ✅ |
| HTTPS Jump → WEB | ✅ |
| SSH Jump → WEB | ✅ |
| RDP/XRDP Jump → WEB | ✅ |
| Certificados RDS | ✅ |
| Licenciamiento Per User | ✅ |
| RD Web Client HTML5 publicado | ✅ |
| Inicio de sesión final HTML5 | ⚠️ Parcial |

## Nota

Repositorio académico creado para documentar la práctica de **Infraestructura 2** en GNS3.