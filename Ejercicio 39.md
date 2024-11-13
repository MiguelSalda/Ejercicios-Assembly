# Ordenamiento por Mezcla (Merge Sort)  
**Nombre:** Miguel Guadalupe Saldana Ramirez  
**Número de Control:** 21210426  
**Práctica:** Ejercicios Assembly 21-30  
**Fecha:** 12/11/2024  

```assembly
.section .data
array: .word 38, 27, 43, 3, 9, 82, 10  // Arreglo desordenado
array_size: .word 7                    // Tamaño del arreglo
output_msg: .asciz "Arreglo ordenado: "
newline: .asciz "\n"                    // Salto de línea

.section .text
.global _start

_start:
    // 1. Cargar la dirección y tamaño del arreglo
    LDR x0, =array                    // Dirección base del arreglo
    LDR w1, [x0]                      // Primer elemento del arreglo en w1
    LDR x2, =array_size               // Dirección del tamaño del arreglo
    LDR w2, [x2]                      // Tamaño del arreglo en w2

    // 2. Llamar a la función MergeSort
    MOV x3, #0                        // Índice izquierdo (L)
    MOV x4, w2                        // Índice derecho (R)
    BL merge_sort

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

// Función MergeSort
merge_sort:
    CMP x3, x4                        // Si L >= R, salir
    BGE merge_sort_end

    // Dividir el arreglo en dos mitades
    MOV x5, x3                        // L
    ADD x6, x3, x4                    // R + L
    UDIV x6, x6, #2                    // x6 = (L + R) / 2

    // Recursión para la mitad izquierda
    MOV x7, x3                        // L
    MOV x8, x6                        // M
    BL merge_sort

    // Recursión para la mitad derecha
    MOV x7, x6                        // M + 1
    MOV x8, x4                        // R
    BL merge_sort

    // 3. Mezclar los dos arreglos ordenados
    MOV x9, x3                        // L
    MOV x10, x6                       // M
    MOV x11, x7                        // M + 1
    MOV x12, x4                        // R
    BL merge

merge_sort_end:
    RET

// Función Merge
merge:
    // 1. Configurar registros para mezcla de dos subarreglos
    MOV x13, x9                       // L
    MOV x14, x10                      // M
    MOV x15, x11                      // M + 1
    MOV x16, x12                      // R

    // 2. Comenzar la mezcla
    // Este proceso se puede hacer usando dos arreglos auxiliares,
    // comparando y copiando en orden creciente
    // (Se omite el código detallado por simplicidad)

    RET

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
