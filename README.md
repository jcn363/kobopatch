# Proceso de parcheado

Para la versión actual (4.10.11655):

0.- Comprobar en Menú/Configuración qué versión tienes instalada. (Si no te aparece 4.10.11655 prosigue en 1, en caso contrario ve al 6).

1.- Descargar el firmware (kobo-update-4.10.11655.zip) de tu disposistivo desde https://pgaskin.net/KoboStuff/kobofirmware.html

2.- Comprobar que la batería está por encima del 70%. Conectar tu dispositivo al ordenador mediante el cable USB.

3.- Descomprimir el contenido (la carpeta upgrade y los ficheros KoboRoot.tgz y manifest.md5sum) del zip (kobo-update-4.10.11655.zip) dentro de la carpeta .kobo (que se encuentra en el directorio raiz) de tu dispositivo.

4.- Desmontar y expulsar el lector del ordenador. Desconectar el cable USB de tu disposistivo. El dispositivo se reiniciará para instalar el nuevo firmware.

5.- Comprobar en Menú/Configuración qué versión tienes instalada. Si no ha pasado nada raro, aparecerá 4.10.11655.

6.- Descargar los parches (kobopatch_4.10.11655.zip) desde https://github.com/geek1011/kobopatch-patches/releases (a día de hoy https://github.com/geek1011/kobopatch-patches/releases/download/v16/kobopatch_4.10.11655.zip)

7.- Descomprimir el fichero anterior. Se crea una carpeta kobopatch_4.10.11655 dentro de la cual verás un fichero kobopatch.yaml. Abre el fichero con un editor de texto (si tu sistema operativo es Windows usa Notepad++ u otro que pueda usar los cambios de línea tipo UNIX. Con Linux no tendrás problema alguno y con Mac es improbable que los tengas).

8.- Modifica el fichero para que te quede a tu gusto. Guardar los cambios. (Si quieres modificar algún parche continúa en 9, de lo contrario en 10).

9.- Los ficheros con extensión yaml que se encuentran en la carpeta src son los que contienen los parches. Hay uno para cada archivo a parchear, pero se pueden añadir más.

   Es decir, si quieres ver/modificar el parche "Custom reading footer style" tendrás que abrir el fichero kobopatch_4.10.11655/src/nickel.yaml.
    
   (Si has descomprimido (7) en el escritorio de Windows será algo así: C:\users\nombre_de_tu_usuario\desktop\kobopatch_4. 10.11655\src\nickel.yaml).

    IMPORTANTE: Modifica únicamente los valores que haya entre las comillas que se encuentran después de Replace: 
    Ejemplo:
    Con la siguiente línea estarías cambiando un tamaño de letra de 29px por uno más pequeño de 26px.
    Código:
      - ReplaceString: {Offset: 357, Find: "font-size:29px", Replace: "font-size:26px"} # Replacement caption font size value for AuraHD/H2O:

10. Tras haber modificado los parches deseados (y haber guardado los cambios realizados) ejecuta kobopatch.sh (si tienes Linux), o kobopatch.bat (si tienes Windows).

    Si no hay problemas, se creará un fichero KoboRoot.tgz, dentro de la carpeta out, que deberás copiar dentro de la carpeta .kobo de tu dispositivo.
    
    Si los hay, dentro de la carpeta out, se habrá creado un archivo log.txt donde encontrarás la(s) causa(s).

11.- Expulsar el lector del ordenador. Desconectar el cable USB de tu disposistivo. El dispositivo se reiniciará para instalar tus modificaciones.

# Destripando kobopatch.yaml

· Como el sistema usado en los Kobo es Linux, se usa la nomenclatura UNIX (con / en vez de la \ de Windows).

· Los sangrados/espacios (siempre que no estén en un comentario) importan.

· Todo lo que va a continuación del símbolo # es un comentario.

· Las palabras que preceden a dos puntos, y llevan texto a continuación, son variables, que guardan el valor que les sigue. Como un diccionario, palabra y definición.

    version: 4.10.11655
    in: src/kobo-update-4.10.11655.zip
    out: out/KoboRoot.tgz
    log: out/log.txt

    patchFormat: kobopatch

·versión: es la versión del firmware que queremos parchear -> la versión.

·in: comprimido (con su ubicación) donde se encuentra el firmware que queremos parchear -> el firmware.

·out: comprimido (con su ubicación) donde se guardará el firmware parcheado listo para su aplicación -> el parche.

·log: archivo de información/referencia donde se guardan las órdenes, errores, etc. gernerados durante la creación del parche -> el log.

·patchFormat: es el formato en el que están escritos los parches -> kobopatch es el actual.

· Las palabras que preceden a dos puntos, pero NO llevan texto a continuación, denominan una sección.

· En la sección patches encontramos una lista de relaciones

    archivo con parches: fichero a parchear
    
· En un archivo con parches sólo puede haber parches que modifiquen un programa o librería. Pero puede haber varios archivos que modifiquen un mismo programa o librería.

