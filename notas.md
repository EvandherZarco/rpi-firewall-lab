## Fase 2: firewall con ufw (10 nov 2026)

### Red de la Pi
```
eth0:  192.168.1.85/24   (cable)
wlan0: 192.168.0.156/24  (WiFi)
```
Mi PC: 192.168.1.100 → regla para 192.168.1.0/24.

**Hallazgo:** `$SSH_CLIENT` mostró que mi PC estaba conectada por IPv6, porque Windows la prefiere.
La regla solo cubre IPv4, así que al activar ufw me habría quedado fuera. Por eso se forzó `ssh -4`.

### Configuración
```bash
sudo apt update && sudo apt install ufw -y
sudo ufw default deny incoming
sudo ufw default allow outgoing
sudo ufw allow from 192.168.1.0/24 to any port 22 proto tcp
sudo ufw show added
sudo ufw enable
```

### Estado final
```
Status: active
Logging: on (low)
Default: deny (incoming), allow (outgoing), disabled (routed)
New profiles: skip

To                         Action      From
--                         ------      ----
22/tcp                     ALLOW IN    192.168.1.0/24
```

### Pruebas
| Prueba | Comando | Resultado | ¿Esperado? |
|---|---|---|---|
| SSH por IPv4 | `ssh -4 zarco@rpi-lab.local` | Entra | Sí |
| SSH por IPv6 por nombre | `ssh -6 ... rpi-lab.local` | Cannot assign requested address | Windows no pudo establecer la conexión con la dirección que le dio el nombre. Como no se estableció conexión con la Pi, la prueba no cuenta ni a favor ni en contra |
| SSH por IPv6 global | `ssh -6 ... 2806:xxxx:...` | Connection timed out | Sí, bloqueado |

**Por qué la última prueba sí es válida:**
1. Antes de ufw, SSH por IPv6 sí funcionaba → el camino IPv6 entre PC y Pi está bien.
2. Mismo prefijo IPv6 → no hay router en medio que pudiera estar bloqueando.
3. Lo único que cambió fue ufw → por lo tanto, el bloqueo es del firewall.

### Errores y lo que aprendí
- Corrí `sudo apt` en Git Bash (Windows).
  Olvidé entrar a la Pi por SSH. Usé `ssh zarco@rpi-lab.local` para acceder.

- Esperaba que `ssh zarco@rpi-lab.local`, sin `-4`, fallara por timeout porque el firewall bloquea IPv6; sin embargo, se quedó congelado unos segundos y luego pidió la passphrase.

  ssh pide la passphrase para autenticar, es decir, ocurre **después** de que la conexión TCP se estableció. Entonces sí se conectó.

  Esto ocurrió porque el nombre `rpi-lab.local` resolvía a una IPv4 y a una IPv6. Primero intentó entrar por IPv6; cuando el firewall la bloqueó, ssh se pasó a la IPv4, que sí está permitida. Eso fue fallback. Los segundos congelados fueron ssh esperando a que la IPv6 respondiera.

  Esto se comprobó quitándole a ssh el plan B: se forzó a que ssh usara únicamente IPv6, lo que dio como resultado "Connection timed out".