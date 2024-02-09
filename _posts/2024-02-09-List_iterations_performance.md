---
layout: post
title:  "Iteraciones en Python y performance"
---

Iterar sobre listas es sin lugar a dudas una de las tareas mas frecuentes en la mayoría de las piezas de código.

![image](/static/img/simpsons/juantoponaranja.jpg)


Al momento de hacer iteraciones y operar sobre listas o arrays, Python nos brinda un número no menor de formas de traducir en código aquello que queremos hacer. Uno ya tiene casi automatizado en su mente la forma:

    for item in iterable:
        {do something with item}


Para la mayoría de los casos, este es el uso mas recomendable, mas claro de leer, mas universal, podríamos llamarlo. Si tenemos 10, 100, 1000, 10.000 elementos, python nos resolverá el asunto en una fracción de segundo y nuestro código seguirá su camino. Sin embargo: ¿Qué sucede cuando el tamaño del iterable es excesivamente grande? ¿Qué sucede si tenemos iteraciones anidadas, que nos generen un O exponencial?

Construyamos una lista de 100 millones de elementos, por ejemplo (Si te parece exagerado piensa en el genoma humano o busca el primer video que encuentres en tu computadora) y operemos sobre ella a ver que sucede.

    from timeit import timeit  
    L = [0] * 100_000_000

    def for_cicle(l):
        for i in range (len(l)):
        l[i] += 1

    print(timeit.timeit('for_cicle(L)', number=1,  globals=globals()))


En mi vieja Toshiba le toma unos 5.7 segundos.

Uno podría decir: "¡Bueno, nada mal, son 100 millones de números y en ese tiempo aprovecho y me cebo un matecito!". Pero pensemos qué sucedería si tuviéramos otro ciclo anidado de la misma magnitud: pues quizás ya nos podríamos tomar un café caliente mientras lo procesa.¿Y si además tuviéramos que repetir esa operación para otras _n_ listas? Pues ya tendríamos tiempo de ir al bar y pedirnos el menú del día. No tengo nada en contra del tiempo libre y la gastronomía, pero quizás en otro entorno esto sería inaceptable. Consideremos este mismo escenario pero corriendo en un backend, con varias peticiones de usuario a este mismo proceso, pues es muy probable que comprometamos seriamente a nuestro servidor. 
Uno podría pensar que no hay mas remedio para esto que mejorar los recursos de hardware,  sin embargo, si llevamos este mismo algoritmo a un lenguaje de bajo nivel como C++ o Rust, notaremos que la eficiencia del código es extremadamente superior. Entonces, podríamos simplemente migrar nuestro desarrollo o al menos las partes mas sensibles a uno de estos lenguajes y asunto solucionado.
Veremos, sin embargo, que esta salida no es la única posible, y que podemos obtener rendimientos equivalentes sin movernos ni un ápice de nuestro código Python, utilizando bibliotecas especialmente diseñadas para este tipo de necesidades.

Vamos entonces a mostrar primero las formas mas comunes de hacer iteraciones equivalentes a la ya descrita, y compararemos su eficiencia, ordenándolas de menor a mayor performance:
Luego, nos introduciremos brevemente en las bibliotecas ***numpy*** y ***numba*** y veremos la diferencia en la velocidad de los resultados:

**\*IMPORTANTE: Cabe aclarar que los resultados están sujetos a cada ordenador, al intérprete python que se use, y a muchos otros factores que harán que los resultados varíen significativamente. Los resultados aquí expuestos son a modo ilustrativo. Las secciones de código no funcionan por si mismas, puede bajarse el [código completo aquí](https://github.com/PabloAliArgentina/DScy/blob/main/iter_performance.py)**

---
---

## Iterando con métodos nativos

### While Loop: El viejo y confiable.

No es una buena idea usar ciclos while donde podrían usarse iteradores, pero no deja de ser una posibilidad, de todas las probadas, la menos eficiente:

    def while_cicle(l):
        i=0
        length = len(l)
        while i < length:
            l[i] += 1
            i += 1
        return l

**8.593 segundos**

---

### Usando *map()*

Probablemente no sería la opción a elegir tampoco por la mayoría de los programadores, y por cierto, tampoco es eficiente comparativamente:

    def map_method(l):
        return list(map(lambda x: x+1, l))

**6.133 segundos**

---

### Ciclo *for*


    def for_cicle(l):
        for i in range(len(l)):
            l[i] += 1
        return l

**5.69 segundos**

---

### Comprensión de lista

Una forma muy utilizada en python, que nos permite generar y manipular listas en una sola línea de código. Aquí vemos que, además, es bastante mas eficiente:

    def comprehensive(l):
        return [i+1 for i in l]

**3.164 segundos**

---
---


## Iterando con *Numpy* y *Numba*


Mas allá de las notables diferencias entre cada método de los revisados hasta acá, notamos que el orden de magnitud de los tiempos de ejecución es relativamente similar, pero muy lejano a lo que podríamos lograr con otros lenguajes. Aquí es donde entran en juego las estrellas que estábamos esperando aparecer.

![image](/static/img/simpsons/malditanaranja.jpeg)

---

### Numpy

[Numpy](https://numpy.org) es una biblioteca programada en C/C++ para manejo vectorizado de arrays numéricos en _n_ dimensiones que optimiza el acceso a memoria, caché y procesador, dotado de un gran número de poderosas herramientas que nos permite operar con arrays de una manera algo diferente a lo convencional y con una velocidad que es realmente prodigiosa. Para aprender a usar numpy basta con leer su documentación y practicar un poco hasta familiarizarse con su sintaxis que a veces de tan simple nos deja perplejos, por ejemplo, nuestra versión del iterador usando _numpy_ se vería así:

    def numpy_method(l):
        return l + 1
        
**0.151 segundos!!!!!**

Como vemos, hemos reducido el tiempo de ejecución mas de 20 veces comparado con nuestra mejor iteración hasta el momento. Nada mal. ¿No es cierto?

---

### Numba

[Numba](https://numba.pydata.org) es una biblioteca de compilación en tiempo real que nos permite compilar parte de nuestro código python a código de máquina sin pasar por el bytecode (código intermedio) que genera python al compilar. De esta manera, el procesador no perderá tiempo y recursos en interpretar ese código intermedio en cada iteración, utilizándolos solamente para resolver los cálculos de nuestro algoritmo, resultando todo esto en una performance sumamente satisfactoria. La "contra" de usar *Numba* es que nos limita el abanico de tipos de datos y tipos de operaciones de Python que podemos utilizar dentro de una función que opere bajo esta modalidad, pero generalmente uno halla la forma de traducir esos datos antes y bien que suele valer la pena hacerlo. Una de las formas mas frecuentes de usar *numba* es a través de los decoradores _jit_ (just in time) colocados justo antes de la declaración de una función, y que le indica al intérprete python que esa función será compilada en tiempo real por *numba*. En nuestro caso de uso, quedaría así:

    @njit
    def njit_for_cicle(l:numpy):
        for i in range(len(l)):
            l[i] += 1
        return l

**Y nos tarda 0.616 segundos**

Acá podemos notar dos cosas interesantes. Por un lado vemos que le pasamos a la función como parámetro una lista de _numpy_. Eso se debe a que *numpy* y *numba* son ampliamente compatibles y el poder utilizarlos juntos los convierte en pura "dinamita". Si quisiéramos pasarle una lista de python nos daría un warning, ya que *numba* "prefiere" listas tipadas.
Por otra parte, vemos que el tiempo de ejecución es bastante superior a lo que obtuvimos con *numpy_method* siendo que dije que el orden iba a ser de mayor a menor tiempo de ejecución. La razón de esto es que, cuando usamos @njit y ejecutamos la función por primera vez, *numba* tiene que compilar el código de la función, y eso toma tiempo de procesador. Sin embargo, si llamamos a la función nuevamente, al estar ya compilada, solo tomará el tiempo de ejecución y allí tendremos una real magnitud. Así que si volvemos a llamar a njit_for_cicle() nos demandará....


### **0.092 segundos. TENEMOS UN GANADOR SEÑORES!!!** 

![image](/static/img/simpsons/primerpremio.jpg)

---

## Resumen:

Como pudimos apreciar, trabajar en un lenguaje de alto nivel como python no tiene por que significar una merma en la performance de nuestro código, al contrario, al utilizar bibliotecas como las antes descritas no sucumbimos a la tentación de "meter mano" en código de bajo nivel mientras el problema que queremos resolver es otro, nos podemos enfocar de lleno en el análisis y proceso de los datos sin perder legibilidad y simpleza. Con este artículo espero estar comenzando una serie acerca del procesamiento eficiente de datos numéricos y data science. Espero que te haya resultado útil y/o interesante y por supuesto espero tu feedback ya sea con un mail en la dirección que figura debajo o dándole una estrella al [REPO](https://github.com/PabloAliArgentina/DScy) donde podrán [descargar el código completo](https://github.com/PabloAliArgentina/DScy/blob/main/iter_performance.py) relativo a este artículo. 

Pablo.


**(El contenido de esta publicación no puede ser reproducido en ninguna otra plataforma o medio sin mi autorización.)**