· Tanto libnickel-PROGRESIVE.yaml como libnickel.so.1.0.0.yaml contienen parches que modifican la misma librería: libnickel.so.1.0.0.

    patches:
      src/nickel-PROGRESIVE.yaml: usr/local/Kobo/nickel
      src/nickel-PROGRESIVE_Nova.yaml: usr/local/Kobo/nickel
      src/nickel.yaml: usr/local/Kobo/nickel
      src/sickel.yaml: usr/local/Kobo/sickel
      src/libadobe.so.yaml: usr/local/Kobo/libadobe.so
      src/libnickel-PROGRESIVE.yaml: usr/local/Kobo/libnickel.so.1.0.0
      src/libnickel.so.1.0.0.yaml: usr/local/Kobo/libnickel.so.1.0.0
      src/librmsdk.so.1.0.0.yaml: usr/local/Kobo/librmsdk.so.1.0.0

· En las sección overrides se encuentra un listado de los archivos con parches (enumerados en la sección patches) junto con los parches que contienen y si los queremos aplicar o no.

  El valor aquí asignado sobreescribe el que se encuentre dentro del fichero de parches (por defecto no).
  
  No es obligatoria, pero es muy útil y cómoda, ya que de un vistazo nos permite ver los parches de que disponemos junto con su estado. Además nos permite activar y desactivar parches (de diferentes programas y librerías) sin tener que abrir el fichero que los contiene para buscarlos en él.
  
  Para decir que queremos aplicar un parche llamado PROGRESIVE, que se encuentra dentro del archivo nickel-PROGRESIVE.yaml, sobre el programa nickel, independientemente de que esté activado o no en él, tendríamos que poner:
  
    overrides:
      src/nickel-PROGRESIVE.yaml:
        PROGRESIVE: yes
    
· Puede haber tantos parches como parches existan dentro de ese archivo. Como se indicó, No es obligatorio que estén todos, aunque si muy útil.

· El nombre del parche se debe corresponder exactamente con el que se encuentre dentro del archivo de parches correspondiente.

· Recordar que el valor que predomina es el aquí indicado.

    src/libnickel-PROGRESIVE.yaml:
      Dictionary, remove long winded closest match notification: yes
      unclassified (PROGRESIVE): yes
      MY ePub/Kepub fixed font sharpness: yes
      Wikipedia Search language: yes
      MY KePub stylesheet additions: yes
      My Dictionary text font-family/font-size/line-height: yes

· Hay parches que son autoexcluyentes. Si se activa más de uno, al mismo tiempo, el programa se detendrá, el parche no se generará y tendremos que recurrir al log para saber donde está el error que hemos cometido.

    Disable reading footer: no
    Custom reading footer style: yes

· Hay parches que están divididos en dos partes porque cada una se aplica a un fichero diferente.

    src/libnickel.so.1.0.0.yaml:
      Force user line spacing in ePubs (part 1 of 2): yes # PART 2 in librmsdk.so.1.0.0.yaml
    src/librmsdk.so.1.0.0.yaml:
      Force user line spacing in ePubs (Part 2 of 2): yes # PART 1 in libnickel.so.1.0.0.yaml

NOTA: como se ve, los comentarios no tienen que estar necesariamente al principio de la línea.

· Si quieres traducir los mensajes del sistema que aparecen por pantalla (al bable pongamos por caso), o simplemente corregir alguno de ellos (alguna vez han puesto libri en vez de libros), puedes hacerlo. Para ello, deberás instalar el programa lrelease o indicarle al programa de parcheo donde se encuentra mediante

    lrelease: /path/to/lrelease

NOTA: yo no la uso (está comentada) porque lo tengo integrado en el sistema.

· En la sección translations debes indicar la lista de traducciones que quieres incluir en el parche.
  archivo con tu traducción: fichero a incluir en el parche

    translations:
      src/translations/trans_es.ts: usr/local/Kobo/translations/trans_es.qm

· Puede que no tengas ganas de traducir pero dispongas de un fichero que quieras incorporar/sustituir al/del sistema (tipos de letra, ficheros de guionado, etc.). Para eso está la sección files. Recuerda: "un sitio para cada cosa y cada cosa en su sitio".
  
  archivo que quieres incorporar al parche: fichero con la ubicación que ocupará en el dispositivo

    files:
      src/hyphenDicts/hyph_de.dic: usr/local/Kobo/hyphenDicts/hyph_de.dic

# Destripando un parche

· Los sangrados/espacios (siempre que no estén en un comentario) importan.

· Todo lo que va a continuación del símbolo # es un comentario.

· Un parche comienza con una palabra o frase al comienzo de una línea y termina cuando comienza el siguiente parche o finaliza el fichero.

· El nombre del parche termina con :. (Disable reading footer)

· Las partes del parche están precedidas por 2 espacios y -.

