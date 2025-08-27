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

### Ejemplo básico

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

### ¿Qué es el broadcast?

Es una dirección especial dentro de una red que se usa para enviar mensajes a todos los dispositivos de esa red al mismo tiempo.

- En una red, siempre hay:

  - IP de red → identifica la red completa (no se asigna a hosts).
  - IPs de hosts → se asignan a dispositivos.
  - IP de broadcast → dirección reservada para comunicarse con todos los hosts de la red.

#### Ejemplo

Red: 192.168.1.0/24 → máscara 255.255.255.0
• IP de red: 192.168.1.0
• Broadcast: 192.168.1.255
• Hosts válidos: 192.168.1.1 → 192.168.1.254

👉 Si un dispositivo envía un mensaje a 192.168.1.255, todos los dispositivos con IP 192.168.1.x recibirán ese mensaje.

**La IP de broadcast nunca se asigna a un host.**

- Regla:
  - Cuantos más bits de red, menos hosts.
  - Cuantos menos bits de red, más hosts.

---

## 3️⃣ Subredes

### ¿Qué es una subred?

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
- Rango de hosts = entre red + 1 y broadcast − 1

> 🔑 En NetPractice muchas veces te piden “repartir IPs” → aquí aplicas subredes.

---

## 4️⃣ Router

- Es un dispositivo que interconecta redes distintas, es su función principal.
- Tiene varias interfaces de red (como tarjetas de red).
- Cada interfaz debe tener:
  - Una IP válida en la red que conecta.
  - Su máscara correspondiente.

> 👉 El router “sabe” en qué red está cada interfaz, y por eso puede reenviar paquetes de una red a otra.

### Ejemplo

- PC1 → 192.168.1.10/24
- PC2 → 10.0.0.5/24

El router debe tener dos interfaces:

- Interfaz 1 → 192.168.1.1/24
- Interfaz 2 → 10.0.0.1/24

Ahora:

- PC1 envía tráfico a su **default gateway (192.168.1.1).**
- El router recibe el paquete y lo reenvía a la red 10.0.0.0/24.
- PC2 lo recibe.

### Reglas en NetPractice

- Cada interfaz del router debe estar en la red correcta.
- **Los hosts deben configurar como gateway la IP del router** en su red.
- Si falta una interfaz o el gateway no está configurado → ❌ no hay conexión.
- **Los routers no pueden tener dos interfaces con la misma IP**.

---

## 5️⃣ Default Gateway

- Es la puerta de salida de un dispositivo hacia otras redes. **Se configura en cada host (PC, servidor, etc.) con la IP del router dentro de su misma red**.
- Cuando un host quiere comunicarse con una IP fuera de su subred, manda el tráfico al default gateway, y este se encarga de reenviarlo a la red correcta.

### Ejemplo

**Red A**: 192.168.1.0/24

- **PC1**: 192.168.1.10/24, gateway = 192.168.1.1
- **Router**:
  - Interfaz A → 192.168.1.1/24
  - Interfaz B → 10.0.0.1/24
- **PC2**: 10.0.0.5/24, gateway = 10.0.0.1

**👉 Flujo:**

1. PC1 quiere hablar con PC2 (10.0.0.5).
2. Ve que no está en su red (192.168.1.x).
3. Envía el paquete a su gateway (192.168.1.1).
4. El router reenvía por su interfaz B (10.0.0.1) hacia PC2.
5. PC2 responde, usando su gateway (10.0.0.1) para volver.

### Reglas clave

- El gateway debe estar en la misma red que el host.
  - Ej: si el host es 192.168.1.50/24, su gateway no puede ser 192.168.2.1.
- En NetPractice, si olvidas configurar el gateway:
  - Los hosts solo se ven dentro de su propia red, pero no llegan a otras redes.
- El gateway **casi siempre es la primera IP** del router en esa red (ej: .1), **pero puede ser cualquiera** dentro del rango válido.

### Aclaración

En cada red o subred existen siempre tres tipos de direcciones:

- **Dirección de red** → identifica a toda la red (no se asigna a hosts).
- **Dirección de broadcast** → permite enviar mensajes a todos los hosts de la red (no se asigna a hosts).
- **Direcciones de host** → las que se asignan a cada dispositivo de forma única.

Cada dispositivo (host) debe configurarse con:

- Una IP válida (dentro del rango de hosts).
- Su máscara de red.
- (Opcional) un default gateway si necesita comunicarse con dispositivos de otras redes.

---

## 6️⃣ Logs en NetPractice

Estos mensajes que aparecen en la parte inferior de la interfaz de NetPractice, muestran el “camino” que sigue un paquete de un host a otro. Te dicen en qué punto el paquete fue aceptado, redirigido o rechazado.

### Mensajes típicos

1. “Wrong network”

   - La máscara o la IP están mal configuradas.
   - Ejemplo: un host con 192.168.1.50/24 intenta hablar con 192.168.2.10/24 sin un router → ❌.

