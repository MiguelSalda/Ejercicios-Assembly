# Encontrar el máximo en un arreglo  
**Nombre:** Miguel Guadalupe Saldana Ramirez  
**Número de Control:** 21210426  
**Práctica:** Ejercicios Assembly 21-30  
**Fecha:** 12/11/2024  

```assembly
.section .data
array: .word 5, 12, 3, 7, 25, 19, 2   // Arreglo de enteros
array_size: .word 7                   // Tamaño del arreglo
output_msg: .asciz "El valor máximo en el arreglo es: "
newline: .asciz "\n"                  // Salto de línea

.section .text
.global _start

_start:
    // 1. Cargar la dirección y tamaño del arreglo
    LDR x0, =array                    // Dirección base del arreglo
    LDR w1, [x0]                      // Primer elemento del arreglo en w1 (valor inicial máximo)
    LDR x2, =array_size               // Dirección del tamaño del arreglo
    LDR w2, [x2]                      // Tamaño del arreglo en w2

    // 2. Iterar sobre el arreglo para encontrar el máximo
find_max:
    SUBS w2, w2, #1                   // Decrementa el tamaño
    BEQ print_result                  // Si w2 es 0, termina el ciclo
    LDR w3, [x0, w2, LSL #2]          // Cargar el elemento actual del arreglo en w3
    CMP w3, w1                        // Comparar el valor en w3 con el valor máximo en w1
    BLS find_max                      // Si w3 es menor o igual a w1, continuar
    MOV w1, w3                        // Si w3 es mayor, actualizar el máximo en w1
    B find_max                        // Repetir el ciclo

print_result:
    // 3. Imprimir mensaje de salida
    MOV x0, #1                        // File descriptor 1 (stdout)
    LDR x1, =output_msg               // Dirección del mensaje de salida
    MOV x2, #32                       // Longitud del mensaje
    MOV x8, #64                       // syscall: write
    SVC #0

    // 4. Convertir el valor máximo en w1 a ASCII y mostrarlo
    MOV w0, w1                        // Mover el valor máximo a w0
    BL int_to_ascii                   // Llamada a función de conversión, resultado en x0 (longitud) y x1 (dirección)
    MOV x2, x0                        // Longitud de la cadena ASCII convertida
    MOV x8, #64                       // syscall: write
    SVC #0

    // 5. Imprimir un salto de línea
    MOV x0, #1                        // File descriptor 1 (stdout)
    LDR x1, =newline                  // Dirección de la cadena de salto de línea
    MOV x2, #1                        // Longitud del salto de línea
    MOV x8, #64                       // syscall: write
    SVC #0

    // 6. Salir del programa
    MOV x8, #93                       // syscall: exit
    MOV x0, #0                        // Estado de salida
    SVC #0

// Subrutina para convertir un número en w0 a una cadena ASCII en x1
// Resultado: longitud de la cadena en x0, dirección en x1
int_to_ascii:
    SUB sp, sp, #16                   // Reserva espacio en la pila
    ADD x1, sp, #15                   // Puntero de fin de cadena
    MOV w2, #10                       // Divisor (base 10)
    MOV w3, w0                        // Copia del número a convertir en w3
    MOV w4, #0                        // Longitud inicial

convert_loop:
    UDIV w5, w3, w2                   // Divide w3 por 10, guarda el cociente en w5
    MADD w6, w5, w2, w3               // Resto en w6 = w3 % 10
    ADD w6, w6, #'0'                  // Convertir a ASCII
    STRB w6, [x1, -w4, LSL #0]        // Almacenar carácter en la cadena
    MOV w3, w5                        // Actualizar w3 con el cociente
    ADD w4, w4, #1                    // Incrementa longitud

    CBNZ w3, convert_loop             // Si w3 no es cero, repetir

    SUB x1, x1, w4                    // Ajustar x1 a la dirección de inicio de la cadena
    MOV x0, x4                        // Longitud de la cadena
    ADD sp, sp, #16                   // Restaurar el puntero de pila
    RET
