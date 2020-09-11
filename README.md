# Buscando defectos de diseño :mag_right: 

En este ejercicio se pide que asuman el rol de quien debe _corregir_ o _revisar_ el código de otra persona. Esto que parece un juego de roles sin mucho sentido, es una actividad bastante habitual en el desarrollo de software y, por lo tanto, merece ser ejercitado.

La solución propuesta funciona, pero tiene algunos defectos hablando en términos de diseño de software. En particular, vamos a focalizarnos en algunos de los que están descriptos en la sección [Cualidades que se pueden estudiar con cierta independencia tecnológica](https://docs.google.com/document/d/14HdvHvS33WqYb6Ak0BGa0IeCTbzeCRSDKs-1Ot-qLDw/edit#heading=h.5ntffpcf14xd) del apunte de cualidades de diseño. 

Lo que se pide es que **lean el enunciado** que está más abajo, **miren la solución propuesta** y agreguen en el código un **comentario por cada defecto** que encuentren. En este comentario, indicar qué cualidad está en juego y por qué creen que lo que señalaron es un defecto. **Ojo :eye:**: se pide solamente _señalar los defectos_, no tienen que corregirlos (al menos, no para esta primera entrega).  

Deberían encontrar defectos relacionados a las siguientes cualidades:
* Simplicidad 
* Robustez
* (Des)acoplamiento 
* Cohesión
* Redundancia mínima
* Mutaciones controladas 

## 🌱 Semillas al viento 🌱

![Huerta](assets/huerta.jpg)

A raíz de la [polémica](https://www.elancasti.com.ar/opinion/2018/11/27/ley-cuestionada-389812.html) reciente sobre la posibilidad de que se modifique la [Ley de semillas y creaciones fitogenéticas, Nº 20.247](http://servicios.infoleg.gob.ar/infolegInternet/anexos/30000-34999/34822/texact.htm), una organización de pequeños productores nos pidió crear una aplicación para poder medir mejor el desempeño de sus huertas.

### 1. Plantas

Comenzaremos modelando a cada una de las plantas que hay en la huerta, de las cuales podemos configurar los siguientes aspectos:

* el **año de obtención** de la semilla. Es decir, en qué año la semilla que le dio origen se sacó de su planta "madre";
* la **altura** que tiene, medida en metros. Para este modelo, asumiremos que la altura **nunca** cambiará.

Además, queremos poder preguntarle:

* cuántas **horas de sol tolera**;
* si **es fuerte** o no;
* si **da nuevas semillas** o no.

De todos ellos, el año de obtención y la altura se configuran para cada planta; el resto se calcula en función de la especie y de características generales.

Se dice que una planta **es fuerte** si tolera más de 10 horas de sol al día, esto es igual para todas las plantas. El cálculo de las **horas de sol que tolera** depende exclusivamente de cada especie (ver más abajo).

Otro aspecto que nos interesa es saber si **da nuevas semillas**, para lo cual se tiene que cumplir que la planta sea fuerte _o bien_ una condición alternativa, que define cada especie.

Contemplaremos las especies que se detallan a continuación.

#### Menta
Tolera seis horas de sol al día. Como condición alternativa para saber si da semillas, hay que mirar si su `altura` es mayor a 0.4 metros.

#### Soja
La tolerancia al sol depende de su altura:
* menor a 0.5 metros: 6 horas;
* entre 0.5 y 1 metro: 7 horas;
* más de 1 metro: 9 horas;

La condición alternativa para que de semillas es que su propia semilla sea de obtención reciente (posterior al 2007) y además su altura sea de más de 1 metro. 

Por ejemplo, si tuviesemos una soja de 0.6 metros y de semilla de 2009, la planta tendría una tolerancia al sol de 7 horas y no daría semillas.

### 2. Variedades

Agregar al modelo la soja transgénica, que es similar a la soja pero con dos diferencias:

* nunca da nuevas semillas, porque las empresas que las comercializan las someten adrede a un proceso de esterilización (que les asegura no perder nunca a su clientes). 
* tolera el doble de horas de sol que la soja común.

### 3. Parcelas

De cada parcela se conoce:
* su **ancho** y su **largo**, medidos en metros. (Para evacuar dudas: sí, van en dos atributos distintos.);
* cuántas **horas de sol** recibe por día;
* las **plantas** que tiene, representadas por una colección.

Y se pide:
* conocer la **superficie** de la parcela, que se calcula... multiplicando `ancho` por `largo`;
* saber la **cantidad máxima** de plantas que tolera, que se calcula de la siguiente manera: si el `ancho` es mayor que el `largo`, la cue  nta es `superficie / 5`; si no `superficie / 3 + largo`;
* saber si **tiene complicaciones**, lo cual es así si alguna de sus plantas tolera menos sol del que recibe la parcela;
* poder **plantar una planta** que se recibe por parámetro. El efecto que produce es que se agregue a la colección. Esto debe arrojar un error si al plantar se supera la cantidad máxima _o bien_ si la parcela recibe al menos 2 horas más de sol que los que la planta tolera.

Un ejemplo:

Una parcela de 20 metros de ancho por 1 metro de largo que recibe 8 horas de sol por día, tiene una superficie de 20 metros cuadrados y la cantidad máxima de plantas que tolera es 4.

Si a esa parcela le plantamos 4 plantas de soja de más de 1 metro (que toleran 9 horas de sol), no tendría complicaciones. Si intentaramos agregar una quinta planta, se superaría la cantidad máxima y nos arrojaría un error.

### 4. Agricultoras

Por último, agregaremos al modelo a las agricultoras, que son las dueñas de la tierra. Para este ejercicio, diremos que la tierra no puede ser ni vendida ni comprada, y por lo tanto cuando se crea una agricultora se configura con todas las parcelas que tiene. 

Se pide lo siguiente:
* poder consultar la lista de **parcelas semilleras**. Decimos que una parcela es _semillera_ si todas sus plantas dan semillas.
* poder **plantar estratégicamente** una planta (valga la redundancia). En este caso lo que haremos es buscar la parcela que más lugar tenga y agregar allí la planta, respetando las restricciones que ya mencionamos a la hora de plantar. 
