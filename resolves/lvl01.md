# LEVEL 01

![Level 01](/img/lvl01.png)

En este nivel tenemos **dos redes distintas**. Para que ambas puedan comunicarse correctamente, es necesario que usen **la misma máscara de red** y que sus **IPs pertenezcan al mismo rango de red** (según la máscara utilizada).

## Goal 01: conectar "my PC" con my little brother's computer

Interface B1  
IP : 104.97.23.12  
Mask : 255.255.255.0

Interface A1  
Mask : 255.255.255.0

```
- Red: 104.97.23.0/24
- Hosts disponibles: 104.97.23.1 – 104.97.23.254
- Broadcast: 104.97.23.255
```

### Solución 01

Como ambas interfaces comparten la misma máscara (/24), basta con asignar a A1 una IP libre dentro de la red.

✅ Interface A1  
IP : 104.97.23.11

<details>
<summary>Ver diagrama de la red</summary>
<div align="center">
<!-- prettier-ignore -->
```text
Red 1: 104.97.23.0/24
┌────────────────────────────────────┐
│             (LAN /24)              │
│                                    │
│       A1 ──────────┬──────── B1    │
│  104.97.23.11/24   104.97.23.12/24 │
│                                    │
│    Rango host: 104.97.23.1–.254    │
│    Broadcast : 104.97.23.255       │
└────────────────────────────────────┘
```
</div>
</details>

> 💡 Recuerda: los hosts no pueden usar ni la dirección de red ni la de broadcast.

## Goal 02: conectar "my MAC" con my little sister's computer

Interface D1  
Mask : 255.255.0.0

Interface C1  
IP : 211.191.30.75  
Mask : 255.255.0.0

```
- Red: 211.191.0.0/16
- Hosts disponibles: 211.191.0.1 – 211.191.255.254
- Broadcast: 211.191.255.255
```

### Solución 02

Misma lógica: al compartir máscara (/16), basta con elegir una IP libre dentro de la red.

✅ Interface D1  
IP : 211.191.30.74

<details>
<summary>Ver diagrama de la red</summary>
<div align="center">
<!-- prettier-ignore -->
```text
Red 2: 211.191.0.0/16
┌────────────────────────────────────┐
│             (LAN /16)              │
│                                    │
│       D1 ──────────┬──────── C1    │
│  211.191.30.74/16  211.191.30.75/16│
│                                    │
│    Rango host: 211.191.0.1–255.254 │
│    Broadcast : 211.191.255.255     │
└────────────────────────────────────┘
```
</div>
</details>

> 💡 Recuerda: los hosts no pueden usar ni la dirección de red ni la de broadcast.
