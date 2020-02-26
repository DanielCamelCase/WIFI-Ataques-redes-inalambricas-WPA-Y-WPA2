# WIFI_Ataques_redes_inalambricas

<a name="top"></a>
# Tabla de referencia de los contenidos para visualizacion rapida
 
## Configuraciones Iniciales de la tarjeta de red inalambrica 
* [Chequeo de tarjeta y resolucion de conflictos](#item1)
* [MAC Spoofing](#item2)
* [Resolucion de problemas de visibilidad y servicios WIFI](#item3)
## Analisis del entorno  
* [Ver puntos de acceso cercanos](#item4)
* [Seleccionada la victima vamos a especificar el escaneo para indagar en ella](#item5)
* [Guardar un .cap de la victima seleccionada para posteriormente trabajar sobre el](#item6)
## Fase de Explotacion en WEB 

* [Contenido 7](#item7)
 
 
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
``` $ airodump-ng -c 1 --bssid 20:34:FB:B1:F5:53 wlan0mon ```
Otra forma seria atraves del nombre de la red victima 
    Airodump-ng -canal -PuntoAccesoVictima  interfaz de red nuestra 
``` $ airodump-ng -c 1 --essid MovistarE34A wlan0mon ```
 
[Subir](#top)
 
<a name="item6"></a>
### Guardar un .cap de la victima seleccionada para posteriormente trabajar sobre el:
 
Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua.
 
Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat.
 
[Subir](#top)
