#reference #Ciberseguridad

**Nikto** = escáner de servidores web open source. Comprueba miles de ficheros/CGI peligrosos, versiones desactualizadas, misconfiguraciones y cabeceras inseguras. **Ruidoso** (no sigiloso) → para recon rápido, no para evasión.

# Uso

```bash
nikto -h http://victima.com
nikto -h 10.10.10.10 -p 80,443         # varios puertos
nikto -h https://victima.com -ssl       # forzar SSL
nikto -h victima.com -o report.html -Format htm   # informe
nikto -h victima.com -Tuning 9          # solo SQLi (ver tuning)
nikto -h victima.com -useproxy http://127.0.0.1:8080   # vía Burp
nikto -h victima.com -id user:pass      # auth básica
```

# Opciones clave

| Flag | Función |
|------|---------|
| `-h` | Host/URL objetivo. |
| `-p` | Puertos. |
| `-ssl` | Forzar HTTPS. |
| `-o` / `-Format` | Salida (txt, html, csv, xml). |
| `-Tuning` | Tipos de prueba (ver tabla). |
| `-useproxy` | Enrutar por proxy. |
| `-evasion` | Técnicas de evasión de IDS. |

## Tuning (`-Tuning`)

| Nº | Categoría |
|----|-----------|
| 1 | Ficheros interesantes |
| 2 | Misconfiguración / defaults |
| 3 | Divulgación de información |
| 4 | Inyección (XSS) |
| 6 | DoS |
| 9 | SQL injection |
| x | Invertir (excluir) |

# Notas

- Genera mucho tráfico y logs → detectable; pásalo por Burp para revisar hallazgos.
- Complementa, no sustituye, escaneo manual / [[bug-bounty-cs]].
- Resultados con falsos positivos: verificar a mano.

# Recursos
### [[RECON_CS]] · [[bug-bounty-cs]]
### [Nikto — repo](https://github.com/sullo/nikto)
