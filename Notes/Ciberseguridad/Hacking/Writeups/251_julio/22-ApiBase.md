# Info
## IP Víctima
```
172.17.0.2
```
# Writeup
## Recon
Empezamos con un escaneo de puertos identificando servicios activos y versiones:
```bash
PORT     STATE SERVICE VERSION
22/tcp   open  ssh     OpenSSH 8.4p1 Debian 5+deb11u4 (protocol 2.0)
| ssh-hostkey: 
|   3072 20:ab:09:61:00:7b:cc:18:48:8e:bf:8d:3d:e4:cd:b5 (RSA)
|   256 42:0c:71:44:7c:13:ba:8f:b7:82:35:f2:b3:f7:b9:ff (ECDSA)
|_  256 85:95:6c:96:ac:a1:f0:3e:1e:0d:c1:c8:b0:6f:bb:1d (ED25519)
5000/tcp open  http    Werkzeug httpd 1.0.1 (Python 3.9.2)
|_http-title: Site doesn't have a title (application/json).
|_http-server-header: Werkzeug/1.0.1 Python/3.9.2
```
Tenemos un SSH por el puerto default (22) y un servidor HTTP mediante Werkzeug 1.0.1 en python3.
## FF
#### [Shinki writeup](https://shinki-organization.gitbook.io/dw/write-ups/quickstart/maquinas-faciles/apibase-write-up)