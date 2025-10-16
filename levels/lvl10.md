# LEVEL 10

![Level 10](/img/lvl10.png)

En este ejercicio tenemos dos routers (**R1** y **R2**) interconectados, con varias subredes locales y una conexión a **Internet (I)**.  
El objetivo es conseguir conectividad total entre todos los hosts (**H1, H2, H3, H4**) y también con Internet.

La clave de este nivel es:

- Dividir el rango **132.159.27.0/24** en subredes más pequeñas.
- Configurar correctamente las rutas en **R1** y **R2**.
- Añadir en Internet una ruta agregada que cubra **todas** las subredes internas.

---

## Goal 01 : Conectar host H1 con host H2

LAN H1/H2

```
Red:        132.159.27.0/25
Rango host: 132.159.27.1 – 132.159.27.126
Broadcast:  132.159.27.127
```

### Solución 01

✅ Interface R11 (R1 ↔ LAN H1/H2)  
IP : 132.159.27.1  
Mask : 255.255.255.128 (/25)

✅ Interface H11 (host H1)  
IP : 132.159.27.2  
Mask : 255.255.255.128 (/25)  
Route : 0.0.0.0/0 → 132.159.27.1

✅ Interface H21 (host H2)  
IP : 132.159.27.3  
Mask : 255.255.255.128 (/25)  
Route : default → 132.159.27.1

<details>
<summary>Ver diagrama de la LAN H1/H2</summary>
<div align="center">
<!-- prettier-ignore-start -->
```text
LAN H1/H2 132.159.27.0/25
H1 (132.159.27.2) ─┐
                    ├── Switch ── R11 (132.159.27.1)
H2 (132.159.27.3) ─┘
```
<!-- prettier-ignore-end -->
</div>
</details>

> Al compartir la misma subred /25, H1 y H2 se comunican directamente a través del switch.

## Goal 02 : Conectar host H3 con host H4

LAN H3/H4 (bajo R2)

```
Red H3: 132.159.27.192/26
Rango: 132.159.27.193 – 132.159.27.254
Broadcast: 132.159.27.255

Red H4: 132.159.27.128/26
Rango: 132.159.27.129 – 132.159.27.190
Broadcast: 132.159.27.191
```

### Solución 02

✅ Interface R22 (R2 ↔ H3)
IP : 132.159.27.193
Mask : 255.255.255.192 (/26)

✅ Interface H31 (host H3)
IP : 132.159.27.194
Mask : 255.255.255.192 (/26)
Route : 0.0.0.0/0 → 132.159.27.193

✅ Interface R23 (R2 ↔ H4)
IP : 132.159.27.129
Mask : 255.255.255.192 (/26)

✅ Interface H41 (host H4)
IP : 132.159.27.131
Mask : 255.255.255.192 (/26)
Route : default → 132.159.27.129

<details>
<summary>Ver diagrama de la LAN H3/H4</summary>
<div align="center">
<!-- prettier-ignore-start -->
```text
          R2
     ┌────┴────┐
R22 132.159.27.193   R23 132.159.27.129
   │                    │
H3 132.159.27.194   H4 132.159.27.131
(default → GW local)
```
<!-- prettier-ignore-end -->
</div>
</details>
> Ambas LAN están separadas bajo R2, cada una con su gateway propio.

## Goal 03 : Conectar host H1 con Internet (I)

WAN R1 ↔ Internet

```
Red:        163.172.250.0/28
Rango host: 163.172.250.1 – 163.172.250.14
Broadcast:  163.172.250.15
```

### Solución 03

Solución 03

✅ Interface R12 (R1 ↔ Internet)
IP : 163.172.250.10
Mask : 255.255.255.240 (/28)

✅ Interface Internet
IP : 163.172.250.12
Mask : 255.255.255.240 (/28)

✅ Rutas
• R1 → 0.0.0.0/0 → 163.172.250.1
• Internet → 132.159.27.0/24 → 163.172.250.12 (retorno)

<details>
<summary>Ver diagrama de la conexión a Internet</summary>
<div align="center">
<!-- prettier-ignore-start -->
```text
H1 → GW 132.159.27.1 → R12 (163.172.250.10)
Internet retorna 132.159.27.0/24 → 163.172.250.12
```
<!-- prettier-ignore-end -->
</div>
</details>

> Este enlace une las redes internas del router R2 con el resto del sistema y con Internet.

