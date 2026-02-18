Herramienta interactiva para llevar a acabo ataques de phishing

```
bash zphisher.sh
```

|Parámetro|Función|
|---|---|
|Plantillas (menú 1-30+)|Selecciona página falsa (e.g., 1=Facebook, 2=Instagram)|
|Hosting Localhost (opción 1)|Servidor PHP local (puerto 3333 por defecto)|
|Ngrok (opción 2)|Túnel HTTPS/TCP para bypass NAT/firewalls|
|LocalXpose (opción 3)|Túnel alternativo para exposición pública|
|CloudFlare (opción 4)|Túnel seguro con zero-trust|
|Serveo (opción 5)|Túnel SSH simple|
|-p PUERTO|Cambia puerto servidor (e.g., -p 8080)|
|--update|Actualiza herramienta|
|--kill|Detiene procesos de túnel|
