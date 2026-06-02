#reference #Ciberseguridad

**Kerberos** = protocolo de autenticación de red basado en **tickets** y criptografía simétrica. Es el mecanismo de auth por defecto de **Active Directory**. Evita enviar contraseñas por la red; un tercero de confianza (**KDC**) emite tickets temporales.

# Componentes

| Pieza | Rol |
|-------|-----|
| **KDC** | Key Distribution Center (corre en el DC). |
| └ **AS** | Authentication Service: emite el TGT. |
| └ **TGS** | Ticket Granting Service: emite tickets de servicio. |
| **TGT** | Ticket-Granting Ticket (prueba de identidad inicial). |
| **ST** | Service Ticket (acceso a un servicio concreto). |
| **SPN** | Service Principal Name (identifica un servicio). |
| **krbtgt** | Cuenta cuyo hash cifra los TGT (clave del reino). |

# Flujo

```
1. AS-REQ/REP : cliente -> AS, recibe TGT (cifrado con clave de krbtgt)
2. TGS-REQ/REP: cliente presenta TGT -> recibe ST para un servicio
3. AP-REQ     : cliente presenta ST al servicio -> acceso
```

# Ataques (AD)

| Ataque | Idea |
|--------|------|
| **Kerberoasting** | Pedir ST de cuentas con SPN → crackear offline su contraseña. |
| **AS-REP Roasting** | Cuentas sin pre-auth → obtener material crackeable sin credenciales. |
| **Golden Ticket** | Con el hash de **krbtgt** → forjar TGTs arbitrarios (dominio entero) → ver [[DCsync]]. |
| **Silver Ticket** | Con el hash de una cuenta de servicio → forjar ST para ese servicio. |
| **Pass-the-Ticket** | Reusar un TGT/ST robado de memoria. |
| **Overpass-the-Hash** | Hash NTLM → obtener un TGT. |

```bash
# Kerberoasting (Impacket)
GetUserSPNs.py corp.local/user:pass -dc-ip 10.0.0.1 -request
# AS-REP Roasting
GetNPUsers.py corp.local/ -usersfile users.txt -no-pass
```

# Mitigación

Contraseñas largas/aleatorias en cuentas de servicio (gMSA), rotar krbtgt periódicamente, monitorizar peticiones TGS anómalas, AES en vez de RC4.

# Recursos
### [[DCsync]] · [[Bloodhound]] · [[PowerShell_CS]]
### [HackTricks — Kerberos](https://book.hacktricks.xyz/windows-hardening/active-directory-methodology/kerberoast) · [Vídeo](https://youtu.be/5N242XcKAsM?list=PL65_wYSEg5HdSq1pSZxQmz7SoNE48vOgR)
