# Búsqueda Binaria  
**Nombre:** Miguel Guadalupe Saldana Ramirez  
**Número de Control:** 21210426  
**Práctica:** Ejercicios Assembly 21-30  
**Fecha:** 12/11/2024  

```assembly
.section .data
array: .word 2, 3, 5, 7, 12, 19, 25, 33, 50, 70   // Arreglo de enteros (ordenado)
array_size: .word 10                               // Tamaño del arreglo
search_value: .word 19                             // Valor a buscar
output_msg: .asciz "Valor encontrado en el índice: "
not_found_msg: .asciz "Valor no encontrado."
newline: .asciz "\n"                                // Salto de línea

.section .text
.global _start

_start:
    // 1. Cargar la dirección y tamaño del arreglo
    LDR x0, =array                    // Dirección base del arreglo
    LDR w1, [x0]                      // Primer elemento del arreglo en w1
    LDR x2, =array_size               // Dirección del tamaño del arreglo
    LDR w2, [x2]                      // Tamaño del arreglo en w2
    LDR x3, =search_value             // Dirección del valor a buscar
    LDR w3, [x3]                      // Cargar el valor a buscar en w3

    // 2. Inicializar índices para la búsqueda binaria
    MOV w4, #0                        // Índice izquierdo (low) en w4
    SUB w5, w2, #1                    // Índice derecho (high) en w5 (size - 1)

binary_search_loop:
    CMP w4, w5                        // Si low > high, salir
    BGT not_found

    // Calcular el índice medio
    ADD w6, w4, w5                    // w6 = low + high
    LSR w6, w6, #1                    // w6 = (low + high) / 2, índice medio

    LDR w7, [x0, w6, LSL #2]          // Cargar el valor en el índice medio en w7
    CMP w7, w3                        // Comparar el valor en el medio con el valor a buscar
    BEQ found                         // Si es igual, valor encontrado

    // Si el valor en el medio es mayor, buscar en la mitad izquierda
    BGT search_left

    // Si el valor en el medio es menor, buscar en la mitad derecha
    B search_right

search_left:
    SUB w5, w6, #1                    // Si el valor es mayor, actualizar high a middle - 1
    B binary_search_loop

search_right:
    ADD w4, w6, #1                    // Si el valor es menor, actualizar low a middle + 1
    B binary_search_loop

found:
    // 3. Imprimir mensaje de valor encontrado
    MOV x0, #1                        // File descriptor 1 (stdout)
    LDR x1, =output_msg               // Dirección del mensaje de salida
    MOV x2, #32                       // Longitud del mensaje
    MOV x8, #64                       // syscall: write
    SVC #0

    // 4. Imprimir el índice donde se encontró el valor
    MOV x0, w6                        // El índice está en w6
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

not_found:
    // 7. Imprimir mensaje de valor no encontrado
    MOV x0, #1                        // File descriptor 1 (stdout)
    LDR x1, =not_found_msg            // Dirección del mensaje de no encontrado
    MOV x2, #25                       // Longitud del mensaje
    MOV x8, #64                       // syscall: write
    SVC #0

    // 8. Imprimir un salto de línea
    MOV x0, #1                        // File descriptor 1 (stdout)
    LDR x1, =newline                  // Dirección de la cadena de salto de línea
    MOV x2, #1                        // Longitud del salto de línea
    MOV x8, #64                       // syscall: write
    SVC #0

    // 9. Salir del programa
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