· Enabled indica si el parche está activado o no. No es importante lo que pongamos si usamos (y tenemos bien configurado) el fichero kobopatch.yaml. La recomendación es dejarlo desactivado aquí y activarlo o desactivarlo en kobopatch.yaml, aunque no es obligatorio.

· PatchGroup se usa para indicar que un parche pertenece a un grupo de parches autoexcluyentes (sólo uno de ellos se puede aplicar al mismo tiempo).

· Description es una descripción (más o menos larga) de lo que hace el parche. Si es larga es mejor utilizar | y comenzar en la siguiente línea (añadiendo un sangrado más de 2 espacios).

· A continuación están las órdenes.

IMPORTANTE: no hay que modificar NUNCA nada que no esté entre "" después de Replace.

    Disable reading footer:
      - Enabled: no
      - PatchGroup: Reading footer alternatives
      - Description: |
          Removes the reading footer for both ePub and KePub books, leaving a
          15px bottom margin which matches the 15px built-in top margin of ePub
          books.
      - FindReplaceString: {Find: "ReadingFooter {\n  qproperty-footerMargin: 105;\n}\n", Replace: "ReadingFooter {min-height:15px;max-height:15px;}\n"}
      - FindReplaceString: {Find: "#caption[qApp_deviceIsTrilogy=true] {\n  font-size: 19px;\n}\n", Replace: "#caption {font-size:1px;color:transparent;}\n\0"}

· Se pueden organizar las órdenes en diferentes líneas. De esta manera evitaremos, más facilmente, modificar lo que no se encuentre trás Replace.

    - FindReplaceString:
        Find:    "ReadingFooter {\n  qproperty-footerMargin: 105;\n}\n"
        Replace: "ReadingFooter {min-height:15px;max-height:15px;}\n"
    - FindReplaceString:
        Find:    "#caption[qApp_deviceIsTrilogy=true] {\n  font-size: 19px;\n}\n"
        Replace: "#caption {font-size:1px;color:transparent;}\n\0"

· En las órdenes que contengan "Zlib" (como ReplaceZlib) la diferencia de longitud entre el texto a cambiar y del texto modificado no es importante (dentro de unos límites, es un comprimido, pero... tiene sus límites).

· Las órdenes que no contienen "Zlib" (como FindReplaceString) deben respetar la igualdad de longitud entre las cadenas de caracteres.
  Si el texto que pongamos en Replace es más corto que el que teníamos en Find, probablemente ocurran cosas raras.
  Si el texto de Replace es más largo dará error.
  
· Hay que tener en cuenta que los caracteres de escape (los que comienzan por \ como \n -cambio de línea-) aunque están compuestos por 2 caracteres, en realidad es uno sólo y se cuantifica como tal.

· Se puede alterar/eliminar todo lo que no corresponda a tu dispositivo para ahorrar espacio y hacer más eficiente el sistema.

· Una \ al final de la línea indica que la orden continúa en la siguiente.

    - ReplaceZlib:
        Find:    "#coverArea[qApp_deviceIsPhoenix=true] {\n  qproperty-spacing: 20;\n}\n\
                  #coverArea[qApp_deviceIsTrilogy=true] {\n  qproperty-spacing: 10;\n}\n\
                  #coverArea[qApp_deviceIsDragon=true] {\n  qproperty-spacing: 30;\n}\n\
                  #coverArea[qApp_deviceIsDaylight=true] {\n  qproperty-spacing: 39;\n}"
        Replace: "#coverArea{qproperty-spacing:30;}"

Ref.: https://www.mobileread.com/forums/showthread.php?t=297338

# DICCIONARIOS

http://kobo.lectoreselectronicos.com/subweb/diccionarios.html

https://www.mobileread.com/forums/showpost.php?p=3312320&postcount=1

https://www.mobileread.com/forums/showthread.php?t=272762

# FONTS

https://www.mobileread.com/forums/showthread.php?t=204363

# nickel GUI not displaying certain characters

https://www.mobileread.com/forums/showthread.php?t=278392

# Kute File Monitor (kfmon)

https://www.mobileread.com/forums/showpost.php?p=3319170&postcount=1

# KSM 09

https://www.mobileread.com/forums/showthread.php?t=293804

# KoReader

https://github.com/koreader/koreader/wiki

# Plato

https://github.com/baskerville/plato

https://www.mobileread.com/forums/showthread.php?t=292914

# Vlasovsoft (cr3)

http://www.pbchess.vlasovsoft.net/en/index.html

# The UltimateMangaReader

https://www.mobileread.com/forums/showthread.php?t=299094

# Random Screensaver Image

https://www.mobileread.com/forums/showthread.php?t=255238

# FBInk

https://www.mobileread.com/forums/showthread.php?t=299110

# Miniclock

https://www.mobileread.com/forums/showpost.php?p=3762123&postcount=6

# Conectar kobo a la nube

http://www.lectoreselectronicos.com/foro/showthread.php?18327-SOLUCIONADO-Conectar-kobo-a-la-nube-Varios-m%C3%A9todos&p=252630&viewfull=1#post252630


