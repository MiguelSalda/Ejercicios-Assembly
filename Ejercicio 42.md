# Transposición de una Matriz  
**Nombre:** Miguel Guadalupe Saldana Ramirez  
**Número de Control:** 21210426  
**Práctica:** Ejercicios Assembly 21-30  
**Fecha:** 19/11/2024  

```assembly
.section .data
matrix: .word 1, 2, 3, 4, 5, 6, 7, 8, 9   // Matriz original 3x3
transpose: .word 0, 0, 0, 0, 0, 0, 0, 0, 0 // Matriz transpuesta
n: .word 3                                // Dimensión de la matriz (3x3)

.section .text
.global _start

_start:
    // Cargar el tamaño de la matriz
    LDR w9, =n                             // Dirección de la variable n
    LDR w9, [w9]                           // Tamaño de la matriz (n)

    // Direcciones de las matrices
    LDR x0, =matrix                        // Dirección de la matriz original
    LDR x1, =transpose                     // Dirección de la matriz transpuesta

    MOV w2, #0                             // Índice de fila i

row_loop:
    CMP w2, w9                             // Verificar si hemos recorrido todas las filas
    BGE end_program                        // Si i >= n, salir del programa

    MOV w3, #0                             // Índice de columna j

col_loop:
    CMP w3, w9                             // Verificar si hemos recorrido todas las columnas
    BGE next_row                           // Si j >= n, ir a la siguiente fila

    // Cargar matrix[i][j]
    MUL w4, w2, w9                         // Desplazamiento de fila i
    ADD w4, w4, w3                         // Dirección de matrix[i][j]
    LDR w5, [x0, w4, LSL #2]               // Cargar valor de matrix[i][j]

    // Almacenar en transpose[j][i]
    MUL w6, w3, w9                         // Desplazamiento de fila j en transpose
    ADD w6, w6, w2                         // Dirección de transpose[j][i]
    STR w5, [x1, w6, LSL #2]               // Guardar valor en transpose[j][i]

    ADD w3, w3, #1                         // Incrementar columna j
    B col_loop                             // Repetir para la siguiente columna

next_row:
    ADD w2, w2, #1                         // Incrementar fila i
    B row_loop                             // Repetir para la siguiente fila

end_program:
    MOV x8, #93                            // syscall: exit
    MOV x0, #0                             // Estado de salida
    SVC #0
