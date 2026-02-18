# Iniciar instancia

```sh
docker pull opensecurity/mobile-security-framework-mobsf:latest

docker run -it --rm \                                                                      
    -p 8000:8000 \
    -p 1337:1337 \
    -e MOBSF_ANALYZER_IDENTIFIER=<device-ID> \
    opensecurity/mobile-security-framework-mobsf:latest
```

Para obtener el device ID: `adb devices -l`