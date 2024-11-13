# Suma de Matrices  
**Nombre:** Miguel Guadalupe Saldana Ramirez  
**Número de Control:** 21210426  
**Práctica:** Ejercicios Assembly 21-30  
**Fecha:** 12/11/2024  

```assembly
.section .data
matrix1: .word 1, 2, 3, 4, 5, 6, 7, 8, 9     // Matriz 3x3
matrix2: .word 9, 8, 7, 6, 5, 4, 3, 2, 1     // Matriz 3x3
result:  .word 0, 0, 0, 0, 0, 0, 0, 0, 0     // Matriz de resultados
size:    .word 9                            // Tamaño de la matriz (3x3)

.section .text
.global _start

_start:
    // Cargar dirección de las matrices y tamaño
    LDR x0, =matrix1                     // Dirección de la primera matriz
    LDR x1, =matrix2                     // Dirección de la segunda matriz
    LDR x2, =result                      // Dirección de la matriz resultado
    LDR w3, =size                        // Tamaño de la matriz
    LDR w3, [w3]                         // Cargar tamaño de la matriz

    MOV w4, #0                           // Índice i = 0

loop:
    CMP w4, w3                           // Comparar índice con el tamaño de la matriz
    BGE end_program                      // Si i >= tamaño, terminar

    // Cargar elementos de las matrices
    LDR w5, [x0, w4, LSL #2]             // Cargar elemento de la primera matriz
    LDR w6, [x1, w4, LSL #2]             // Cargar elemento de la segunda matriz

    // Sumar los elementos
    ADD w7, w5, w6                       // Sumar los elementos

    // Almacenar el resultado en la matriz resultante
    STR w7, [x2, w4, LSL #2]             // Guardar la suma en la matriz resultante

    ADD w4, w4, #1                       // Incrementar el índice
    B loop                               // Repetir para el siguiente elemento

end_program:
    // 5. Salir del programa
    MOV x8, #93                          // syscall: exit
    MOV x0, #0                           // Estado de salida
    SVC #0
