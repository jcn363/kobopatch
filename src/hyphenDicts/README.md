# Cursillo acelerado.

   * Números pares unen, números impares separan.
   * Los números más bajos tienen mayor prioridad.
   * Los puntos indican los extremos, inicio o final de palabra.
   * El orden de las reglas importa, se leen secuencialmente.
   * `NOHYPHEN` caracteres (separados por comas) tras los que no se pone guión. Es decir, si tenemos `NOHYPHEN -` si se separa `ab-cd` será como `ab-` `cd` y no como _ab-- cd_.
   * `NEXTLEVEL` indica la finalización de un nivel de reglas y el inicio del siguiente. No se procesa un nivel sin haber procesado completamente el anterior.
   * `LEFTHYPHENMIN n` -> número mínimo de caracteres a la izquierda de la separación.
   * `RIGHTHYPHENMIN n` -> número mínimo de caracteres a la derecha de la separación.
   * `COMPOUNDLEFTHYPHENMIN n` -> número mínimo de caracteres a la izquierda de la separación en palabras compuestas.
   * `COMPOUNDRIGHTHYPHENMIN n`-> número mínimo de caracteres a la derecha de la separación en palabras compuestas.
    
La base está en la regla de oro: **_si siempre haces lo mismo, siempre obtendrás el mismo resultado_**.

Yo quería algo que funcionara siempre igual. Es decir, lo mejor posible, teniendo que hacer las menores modificaciones posibles, sin depender de lo que existe ni de lo que se pueda inventar... Pretencioso pero... estoy en ello.

Obviamente cada lenguaje tiene sus particularidades y, por lo tanto, sus propias reglas.
El número de letras a dejar, antes y depués del guión de división, depende del usuario. Izquierda y derecha son independientes. Puedes poner 2 y 5 ó 5 y 4, lo que más te guste. En general, cuanto más bajos los números más se aprovechará el espacio.

La primera intención era buscar una solución de compromiso que pudiera servir para todos los idiomas, de ahí que haya normas para alemán, pero también para euskara o inglés, en este fichero. El problema es que hay normas contradictorias: `nst` en castellano se dividiría `ns/t` pero en alemán `n/st`.

Nunca he entendido lo de las terminaciones _"malsonantes"_ (y en otros idiomas tienen lo mismo).

Ejemplo:

En un idioma dado existe una palabra `abcdefghijk`, que según las normas de ese idioma, puede tener punto de ruptura entre dos culesquiera de sus letras.

El fichero de guionado usado indica `LEFTHYPHENMIN 4` y `RIGHTHYPHENMIN 3`.

Luego sólo se puede dividir por cualquiera de estos puntos `abcd`/e/f/g/h/`ijk`.

Esto es, ~~a/b/c/d~~efgh~~i/j/k~~ no son opciones válidas.

    LEFTHYPHENMIN=3
    RIGHTHYPHENMIN=2
es como mejor aprovechamiento/regularidad se obtiene con este fichero en los Kobo debido a un error (`LEFTHYPHENMIN=3` es interpretado como `LEFTHYPHENMIN=2`).

 ### ¿De qué estamos hablando?

    Pequeño experimento
    Datos:

        Hay 470.260 palabras (según el diccionario)

        2 84
        3 437
        4 2444
        5 9004
        6 20600
        7 39502
        8 60267
        9 74786
        10 78715
        11 68731
        12 50448
        13 32445
        14 17986
        15 8331
        16 3868
        17 1533
        18 612
        19 313
        20 100
        21 32
        22 16
        23 4
        24 2

        Caso Adobe-2010 (LEFTHYPHENMIN=5 y RIGHTHYPHENMIN=5)
            285.839 palabras tienen 10 caracteres o menos.
            184.421 palabras tienen 11 caracteres o más. 
Es decir, que (sin otras consideraciones) se tratarían unas 2 palabras por cada 3 que se obviarían.

        Caso Propuesta_RAE (LEFTHYPHENMIN=3 y RIGHTHYPHENMIN=3)
            32.569 palabras tienen 6 caracteres o menos.
            437.691 palabras tienen 7 caracteres o más. 
Es decir, que (sin otras consideraciones) se tratarían unas 40 palabras por cada 3 que se obviarían.

        Caso Agresivo (LEFTHYPHENMIN=2 y RIGHTHYPHENMIN=2)
            2.965 palabras tienen 4 caracteres o menos.
            467.295 palabras tienen 5 caracteres o más. 
Es decir, que (sin otras consideraciones) se tratarían unas 472 palabras por cada 3 que se obviarían.

