# LEVEL 09

![Level 09](/img/lvl09.png)

En este ejercicio tenemos tres LAN (A/B bajo R1; C y D bajo R2), un enlace punto a punto R1↔R2 y la salida a Internet (I) desde R1.
El objetivo es lograr conectividad total entre los hosts y con Internet. La clave es:
• Asignar IPs y máscaras correctas en cada LAN.
• Configurar rutas cruzadas entre R1 y R2.
• Añadir en Internet las rutas de retorno hacia las LAN internas.

## Goal 01 : Conectar host A (meson) con host B (ion)

LAN A/B

```
Red: 42.5.4.0/25
Rango host: 42.5.4.1 – 42.5.4.126
Broadcast: 42.5.4.127
```

### Solución 01

Solución 01

✅ Interface R11 (R1 ↔ LAN A/B)
IP : 42.5.4.1
Mask : 255.255.255.128 (/25)

✅ Interface A1 (host A)
IP : 42.5.4.2
Mask : 255.255.255.128 (/25)

✅ Interface B1 (host B)
IP : 42.5.4.3
Mask : 255.255.255.128 (/25)

<details>
<summary>Ver diagrama de la LAN A/B</summary>
<div align="center">
<!-- prettier-ignore-start -->
```text
LAN A/B 42.5.4.0/25
A (42.5.4.2) ──┐
               ├── Switch ── R11 (42.5.4.1)
B (42.5.4.3) ──┘
```
<!-- prettier-ignore-end -->
</div>
</details>

> Al estar en la misma subred, A y B se conectan directamente.

## Goal 02 : conectar host C (cation) con host D (gluon)

LAN C

```
Red: 78.3.4.0/24
Rango host: 78.3.4.1 – 78.3.4.254
Broadcast: 78.3.4.255
```

LAN D

```
Red:        75.97.128.0/18
Rango host: 75.97.128.1 – 75.97.191.254
Broadcast:  75.97.191.255
```

### Solución 02

✅ Interface R22 (R2 ↔ LAN C)
IP : 78.3.4.1
Mask : 255.255.255.0 (/24)

✅ Interface C1 (host C)
IP : 78.3.4.2
Mask : 255.255.255.0 (/24)
Gateway : 78.3.4.1

✅ Interface R23 (R2 ↔ LAN D)
IP : 75.97.158.184
Mask : 255.255.192.0 (/18)

✅ Interface D1 (host D)
IP : 75.97.158.183
Mask : 255.255.192.0 (/18)
Route (fija): default → 75.97.158.184

<details>
<summary>Ver diagrama de la solución (D ↔ Internet)</summary>
<div align="center">
<!-- prettier-ignore-start -->
           R2
     ┌─────┴─────┐
R22 78.3.4.1   R23 75.97.158.184
   │                │
 C 78.3.4.2      D 75.97.158.183
                 (default → 75.97.158.184)
<!-- prettier-ignore-end -->
</div>
</details>

> Ambos cuelgan de R2, así que C y D se alcanzan vía R2.

## Goal 03 : conectar host A con Internet (I)

WAN R1 ↔ Internet

```
Red:        163.172.250.0/28
Rango host: 163.172.250.1 – 163.172.250.14
Broadcast:  163.172.250.15
```

### Solución 03

✅ Interface R12 (R1 ↔ Internet)
IP : 163.172.250.12
Mask : 255.255.255.240 (/28)

✅ Rutas
• Host A: 0.0.0.0/0 → 42.5.4.1
• R1 (proton): 0.0.0.0/0 → 163.172.250.1
• Internet (I): 42.5.4.0/25 → 163.172.250.12 (retorno)

details>

<summary>Ver diagrama de la solución (D ↔ Internet)</summary>
<div align="center">
<!-- prettier-ignore-start -->
A 42.5.4.2 → GW 42.5.4.1 (R1) → 163.172.250.1 (I)
I retorna 42.5.4.0/25 → 163.172.250.12
<!-- prettier-ignore-end -->
</div>
</details>

## Goal 04 : conectar host A con host D

Enlace R1 ↔ R2

```
Red: 2.3.4.252/30
Rango host: 2.3.4.253 – 2.3.4.254
Broadcast: 2.3.4.255
```

### Solución 04

✅ Interface R13 (R1 ↔ R2)
IP : 2.3.4.253
Mask : 255.255.255.252 (/30)

✅ Interface R21 (R2 ↔ R1)
IP : 2.3.4.254
Mask : 255.255.255.252 (/30)

✅ Rutas
• R1: 75.97.128.0/18 → 2.3.4.254
• R2: 0.0.0.0/0 → 2.3.4.253
• D: ya tiene default → 75.97.158.184 (fija)

<details>
<summary>Ver diagrama de la solución (D ↔ Internet)</summary>
<div align="center">
<!-- prettier-ignore-start -->
```text
A → R11 → R13 (2.3.4.253) ─── R21 (2.3.4.254) → R23 → D
R1 conoce 75.97.128.0/18 vía 2.3.4.254
R2 manda por defecto hacia 2.3.4.253
```
<!-- prettier-ignore-end -->
</div>
</details>

## Goal 05 : conectar host B con host C

Subredes

LAN A/B y LAN C (ya definidas)
Enlace R1 ↔ R2 /30 (arriba)

### Solución 05

✅ Rutas
• Host B: default → 42.5.4.1
• R1: 78.3.4.0/24 → 2.3.4.254
• R2: 0.0.0.0/0 → 2.3.4.253
• C: gateway local 78.3.4.1

<details>
<summary>Ver diagrama de la solución (D ↔ Internet)</summary>
<div align="center">
<!-- prettier-ignore-start -->
```text
B → R11 → R13 ─── R21 → R22 → C
R1 conoce 78.3.4.0/24 vía 2.3.4.254
R2 por defecto → 2.3.4.253
```
<!-- prettier-ignore-end -->
</div>
</details>

## Goal 06 : conectar host C con Internet (I)

Subredes

LAN C (ya definida)
Enlace R1 ↔ R2 /30
WAN R1 ↔ Internet /28

### Solución 06

✅ Rutas
• C: 0.0.0.0/0 → 78.3.4.1
• R2: 0.0.0.0/0 → 2.3.4.253
• R1: 0.0.0.0/0 → 163.172.250.1
• Internet (I): 78.3.4.0/24 → 163.172.250.12 (retorno)

<details>
<summary>Ver diagrama de la solución (D ↔ Internet)</summary>
<div align="center">
<!-- prettier-ignore-start -->
```text
C → R22 → R21 ─── R13 → R12 → Internet
I retorna 78.3.4.0/24 → 163.172.250.12
```
<!-- prettier-ignore-end -->
</div>
</details>
