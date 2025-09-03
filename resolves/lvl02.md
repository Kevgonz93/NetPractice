# LEVEL 02

![Level 02](/img/lvl02.png)

En este nivel también tenemos **dos redes**. Para que funcionen correctamente deben compartir **la misma máscara de red** y asignar **IPs válidas dentro de su rango**.

## Goal 01: conectar Computer B con Computer A

Interface B1  
IP: 192.168.72.222

Interface A1  
Mask: 255.255.255.224

```
- Red: 192.168.72.192/27
- Hosts disponibles: 192.168.72.193 – 192.168.72.222
- Broadcast: 192.168.72.223
```

### Solución 01

Ambos deben usar la misma máscara (/27). Basta con asignar a cada uno un IP disponible dentro de la red.

✅ Interface B1  
Mask: 255.255.255.224

✅ Interface A1  
IP: 192.168.72.221

<details>
<summary>Ver diagrama de red</summary>
<div align="center">
<!-- prettier-ignore-start -->
```text
Red 1: 192.168.72.192/27
┌─────────────────────────────────────┐
│             (LAN /27)               │
│                                     │
│       A1 ──────────┬──────── B1     │
│  192.168.72.221/27 192.168.72.222/27│
│                                     │
│    Rango host: 192.168.72.193–.222  │
│    Broadcast : 192.168.72.223       │
└─────────────────────────────────────┘
```
<!-- prettier-ignore-end -->
</div>
</details>

> 💡 Recuerda: los hosts no pueden usar ni la dirección de red ni la de broadcast.

## Goal 02: conectar Computer D con Computer C

Interface D1  
Mask: /30

Interface C1  
Mask: 255.255.255.252

```
- Red: 127.0.0.0/30
- Hosts disponibles: 127.0.0.1 – 127.0.0.2
- Broadcast: 127.0.0.3
```

### Solución 02

La máscara /30 equivale a 255.255.255.252, por lo tanto ya coinciden. Los IPs 127.0.0.1 y 127.0.0.2 pertenecen al rango, aunque 127.0.0.x suele reservarse para loopback. Por eso vamos a copiar los IPs de la otra red.

<details>
<summary>Ver diagrama de red</summary>
<div align="center">
<!-- prettier-ignore-start -->

<!-- prettier-ignore-end -->
</div>
</details>

> 💡 Recuerda: los hosts no pueden usar ni la dirección de red ni la de broadcast.
