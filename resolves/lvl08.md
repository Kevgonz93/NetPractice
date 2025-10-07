# LEVEL 08

![Level 08](/img/lvl08.png)

En este ejercicio tenemos dos clientes (**host C** y **host D**) conectados a través del router **R2**, el cual a su vez se comunica con **R1**, que actúa como puerta de enlace hacia **Internet (I)**.

El objetivo es lograr la conectividad total entre los hosts internos y también con Internet.  
Para ello, debemos trabajar **dentro del rango 159.121.36.0/26** (el rango que Internet reconoce), dividiéndolo en **subredes /28** para los diferentes enlaces.

## Conceptos

- Todas las redes deben estar **dentro del bloque 159.121.36.0/26**, ya que Internet sólo tiene una ruta hacia ese rango.
- Dividimos el bloque /26 en subredes /28 (4 bloques de 16 direcciones).
- Cada enlace (R1↔R2, R2↔C y R2↔D) ocupará una de esas subredes.
- El objetivo es permitir rutas de ida y vuelta (**forward** y **reverse**) entre todos los dispositivos.

## Goal 01 : conectar host C con host D

### Subredes implicadas

**Subred R2 ↔ C**

```
Red:        159.121.36.16/28
Rango host: 159.121.36.17 – 159.121.36.30
Broadcast:  159.121.36.31
```

**Subred R2 ↔ D**

```
Red:        159.121.36.0/28
Rango host: 159.121.36.1 – 159.121.36.14
Broadcast:  159.121.36.15
```

### Solución 01

✅ Interface R22 (R2 ↔ C)  
IP : 159.121.36.30  
Mask : 255.255.255.240 (/28)

✅ Interface C1 (host C)  
IP : 159.121.36.17  
Mask : 255.255.255.240 (/28)  
Gateway : 159.121.36.30

✅ Interface R23 (R2 ↔ D)  
IP : 159.121.36.14  
Mask : 255.255.255.240 (/28)

✅ Interface D1 (host D)  
IP : 159.121.36.1  
Mask : 255.255.255.240 (/28)  
Gateway : 159.121.36.14

<details>
<summary>Ver diagrama de la red interna</summary>
<div align="center">
<!-- prettier-ignore-start -->

```text
LAN-C (159.121.36.16/28)                     LAN-D (159.121.36.0/28)
      C (17) ──┐                              ┌── (1) D
               │                              │
            R22 (30) ── R2 ── (14) R23 ───────┘
```

<!-- prettier-ignore-end -->
</div>
</details>

> 💡 Ambas LANs están bajo R2. Los hosts C y D se comunican correctamente a través de él.

## Goal 02 : conectar host C con Internet I

**Subred WAN (R1 ↔ R2)**

```
Red:        159.121.36.48/28
Rango host: 159.121.36.49 – 159.121.36.62
Broadcast:  159.121.36.63
```

### Solución 02

✅ Interface R13 (R1 ↔ R2)
IP : 159.121.36.62
Mask : 255.255.255.240 (/28)

✅ Interface R21 (R2 ↔ R1)
IP : 159.121.36.49
Mask : 255.255.255.240 (/28)

**RUTAS**

✅ Router R2
0.0.0.0/0 → 159.121.36.62

✅ Router R1
159.121.36.0/26 → 159.121.36.49
0.0.0.0/0 → 163.80.250.1

✅ Internet (I)
159.121.36.0/26 → 163.80.250.254

<details>
<summary>Ver diagrama de la solución (C ↔ Internet)</summary>
<div align="center">
<!-- prettier-ignore-start -->
```text
               Internet (I)
                159.121.36.0/26
                      │
               ┌──────┴──────┐
          R12 (163.80.250.12)
                      │
                R1 (159.121.36.62)
                      │
          ┌───────────┴───────────┐
      R2 (159.121.36.49)
       ├────────────┬────────────┤
   LAN-D        LAN-C        (C↔D internos)
159.121.36.0   159.121.36.16
````

<!-- prettier-ignore-end -->
</div>
</details>

> 💡 Internet ve todo el bloque 159.121.36.0/26 y lo envía a R1, quien a su vez reenvía a R2, garantizando el flujo completo.

## Goal 03 : conectar host D con Internet I

### Subredes implicadas

Mismas que en Goal 02 (todo dentro del /26 de Internet).

### Solución 03

✅ Interface D1 (host D)
IP : 159.121.36.1
Mask : 255.255.255.240 (/28)
Gateway : 159.121.36.14

✅ Interface R23 (R2 ↔ D)
IP : 159.121.36.14
Mask : 255.255.255.240 (/28)

**RUTAS**

✅ R2 : 0.0.0.0/0 → 159.121.36.62
✅ R1 : 159.121.36.0/26 → 159.121.36.49
✅ Internet : 159.121.36.0/26 → 163.80.250.254

<details>
<summary>Ver diagrama de la solución (D ↔ Internet)</summary>
<div align="center">
<!-- prettier-ignore-start -->
```text
D (1) → R23 (14) → R2 (49) → R13 (62) → R1 → Internet (I)
```
<!-- prettier-ignore-end -->
</div>
</details>

> 💡 Ahora los hosts C y D están en subredes separadas pero dentro del rango que Internet conoce, permitiendo comunicación de ida y vuelta.
