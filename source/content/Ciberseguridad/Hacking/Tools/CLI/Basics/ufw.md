UFW (Uncomplicated Firewall) es una interfaz amigable para gestionar reglas de firewall basadas en iptables en sistemas Linux (principalmente Ubuntu/Debian). Permite bloquear o permitir tráfico de red de forma sencilla para mejorar la seguridad.

```zsh
sudo ufw [opción] [regla]
```

| **Parámetro/Comando** | **Función**                                                    | **Ejemplo de Sintaxis**                    |
| ----------------- | ---------------------------------------------------------- | -------------------------------------- |
| allow             | Permite tráfico en puerto/protocolo.                       | `sudo ufw allow 22/tcp`                |
| deny              | Bloquea tráfico en puerto/protocolo.                       | `sudo ufw deny 80/tcp`                 |
| delete            | Elimina una regla existente.                               | `sudo ufw delete allow 22/tcp`         |
| enable            | Activa el firewall.                                        | `sudo ufw enable`                      |
| disable           | Desactiva el firewall.                                     | `sudo ufw disable`                     |
| status            | Muestra estado y reglas actuales.                          | `sudo ufw status`                      |
| reset             | Reinicia todas las reglas a predeterminadas.               | `sudo ufw reset`                       |
| logging           | Configura nivel de registro (on/off/low/medium/high/full). | `sudo ufw logging on`                  |
| allow from        | Permite tráfico desde IP específica.                       | `sudo ufw allow from 192.168.1.100`    |
| insert            | Inserta regla en posición específica.                      | `sudo ufw insert 1 deny from 10.0.0.1` |
