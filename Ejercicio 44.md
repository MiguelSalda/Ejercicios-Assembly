# Conversión de Entero a ASCII  
**Nombre:** Miguel Guadalupe Saldana Ramirez  
**Número de Control:** 21210426  
**Práctica:** Ejercicios Assembly 21-30  
**Fecha:** 19/11/2024  

```assembly
.section .data
input: .word 12345           // Número entero a convertir
buffer: .space 16            // Espacio para almacenar la cadena ASCII
length: .word 0              // Variable para almacenar la longitud de la cadena
newline: .asciz "\n"         // Salto de línea

.section .text
.global _start

_start:
    // Cargar el número entero
    LDR w0, =input            // Dirección del número de entrada
    LDR w0, [w0]              // Cargar el valor del número en w0
    MOV x1, #0                // Inicializar índice del buffer

    // Llamar a la subrutina de conversión
    BL int_to_ascii           // Conversión de w0 a ASCII, resultado en buffer

    // Imprimir la cadena ASCII
    MOV x0, #1                // File descriptor 1 (stdout)
    LDR x1, =buffer           // Dirección del buffer
    LDR w2, =length           // Longitud de la cadena
    LDR w2, [w2]              // Cargar la longitud en w2
    MOV x8, #64               // syscall: write
    SVC #0

    // Imprimir un salto de línea
    MOV x0, #1                // File descriptor 1 (stdout)
    LDR x1, =newline          // Dirección del salto de línea
    MOV x2, #1                // Longitud del salto de línea
    MOV x8, #64               // syscall: write
    SVC #0

    // Finalizar el programa
    MOV x8, #93               // syscall: exit
    MOV x0, #0                // Estado de salida
    SVC #0

// Subrutina para convertir un número entero a ASCII
// Entrada: w0 (número entero a convertir)
// Salida: buffer con la cadena ASCII, longitud en length
int_to_ascii:
    SUB sp, sp, #16           // Reservar espacio en la pila
    ADD x2, sp, #15           // Inicializar puntero al final del buffer
    MOV w1, #10               // Divisor (base 10)
    MOV w3, w0                // Copiar el número a w3
    MOV w4, #0                // Longitud inicial

convert_loop:
    UDIV w5, w3, w1           // Cociente = número / 10
    MADD w6, w5, w1, w3       // Resto = número % 10
    ADD w6, w6, #'0'          // Convertir el resto a carácter ASCII
    STRB w6, [x2], #-1        // Almacenar el carácter y mover el puntero hacia atrás
    MOV w3, w5                // Actualizar el número con el cociente
    ADD w4, w4, #1   