###### La cosa debería funcionar así:
* Si el epub (lo mismo se aplica a cualquiera de los ficheros y/o tag que contine) está codificado como `lang="es_ES"` y en el dispositivo tan sólo existe `hyph_es.dic`, cojerá éste y no habrá ningún problema.
* Si en el dispositivo se encuentran tanto `hyph_es.dic` como `hyph_es_ES.dic`, debería ejecutarlos en ese orden, o, como mal menor, ejecutar únicamente el último.
* Si el epub está codificado como `lang="es"` y en el dispositivo tan sólo existe `hyph_es_ES.dic`, aplicará éste o, como mal menor, no hará nada.
* Si en el dispositivo se encuentran 2 o más diccionarios (p. ej. `hyph_es_MX.dic` y `hyph_es_ES.dic`), no hará nada o cojerá el de la localización (si es cualquiera de las dos inidcadas, en este caso México o España).
* Y si no se encuentra un diccionario, para una codificación lingüística determinada, no hará nada. Así, algo como `<span lang="la">"Ave, Imperator, morituri te salutant"</span>` se quedará sin dividir, si no existe un hyph_la.dic en el dispositivo.

En cuanto a los xenismos (palabras de idiomas diferentes al castellano y que, por lo tanto, pueden atenerse a reglas de silabeo diferentes), hay tres posibilidades:
1. están declaradas expresamente en el libro mediante `<tag lang="cod_lang">palabra_o_expresión</tag>` (p.ej. `<i lang="la">ad hoc</i>`) con lo que serán tratadas por el fichero de silabeo de ese idioma (`hyphen_la.dic`), si existe, y serán buscadas en el diccionario correspondiente. Este sistema sería el más aconsejable, ya que tiene la ventaja añadida de que, si se convierte en audio, cojerá la voz del idioma correspondiente.
2. se controlan con un archivo de silabeo universal (mi pobre intento) al que se han añadido reglas de otros idiomas que no entren en conflicto con las del idioma base.
3. sólo se ha definido el atributo `lang` a nivel de libro o de archivo, con lo que el programa aplica las mismas normas a todas las palabras del libro o archivo. Es decir, si el libro tiene `lang="en"` en el `opf`, aunque esté escrito en castellano, las reglas que se aplicarán serán las del idioma inglés (`hyphen_en.dic`). 

Es tu fichero, son tus reglas. Quiero decir que pongo el fichero a disposición para que cada cual lo use como buenamente quiera.

###### El proceso de mezcla, de dos o más archivos de reglas, que se me ocurre, sería:
   1. Separar las reglas de cada archivo
   2. Agruparlas en una columna de una hoja de cálculo
   3. Duplicar la columna al lado
   4. Eliminar los números de una columna
   5. Seleccionar las dos columnas
   6. Ordenar por la columna sin números
   7. Eliminar la columna sin números
   8. Eliminar las reglas duplicadas
   9. Eliminar las reglas redundantes (aquellas con las mismas letras y diferentes números)
   10. Copiar las reglas a tu archivo de silabeo. 

# BIBLIOGRAFÍA

Ignacio Bosque y Violeta Demonte, Gramática descriptiva de la lengua española, Madrid, Espasa-Calpe, 1999.

Jorge de Buen, Manual de diseño editorial, México, Santillana, 2000.

José Fernández Castillo, Normas para correctores y compositores tipógrafos, Madrid, Espasa-Calpe, 1959.

Real Academia Española y Asociación de Academias de la Lengua Española, Diccionario panhispánico de dudas [DPD], Madrid, Santillana, 2005.

Real Academia Española [RAE], Nuevas normas de prosodia y ortografía, Madrid, Imprenta Aguirre, 1952.

Real Academia Española [RAE], Ortografía, Madrid, Imprenta Aguirre, 1969.

Real Academia Española [RAE], Ortografía de la lengua española, Madrid, Espasa, 1999.

Real Academia Española [RAE], Esbozo de una nueva gramática de la lengua española, Madrid, Espasa-Calpe, 1973.

V. Martínez Sicluna, Teoría y práctica de la tipografía, Barcelona, Gustavo Gili, 1945.

José Martínez de Sousa, Diccionario de ortografía de la lengua española, Madrid, Paraninfo, 1996.

Juan José Morato, Guía práctica del compositor tipográfico, Madrid, Hernando, 2. a ed., 1908 (1. a ed., 1900, 3. a ed., 1933).

José Polo, Ortografía y ciencia del lenguaje, Madrid, Paraninfo, 1974.

R. Ramos Martínez, Corrección de pruebas tipográficas, México, UTEHA, 1963.

P. Sanmartí, Compendio de gramática castellana, [Barcelona, s/e, 1907].

[TEX hyphenation patterns](https://tug.org/tex-hyphen/)

[hyph-es.tex](https://github.com/hyphenation/tex-hyphen/blob/master/hyph-utf8/tex/generic/hyph-utf8/patterns/tex/hyph-es.tex)
