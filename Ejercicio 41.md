# Multiplicación de Matrices  
**Nombre:** Miguel Guadalupe Saldana Ramirez  
**Número de Control:** 21210426  
**Práctica:** Ejercicios Assembly 21-30  
**Fecha:** 19/11/2024  

```assembly
.section .data
matrix1: .word 1, 2, 3, 4, 5, 6, 7, 8, 9     // Matriz 3x3
matrix2: .word 9, 8, 7, 6, 5, 4, 3, 2, 1     // Matriz 3x3
result:  .word 0, 0, 0, 0, 0, 0, 0, 0, 0     // Matriz de resultados
n:       .word 3                             // Número de filas/columnas (3x3)

.section .text
.global _start

_start:
    // Cargar dimensiones de la matriz
    LDR w9, =n                              // Tamaño de la matriz
    LDR w9, [w9]                            // Cargar tamaño n

    // Direcciones de las matrices
    LDR x0, =matrix1                        // Dirección de la primera matriz
    LDR x1, =matrix2                        // Dirección de la segunda matriz
    LDR x2, =result                         // Dirección de la matriz resultado

    MOV w3, #0                              // Índice de fila i

row_loop:
    CMP w3, w9                              // Verificar si hemos recorrido todas las filas
    BGE end_program                         // Si i >= n, salir del programa

    MOV w4, #0                              // Índice de columna j

col_loop:
    CMP w4, w9                              // Verificar si hemos recorrido todas las columnas
    BGE next_row                            // Si j >= n, ir a la siguiente fila

    MOV w5, #0                              // Inicializar acumulador para el resultado de result[i][j]
    MOV w6, #0                              // Índice k para la multiplicación

mul_loop:
    CMP w6, w9                              // Verificar si hemos recorrido todas las posiciones de la fila/columna
    BGE store_result                        // Si k >= n, almacenar resultado

    // Cargar matrix1[i][k]
    MUL w7, w3, w9                          // Desplazamiento de fila i en matrix1
    ADD w7, w7, w6                          // Dirección de matrix1[i][k]
    LDR w8, [x0, w7, LSL #2]                // Cargar valor de matrix1[i][k]

    // Cargar matrix2[k][j]
    MUL w7, w6, w9                          // Desplazamiento de fila k en matrix2
    ADD w7, w7, w4                          // Dirección de matrix2[k][j]
    LDR w10, [x1, w7, LSL #2]               // Cargar valor de matrix2[k][j]

    // Multiplicar y acumular
    MLA w5, w8, w10, w5                     // result[i][j] += matrix1[i][k] * matrix2[k][j]

    ADD w6, w6, #1                          // Incrementar k
    B mul_loop                              // Repetir el bucle de multiplicación

store_result:
    // Almacenar result[i][j]
    MUL w7, w3, w9                          // Desplazamiento de fila i en result
    ADD w7, w7, w4                          // Dirección de result[i][j]
    STR w5, [x2, w7, LSL #2]                // Guardar el acumulador en la matriz result

    ADD w4, w4, #1                          // Incrementar j
    B col_loop                              // Repetir para la siguiente columna

next_row:
    ADD w3, w3, #1                          // Incrementar i
    B row_loop                              // Repetir para la siguiente fila

end_program:
    MOV x8, #93                             // syscall: exit
    MOV x0, #0                              // Estado de salida
    SVC #0
