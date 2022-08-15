# reverse-proxy
Para crear nuestro reverse proxy debemos primero crear tres máquinas virtuales de linux en la cual dos de ellas van a correr un servidor en su respectivo localhost y la  tercera máquina va a ser nuestro reverse proxy, el encargado de enrutar las peticiones a los otros dos servidores.

## Primera máquina virtual (docker-compose)
En nuestra primera máquina levantamos una aplicación traida de docker. Para instalarla debemos hacer lo siguiente:
- Actualizamos los repositorios de linux e instalamos docker-compose
```
sudo apt update
sudo apt install docker-compose
```

- Creamos un script
```
nano script.sh
```

- Copiamos el siguiente código en el script
```
#!/usr/bin/env bash
CURRENT_WORKDIR=$(pwd)
FILE_ID="1EH1zS1wQ_5WmvUEar2eXcB4QMj1Mo49g"
FILENAME="docker-compose-example.tgz"
mkdir example && cd example
wget --no-check-certificate "https://docs.google.com/uc?export=download&id=${FILE_ID}" -O ${FILENAME}
tar xfz ${FILENAME}
cd ${CURRENT_WORKDIR}
```

- Cambiamos los permisos de ejecución del script y lo ejecutamos
```
chmod +x script.sh && ./script.sh
```

- Ingresamos a la carpeta *example* generada y ejecutamos la aplicación con el docker-compose
```
cd example && docker-compose up
```

Listo, tenemos nuestro servidor corriendo en el puerto 8000.

## Segunda máquina virtual (apache)
En esta segunda máquina correremos un servidor de apache, para ello hacemos los siguientes pasos:
- Actualizamos los repositorios de linux e instalamos apache2
```
sudo apt update
sudo apt install apache2
```
Listo!, con solo instalar apache ya tendremos un servidor corriendo en nuestro localhost

## Tecera máquina virtual (nginx)
En esta última máquina instalaremos nginx y configuraremos el servidor para que sirva como un reverse-proxy.
- Actualizamos los repositorios de linux e instalamos nginx
```
sudo apt update
sudo apt install nginx
```
Con esto ya tendremos corriendo a nginx en nuestro localhost, ahora debemos configurarlo
- Entramos al archivo de configuración por default
```
cd /etc/nginx/sites-available/
sudo nano default
```
- Aquí configuramos nuestras rutas, en este caso configuramos las rutas de las otras dos máquinas virtuales que creamos con sus respectivas direcciones ip
```
server {
    listen 80 default_server;
    listen [::]:80 default_server;
    
    location = /compose {
        return 301 http://192.168.239.3:8000
    }
    
    location = /apache {
        return 301 http://192.168.239.4;
    }
}
```
Listo!, ya tenemos nuestro reverse-proxy corriendo