2. “No route to host”

   - No hay gateway configurado, o el router no tiene la interfaz necesaria.
   - El host no sabe cómo salir de su subred.

3. “Duplicate IP”

   - Dos dispositivos tienen la misma dirección IP.
   - Esto rompe la red porque las IPs deben ser únicas.

4. “Destination IP reached”

   - ✅ El paquete llegó a destino correctamente.

### Ejemplos

#### Wrong Network

Un host intenta hablar con otro, pero la máscara de red y la IP están mal configuradas → los dispositivos creen que están en la misma red cuando no lo están.

```bash
  forward way : A -> B (192.168.2.20)
  on A : packet accepted
  on A: send to gateway
  on A : packet rejected (wrong network)
```

👉 A y B están en redes distintas sin router → no hay comunicación.

- Verifica que ambos hosts tienen máscara de red correcta.
- Confirma que las IPs asignadas estén en el mismo rango de subred.
- Si deben estar en redes distintas, asegúrate de que haya un router en medio.

#### No Route to Host

El host quiere salir de su red, pero no tiene configurado un gateway o el router no tiene la interfaz necesaria.

```bash
  forward way : A -> B (10.0.0.5)
  on A : packet accepted
  on A: send to gateway
  on A : packet rejected (no route to host)
```

👉 El host no tiene gateway configurado, así que no puede salir de su red.

- ¿Tiene el host configurado un default gateway válido (una IP en su misma subred)?
- ¿El router tiene una interfaz en cada red que conecta?
- Si hay varias redes encadenadas, revisa que cada salto tenga su gateway.

#### Duplicate IP

Hay dos dispositivos con la misma dirección IP → colisión, la red no sabe a quién mandar los paquetes.

```bash
  forward way : A -> B (192.168.1.20)
  on A : packet accepted
  on A: send to gateway
  on Router : packet rejected (duplicate IP detected)
```

👉 Dos dispositivos usan la misma IP, el router no sabe a quién mandar el paquete.

- Verifica que cada host tenga una IP única dentro de la misma red.
- Si hay routers, confirma que sus interfaces tampoco repitan IPs ya usadas en esa subred.

#### Destination IP reached

```bash
  forward way : A -> B (192.168.1.20)
  on A : packet accepted
  on A: send to gateway
  on B : packet accepted
  on B: destination IP reached
```

👉 Significa que el paquete salió de A, pasó por el gateway y llegó a B sin problemas.

#### Resumen

- Wrong network → revisa máscaras e IPs de red.
- No route to host → revisa gateway y presencia de router.
- Duplicate IP → revisa que no haya direcciones repetidas.
- Destination IP reached → ✅ no hay nada que arreglar, todo funciona.

---

## 7️⃣ Estrategia para resolver niveles

1. Lee el objetivo arriba de la interfaz.

   - Arriba de la interfaz te ponen el Goal ID o la condición de éxito.
   - Puede ser algo como: “A debe comunicarse con B” o “Todos los hosts deben estar conectados”.
   - Antes de tocar nada, entiende qué pide exactamente.

2. Identifica cuántas redes distintas hay.

   - Mira las máscaras y las IPs ya dadas.
   - Pregúntate: ¿están todos en la misma red o hay varias redes distintas?
   - Si ves /24 y /16 mezclados, o routers de por medio → seguro hay varias redes.

3. Asigna IPs dentro de cada subred, respetando:
   - A cada host dale una IP única dentro de su subred.
   - Recuerda que no puedes usar:
     - IP de red (ej: .0 en /24).
     - IP de broadcast (ej: .255 en /24).
   - Mantén orden: suele ser más fácil poner al router como .1 y luego numerar hosts a partir de .2.
4. Configura routers → cada interfaz debe pertenecer a la red que conecta:

   - Cada interfaz del router debe estar en la red que conecta.
   - Ejemplo: si conecta 192.168.1.0/24 y 10.0.0.0/24, sus interfaces deben ser algo como 192.168.1.1/24 y 10.0.0.1/24.

5. Si hay tráfico entre redes, añade gateway en cada host.

   - Si un host debe comunicarse fuera de su red, ponle como gateway la IP del router que está en su subred.
   - Si solo debe hablar con otros de su misma red → no hace falta gateway.

6. Testea con Check again y ajusta.

   - Pulsa Check again y baja a leer los logs.
   - Si ves Wrong network → revisa máscara o IP.
   - Si ves No route to host → revisa gateway o interfaces de router.
   - Si ves Duplicate IP → busca IP repetida.
   - Si todo está bien → Destination IP reached.

7. Avanza paso a paso

   - Primero asegúrate de que cada subred funciona internamente.
     - Ej: PC1 ↔ PC2 dentro de la misma red.
   - Luego prueba la comunicación entre redes a través de los routers.

8. Exporta siempre tu configuración

   - Antes de pasar al siguiente nivel, pulsa Get my config y guarda el archivo.
   - Es obligatorio subir uno por nivel al repositorio.
