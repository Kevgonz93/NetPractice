# LEVEL 04

![Level 04](/img/lvl04.png)

En este nivel tenemos **una misma red**, con un router y dos hosts.  
Para que los tres dispositivos puedan comunicarse, es necesario que **usen la misma máscara de red** y que sus **IPs pertenezcan al mismo rango de red** (según la máscara utilizada).

## Goal 01: Conectar "A Nice Host" con "Another Host"

Interface R2  
IP : 117.11.117.1  
Mask : 255.255.255.128 (/25)

Interface A1  
IP : 117.11.117.132

```
- Red: 117.11.117.129/25
- Hosts disponibles: 117.11.117.129 – 117.11.117.254
- Broadcast: 117.11.117.255
```

### Solución 01

✅ Interface A1  
IP : 117.11.117.132  
Mask : 255.255.255.128

✅ Interface B1  
IP : 117.11.117.131  
Mask : 255.255.255.128

<details>
<summary>Ver diagrama de red</summary>
<div align="center">
<!-- prettier-ignore-start -->
```text
Red 1: 117.11.117.129/25
┌─────────────────────────────────────┐
│              (LAN /25)              │
│                                     │
│   A1 ────┬──── B1 ────┬──── R2      │
│ .132/25  │   .131/25  │   .1/25     │
│                                     │
│   Rango host: 117.11.117.129–.254   │
│   Broadcast : 117.11.117.255        │
└─────────────────────────────────────┘
```
<!-- prettier-ignore-end -->
</div>
</details>

> 💡 Recuerda: los hosts no pueden usar ni la dirección de red ni la de broadcast.

## Goal 02 : Conectar "A Nice Host" con "My_Gate"

Dado que las interfaces **A1** y **B1** comparten la misma máscara (/25) y rango de red, será necesario configurar **R1** en ese mismo rango.

### Solución 02

✅ Interface R1
IP : 117.11.117.130
Mask : 255.255.255.128

<details>
<summary>Ver diagrama de red</summary>
<div align="center">
<!-- prettier-ignore-start -->
```text
Red 1: 117.11.117.129/25
┌────────────────────────────────────────────┐
│                  (LAN /25)                 │
│                                            │
│   A1 ────┬──── B1 ────┬──── R2 ──── R1     │
│ .132/25  │   .131/25  │   .1/25   .130/25  │
│                                            │
│    Rango host: 117.11.117.129–.254         │
│    Broadcast : 117.11.117.255              │
└────────────────────────────────────────────┘
```

<!-- prettier-ignore-end -->
</div>
</details>

> 💡 Recuerda: los hosts no pueden usar ni la dirección de red ni la de broadcast.

## Goal 03 : Conectar "Another Host" con "My_Gate"

Con las modificaciones anteriores, todos los dispositivos pertenecen a **la misma red /25**, por lo que la conexión será automática.

### Solución 03

Nada que añadir: la red ya está correctamente configurada.
