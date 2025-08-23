# 📚 Mapa de conceptos de NetPractice

## 1️⃣ Dirección IP (IPv4)

### ¿Qué es IP?

Una IP (Internet Protocol address) es un identificador único que se le asigna a cada dispositivo conectado a una red.

> IPv4 (Internet Protocol version 4) es la versión más usada, aunque ya existe IPv6.

### Formato

Usa direcciones de 32 bits, que se representan en 4 bloques decimales separados por puntos:

```bash
 192.168.1.42
```

> Cada bloque va de 0 a 255, porque cada bloque son 8 bits (un byte).
> Total de combinaciones posibles: 2³² ≈ 4.3 mil millones de direcciones.

- Formato: X.X.X.X (4 números de 0–255).
- Cada IP tiene dos partes:
  - Network (red): identifica la red. Todos los hosto de la misma red tienen en común ese fragmento.
  - Host (anfitrión): identifica la máquina dentro de la red. **ÚNICO E IRREPETIBLE**.
- Ejemplo: 192.168.1.15/24
  - Red: 192.168.1.0
  - Host: 15
  - Broadcast: 192.168.1.255

> **🔑 Importante: no puedes asignar una IP de red ni de broadcast a un host.**

### Tipos de IPv4

- Pública 🌍 → visible en Internet (ej: la que te da tu proveedor).
- Privada 🏠 → solo dentro de redes locales (ej: 192.168.x.x, 10.x.x.x, 172.16–31.x.x).
- Loopback 🔄 → 127.0.0.1, se usa para “hablar consigo mismo”.
- Broadcast 📡 → se envía a todos los dispositivos de la red.
- Red 🌐 → identifica a toda la red (no a un host).

---

## 2️⃣ Máscara de red

### ¿Qué es?

La máscara de red (subnet mask) es un número que indica qué parte de una dirección IP corresponde a la red y qué parte al host.

Se usa junto con la IP para saber:

- Qué máquinas están en la misma red (y pueden comunicarse directamente).
- Qué máquinas están en otra red (y necesitan un router para comunicarse).

### 📊 Ejemplo básico

```bash
 IP: 192.168.1.42
 Máscara: 255.255.255.0
```

En binario:

```bash
 IP:      11000000.10101000.00000001.00101010 (192.168.1.42)
 Máscara: 11111111.11111111.11111111.00000000 (255.255.255.0)
```

### CIDR

CIRD o Classless Inter-Domain Routing (Enrutamiento Inter-Dominio Sin Clases) es un método de notación para representar direcciones IP y su máscara de red. Fue creado para reemplazar las clases de red antiguas: Clase A (/8), B (/16) y C (/24) que eran demasiado rígidas.

- /16 = 255.255.0.0 → 65536 IPs posibles (65534 host utilizables).
- /24 → 255.255.255.0 → 256 IPs posibles (254 hosts utilizables).
- /30 → 255.255.255.252 → 4 IPs posibles (2 hosts utilizables).

> ⚠️ ¿Por qué se restan 2?
> Una dirección se reserva para la red (todos los bits de host en 0).
> Otra se reserva para el broadcast (todos los bits de host en 1).

### 📡 ¿Qué es el broadcast?

Es una dirección especial dentro de una red que se usa para enviar mensajes a todos los dispositivos de esa red al mismo tiempo.

- En una red, siempre hay:

  - IP de red → identifica la red completa (no se asigna a hosts).
  - IPs de hosts → se asignan a dispositivos.
  - IP de broadcast → dirección reservada para comunicarse con todos los hosts de la red.

#### 📊 Ejemplo

Red: 192.168.1.0/24 → máscara 255.255.255.0
• IP de red: 192.168.1.0
• Broadcast: 192.168.1.255
• Hosts válidos: 192.168.1.1 → 192.168.1.254

👉 Si un dispositivo envía un mensaje a 192.168.1.255, todos los dispositivos con IP 192.168.1.x recibirán ese mensaje.

**La IP de broadcast nunca se asigna a un host.**

- Regla:
  - Cuantos más bits de red, menos hosts.
  - Cuantos menos bits de red, más hosts.

⸻

## 3️⃣ Subredes

### 📌 ¿Qué es una subred?

- Una subred (subnetwork) es una porción más pequeña de una red grande.
- Se crean modificando la máscara de red para “prestar” bits de la parte de host y convertirlos en bits de red.

> 👉 Esto permite dividir un bloque grande de IPs en varios bloques más pequeños, cada uno funcionando como una red independiente.

### Ejemplo completo

1. Punto de partida

- Red original: 192.168.1.0/24
- /24 ⇒ 24 bits de red y 8 bits de host.

2. ¿Cuántas subredes quieres? → 4

¿Cuántos bits necesito “prestar” para obtener 4 subredes?

$2^{\text{bits\_prestados}} \ge 4 \;\Rightarrow\; \text{bits\_prestados} = 2$

3. Nuevo prefijo

- /24 + 2 bits prestados = /26
- Ahora: 26 bits de red y 6 bits de host (32−26).

4. Tamaño de cada subred (bloque)

- Con 6 bits de host: 2^6 = 64 direcciones por subred (incluyen red y broadcast).
- Incremento en el último octeto = 64.
- Hosts utilizables por subred = 64 - 2 = 62.

5. Listar las subredes /26 (incrementos de 64 en el 4.º octeto)

- Subred 1: empieza en .0 → va hasta .63
- Subred 2: empieza en .64 → va hasta .127
- Subred 3: empieza en .128 → va hasta .191
- Subred 4: empieza en .192 → va hasta .255

6. Calcular red, hosts y broadcast de cada subred

Regla rápida:

- IP de red = inicio del bloque
- Broadcast = (siguiente inicio de bloque) − 1
- Rango de hosts = entre red+1 y broadcast−1

  🔑 En NetPractice muchas veces te piden “repartir IPs” → aquí aplicas subredes.

⸻

4️⃣ Router
• Un router tiene varias interfaces de red.
• Cada interfaz debe tener una IP válida en la red que conecta.
• Función: interconectar subredes.
• Ejemplo: PC1 (192.168.1.x) → Router → PC2 (10.0.0.x).
• El router debe tener una interfaz en 192.168.1.0/24 y otra en 10.0.0.0/24.

⸻

5️⃣ Default Gateway
• Es la IP del router al que un host envía tráfico si el destino no está en su subred.
• Ejemplo:
• PC1: 192.168.1.10/24 con gateway 192.168.1.1.
• PC2: 10.0.0.5/24.
• Router:
• 192.168.1.1/24
• 10.0.0.1/24.
• PC1 envía a su gateway (192.168.1.1) para llegar a PC2.

⸻

6️⃣ Logs en NetPractice
• Abajo en la interfaz te dirán por qué no conecta:
• “Wrong network” → máscara mal puesta.
• “No route to host” → falta gateway o router.
• “Duplicate IP” → dos máquinas con la misma IP.

⸻

7️⃣ Estrategia para resolver niveles 1. Lee el objetivo arriba de la interfaz. 2. Identifica cuántas redes distintas hay. 3. Asigna IPs dentro de cada subred, respetando:
• IP de red (no se usa).
• IP de broadcast (no se usa).
• IPs de hosts (válidas). 4. Configura routers → cada interfaz debe pertenecer a la red que conecta. 5. Si hay tráfico entre redes, añade gateway en cada host. 6. Testea con Check again y ajusta.

⸻
