# LEVEL 03

En la imagen podemos ver lo que podrían ser tres subredes. Pero al tratarse de un **switch**, sólo existe una red.

Al igual que los otros niveles, al ser una red, las máscaras deberán ser la misma y los IPs, del mismo rango de puertos disponibles.

## Goal 01: Conectar "Host A" con "Host B"

Interface A1:
IP : 104.198.103.125

### Solución 01

Dado a que las máscaras deben ser la misma, y la única que tiene un valor fijo es el del interface C1, tomaremos ese valor para aplicarlo a los otros dos Interfaces.

Y el único IP fijo es el de A1, por lo que tendremos que tomarlo como referencia para asignar los IPs faltantes.

Interface A1:
IP : 104.198.103.125
Mask : 255.255.255.128

> Copiamos la máscara de C1

Interface B1:
IP : 104.198.103.124

> uno menos ya que la máscara indica que pueden tienen 126 puertos disponibles (excluyendo el del broadcast y el gateway)

Mask : 255.255.255.128

> Copiamos la máscara de C1

## Goal 02: Conectar "Host A" con "Host C"

La conexión se dará cuando pongamos la misma máscara y las IPs correspondientes.

### Solución 02

Una vez que ya tenemos conectados A con B y tenemos las mismas máscaras, faltaría modificar la IP de C1

Interface C1

IP : 104.198.103.123

> dos menos de A1, que también está dentro del rango.

## Goal 03: Conectar "Host B" con "Host C"

Ya que tenemos las IPs adecuadas y la máscara correcta, la conexión será automática.

### Solución 03

Nada.
