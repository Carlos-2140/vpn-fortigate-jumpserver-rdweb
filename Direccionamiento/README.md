# Direccionamiento IP

Este documento resume el direccionamiento utilizado en la práctica **VPN FortiGate + Jump Server + RD Web**.

## Tabla de direccionamiento

| Dispositivo / Interfaz | Dirección IP | Prefijo | Máscara | Gateway / Siguiente salto | Función |
|---|---:|---:|---:|---:|---|
| R-ISP | 198.51.100.1 | /30 | 255.255.255.252 | — | Gateway WAN del FortiGate |
| FortiGate - WAN-ISP (port1) | 198.51.100.2 | /30 | 255.255.255.252 | 198.51.100.1 | Salida WAN y terminación de VPN IPsec |
| FortiGate - WEB-LAN (port2) | 10.21.40.129 | /29 | 255.255.255.248 | — | Gateway de la red del Web Server |
| WEB-Server Ubuntu | 10.21.40.130 | /29 | 255.255.255.248 | 10.21.40.129 | Servidor Web Ubuntu |
| FortiGate - JUMP-LAN (port3) | 10.21.40.137 | /29 | 255.255.255.248 | — | Gateway de la red del Jump Server |
| JUMP-Server Windows Server | 10.21.40.138 | /29 | 255.255.255.248 | 10.21.40.137 | AD DS, DNS, IIS, RDS y RD Web |
| Cliente VPN FortiClient | Dinámica: 10.21.41.1 - 10.21.41.6 | /29 | 255.255.255.248 | Túnel VPN-RA-01 | Pool asignado a clientes VPN |
| Cliente VPN observado | 10.21.41.1 | /29 | 255.255.255.248 | Túnel VPN-RA-01 | Dirección virtual observada durante las pruebas |

## Subredes utilizadas

| Red | Rango de hosts | Broadcast | Uso |
|---|---|---|---|
| 198.51.100.0/30 | 198.51.100.1 - 198.51.100.2 | 198.51.100.3 | Enlace WAN R-ISP ↔ FortiGate |
| 10.21.40.128/29 | 10.21.40.129 - 10.21.40.134 | 10.21.40.135 | WEB-LAN |
| 10.21.40.136/29 | 10.21.40.137 - 10.21.40.142 | 10.21.40.143 | JUMP-LAN |
| 10.21.41.0/29 | 10.21.41.1 - 10.21.41.6 | 10.21.41.7 | Pool de clientes VPN |

## Ruta por defecto del FortiGate

- **Destino:** `0.0.0.0/0`
- **Gateway:** `198.51.100.1`
- **Interfaz:** `WAN-ISP (port1)`

## Nombres relevantes

- **Dominio:** `INFRA2.LOCAL`
- **Jump Server:** `WIN-LEU177NL1UK.INFRA2.LOCAL`
- **VPN:** `VPN-RA-01`

> La VPN asigna direcciones del rango `10.21.41.1-10.21.41.6/29` y permite el acceso hacia el Jump Server según las políticas configuradas en FortiGate.
