# Operaciones AND, OR, XOR a nivel de bits  
**Nombre:** Miguel Guadalupe Saldana Ramirez  
**Número de Control:** 21210426  
**Práctica:** Ejercicios Assembly 21-30  
**Fecha:** 19/11/2024  

```assembly
.section .data
msg_and: .asciz "Resultado AND: "   // Mensaje para AND
msg_or: .asciz "Resultado OR: "     // Mensaje para OR
msg_xor: .asciz "Resultado XOR: "   // Mensaje para XOR
newline: .asciz "\n"               // Salto de línea

.section .text
.global _start

_start:
    // Definir los valores iniciales
    MOV w0, #45                // Primer número (45)
    MOV w1, #25                // Segundo número (25)

    // Operación AND
    AND w2, w0, w1             // Realizar w2 = w0 AND w1
    LDR x0, =msg_and           // Mensaje para AND
    BL print_msg               // Imprimir mensaje
    MOV w0, w2                 // Pasar el resultado de AND a w0
    BL print_number            // Imprimir el número resultante

    // Operación OR
    ORR w2, w0, w1             // Realizar w2 = w0 OR w1
    LDR x0, =msg_or            // Mensaje para OR
    BL print_msg               // Imprimir mensaje
    MOV w0, w2                 // Pasar el resultado de OR a w0
    BL print_number            // Imprimir el número resultante

    // Operación XOR
    EOR w2, w0, w1             // Realizar w2 = w0 XOR w1
    LDR x0, =msg_xor           // Mensaje para XOR
    BL print_msg               // Imprimir mensaje
    MOV w0, w2                 // Pasar el resultado de XOR a w0
    BL print_number            // Imprimir el número resultante

    // Imprimir salto de línea
    MOV x0, #1                 // File descriptor 1 (stdout)
    LDR x1, =newline           // Dirección del salto de línea
    MOV x2, #1                 // Longitud del salto de línea
    MOV x8, #64                // syscall: write
    SVC #0

    // Finalizar el programa
    MOV x8, #93                // syscall: exit
    MOV x0, #0                 // Código de salida
    SVC #0

// Subrutina para imprimir un mensaje
// Entrada: x0 = dirección del mensaje
print_msg:
    MOV x1, x0                // Dirección del mensaje
    MOV x0, #1                // File descriptor 1 (stdout)
    MOV x2, #16               // Longitud fija del mensaje
    MOV x8, #64               // syscall: write
    SVC #0
    RET

// Subrutina para convertir un número en w0 a ASCII e imprimirlo
// Entrada: w0 = número a imprimir
print_number:
    SUB sp, sp, #16           // Reservar espacio en la pila
    ADD x1, sp, #15           // Puntero al final del buffer
    MOV w2, #10               // Divisor (base 10)
    MOV w3, w0                // Copiar el número a w3
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
    MOV x0, #1                // File descriptor 1 (stdout)
    MOV x2, w4                // Longitud de la cadena
    MOV x8, #64               // syscall: write
    SVC #0
    ADD sp, sp, #16           // Restaurar el puntero de pila
    RET
