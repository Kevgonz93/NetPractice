# LEVEL 05

![Level 05](/img/lvl05.png)

En la imagen vemos dos subredes distintas, unidas por el mismo router R (The Mighty Router). Para que cada máquina pueda hablar con su router, deben compartir red y máscara con la interfaz del router a la que están conectadas. Luego, para que A y B se comuniquen entre sí, en cada host bastará con definir una ruta por defecto (default gateway) que apunte a su interfaz de router.

## Goal 01: Conectar "Machine A" con "The Mighty Router"

Observamos que los valores del interface R1, IP y máscara de red, son fijos; por lo que con esos valores vamos a asignar al interface A1.

Interface R1
IP : 80.238.151.126
Mask : 255.255.255.128 (/25)

```
- Red : 80.238.151.0 (/25)
- Host disponibles : 80.238.151.1 - 80.238.151.126
- Broadcast : 80.238.151.127
```

### Solución 01

Asignamos a A1 una IP libre del rango y la misma máscara:

✅ Interface A1
IP : 80.238.151.125
Mask : 255.255.255.128

<details>
<summary>Ver diagrama de red</summary>
<div align="center">
<!-- prettier-ignore-start -->
```text
Red 1: 80.238.151.0/25
┌─────────────────────────────────────────┐
│                (LAN /25)                │
│                                         │
│  A1 ──────────────── R1                 │
│ 80.238.151.125/25   80.238.151.126/25   │
│                                         │
│ Host range: .1 – .126   Broadcast: .127 │
└─────────────────────────────────────────┘
```
<!-- prettier-ignore-end -->
</div>
</details>

> 💡 Recuerda: los hosts no pueden usar ni la dirección de red ni la de broadcast.

## Goal 02 : Conectar "Machine B" con "The Mighty Router"

Así como realizamos la conexión anterior, hacemos la segunda conexión.

Interface R2
IP : 162.177.49.254
Mask : 255.255.192.0 (/18)

```
- Red       : 162.177.0.0/18
- Hosts     : 162.177.0.1 – 162.177.63.254
- Broadcast : 162.177.63.255
```

### Solución 02

Asignamos a B1 una IP libre del rango y la misma máscara:

✅ Interface B1
IP : 162.177.49.253
Mask : 255.255.192.0

<details>
<summary>Ver diagrama de red</summary>
<div align="center">
<!-- prettier-ignore-start -->
```text
Red 2: 162.177.0.0/18
┌─────────────────────────────────────────┐
│                (LAN /18)                │
│                                         │
│  B1 ──────────────── R2                 │
│ 162.177.49.253/18   162.177.49.254/18   │
│                                         │
│ Host range: 162.177.0.1 – 63.254        │
│ Broadcast : 162.177.63.255              │
└─────────────────────────────────────────┘
```
<!-- prettier-ignore-end -->
</div>
</details>

> 💡 Recuerda: los hosts no pueden usar ni la dirección de red ni la de broadcast.

## Goal 03 : Conectar "Machine A" con "Machine B"

Como son subredes distintas, A no conoce la red de B ni viceversa. Para que el tráfico cruce el router, definimos en cada host una ruta por defecto hacia su interfaz de router correspondiente.

### Solución 03

En Machine A (red 80.238.151.0/25):
✅ default ⇒ 80.238.151.126 (IP de R1)

En Machine B (red 162.177.0.0/18):
✅ default ⇒ 162.177.49.254 (IP de R2)

<details>
<summary>Flujo esperado (logs esperados)</summary>

forward way : A -> B (162.177.49.253)
on A : packet accepted
on A: send to gateway 80.238.151.126
on R : packet accepted
on R: send to R2
on B : packet accepted
on B: destination IP reached

reverse way : B -> A (80.238.151.125)
on B : packet accepted
on B: send to gateway 162.177.49.254
on R : packet accepted
on R: send to R1
on A : packet accepted
on A: destination IP reached

</details>
