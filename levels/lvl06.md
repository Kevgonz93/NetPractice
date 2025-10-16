# LEVEL 06

![Level 06](/img/lvl06.png)

Tenemos una LAN conectada al router **R** (dos interfaces: R1 hacia el switch y R2 hacia el ISP). El objetivo es que el host **A (webserv)** tenga conectividad con **Internet**.  
Para ello, debemos **alinear la máscara de la LAN**, **configurar el gateway en A**, **poner una ruta por defecto en el router**, y finalmente **corregir la ruta en Internet I** para que pueda devolver tráfico hacia la LAN.

## Conceptos: LAN vs WAN

- **LAN (Local Area Network)**: Red de área local. Conecta dispositivos dentro de un espacio limitado (como una casa, oficina o laboratorio). Generalmente es más rápida, privada y bajo control del usuario. En este ejercicio, la LAN es `78.119.190.128/25`, donde se encuentran el host **A** y el router **R1**.

- **WAN (Wide Area Network)**: Red de área amplia. Conecta múltiples redes locales a través de grandes distancias, como ocurre con Internet. Normalmente está gestionada por un proveedor (ISP). En este ejercicio, la red `163.172.250.0/28` conecta el router **R2** con el ISP y, a través de este, con Internet.

Interface R1 (LAN)
— IP: 78.119.190.254
— Mask: 255.255.255.128 (/25)

```
Red (LAN): 78.119.190.128/25
Hosts:     78.119.190.129 – 78.119.190.254
Broadcast: 78.119.190.255
```

Interface R2 (WAN)
— IP: 163.172.250.12
— Mask: 255.255.255.240 (/28)

```
Red (WAN): 163.172.250.0/28
Hosts:     163.172.250.1 – 163.172.250.14
Broadcast: 163.172.250.15
(El next-hop del ISP suele ser 163.172.250.1)
```

## Goal 01: Conectar "webserv.non-real.com" con "Somewhere on the Net"

Tendremos que hacer las conexiones del LAN (IPs y máscaras correctas). También modificaremos las rutas para que puedan establecer las conexiones entre las subredes (WAN y LAN).

✅ Interface A1 (host A)
IP: 78.119.190.227 (se mantiene, está dentro del rango /25)
Mask: 255.255.255.128 (/25)

✅ Ruta de host A (default gateway)
0.0.0.0/0 => 78.119.190.254

✅ Ruta del router R hacia Internet
0.0.0.0/0 => 163.172.250.1 (next-hop del ISP en la red /28)

✅ Ruta de Internet I hacia la LAN
78.119.190.128/25 => 163.172.250.12

> Es importante modificar la ruta de Internet I hacía la LAN. En caso contrario, Host A podrá salir pero no volver.

<details>
<summary>Ver diagrama de la LAN</summary>
<div align="center">
<!-- prettier-ignore-start -->
```text
LAN 78.119.190.128/25
┌────────────────────────────────────────────────┐
│                 (Switch sw-1)                  │
│                                                │
│  A1 (host A) --------------------- R1 (router) │
│       78.119.190.227/25 78.119.190.254/25      │
│                                                │
│            GW de A: 78.119.190.254             │
│       Rango host: 78.119.190.129 – .254        │
│           Broadcast: 78.119.190.255            │
└────────────────────────────────────────────────┘
```
<!-- prettier-ignore-end -->
</div>
</details>

<details>
<summary>Ver diagrama WAN/ISP</summary>
<div align="center">
<!-- prettier-ignore-start -->
```text
WAN 163.172.250.0/28
┌───────────────────────────────────────────────┐
│    ISP GW: 163.172.250.1 ← default en R       │
│                                               │
│                  R2 (router)                  │
│              163.172.250.12/28                │tu so
└───────────────────────────────────────────────┘
```
<!-- prettier-ignore-end -->
</div>
</details>

###
