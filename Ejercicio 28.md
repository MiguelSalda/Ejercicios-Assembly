# Serie de Fibonacci  
**Nombre:** Miguel Guadalupe Saldana Ramirez  
**Número de Control:** 21210426  
**Práctica:** Ejercicios Assembly 21-30  
**Fecha:** 11/11/2024  

```assembly
.section .data
num: .word 10               // Número de términos de la serie de Fibonacci
output_msg: .asciz "Serie de Fibonacci: " // Mensaje de salida
newline: .asciz "\n"        // Salto de línea

.section .text
.global _start

_start:
    // 1. Cargar el valor de num (número de términos de la serie)
    LDR w0, =num            // Cargar dirección de num
    LDR w0, [w0]            // Cargar el valor de num en w0 (n)

    // 2. Inicializar los primeros dos términos de la serie: F0 = 0, F1 = 1
    MOV w1, #0              // F0 = 0
    MOV w2, #1              // F1 = 1

    // 3. Mostrar el mensaje de salida
    MOV x0, #1              // File descriptor 1 (stdout)
    LDR x1, =output_msg     // Dirección del mensaje de salida
    MOV x2, #19             // Longitud del mensaje
    MOV x8, #64             // syscall: write
    SVC #0

    // 4. Imprimir el primer término de la serie (F0)
    MOV x0, #1              // File descriptor 1 (stdout)
    MOV w0, w1              // Mover F0 a w0
    BL int_to_ascii         // Convertir y mostrar F0
    MOV x2, x0              // Longitud de la cadena ASCII convertida
    MOV x8, #64             // syscall: write
    SVC #0

    // 5. Imprimir el segundo término de la serie (F1)
    MOV x0, #1              // File descriptor 1 (stdout)
    MOV w0, w2              // Mover F1 a w0
    BL int_to_ascii         // Convertir y mostrar F1
    MOV x2, x0              // Longitud de la cadena ASCII convertida
    MOV x8, #64             // syscall: write
    SVC #0

    // 6. Calcular e imprimir los siguientes términos de la serie
    MOV w3, #2              // Contador de términos (empezamos desde el tercero)
fibonacci_loop:
    ADD w4, w1, w2          // F(n) = F(n-1) + F(n-2)
    MOV w1, w2              // Actualizar F(n-2) a F(n-1)
    MOV w2, w4              // Actualizar F(n-1) a F(n)

    MOV x0, #1              // File descriptor 1 (stdout)
    MOV w0, w4              // Mover F(n) a w0
    BL int_to_ascii         // Convertir y mostrar F(n)
    MOV x2, x0              // Longitud de la cadena ASCII convertida
    MOV x8, #64             // syscall: write
    SVC #0

    ADD w3, w3, #1          // Incrementar el contador
    LDR w5, =num            // Cargar la dirección de num
    LDR w5, [w5]            // Cargar el valor de num (n)
    CMP w3, w5              // Comparar el contador con n
    BLT fibonacci_loop      // Si el contador es menor que n, continuar

    // 7. Imprimir un salto de línea
    MOV x0, #1              // File descriptor 1 (stdout)
    LDR x1, =newline        // Dirección de la cadena de salto de línea
    MOV x2, #1              // Longitud del salto de línea
    MOV x8, #64             // syscall: write
    SVC #0

    // 8. Salir del programa
    MOV x8, #93             // syscall: exit
    MOV x0, #0              // Estado de salida
    SVC #0

// Subrutina para convertir un número en w0 a una cadena ASCII en x1
// Resultado: longitud de la cadena en x0, dirección en x1
int_to_ascii:
    SUB sp, sp, #16          // Reserva espacio en la pila
    ADD x1, sp, #15          // Puntero de fin de cadena
    MOV w2, #10              // Divisor (base 10)
    MOV w3, w0               // Copia del número a convertir en w3
    MOV w4, #0               // Longitud inicial

convert_loop:
    UDIV w5, w3, w2          // Divide w3 por 10, guarda el cociente en w5
    MADD w6, w5, w2, w3      // Resto en w6 = w3 % 10
    ADD w6, w6, #'0'         // Convertir a ASCII
    STRB w6, [x1, -w4, LSL #0]  // Almacenar carácter en la cadena, decrementando el puntero
    MOV w3, w5               // Actualizar w3 con el cociente
    ADD w4, w4, #1           // Incrementa longitud

    CBNZ w3, convert_loop    // Si w3 no es cero, repetir

    SUB x1, x1, w4           // Ajustar x1 a la dirección de inicio de la cadena
    MOV x0, x4               // Longitud de la cadena
    ADD sp, sp, #16          // Restaura el puntero de pila
    RET
