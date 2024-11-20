# Contar Vocales y Consonantes  
**Nombre:** Miguel Guadalupe Saldana Ramirez  
**Número de Control:** 21210426  
**Práctica:** Ejercicios Assembly 21-30  
**Fecha:** 19/11/2024  

```assembly
.section .data
string: .asciz "Hola, mundo!"       // Cadena de texto a evaluar
vowel_msg: .asciz "Vocales: "       // Mensaje para vocales
consonant_msg: .asciz "Consonantes: "// Mensaje para consonantes
newline: .asciz "\n"               // Salto de línea

.section .text
.global _start

_start:
    // Inicializar registros y direcciones
    LDR x0, =string           // Dirección de la cadena
    MOV x1, #0                // Contador de vocales
    MOV x2, #0                // Contador de consonantes

count_loop:
    LDRB w3, [x0], #1         // Cargar un byte de la cadena y avanzar el puntero
    CBZ w3, print_result      // Si el byte es 0 (fin de cadena), salir del bucle

    // Verificar si es una vocal
    CMP w3, #'a'
    B.EQ is_vowel
    CMP w3, #'e'
    B.EQ is_vowel
    CMP w3, #'i'
    B.EQ is_vowel
    CMP w3, #'o'
    B.EQ is_vowel
    CMP w3, #'u'
    B.EQ is_vowel
    CMP w3, #'A'
    B.EQ is_vowel
    CMP w3, #'E'
    B.EQ is_vowel
    CMP w3, #'I'
    B.EQ is_vowel
    CMP w3, #'O'
    B.EQ is_vowel
    CMP w3, #'U'
    B.EQ is_vowel

    // Verificar si es una consonante
    CMP w3, #'a'              // Comparar con 'a'
    B.LT count_loop           // Si menor, no es una letra
    CMP w3, #'z'              // Comparar con 'z'
    B.GT not_consonant        // Si mayor, no es una letra
    CMP w3, #'A'              // Comparar con 'A'
    B.LT count_loop           // Si menor, no es una letra
    CMP w3, #'Z'              // Comparar con 'Z'
    B.GT not_consonant        // Si mayor, no es una letra

    // Incrementar contador de consonantes
    ADD x2, x2, #1
    B count_loop

is_vowel:
    ADD x1, x1, #1            // Incrementar contador de vocales
    B count_loop

not_consonant:
    B count_loop              // Continuar el bucle

print_result:
    // Imprimir el mensaje de vocales
    MOV x0, #1                // File descriptor 1 (stdout)
    LDR x1, =vowel_msg        // Mensaje para vocales
    MOV x2, #9                // Longitud del mensaje
    MOV x8, #64               // syscall: write
    SVC #0

    // Convertir el número de vocales a ASCII y mostrarlo
    MOV w0, x1                // Mover el número de vocales a w0
    BL int_to_ascii           // Llamada a función de conversión
    MOV x2, x0                // Longitud de la cadena convertida
    MOV x8, #64               // syscall: write
    SVC #0

    // Imprimir el mensaje de consonantes
    MOV x0, #1                // File descriptor 1 (stdout)
    LDR x1, =consonant_msg    // Mensaje para consonantes
    MOV x2, #13               // Longitud del mensaje
    MOV x8, #64               // syscall: write
    SVC #0

    // Convertir el número de consonantes a ASCII y mostrarlo
    MOV w0, x2                // Mover el número de consonantes a w0
    BL int_to_ascii           // Llamada a función de conversión
    MOV x2, x0                // Longitud de la cadena convertida
    MOV x8, #64               // syscall: write
    SVC #0

    // Imprimir un salto de línea
    MOV x0, #1                // File descriptor 1 (stdout)
    LDR x1, =newline          // Dirección del salto de línea
    MOV x2, #1                // Longitud del salto de línea
    MOV x8, #64               // syscall: write
    SVC #0

    // Salir del programa
    MOV x8, #93               // syscall: exit
    MOV x0, #0                // Estado de salida
    SVC #0

// Subrutina para convertir un número en w0 a una cadena ASCII en x1
// Resultado: longitud de la cadena en x0, dirección en x1
int_to_ascii:
    SUB sp, sp, #16           // Reservar espacio en la pila
    ADD x1, sp, #15           // Puntero al final del buffer
    MOV w2, #10               // Divisor (base 10)
    MOV w3, w0                // Copiar el número a convertir a w3
    MOV w4, #0                // Longitud inicial

convert_loop:
    UDIV w5, w3, w2           // Cociente = número / 10
    MADD w6, w5, w2, w3       // Resto = número % 10
    ADD w6, w6, #'0'          // Convertir a carácter ASCII
    STRB w6, [x1, -w4, LSL #0]// Almacenar carácter y ajustar puntero
    MOV w3, w5                // Actualizar el número con el cociente
    ADD w4, w4, #1            // Incrementar longitud

    CBNZ w3, convert_loop     // Si el cociente no es cero, repetir

    SUB x1, x1, w4            // Ajustar el puntero al inicio de la cadena
    MOV x0, w4                // Longitud de la cadena
    ADD sp, sp, #16           // Restaurar el puntero de pila
    RET
