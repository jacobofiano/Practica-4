Xestión de redes en Docker

1. *Crear unha rede en Docker*
Para crear unha rede en Docker, podemos empregar o seguinte comando:

docker network create my_network

Este comando crea unha rede chamada my_network.

2. *Crear dous contenedores unidos a esa rede*

Agora creamos dous contenedores e conectámolos á rede creada. Para iso, utilizamos a opción --network ao lanzar os contenedores:

docker run -d --name contenedorprimero --network mi-network busybox sleep 3600
docker run -d --name container2 --network mi-network busybox sleep 3600

Aquí estamos lanzando dous contenedores (contenedorprimero e container2) que executan a imaxe busybox e están conectados á rede mi-network.

3. Comprobar que os contenedores están na rede

Para verificar que ambos os contenedores están na rede, executamos:

docker network inspect mi-network

Este comando mostrará información sobre a rede, incluíndo os contenedores conectados a ela. Deben aparecer container1 e container2.

4. Comprobar que os contenedores poden verse entre eles

Para verificar que os contenedores poden comunicarse, usamos ping. Entramos no contenedor container1 e facemos ping a container2:

docker exec -it container1 ping container2

Se a rede está configurada correctamente, deberías ver que container1 pode facer ping a container2.

5. Listar os contenedores conectados á rede

Para listar todos os contenedores conectados á rede, volvemos utilizar:
docker network inspect mi-network

Esta información xa a revisamos antes, pero aquí tamén podemos comprobar a lista de todos os contenedores conectados que seran contenedorprimero e container2.

6. Listar as propiedades da rede

Para listar as propiedades dunha rede de Docker, o comando inspect de novo permítenos ver detalles avanzados, como o driver, subredes e enderezos IP asignados:
ip1:172.18.0.2/16
ip2:172.18.0.3/16
docker network inspect mi_network

7. Crear outra rede

Agora imos crear unha segunda rede:

docker network create mi-network2

8. Lanzar dous novos contenedores conectados á nova rede

Creamos dous contenedores novos e conectámolos á segunda rede:

docker run -d --name container3 --network my_second_network busybox sleep 3600

docker run -d --name container4 --network my_second_network busybox sleep 3600

9. Comprobar as posibles conexións entre os 4 contenedores

container1 debería poder facer ping a container2 porque están na mesma rede (mi-network)

container3 debería poder facer ping a container4 porque están na mesma rede (my_second_network).

Non debería haber comunicación entre container1 e container3 ou entre container2 e container4 porque están en redes diferentes.

Podemos probar isto executando:
docker exec -it container1 ping container3
docker exec -it container3 ping container4
Se non hai conexións entre redes diferentes, isto indicará que as redes están illadas.

Parte 2:DOCKER-COMPOSE

1. Seguir a guía de iniciación de Docker Compose

Os pasos básicos para usar Docker Compose son:

Crear o ficheiro docker-compose.yml: Este arquivo describe os servizos, redes e volumes que queres lanzar.

Usar docker-compose up para lanzar os servizos definidos no arquivo YAML.

Usar docker-compose down para deter e eliminar os servizos.

Exemplo simple de docker-compose.yml:
version: '3'
services:
  web:
    image: nginx
    ports:
      - "8080:80"redis:
    image: "redis:alpine"

Neste caso, temos dous servizos:

O servizo web que usa a imaxe nginx e expón o porto 8080 ao porto 80 dentro do contenedor.

O servizo redis que usa a imaxe de Redis.

Pasos detallados:

Crea o arquivo docker-compose.yml.

Executa docker-compose up para lanzar os contenedores baseados nas imaxes especificadas.
O servizo web estará accesible no teu navegador a través de localhost:8080.

2. Crear unha rede con 3 contenedores conectados

Agora que entendemos un pouco máis sobre Docker Compose, imos crear unha configuración que conecte tres contenedores a unha rede común.

Exemplo de docker-compose.yml:
version: '3'
services:
  app1:
    image: busybox
    command: sleep 3600
    networks:
      - my_network
  app2:
    image: busybox
    command: sleep 3600
    networks:
      - my_network
  app3:
    image: busybox
    command: sleep 360networks:
      - my_network

networks:
  my_network:
    driver: bridge

Explicación:

Temos tres servizos (app1, app2, app3), todos usando a imaxe busybox e un comando para manter os contenedores en execución.
Todos os contenedores están conectados á rede my_network que utilizamos o driver bridge.

Comandos:

Lanzamos os contenedores usando docker-compose up.
Todos os contenedores estarán na mesma rede e poderán verse entre eles.

3. Parámetros e configuracións diferentes en Docker Compose

Agora, imos explorar algúns parámetros diferentes que podemos incluír no arquivo docker-compose.yml:

depends_on: Establece unha orde de dependencia entre servizos.

app1:
  image: busybox
  command: sleep 3600
app2:
  image: busybox
  depends_on:
    - app1

Isto asegura que app2 só se execute despois de que app1 estea iniciado.
volumes: Para montar volumes dentro do contenedor, podemos definir a opción volumes.

app1:
  image: nginx
  volumes:
    - ./data:/usr/share/nginx/html

Isto monta o directorio ./data do host no directorio /usr/share/nginx/html do contenedor.

restart: Define políticas de reinicio do contenedor.

app1:
  image: busybox
  command: sleep 3600
  restart: always

Neste caso, o contenedor app1 reiniciarase automaticamente se se detén.

environment: Define variables de entorno que serán usadas polos contenedores.

app1:
  image: redis
  environment:
    - REDIS_PASSWORD=mysecretpassword

Aquí estamos definindo unha variable de entorno chamada REDIS_PASSWORD que Redis utilizará durante a execución.

Con estes parámetros e configuracións, podes axustar o comportamento dos teus servizos en Docker Compose segundo as túas necesidades.