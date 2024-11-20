# Conversión de ASCII a Entero  
**Nombre:** Miguel Guadalupe Saldana Ramirez  
**Número de Control:** 21210426  
**Práctica:** Ejercicios Assembly 21-30  
**Fecha:** 19/11/2024  

```assembly
.section .data
input: .asciz "12345"     // Cadena de entrada en formato ASCII
length: .word 5           // Longitud de la cadena
result: .word 0           // Variable para almacenar el resultado

.section .text
.global _start

_start:
    // Inicializar puntero a la cadena de entrada
    LDR x0, =input        // Dirección de la cadena de entrada
    LDR w1, =length       // Longitud de la cadena
    LDR w1, [w1]          // Cargar la longitud de la cadena en w1
    MOV w2, #0            // Inicializar el resultado en w2

    // Conversión de ASCII a entero
convert_loop:
    CMP w1, #0            // Verificar si quedan caracteres por procesar
    BEQ end_conversion    // Si no quedan caracteres, terminar

    LDRB w3, [x0], #1     // Cargar el siguiente carácter ASCII y avanzar el puntero
    SUB w3, w3, #'0'      // Convertir carácter ASCII a valor numérico
    MUL w2, w2, #10       // Multiplicar el resultado acumulado por 10
    ADD w2, w2, w3        // Sumar el valor numérico al resultado acumulado

    SUB w1, w1, #1        // Decrementar la longitud de la cadena
    B convert_loop        // Repetir para el siguiente carácter

end_conversion:
    STR w2, [x0, #-8]     // Guardar el resultado en la dirección de la variable `result`

    // Finalizar programa
    MOV x8, #93           // syscall: exit
    MOV x0, #0            // Estado de salida
    SVC #0
