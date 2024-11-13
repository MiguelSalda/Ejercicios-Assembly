# Ordenamiento por Selección  
**Nombre:** Miguel Guadalupe Saldana Ramirez  
**Número de Control:** 21210426  
**Práctica:** Ejercicios Assembly 21-30  
**Fecha:** 12/11/2024  

```assembly
.section .data
array: .word 25, 18, 30, 10, 50, 2, 14, 40  // Arreglo de números desordenados
array_size: .word 8                        // Tamaño del arreglo
output_msg: .asciz "Arreglo ordenado: "
newline: .asciz "\n"                        // Salto de línea

.section .text
.global _start

_start:
    // 1. Cargar la dirección y tamaño del arreglo
    LDR x0, =array                    // Dirección base del arreglo
    LDR w1, [x0]                      // Primer elemento del arreglo en w1
    LDR x2, =array_size               // Dirección del tamaño del arreglo
    LDR w2, [x2]                      // Tamaño del arreglo en w2

    // 2. Inicializar índice
    MOV w3, #0                        // Índice i (se inicia en 0)

selection_sort_outer_loop:
    CMP w3, w2                        // Si i >= tamaño del arreglo, salir
    BGE end_sort

    MOV w4, w3                        // Índice de mínimo (min_index) se inicia en i
    ADD w5, w3, #1                    // j = i + 1, comenzamos desde el siguiente elemento

selection_sort_inner_loop:
    CMP w5, w2                        // Si j >= tamaño del arreglo, salir del ciclo interno
    BGE selection_sort_outer_loop_end

    LDR w6, [x0, w4, LSL #2]          // Cargar array[min_index] en w6
    LDR w7, [x0, w5, LSL #2]          // Cargar array[j] en w7

    CMP w6, w7                        // Si array[min_index] <= array[j], no hacer nada
    BLE selection_sort_no_swap

    MOV w4, w5                        // Si no, actualizar min_index con j

selection_sort_no_swap:
    ADD w5, w5, #1                    // Incrementar j
    B selection_sort_inner_loop       // Continuar el ciclo interno

selection_sort_outer_loop_end:
    // Intercambiar el valor mínimo encontrado con el elemento en la posición i
    LDR w6, [x0, w4, LSL #2]          // Cargar el valor mínimo en w6
    LDR w7, [x0, w3, LSL #2]          // Cargar el valor en la posición i en w7
    STR w7, [x0, w4, LSL #2]          // Guardar el valor de array[i] en array[min_index]
    STR w6, [x0, w3, LSL #2]          // Guardar el valor mínimo en array[i]

    ADD w3, w3, #1                    // Incrementar i
    B selection_sort_outer_loop       // Continuar el ciclo externo

end_sort:
    // 3. Imprimir mensaje de "Arreglo ordenado"
    MOV x0, #1                        // File descriptor 1 (stdout)
    LDR x1, =output_msg               // Dirección del mensaje de salida
    MOV x2, #19                       // Longitud del mensaje
    MOV x8, #64                       // syscall: write
    SVC #0

    // 4. Imprimir el arreglo ordenado
    MOV w3, #0                        // Índice para imprimir el arreglo

print_array_loop:
    CMP w3, w2                        // Si el índice llega al final del arreglo, salir
    BGE end_program

    LDR w4, [x0, w3, LSL #2]          // Cargar array[i] en w4
    BL int_to_ascii                   // Convertir el número a ASCII
    MOV x2, x0                        // Longitud de la cadena ASCII convertida
    MOV x8, #64                       // syscall: write
    SVC #0

    // Imprimir un espacio entre los números
    MOV x0, #1                        // File descriptor 1 (stdout)
    LDR x1, =newline                  // Dirección de la cadena de salto de línea
    MOV x2, #1                        // Longitud del salto de línea
    MOV x8, #64                       // syscall: write
    SVC #0

    ADD w3, w3, #1                    // Incrementar índice
    B print_array_loop                // Continuar con el siguiente número

end_program:
    // 5. Salir del programa
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
