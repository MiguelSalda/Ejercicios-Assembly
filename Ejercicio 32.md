# Verificar si una cadena es palíndromo  
**Nombre:** Miguel Guadalupe Saldana Ramirez  
**Número de Control:** 21210426  
**Práctica:** Ejercicios Assembly 21-30  
**Fecha:** 12/11/2024  

```assembly
.section .data
input_str: .asciz "radar"         // Cadena de entrada
output_msg_palindrome: .asciz "La cadena es un palíndromo."  // Mensaje si es palíndromo
output_msg_not_palindrome: .asciz "La cadena no es un palíndromo." // Mensaje si no es palíndromo
newline: .asciz "\n"              // Salto de línea

.section .text
.global _start

_start:
    // 1. Cargar la dirección de la cadena de entrada en x0
    LDR x0, =input_str             // Dirección de input_str
    MOV x1, #0                     // Inicializar el índice para encontrar la longitud

find_length:
    // 2. Encontrar la longitud de la cadena de entrada
    LDRB w2, [x0, x1]              // Cargar un byte de la cadena
    CMP w2, #0                     // Verificar si es el final de la cadena (null byte)
    BEQ check_palindrome           // Si es null, saltar a verificar el palíndromo
    ADD x1, x1, #1                 // Incrementar el índice
    B find_length                  // Continuar buscando

check_palindrome:
    // 3. Preparar índices para verificación de palíndromo
    MOV x2, x1                     // Longitud de la cadena
    SUB x2, x2, #1                 // Índice del último carácter válido
    MOV x3, #0                     // Índice del primer carácter

compare_loop:
    // 4. Comparar caracteres desde el inicio y el final
    LDRB w4, [x0, x3]              // Cargar carácter desde el inicio
    LDRB w5, [x0, x2]              // Cargar carácter desde el final
    CMP w4, w5                     // Comparar los dos caracteres
    BNE not_palindrome             // Si son diferentes, no es palíndromo
    ADD x3, x3, #1                 // Incrementar el índice del inicio
    SUB x2, x2, #1                 // Decrementar el índice del final
    CMP x3, x2                     // Verificar si se han comparado todos los pares
    BLT compare_loop               // Si no, continuar comparando

is_palindrome:
    // 5. Imprimir mensaje "La cadena es un palíndromo"
    MOV x0, #1                     // File descriptor 1 (stdout)
    LDR x1, =output_msg_palindrome // Dirección del mensaje
    MOV x2, #29                    // Longitud del mensaje
    MOV x8, #64                    // syscall: write
    SVC #0
    B end_program                  // Saltar al final del programa

not_palindrome:
    // 6. Imprimir mensaje "La cadena no es un palíndromo"
    MOV x0, #1                     // File descriptor 1 (stdout)
    LDR x1, =output_msg_not_palindrome  // Dirección del mensaje
    MOV x2, #32                    // Longitud del mensaje
    MOV x8, #64                    // syscall: write
    SVC #0

end_program:
    // 7. Imprimir un salto de línea
    MOV x0, #1                     // File descriptor 1 (stdout)
    LDR x1, =newline               // Dirección de la cadena de salto de línea
    MOV x2, #1                     // Longitud del salto de línea
    MOV x8, #64                    // syscall: write
    SVC #0

    // 8. Salir del programa
    MOV x8, #93                    // syscall: exit
    MOV x0, #0                     // Estado de salida
    SVC #0
