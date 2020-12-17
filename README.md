# TTNMAD_LoRaWAN_Geocaching
Materiales para crear una actividad de geocaching utilizando la tecnología LoRaWAN y The Things Network

Se esconden nodos numerados del que emiten cada 13-14 segundos y siempre en la misma frecuencia (pueden emitir todos en la misma frecuencia, o distribuirlos por varias frecuencias para reducir el riesgo de colisiones si hay mucho nodos). La carga de pago es indiferente (usaremos el nivel de batería), pero cada nodo envía por un puerto diferente (este puerto debe coincidir con el número del nodo).

Se asigna a cada equipo un gateway monocanal identificado con el número del nodo que tiene que localizar y, obviamente, configurado para recibir en la misma frecuencia y spread factor que ese nodo. Cada gateway va a depender del teléfono móvil del equipo para poder enviar los datos a Internet; el gateway se conecta por WiFi al móvil, que a su vez envía los datos a Internet a tavés de su conexión celular. El gateway puede alimentarse con un power bank, por ejemplo.

Se instala en el teléfono móvil Android la app TTNMAD LoRaWAN Geocaching. El teléfono móvil deberá disponer de datos y configuraremos en él un punto de acceso WiFi con el SSID y el password correspondiente al gateway de nuestro equipo.

El equipo selecciona en la app el número del nodo que le haya correspondido localizar. La app mostrará el mapa resaltando la parcela en la que debe localizarlo.

Sobre este mapa se indica la posición del equipo (obtenida a través del servicio de posicionamiento del teléfono móvil).

Adicionalmente, si el gateway del equipo está dentro de la zona de alcance del nodo que tiene que localizar, se indicará en la app con un marcador de color:

- Negro: No está dentro de la zona de alcance.
- Azul: Dentro de la zona de alcance pero con recepción muy débil.
- Verde
- Amarillo
- Naranja
- Rojo: Muy próximo al nodo. En este momento la app desvela su posición exacta (latitud y longitud) para que el equipo pueda recuperarlo.

Los nodos envían la información a The Things Network a través de los gateways.

Esta información se recoje desde un flujo Node-RED mediante una suscripción MQTT al topic de uplink de los dispositivos.

En Node-RED, además, se procesa esta información para almacenar en una base de datos Sqlite el puerto, el timestamp y el RSSI con el que cada gateway va recibiendo los paquetes de su correspondiente nodo. Para establecer la correspondencia entre nodos y gateways, el nombre del nodo debe coincidir con el id del gateway (por ejemplo: eui-58a0cbfffe8002e1).

Cada 15 segundos, la app realiza una petición GET al flujo de Node-RED solicitando el color que debe asignar al marcador.

Con la idea de que el proyecto sea lo más replicable posible, la app permite configurar el url del servidor Node-RED, y es en el flujo Node-RED donde se configuran todos los parámetros de funcionamiento, como:

- Posición inicial del mapa y coordenadas de los polígos de las parcelas.
- Posiciones exactas de los nodos.
- Correspondencia entre RSSI y colores de las marcas.
