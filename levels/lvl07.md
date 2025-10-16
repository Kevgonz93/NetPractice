# LEVEL 07

![Level 07](/img/lvl07.png)

En esta práctica, tenemos una red con un problema de direcciones IP duplicadas en la LAN. El objetivo es corregir las IPs para evitar conflictos y restaurar la conectividad entre el host **B (dbserv)** y el resto de la red, incluyendo el acceso a Internet.  
Para ello, debemos **reasignar las IPs en la LAN**, **configurar correctamente las máscaras y gateways**, y **ajustar las rutas estáticas en el router** para que el tráfico fluya adecuadamente.

## Goal 01 : Conectar Host A con Host C

El problema es que C1 y R11 comparten la misma IP (113.198.14.1), por lo que “se alcanza la IP correcta pero el host es erróneo”. Separaremos en 3 subredes, reasignaremos IPs y configuraremos las rutas entre R1 y R2.

Subred 1 - LAN (A <-> R11)

```
Red:        113.198.14.0/26
Rango host: 113.198.14.1 – 113.198.14.62
Broadcast:  113.198.14.63
```

Subred 2 - R1 <-> R2

```
Red:        113.198.14.252/30
Rango host: 113.198.14.253 – 113.198.14.254
Broadcast:  113.198.14.255
```

Subred 3 - LAN (R22 <-> C1)

```
Red:        113.198.14.128/26
Rango host: 113.198.14.129 – 113.198.14.190
Broadcast:  113.198.14.191
```

### Solución 01

✅ Interface A1 (**host A**)
IP : 113.198.14.2
Mask : 255.255.255.192 (/26)
Gateway : 0.0.0.0/0 => 113.198.14.1

✅ Interface R11 (**router R1 <-> hacia A**)
IP : 113.198.14.1
Mask : 255.255.255.192 (/26)

✅ Interface R12 (**router R1 <-> hacia R2**)
IP : 113.198.14.254
Mask : 255.255.255.252 (/30)

✅ Interface R21 (**router R2 <-> hacia R1**)
IP : 113.198.14.253
Mask : 255.255.255.252 (/30)

✅ Interface R22 (**router R2 <-> hacia C**)
IP : 113.198.14.129
Mask : 255.255.255.192 (/26)

✅ Interface C1 (**host C**)
IP : 113.198.14.130 (dejamos de usar .1 para evitar el duplicado)
Mask : 255.255.255.192 (/26)
Gateway : 0.0.0.0/0 => 113.198.14.129

Rutas:

✅ R1:
113.198.14.128/26 => 113.198.14.253 (next-hop: R21)
✅ R2:
113.198.14.0/26 => 113.198.14.254 (next-hop: R12)

> No hace falta ruta por defecto (Internet) para este objetivo. Solo requerimos rutas de ida y vuelta entre las LAN.

<details>
<summary>Ver diagrama de la red</summary>
<div align="center">
<!-- prettier-ignore -->
```text
# Subred 1 — LAN-A (113.198.14.0/26)
A (113.198.14.2/26) ── Switch ── R11 (113.198.14.1/26)
                        │
                        │
# Subred 2 — Enlace R1↔R2 (113.198.14.252/30)
                R12 (…14.254/30) ─── R21 (…14.253/30)
                                    │
                                    │
# Subred 3 — LAN-C (113.198.14.128/26)
                            R22 (…14.129/26) ── Switch ── C (…14.130/26)
```
</div>

Gateways:
• A → 113.198.14.1
• C → 113.198.14.129

Rutas:
• R1: 113.198.14.128/26 → 113.198.14.253
• R2: 113.198.14.0/26 → 113.198.14.254

<!-- prettier-ignore-end -->

</details>

> 💡 **Por qué fallaba:** A “llegaba” a `113.198.14.1`, pero esa IP estaba **duplicada** (R11 y C1), así que el simulador indicaba _Correct IP reached but wrong host_.
> Con IPs únicas por subred y **rutas cruzadas** entre los routers, A y C se comunican correctamente.
