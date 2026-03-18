En primer lugar, se saca la IP del objetivo

```zsh
sudo netdiscover

 10 Captured ARP Req/Rep packets, from 10 hosts.   Total size: 600                                                                           
 _____________________________________________________________________________
   IP            At MAC Address     Count     Len  MAC Vendor / Hostname      
 -----------------------------------------------------------------------------
...
 192.168.1.145   08:00:27:e1:06:26      1      60  PCS Systemtechnik GmbH                                                       ...   
```

Una vez descubierta la IP, se analizan los puertos, en este caso, no parece haber gran cosa más allá de un servidor SSH y una Web.

```zsh
PORT   STATE SERVICE VERSION  
22/tcp open  ssh     OpenSSH 9.2p1 Debian 2+deb12u3 (protocol 2.0)  
| ssh-hostkey:    
|   256 af:79:a1:39:80:45:fb:b7:cb:86:fd:8b:62:69:4a:64 (ECDSA)  
|_  256 6d:d4:9d:ac:0b:f0:a1:88:66:b4:ff:f6:42:bb:f2:e5 (ED25519)  
80/tcp open  http    Apache httpd 2.4.62 ((Debian))  
|_http-title: Site doesn't have a title (text/html).  
|_http-server-header: Apache/2.4.62 (Debian)
```

Al visitar la página lo único que hay es nada, una página totalmente vacía, sin código fuente.