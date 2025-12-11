# Pasos
1. Portátil exclusivo, corriendo [TailsOS](https://tails.net/install/index.en.html).
2. Conexión sólo desde routers crackeados/públicos o portátiles
3. VPS segura, conexión a través de TOR+VPN y X11 forwarding ([[SSH_protips]])  por SSH o [Wireguard](https://www.wireguard.com/quickstart/)
4. A través del X11 forwarded de esa primera se compra otra VPS remota en otro servicio, y se configura la primera VPS para actuar como router a la próxima.
5. En esa segunda VPS se alojan los backend C2 en contenedores. [docker-nginx-certbot](https://github.com/JonasAlfredsson/docker-nginx-certbot/****) para certificados SSL
> "Honestly the key is strict protocols.
> ...
> Slow speeds are better than a knock on your door."

# Recursos
- [Mullvad VPN](https://mullvad.net/en)