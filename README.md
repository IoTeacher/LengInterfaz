#  Lectura y ejercicios de ARM32 del ebook OpenSource

<a href="https://cooltext.com"><img src="https://images.cooltext.com/5474865.png" width="353" height="74" alt="RESUMEN" /></a>
<br />Image by <a href="https://cooltext.com">Cool Text: Free Graphics Generator</a> - <a href="https://cooltext.com/Edit-Logo?LogoID=3664571937">Edit Image</a>

## 1.1. Lectura previa

### 1.1.1. Características generales de la arquitectura ARM

ARM es una arquitectura RISC de 32 bits, salvo la versión del core ARMv8- A que es mixta 32/64 bits . Se trata de una arquitectura licenciable.

El chip en concreto que lleva la Raspberry Pi es el BCM2835, se trata de un SoC que contiene además de la CPU otros elementos como un núcleo GPU y un núcleo DSP que es un procesador más pequeño y simple que el principal, pero especializado en el procesado y representación de señales analógicas. La CPU en cuestión es la ARM1176JZF-S.

La arquitectura ARMv6 presenta un conjunto de 17 registros (16 principales más uno de estado) de 32 bits cada uno.
**Registros Generales**. Su función es el almacenamiento temporal de datos. Son los 13 registros que van R0 hasta R12.
**Registros Especiales**. Son los últimos 3 registros principales: R13, R14 y R15. Como son de propósito especial, tienen nombres alternativos.
**Registro CPSR**. Almacena las banderas condicionales y los bits de control. 

### 1.1.2. El lenguaje ensamblador
El ensamblador es un lenguaje de bajo nivel que permite un control directo de la CPU y todos los elementos asociados. Cada línea de un programa ensamblador consta de una instrucción del procesador y la posición que ocupan los datos de esa instrucción. El procedimiento es similar al de cualquier lenguaje compilado. Un conjunto de instrucciones y/o datos forman un módulo fuente. Este módulo es la entrada del compilador, que chequea la sintaxis y lo traduce a código máquina formando un módulo objeto. Finalmente, un enlazador (montador ó linker) traduce todas las referencias relativas a direcciones absolutas y termina generando el ejecutable.

Al ser un lenguaje de bajo nivel, presenta como principal característica la flexibilidad y la posibilidad de acceso directo a nivel de registro. En contrapartida, programar en ensamblador es laborioso.

### 1.1.3. El entorno
Los pasos habituales para hacer un programa son los siguientes: lo primero es escribir el programa en el lenguaje fuente. Traducirlo a lenguaje máquina. Este genera un fichero con la traducción de dicho programa, pero todavía no es un programa ejecutable. Un fichero ejecutable contiene el programa traducido más una serie de códigos que debe tener todo programa que vaya a ser ejecutado en una máquina determinada. El encargado de unir el código del programa con el código de estas librerías es un programa llamado montador que genera el programa ejecutable.

