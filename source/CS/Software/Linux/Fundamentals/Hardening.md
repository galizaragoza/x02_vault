#reference #Software #Ciberseguridad

**Hardening** = proceso de endurecer un sistema: conjunto de directrices, técnicas y software para reducir la superficie de ataque y dar robustez a su seguridad. Principio rector: **mínimo privilegio** + desactivar lo que no se usa.

# Checklist (Linux)

## Cuentas y autenticación
- Deshabilitar login root por SSH (`PermitRootLogin no`), usar `sudo`.
- SSH con **claves**, no contraseñas (`PasswordAuthentication no`); cambiar puerto, `fail2ban`.
- Política de contraseñas fuerte (`pam_pwquality`), caducidad, bloqueo tras intentos.
- Eliminar cuentas/servicios sin uso; revisar UID 0 duplicados.

## Servicios y red
- Cerrar puertos/servicios innecesarios (`systemctl disable`, `ss -tulpn`).
- Firewall por defecto deny (`ufw`/`nftables`), solo abrir lo necesario.
- Mantener el sistema actualizado (`unattended-upgrades`).

## Permisos y kernel
- Revisar binarios **SUID/SGID** y [[Capabilities]] sobrantes.
- `sysctl` hardening: ASLR (`kernel.randomize_va_space=2`), `dmesg_restrict`, anti IP spoofing.
- Montar `/tmp`, `/var` con `noexec,nosuid,nodev` donde aplique.
- **MAC**: AppArmor / SELinux en modo enforcing.

## Auditoría y logging
- `auditd` para eventos sensibles; centralizar logs.
- Integridad de ficheros (AIDE/Tripwire).
- Revisar cron y tareas programadas.

# Herramientas

| Herramienta | Uso |
|-------------|-----|
| **Lynis** | Auditoría de hardening automatizada. |
| **OpenSCAP** | Cumplimiento contra benchmarks (CIS, STIG). |
| **CIS Benchmarks** | Guías de referencia por SO. |
| **fail2ban** | Bloqueo de IPs por intentos fallidos. |
| **chkrootkit / rkhunter** | Detección de rootkits. |

```bash
sudo lynis audit system        # informe + warnings + score
```

> El hardening es continuo: aplicar un benchmark (CIS), medir con Lynis/OpenSCAP, corregir, repetir. Equilibrar seguridad y funcionalidad.

# Recursos
### [[Capabilities]] · [[Kali]]
### [Lynis (CISOfy)](https://github.com/CISOfy/lynis) · [CIS Benchmarks](https://www.cisecurity.org/cis-benchmarks)
### [Hardening Kali](https://linuxconfig.org/hardening-kali-linux) · [Kali hardening basics](https://youtu.be/TwiEdEbVL6I?list=PL65_wYSEg5HdSq1pSZxQmz7SoNE48vOgR)
