Desarrollamos un contenedor con una serie de aplicaciones de uso cotidianos al que nos conectaremos utilizando ssh en modo gráfico y nos permitirá sandboxear cualquier aplicación evitando muchos vectores de ataque

Dockerfile
```yaml
FROM debian:latest

RUN apt-get update \
        && apt-get install -y vim \
        && apt-get install -y nano \
        && apt-get install -y wget \
        && apt-get install -y okular \
        && apt-get install -y openssh-server \
		&& apt install -y xauth
		
RUN echo "
X11Forwarding yes
X11DisplayOffset 10
X11UseLocalhost no
PrintMotd no
" > /etc/ssh/sshd_config

RUN useradd -p $(openssl passwd -crypt sandbox) sandbox

ENTRYPOINT service ssh restart && bash


```
La contraseña del usuario sandbox es 'sandbox'

Para construirlo:
`sudo docker build -t mysandbox .`
Antes de ejecutarlo para permitir al docker utilizar el servidor X11 del anfitrión
```
#Ejecuta en el anfitrion!
xhost +
```
Para ejecutarlo
`sudo docker run -p 2222:22 -it -e DISPLAY -v /tmp/.X11-unix:/tmp/.X11-unix test1`
