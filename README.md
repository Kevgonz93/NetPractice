# 🧠 NetPractice — Guías y Soluciones (42 Madrid)

Repositorio con resoluciones completas y comentadas de los ejercicios de NetPractice. Incluye explicaciones de los conceptos clave, tablas de subredes, rutas entre routers y logs de verificación.

---

## Índice

### 🧩 Niveles resueltos (/levels)

| Nivel | Descripción breve                             | Enlace                        |
| :---- | :-------------------------------------------- | :---------------------------- |
| 01    | Conexión entre dos hosts en una misma red     | [lvl01.md](./levels/lvl01.md) |
| 02    | Router como intermediario entre dos LAN       | [lvl02.md](./levels/lvl02.md) |
| 03    | Conexión a Internet (gateway)                 | [lvl03.md](./levels/lvl03.md) |
| 04    | Máscaras de red /25 y /26                     | [lvl04.md](./levels/lvl04.md) |
| 05    | Subredes y broadcast                          | [lvl05.md](./levels/lvl05.md) |
| 06    | Rutas y gateway predeterminado                | [lvl06.md](./levels/lvl06.md) |
| 07    | Rutas cruzadas entre routers                  | [lvl07.md](./levels/lvl07.md) |
| 08    | Divisiones de rango /26 → /28                 | [lvl08.md](./levels/lvl08.md) |
| 09    | Enlace punto a punto y retorno desde Internet | [lvl09.md](./levels/lvl09.md) |
| 10    | Configuración final y conectividad global     | [lvl10.md](./levels/lvl10.md) |

### 📘 Conceptos clave

> Esta sección resume los fundamentos teóricos necesarios para resolver los ejercicios de NetPractice.  
> Cada concepto tiene su desarrollo completo en los ficheros de `/concepts/`.

#### Dirección IP (IPv4) — [concepts.md](./concepts/concepts.md)

- IPv4: 32 bits → formato `X.X.X.X` (0–255).
- Partes: **Red** + **Host** (el host debe ser único).
- Tipos: pública, privada (10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16), loopback (127.0.0.1), broadcast.
- Reglas:
  - ❌ No usar IP de red ni de broadcast en hosts.
  - Ejemplo `/24`:
    - Red: `192.168.1.0`
    - Rango: `192.168.1.1 – 192.168.1.254`
    - Broadcast: `192.168.1.255`

#### Máscara y CIDR — [concepts.md](./concepts/concepts.md)

- La **máscara** define cuántos bits son de red vs host.
- **CIDR** (notación /prefijo):
  - `/16` → `255.255.0.0` → 65,536 direcciones (65,534 hosts utilizables)
  - `/24` → `255.255.255.0` → 256 direcciones (254 hosts)
  - `/30` → `255.255.255.252` → 4 direcciones (2 hosts)
- Por qué se restan 2: **red** (todos 0) y **broadcast** (todos 1).

**Tabla útil de CIDR comunes**

| Prefijo | Máscara         | Direcciones | Hosts utilizables |
| :-----: | :-------------- | ----------: | ----------------: |
|   /30   | 255.255.255.252 |           4 |                 2 |
|   /29   | 255.255.255.248 |           8 |                 6 |
|   /28   | 255.255.255.240 |          16 |                14 |
|   /27   | 255.255.255.224 |          32 |                30 |
|   /26   | 255.255.255.192 |          64 |                62 |
|   /25   | 255.255.255.128 |         128 |               126 |
|   /24   | 255.255.255.0   |         256 |               254 |
|   /23   | 255.255.254.0   |         512 |               510 |
|   /22   | 255.255.252.0   |       1,024 |             1,022 |
|   /21   | 255.255.248.0   |       2,048 |             2,046 |
|   /20   | 255.255.240.0   |       4,096 |             4,094 |
|   /16   | 255.255.0.0     |      65,536 |            65,534 |

#### Subredes (subnetting) — [concepts.md](./concepts/concepts.md)

- “Prestar” bits de host para crear más redes:  
  Ej.: `/24` → 4 subredes → **+2 bits** → `/26`
- Tamaño de bloque = `2^(bits_host)` direcciones  
  (Hosts utilizables = bloque − 2)
- Regla de cálculo rápida con `/26`:
  - Incremento: **64** en el 4.º octeto → subredes: `.0-.63`, `.64-.127`, `.128-.191`, `.192-.255`
  - Red = inicio del bloque | Broadcast = siguiente inicio − 1

#### Router — [concepts.md](./concepts/concepts.md)

- Interconecta **redes distintas**.
- Cada interfaz:
  - IP válida **en esa subred** + máscara correcta.
- Buenas prácticas:
  - Asignar gateway del host a la **IP del router** dentro de su subred.
  - Mantener consistencia (p. ej., usar `.1` o `.254` para gateway).

#### Default Gateway — [concepts.md](./concepts/concepts.md)

- Puerta de salida del host hacia **otras redes**.
- Debe estar **en la misma subred** que el host.
- Si falta el gateway → sólo hay conectividad **dentro** de la subred.

#### Logs típicos en NetPractice — [concepts.md](./concepts/concepts.md)

- **Wrong network** → máscara/IP mal; creen estar juntos cuando no.
- **No route to host** → falta gateway o interfaz en router.
- **Duplicate IP** → dos equipos con la misma IP (colisión).
- **Destination IP reached** → ✅ todo correcto.

#### Estrategia para resolver — [notes.md](./concepts/notes.md)

1. Lee el objetivo (Goal).
2. Cuenta **cuántas redes** hay (fíjate en máscaras).
3. Asigna IPs únicas y válidas (evita red/broadcast).
4. Configura **todas** las interfaces del router.
5. Añade gateway en hosts que salgan de su red.
6. Revisa logs y corrige.
7. Avanza por etapas (intra-red → inter-red).
8. Exporta cada nivel: **Get my config**.

### 📄 Documentos originales

- 🧿 Introducción: [intro.md](./concepts/intro.md)
- 📚 Mapa de conceptos: [concepts.md](./concepts/concepts.md)
- 📝 Revisión del Subject / Pautas: [notes.md](./concepts/notes.md)

---

## 🧩 Sobre este repositorio

Este repositorio fue creado para **comprender y documentar los fundamentos de redes** mediante los ejercicios de **NetPractice (42 School)**. Cada nivel incluye:

- Explicación visual (`/img`)
- Tablas de subredes y rangos
- Logs de verificación y rutas comentadas

---

## 👨‍💻 Autor

<p align="center">
  <b>Kevin Gonzales</b>  
  <br>Estudiante en <a href="https://www.42madrid.com">42 Madrid</a>  
  <br><a href="https://github.com/Kevgonz93">github.com/Kevgonz93</a>
</p>
