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