![](https://github.com/JacoboRosas/LengInterfaz/blob/main/imagenes/1.png)

Durante el proceso de creación de un programa se suelen producir errores. Hay dos tipos de errores: los **sintácticos** o detectables en tiempo de traducción y los errores **semánticos** o detectables en tiempo de ejecución.

### 1.1.5 Aspecto de un programa ensamblador
La principal característica de un módulo fuente en ensamblador es que existe una clara separación entre las instrucciones y los datos. La estructura más general de un módulo fuente es: 

 - **Sección de datos**. Viene identificada por la directiva **.data**. En esta zona se definen todas las variables que utiliza el programa  con el objeto de reservar memoria para contener los valores asignados.
 - **Sección de código**. Se indica con la directiva **.text**, y sólo puede contener código o datos no modificables.

De estas dos secciones la única que obligatoriamente debe existir es la sección .text.

Las instrucciones son representaciones nemotécnicas del juego de instrucciones del procesador. Un dato es una entidad que aporta un valor numérico, que puede expresarse en distintas bases o incluso a través de una cadena. Los símbolos son representaciones abstractas que el ensamblador maneja en tiempo de ensamblado pero que en el código binario resultante tendrá un valor numérico concreto. Hay tres tipos de símbolos: las etiquetas, las macros y las constantes simbólicas. Por último tenemos las directivas, que sirven para indicarle ciertas cosas al ensamblador.

**Datos**
Los datos se pueden representar de distintas maneras. Para representar números tenemos 4 bases. La más habitual es en su forma decimal, la cual no lleva ningún delimitador especial. Luego tenemos otra muy útil que es la representación hexadecimal, que indicaremos con el prefijo 0x. Otra interesante es la binaria, que emplea el prefijo 0b antes del número en binario. La cuarta y última base es la octal, que usaremos en raras ocasiones y se especifica con el prefijo 0.

**Simbolos**
En la zona de datos, las etiquetas pueden representar variables, constantes y cadenas. En la zona de código podemos usar etiquetas de salto, funciones y punteros a zona de datos.

**Instrucciones**
Las instrucciones del **as**  responden al formato general:

![](https://github.com/JacoboRosas/LengInterfaz/blob/main/imagenes/2.png)

El Campo *etiqueta*, si aparece, debe estar formado por una cadena alfanumérica. La cadena no debe comenzar con un dígito y no se puede utilizar como cadena alguna palabra reservada del **as** ni nombre de registro del microprocesador.

El campo *Nemotécnico* (**ldr** en el ejemplo) es una forma abreviada de nombrar la instrucción del procesador.

El campo *Operando/s* indica dónde se encuentran los datos. Los operandos pueden ser registros, etiquetas, valores inmediatos o incluso elementos más complejos como desplazadores/rotadores o indicadores de pre/post-incrementos. 

El campo *Comentario* es opcional y debe comenzar con la secuencia /* y acabar con */ al igual que los comentarios multilínea en C.

Cada instrucción del as se refiere a una operación que puede realizar el microprocesador. También hay pseudoinstrucciones que son tratadas por el preprocesador como si fueran macros y codifican otras instrucciones, como lsl rn, #x que codifica mov rn, rn, lsl #x, o bien push/pop que se traducen instrucciones stm/ldm más complejas y difíciles de recordar para el programador. Podemos agrupar el conjunto de instrucciones del as, según el tipo de función que realice el microprocesador en las siguientes categorías:
 - **Instrucciones de transferencia de datos.** Mueven información entre registros y posiciones de memoria.
 - **Instrucciones aritméticas.** Realizan operaciones aritméticas sobre números binarios o BCD.
 - **Instrucciones de manejo de bits.** Realizan operaciones de desplazamiento, rotación y lógicas sobre registros o posiciones de memoria.
 - **Instrucciones de transferencia de control.** Se utilizan para controlar el flujo de ejecución de las instrucciones del programa.
 
**Directivas**
Las directivas son expresiones que aparecen en el módulo fuente e indican al compilador que realice determinadas tareas en el proceso de compilación. Son fácilmente distinguibles de las instrucciones porque siempre comienzan con un punto. El uso de directivas es aplicable sólo al entorno del compilador, por tanto varían de un compilador a otro y para diferentes versiones de un mismo compilador. Las directivas más frecuentes en el **as** son:

*Directivas de asignación*: Se utilizan para dar valores a las constantes o reservar posiciones de memoria para las variables (con un posible valor inicial). .byte, .hword, .word, .ascii, .asciz, .zero y .space son directivas que indican al compilador que reserve memoria para las variables del tipo indicado.

*Directivas de control*: .text y .data sirven para delimitar las distintas secciones de nuestro módulo. .align alineamiento es para alinear el siguiente dato, rellenando con ceros, de tal forma que comience en una dirección múltiplos del número que especifiquemos en alineamiento, normalmente potencia de 2.

*Directivas de operando*: Se aplican a los datos en tiempo de compilación. En general, incluyen las operaciones lógicas &, |, ∼, aritméticas +, -, *, /, % y de desplazamiento <, >, <<, >>.

*Directivas de Macros*: Una .macro es un conjunto de sentencias en ensamblador (directivas e instrucciones) que pueden aparecer varias veces repetidas en un programa con algunas modificaciones (opcionales). 

### 1.1.6 Ensamblar y linkar un program
La traducción o ensamblado de un módulo fuente (nombreprograma.s) se realiza con el programa Gnu Assembler, con el siguiente comando:
```bash
as -o nombreprograma.o nombreprograma.s
```
Para montar (linkar) hay que hacer:
```bash
gcc -o nombreprograma nombreprograma.o
```
## 1.2. Enunciados de la práctica
### 1.1.6 Cómo empezar
Antes de nada averigua cuál es la dirección IP de la Raspberry Pi dentro de la red local. Por defecto el usuario es pi y la contraseña raspberry. Suponiendo que la dirección IP asignada es 192.168.1.42, utilizaremos ssh:
```bash
ssh pi@192 .168.1.42
```
Podemos editar programas con el comando **nano**. 
Dentro del editor tenemos una pequeña guía de comandos en la parte inferior. También podemos acceder a una ayuda más detallada pulsando F1 dentro del editor. 
Estos son los atajos de teclado más comunes:
|Atajo| Función |
|--|--|
| Ctrl-x | Salir de nano. |
| Ctrl-o | Salvar cambios |
| Ctrl-c | Muestra panel con información sobre número de linea |
| Alt-g | Saltar a un número de línea en concreto. |
| Alt-a ó Ctrl-6 | Seleccionar texto, mover cursores para definir región. |
| Alt-^ | Copiar selección. |
| Ctrl-k | Cortar selección. |
| Ctrl-u | Pegar selección. |
| Ctrl-w | Buscar texto. |
| Alt-w | Repetir última búsqueda. |
| Alt-r | Buscar y reemplazar. |


Primero traducimos con:
```bash
as -o intro1.o intro1.s
```
Posteriormente
```bash
gcc -o intro1 intro1.o
```
Y por último, podemos abrir el programa
```bash
gdb intro1
```
El gdb ofrece muchas posibilidades, sólo explicaremos las que vamos a utilizar. Nada más ejecutar el comando anterior, el gdb se encuentra en modo interactivo:
Podemos escribir help para acceder a la ayuda integrada, o bien irnos a la página
web de documentación del gdb
```bash
( gdb ) quit
```
Un primer paso es decirle al depurador que queremos lanzar el programa,
esto es, cargarlo en memoria y apuntar a la primera instrucción del mismo, esto se realiza con el siguiente comando:
```bash
( gdb ) start
```
Podemos modificar el valor de los registros por medio de la orden print, teniendo en cuenta los efectos adversos que esto podría ocasionar, ya que estamos alterando el funcionamiento de nuestro programa.

Puedes emplear disas (pero no disa) como comando abreviado. En realidad todos los comandos pueden abreviarse, sólo que no lo hemos hecho para os resulte más fácil su memorización

**1.2.2. Enteros y naturales**
Recordemos que cuando se representa cualquier dato en memoria, éste tiene un valor explícito y un valor implícito. En este apartado queremos que veais la diferencia entre el valor explícito y el valor implícito interpretado como un natural y como un entero.

**1.2.4. Rotaciones y desplazamientos**
Las instrucciones de desplazamiento pueden ser lógicas o aritméticas. Los desplazamientos lógicos desplazan los bit del registro fuente introduciendo ceros (uno o más de uno). El último bit que sale del registro fuente se almacena en el flag C. El desplazamiento aritmético hace lo mismo, pero manteniendo el signo.

**Instrucciones de desplazamiento lógico**
<br>
![](https://github.com/JacoboRosas/LengInterfaz/blob/main/imagenes/3.png)

**Instrucciones de desplazamiento aritmético**
<br>
![](https://github.com/JacoboRosas/LengInterfaz/blob/main/imagenes/4.png)

Las instrucciones de rotación también desplazan, pero el bit que sale del valor se realimenta. No existe ninguna instrucción para rotar hacia la izquierda ROL, ya que puede simularse con la de rotación a la derecha ROR que sí existe.
Las instrucciones de rotación con el carry funcionan de manera similar, pero el bit que entra es el que había en el flag C y el que sale va a parar al flag C.  Estas instrucciones sólo rotan un bit, al contrario que las anteriores que
podían rotar/desplazar varios.

También podemos forzar el flag C o cualquier otro flag al valor que queramos con la siguiente instrucción.
```bash
msr cpsr_f, # valor
```
Todas las instrucciones de rotación en realidad son subinstrucciones que el ensamblador traduce a una instrucción mov. En realidad las dos siguientes instrucciones son totalmente equivalentes.

```bash
LSRs r0, r0,#1 
movs r0, r0, LSR #1
```
Pero se tiende a escoger siempre la más sencilla, en este caso la primera.

**1.2.5. Instrucciones de multiplicación**
Las instrucciones de multiplicación admiten muchas posibilidades, debido a que es una operación en la cual el resultado tiene el doble de bits que cada operando.

| Instrucción | Bits | Nombre |
|--|--|--|
| mul | 32=32x32 | Multiplicación truncada. |
| umull | 64=32x32 | Multiplicación sin signo de 32bits. |
| smull | 64=32x32 | Multiplicación con signo de 32bits. |
| smulw* | 32=32x16 | Multiplicación con signo de 32x16bits. |
| smul** | 32=16x16 | Multiplicación con signo de 16x16bits. |

# Capítulo 2 Tipos de datos y sentencias de alto nivel
### 2.1.1. Modos de direccionamiento del ARM
En la arquitectura ARM los accesos a memoria se hacen mediante instrucciones específicas ldr y str. El resto de instrucciones toman operandos desde registros o valores inmediatos, sin excepciones. Existen otras arquitecturas como la Intel x86, donde las instrucciones de procesado nos permiten leer o escribir directamente de memoria. Normalmente se opta por direccionamiento a memoria en instrucciones de procesado en arquitecturas con un número reducido de registros, donde se emplea la memoria como almacén temporal.

**Direccionamiento inmediato**. El operando fuente es una constante, formando
parte de la instrucción.
```bash
mov r0, # 1
add r2, r3, #4
```
**Direccionamiento inmediato con desplazamiento o rotación**. Es una variante del anterior en la cual se permiten operaciones intermedias sobre los registros.
```bash
mov r1, r2, LSL #1 /* r1 <- (r2*2) */
mov r1, r2, LSL #2 /* r1 <- (r2*4) */
mov r1, r3, ASR #3 /* r1 <- (r3/8) */
```
Estas instrucciones también se usan implicitamente para la creación de constantes, rotando o desplazando constantes más pequeñas de forma transparente al usuario. Como todas las instrucciones ocupan 32 bits, es técnicamente imposible que podamos cargar en un registro cualquier constante de 32 bits con la instrucción mov.

Un método para determinar si una constante entra o no en una instrucción mov es pasar la constante a binario y quitar los ceros de la izquierda y de la derecha y contar el número de bits resultante. Si el número de bits es menor o igual que 8, la constante entra en una instrucción mov.

**Direccionamiento a memoria, sin actualizar registro puntero.** Es la forma más sencilla y admite 4 variantes. Después del acceso a memoria ningún registro implicado en el cálculo de la dirección se modifica.

 - [Rx, #+inmediato] [Rx, #-inmediato]
 Simplemente añade (o sustrae) un valor inmediato al registro dado para
calcular la dirección
 - [Rx, +Ry] [Rx, -Ry]
 Parecido al anterior pero en lugar de un inmediato emplea otro registro. Útil en el caso de queramos mantener fijo el registro Rx y movernos con Ry, o bien para acceder a desplazamientos mayores a 4095.
 - [Rx, +Ry, operación_desp #inmediato] [Rx, -Ry, operación_desp  #inmediato]
 En este caso aplicamos una operación de desplazamiento o rotación sobre
el segundo registro Ry.

**Direccionamiento a memoria, actualizando registro puntero.** En este modo de direccionamiento, el registro que genera la dirección se actualiza con la propia dirección.  De esta forma podemos recorrer un array con un sólo registro sin necesidad de hacer el incremento del puntero en una instrucción aparte. Los tres siguientes tipos
son los postindexados.

 - [Rx], #+inmediato [Rx], #-inmediato
 Una notación muy parecida a la versión que no actualiza registro, la única diferencia es que la constante de desplazamiento queda fuera de los corchetes. 
 - [Rx], +Ry [Rx], -Ry
 Igual que antes pero con registro en lugar de inmediato.

 - [Rx], +Ry, operación_desp #inmediato [Rx], -Ry, operación_desp #inmediato
   Nótese que en todos los modos postindexados encerramos entre llaves el primer registro, que es el que se va a utilizar en la instrucción de lectura o escritura en memoria.
   
Ya hemos visto la notación postindexada. Veamos ahora los tres modos preindexados.

 - [Rx, #+inmediato]! [Rx, #-inmediato]!
 La idea en todos los casos es encerrar entre corchetes la dirección que se
va a usar en la instrucción. Para diferenciarlo del caso que no actualiza
el registro le añadimos un ! al final.

 - [Rx, +Ry]! [Rx, -Ry]!
Similar al anterior pero usando Ry en lugar de inmediato.

 - [Rx, +Ry, operación_desp #inmediato]! [Rx, -Ry, operación_desp
   #inmediato]!
Tercer y último caso de direccionamiento preindexado. Al igual que antes, desgloso en dos instrucciones para ver el funcionamiento exacto.

**2.1.2. Tipos de datos**
Tipos de datos básicos. En la siguiente tabla se recogen los diferentes tipos de datos básicos que podrán aparecer en los ejemplos, así como su tamaño y rango de representación.

![](https://github.com/JacoboRosas/LengInterfaz/blob/main/imagenes/6.png)

**Punteros.** Un puntero siempre ocupa 32 bits y contiene una dirección de memoria

**Vectores**. Todos los elementos de un vector se almacenan en un único bloque de memoria a partir de una dirección determinada. Los diferentes elementos se almacenan en posiciones consecutivas, de manera que el elemento i está entre los i-1 e i+1.  Los vectores están definidos siempre a partir de la posición 0.

**Matrices bidimensionales**. Una matriz bidimensional de N×M elementos se almacena en un único bloque de memoria. Interpretaremos una matriz de N×M como una matriz con N filas de M elementos cada una. Si cada elemento de la matriz ocupa B bytes, la matriz ocupará un bloque de M ×N ×B bytes

**2.1.3. Instrucciones de salto**
Las instrucciones de salto pueden producir saltos incondicionales (b y bx) o saltos condicionales. Cuando saltamos a una etiqueta empleamos b, mientras que si queremos saltar a un registro lo hacemos con bx. La variante de registro bx la solemos usar como instrucción de retorno de subrutina, raramente tiene otros usos. En los saltos condicionales añadimos dos o tres letras a la (b/bx), mediante las cuales condicionamos si se salta o no dependiendo del estado de los flags. Estas condiciones se pueden añadir a cualquier otra instrucción, aunque la mayoría de las veces lo que nos interesa es controlar el flujo del programa y así ejecutar o no un grupo de instrucciones dependiendo del resultado de una operación.
 
**2.1.4. Estructuras de control de alto nivel**.
Veremos cómo se traducen a ensamblador las estructuras de control de alto nivel que definen un bucle, así como las condicionales (if-else). Las estructuras for y while se pueden ejecutar un mínimo de 0 iteraciones. Para programar en ensamblador estas estructuras se utilizan instrucciones de salto condicional. Previo a la instrucción de salto es necesario evaluar la condición del bucle o de la sentencia if, mediante instrucciones aritméticas o lógicas, con el fin de actualizar los flags de estado. 

**Estructura del for y while en C.** 
```c
int vi , vf , i ;
for ( i= vi ; i <= vf ; i ++ ){
/* Cuerpo del bucle */
}
i= vi ;
while ( i <= vf ){
/* Cuerpo del bucle */
i ++;
}
```
**Traducción de las estructuras for y while.**
```bash
             ldr r1, = vi
             ldr r1, [ r1 ]
             ldr r2, = vf
             ldr r2, [ r2 ]
bucle :      cmp r1, r2
             bhi salir
             /* Cuerpo
                   del
                   bucle */
             add r1, r1, # 1
             b bucle
salir :
```
**2.1.5. Compilación a ensamblador**
Normalmente los compiladores crean código compilado en un único paso. En el caso de gcc este proceso se hace en dos fases: en una primera se pasa de C a ensamblador, y en una segunda de ensambladador a código compilado.
```c
# include < stdio .h >
void main ( void ){
int i;
for ( i= 0; i <5; i ++ ){ 
printf ( " %d\n " , i );
} 
}
```
Después de crear el fichero tipos3.s, lo compilamos con este comando.
```c
gcc -Os -S -o tipos3a.s tipos3.c
```
Con el parámetro -S forzamos la generación del .s en lugar del .o y con -Os le indicamos al compilador que queremos optimizar en tamaño, es decir que queremos código ensamblador lo más pequeño posible, sin importar el rendimiento del mismo.
### 2.2. Enunciados de la práctica
**2.2.1. Suma de elementos de un vector**
En este primer apartado, estudiaremos un bucle que calcula la suma de todos los elementos de un vector. El vector se denomina vector y tiene 5 elementos de tipo int (entero de 32 bits). Los algoritmos que realizan la suma de sus elementos, tanto en C como en ensamblador, se pueden encontrar en los listados 2.8 y 2.9.

**Suma de elementos de un vector (tipos4.c)**
```c
# include < stdio .h >
void main ( void ){
   int i , suma ;
   int vector [5]= {128 , 32 , 100 , -30 , 124};
   for ( suma = i = 0; i <5; i ++ ){
      suma += vector [i ]; 
   }
   printf (" La suma es %d \n" , suma );
)
```
**Suma de elementos de un vector (tipos4.s)**
```bash
data
var1 : .asciz " La suma es %d \n"
var2 : .word 128, 32, 100, - 30, 124

.text
.global main

/* Salvamos registros */
main : push { r4, lr }

/* Inicializamos variables y apuntamos r2 a var2 */
     mov r0, # 5
     mov r1, # 0
     ldr r2, = var2
     
/* Bucle que hace la suma */
bucle : ldr r3, [ r2 ] , # 4
     add r1, r1, r3
     subs r0, r0, #1
     bne bucle

/* Imprimimos resultado */
     ldr r0, = var1
     bl printf
     
/* Recuperamos registros y salimos */
     pop { r4, lr }
     bx lr
```
Si analizamos el código en ensamblador (listado 2.9), veremos que se recorre todo el vector con el registro r0, realizándose la suma sobre el registro r1. A diferencia de ejemplos anteriores decrementamos de 5 a 0, así nos ahorramos una comparación, ya que la instrucción subs detecta cuando hemos llegado al valor cero activando el flag Z.

En r2 vamos recorriendo el vector elemento a elemento mediante un modo postindexado que apunta al siguiente elemento una vez leemos el actual con ldr. Una vez calculada la suma en r1, la mostramos por pantalla mediante una llamada a printf.
