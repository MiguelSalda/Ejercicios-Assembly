# Verificar si un número es primo  
**Nombre:** Miguel Guadalupe Saldana Ramirez  
**Número de Control:** 21210426  
**Práctica:** Ejercicios Assembly 21-30  
**Fecha:** 11/11/2024  

```assembly
.section .data
num: .word 29               // Número a verificar si es primo
output_msg_prime: .asciz "El número es primo."  // Mensaje si es primo
output_msg_not_prime: .asciz "El número no es primo." // Mensaje si no es primo
newline: .asciz "\n"        // Salto de línea

.section .text
.global _start

_start:
    // 1. Cargar el valor de num (número a verificar)
    LDR w0, =num            // Cargar dirección de num
    LDR w0, [w0]            // Cargar el valor de num en w0 (n)

    // 2. Verificar si el número es menor o igual a 1
    CMP w0, #1
    BLE not_prime           // Si el número es 1 o menor, no es primo

    // 3. Comenzar la verificación de divisores
    MOV w1, #2              // Empezamos a verificar desde 2

check_prime_loop:
    MOV w2, w0              // Copiar el número original en w2
    UDIV w3, w2, w1         // Dividir el número entre el divisor
    MUL w4, w3, w1          // Multiplicar el cociente por el divisor
    CMP w4, w0              // Comparar el resultado con el número original
    EQ is_prime             // Si son iguales, el número es primo

    ADD w1, w1, #1          // Incrementar el divisor
    CMP w1, w0              // Comparar el divisor con el número
    BLT check_prime_loop    // Si el divisor es menor, continuar verificando

not_prime:
    // 4. Imprimir mensaje "El número no es primo"
    MOV x0, #1              // File descriptor 1 (stdout)
    LDR x1, =output_msg_not_prime  // Dirección del mensaje
    MOV x2, #24             // Longitud del mensaje
    MOV x8, #64             // syscall: write
    SVC #0
    B end_program

is_prime:
    // 5. Imprimir mensaje "El número es primo"
    MOV x0, #1              // File descriptor 1 (stdout)
    LDR x1, =output_msg_prime  // Dirección del mensaje
    MOV x2, #23             // Longitud del mensaje
    MOV x8, #64             // syscall: write
    SVC #0

end_program:
    // 6. Imprimir un salto de línea
    MOV x0, #1              // File descriptor 1 (stdout)
    LDR x1, =newline        // Dirección de la cadena de salto de línea
    MOV x2, #1              // Longitud del salto de línea
    MOV x8, #64             // syscall: write
    SVC #0

    // 7. Salir del programa
    MOV x8, #93             // syscall: exit
    MOV x0, #0              // Estado de salida
    SVC #0
