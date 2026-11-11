# rpi-firewall-lab

## ¿Qué es?

Es un servidor Linux headless (sin monitor ni teclado) en una Raspberry Pi 3 Model B. Está conectado a la red de mi casa por cable Ethernet y lo administro de forma remota por SSH desde mi PC.

Es un laboratorio para practicar acceso remoto seguro, configuración de un firewall con ufw y análisis de tráfico con Wireshark.

## Hardware y software

- Raspberry Pi 3 Model B v1.2
- microSD Kingston Canvas Select Plus de 64 GB (A1)
- Raspberry Pi OS Lite 64-bit (basado en Debian 13 "trixie"), grabado con Raspberry Pi Imager v2.0.12
- PC con Windows y Git Bash como terminal
- Wireshark 4.6.9 (con Npcap)

## Estado

- ✅ Sistema instalado y actualizado (`apt full-upgrade`; kernel verificado con `uname -r` después de reiniciar: 6.18.60)
- ✅ Acceso remoto por SSH con llave ed25519
- ✅ Firewall ufw: denegar por defecto lo que entra y permitir SSH solo desde mi red local
- ✅ Prueba de un puerto bloqueado y después abierto
- ✅ Capturas en Wireshark: ICMP, saludo TCP y una conexión bloqueada

## ¿Por qué SSH con llave y no con contraseña?

Con llave, ninguna contraseña viaja por la red y no se puede adivinar probando combinaciones. La llave privada nunca sale de mi PC; la Pi solo tiene la llave pública. Además, la llave privada está protegida con una frase.

## Red

La Pi obtiene su IP por DHCP del módem (en mi red quedó en 192.168.1.85). Como la asigna el módem, puede cambiar; reservarla está pendiente.
- PC (192.168.1.100), misma red 192.168.1.0/24, sin router entre ellas

## Firewall con ufw

## Prueba de puerto bloqueado y abierto

## Análisis de tráfico con Wireshark

## Bitácora

## Decisiones y límites

### ICMP (ping)

### Saludo TCP

### 09/11/2026: diagnóstico de hardware e instalación

**Problema:** tenía dos Raspberry Pi 3 y creía que una no funcionaba. Con la microSD que venía con ellas, el LED verde (actividad de la tarjeta) no encendía en ninguna de las dos placas.

**Diagnóstico por descarte:**
- La fuente daba suficiente corriente: el LED rojo se mantenía fijo.
- Probé la misma microSD en la otra placa y la tarjeta se calentó mucho.
- La PC tampoco reconocía la microSD.
- Conclusión: la falla estaba en la tarjeta, no en las placas.

**Solución:** compré una microSD nueva, la grabé con Raspberry Pi Imager y verifiqué la escritura. Ambas placas arrancaron correctamente y obtuvieron IP en la red.

**Lo que aprendí:**
- Confirmé conectividad con `ping` (respuestas con TTL=64, valor típico de Linux).
- Mi primer intento de SSH falló con `Permission denied (publickey)` porque usé el nombre del equipo (`rpi-lab`) como usuario. Con el usuario correcto funcionó.
- Al pasar la misma microSD de una placa a otra, la huella SSH del servidor fue la misma: la identidad del servidor y los usuarios viven en la tarjeta, no en la placa. Lo que sí cambió fue la IP, porque cada placa tiene su propia MAC.

### 10/11/2026: firewall e IPv6

### 11/11/2026: pruebas y Wireshark