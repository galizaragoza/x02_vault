#reference #Ciberseguridad

**MobSF** (Mobile Security Framework) = framework todo-en-uno para pentest de apps **móviles** (Android APK/source, iOS IPA). Hace análisis **estático**, **dinámico** y de APIs, con informe web.

# Iniciar instancia

```sh
docker pull opensecurity/mobile-security-framework-mobsf:latest

docker run -it --rm \
    -p 8000:8000 \
    -p 1337:1337 \
    -e MOBSF_ANALYZER_IDENTIFIER=<device-ID> \
    opensecurity/mobile-security-framework-mobsf:latest
```

Para obtener el device ID: `adb devices -l`. Interfaz web en `http://localhost:8000`.

# Análisis estático (SAST)

Subir el APK/IPA por la web → MobSF descompila y revisa:
- Permisos peligrosos, componentes exportados (activities, services, receivers).
- Secretos hardcodeados (API keys, URLs).
- Configuración insegura (cleartext traffic, debuggable, backups).
- Librerías con CVEs, certificado de firma.
- Mapeo a **OWASP MASVS / Mobile Top 10**.

# Análisis dinámico (DAST)

Requiere emulador/dispositivo con root + Frida. Permite:
- Instrumentación en runtime (hooks Frida).
- Captura de tráfico (proxy), bypass de SSL pinning.
- Inspección de almacenamiento, logs, llamadas a API en vivo.

```bash
# Preparar dispositivo
adb devices -l
# MobSF lanza la app y permite interactuar mientras instrumenta
```

# Flujo típico

```
subir APK -> revisar hallazgos SAST -> análisis dinámico (runtime) -> informe (PDF)
```

# Recursos
### [[OSINT_cheatsheet]]
### [MobSF — docs](https://mobsf.github.io/docs/) · [OWASP MASVS](https://mas.owasp.org/)
