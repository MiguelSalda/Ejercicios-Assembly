#Convertir temperatura de Celsius a Fahrenheit
#Nombre:** [Miguel Guadalupe Saldana Ramirez]
#Número de Control:** [21210426]
#Práctica:** [Ejercicios Assembly 21-30]
#Fecha:** [11/11/2024]

.section .data
output_msg:
    .asciz "Temperatura en Fahrenheit: "  // Mensaje de salida
newline:
    .asciz "\n"                           // Salto de línea

.section .text
.global _start

_start:
    // 1. Convertir Celsius a Fahrenheit
    MOV w0, #25           // Celsius = 25; puedes cambiar este valor
    MOV w1, #9            // Cargar el valor 9 en w1
    MUL w2, w0, w1        // w2 = C * 9
    MOV w1, #5            // Cargar el valor 5 en w1
    SDIV w2, w2, w1       // w2 = (C * 9) / 5
    ADD w1, w2, #32       // Fahrenheit = (C * 9 / 5) + 32, resultado en w1

    // 2. Llamada al sistema para escribir el mensaje de salida
    MOV x0, #1            // File descriptor 1 (stdout)
    LDR x1, =output_msg   // Dirección del mensaje de salida
    MOV x2, #24           // Longitud del mensaje
    MOV x8, #64           // syscall: write
    SVC #0

    // 3. Convertir el valor en w1 a ASCII y mostrarlo
    MOV w0, w1            // Mover el valor de Fahrenheit a w0
    BL int_to_ascii       // Llamada a función de conversión, resultado en x0 (longitud) y x1 (dirección)
    MOV x2, x0            // Longitud de la cadena ASCII convertida
    MOV x8, #64           // syscall: write
    SVC #0

    // 4. Imprimir un salto de línea
    MOV x0, #1            // File descriptor 1 (stdout)
    LDR x1, =newline      // Dirección de la cadena de salto de línea
    MOV x2, #1            // Longitud del salto de línea
    MOV x8, #64           // syscall: write
    SVC #0

    // 5. Salir del programa
    MOV x8, #93           // syscall: exit
    MOV x0, #0            // Estado de salida
    SVC #0

// Subrutina para convertir un número en w0 a una cadena ASCII en x1
// Resultado: longitud de la cadena en x0, dirección en x1
int_to_ascii:
    SUB sp, sp, #16            // Reserva espacio en la pila
    ADD x1, sp, #15            // Puntero de fin de cadena
    MOV w2, #10                // Divisor (base 10)
    MOV w3, w0                 // Copia del número a convertir en w3
    MOV w4, #0                 // Longitud inicial

convert_loop:
    UDIV w5, w3, w2            // Divide w3 por 10, guarda el cociente en w5
    MADD w6, w5, w2, w3        // Resto en w6 = w3 % 10
    ADD w6, w6, #'0'           // Convertir a ASCII
    STRB w6, [x1, -w4, LSL #0] // Almacenar carácter en la cadena, decrementando el puntero
    MOV w3, w5                 // Actualizar w3 con el cociente
    ADD w4, w4, #1             // Incrementa longitud

    CBNZ w3, convert_loop      // Si w3 no es cero, repetir

    SUB x1, x1, w4             // Ajustar x1 a la dirección de inicio de la cadena
    MOV x0, x4                 // Longitud de la cadena
    ADD sp, sp, #16            // Restaura el puntero de pila
    RET
