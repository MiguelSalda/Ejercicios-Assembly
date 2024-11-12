# Invertir una cadena  
**Nombre:** Miguel Guadalupe Saldana Ramirez  
**Número de Control:** 21210426  
**Práctica:** Ejercicios Assembly 21-30  
**Fecha:** 11/11/2024  

```assembly
.section .data
input_str: .asciz "Hola Mundo"  // Cadena de entrada
output_msg: .asciz "Cadena invertida: "  // Mensaje de salida
newline: .asciz "\n"            // Salto de línea

.section .bss
reversed_str: .skip 20         // Espacio reservado para la cadena invertida

.section .text
.global _start

_start:
    // 1. Cargar la dirección de la cadena de entrada en w0
    LDR x0, =input_str        // Dirección de input_str
    MOV x1, #0                // Inicializar el índice

find_length:
    // 2. Encontrar la longitud de la cadena de entrada
    LDRB w2, [x0, x1]         // Cargar un byte de la cadena
    CMP w2, #0                // Verificar si es el final de la cadena (null byte)
    BEQ reverse_string        // Si es null, saltar a invertir la cadena
    ADD x1, x1, #1            // Incrementar el índice
    B find_length             // Continuar buscando

reverse_string:
    // 3. Invertir la cadena
    MOV x2, x1                // Longitud de la cadena
    SUB x2, x2, #1            // Ajustar índice al último carácter válido
    MOV x3, #0                // Inicializar el índice de la cadena invertida

invert_loop:
    // 4. Copiar los caracteres de la cadena original a la invertida
    LDRB w4, [x0, x2]         // Cargar el carácter de la cadena original
    STRB w4, [x0, x3]         // Almacenar el carácter en la cadena invertida
    SUB x2, x2, #1            // Decrementar índice de la cadena original
    ADD x3, x3, #1            // Incrementar índice de la cadena invertida
    CMP x2, #0                // Verificar si se han copiado todos los caracteres
    BGE invert_loop           // Si hay más caracteres, seguir invirtiendo

    // 5. Imprimir mensaje "Cadena invertida: "
    MOV x0, #1                // File descriptor 1 (stdout)
    LDR x1, =output_msg       // Dirección del mensaje
    MOV x2, #22               // Longitud del mensaje
    MOV x8, #64               // syscall: write
    SVC #0

    // 6. Imprimir la cadena invertida
    MOV x0, #1                // File descriptor 1 (stdout)
    LDR x1, =reversed_str     // Dirección de la cadena invertida
    MOV x2, x3                // Longitud de la cadena invertida
    MOV x8, #64               // syscall: write
    SVC #0

    // 7. Imprimir un salto de línea
    MOV x0, #1                // File descriptor 1 (stdout)
    LDR x1, =newline          // Dirección de la cadena de salto de línea
    MOV x2, #1                // Longitud del salto de línea
    MOV x8, #64               // syscall: write
    SVC #0

    // 8. Salir del programa
    MOV x8, #93               // syscall: exit
    MOV x0, #0                // Estado de salida
    SVC #0