## Goal 04 : Conectar R1 con R2

Enlace punto a punto entre routers

```
Red: 132.159.27.252/30
Rango host: 132.159.27.253 – 132.159.27.254
Broadcast: 132.159.27.255
```

### Solución 04

✅ Interface R13 (R1 ↔ R2)
IP : 132.159.27.254
Mask : 255.255.255.252 (/30)

✅ Interface R21 (R2 ↔ R1)
IP : 132.159.27.253
Mask : 255.255.255.252 (/30)

✅ Rutas
• R1 → 132.159.27.128/26 → 132.159.27.253
• R1 → 132.159.27.192/26 → 132.159.27.253
• R2 → 0.0.0.0/0 → 132.159.27.254

<details>
<summary>Ver diagrama del enlace R1↔R2</summary>
<div align="center">
<!-- prettier-ignore-start -->
```text
R1 (132.159.27.254) ───── R2 (132.159.27.253)
```
<!-- prettier-ignore-end -->
</div>
</details>

> Este enlace une las redes internas del router R2 con el resto del sistema y con Internet.

## Goal 05 : Conectar host H4 con Internet (I)

Solución 05

✅ Host H4 → default → 132.159.27.129
✅ R2 → 0.0.0.0/0 → 132.159.27.254
✅ R1 → 0.0.0.0/0 → 163.172.250.1
✅ Internet → 132.159.27.0/24 → 163.172.250.12

<details>
<summary>Ver diagrama de la conexión H4 ↔ Internet</summary>
<div align="center">
<!-- prettier-ignore-start -->
```text
H4 → R23 → R21 → R13 → R12 → Internet
```
<!-- prettier-ignore-end -->

> H4 sale por R23 (su gateway), pasa por R2 y luego a R1, que reenvía el tráfico a Internet.

## Goal 06 : Conectar host H3 con Internet (I)

### Solución 06

✅ Host H3 → default → 132.159.27.193
✅ R2 → 0.0.0.0/0 → 132.159.27.254
✅ R1 → 0.0.0.0/0 → 163.172.250.1
✅ Internet → 132.159.27.0/24 → 163.172.250.12

<details>
<summary>Ver diagrama de la conexión H3 ↔ Internet</summary>
<div align="center">
<!-- prettier-ignore-start -->
```text
H3 → R22 → R21 ─── R13 → R12 → Internet
I retorna 132.159.27.0/24 → 163.172.250.12
```
<!-- prettier-ignore-end -->
</div>
</details>

## Goal 07 : Conectividad total

Una vez configuradas las interfaces y rutas anteriores, todos los objetivos quedan completados ✅

| Dispositivo     | IP             | Máscara         | Gateway / Ruta                                                                                        |
| --------------- | -------------- | --------------- | ----------------------------------------------------------------------------------------------------- |
| **H1**          | 132.159.27.2   | 255.255.255.128 | 132.159.27.1                                                                                          |
| **H2**          | 132.159.27.3   | 255.255.255.128 | 132.159.27.1                                                                                          |
| **H3**          | 132.159.27.194 | 255.255.255.192 | 132.159.27.193                                                                                        |
| **H4**          | 132.159.27.131 | 255.255.255.192 | 132.159.27.129                                                                                        |
| **R11**         | 132.159.27.1   | 255.255.255.128 | —                                                                                                     |
| **R12**         | 163.172.250.10 | 255.255.255.240 | —                                                                                                     |
| **R13**         | 132.159.27.254 | 255.255.255.252 | —                                                                                                     |
| **R21**         | 132.159.27.253 | 255.255.255.252 | —                                                                                                     |
| **R22**         | 132.159.27.193 | 255.255.255.192 | —                                                                                                     |
| **R23**         | 132.159.27.129 | 255.255.255.192 | —                                                                                                     |
| **R1 – Routes** | —              | —               | 132.159.27.128/26 → 132.159.27.253<br>132.159.27.192/26 → 132.159.27.253<br>0.0.0.0/0 → 163.172.250.1 |
| **R2 – Routes** | —              | —               | 0.0.0.0/0 → 132.159.27.254                                                                            |
| **Internet**    | 163.172.250.12 | 255.255.255.240 | 132.159.27.0/24 → 163.172.250.12                                                                      |

> ✅ Todos los hosts alcanzan entre sí y con Internet gracias a la agregación /24 en la ruta de Internet
