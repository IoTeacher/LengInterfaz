#  Lectura y ejercicios de ARM32 del ebook OpenSource

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

![IMG1](https://github.com/JacoboRosas/LengInterfaz/blob/main/1.png)

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

![IMG2](https://github.com/JacoboRosas/LengInterfaz/blob/main/2.png)

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
