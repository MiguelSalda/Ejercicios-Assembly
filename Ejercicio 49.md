# Establecer, borrar y alternar bits  
**Nombre:** Miguel Guadalupe Saldana Ramirez  
**Número de Control:** 21210426  
**Práctica:** Ejercicios Assembly 21-30  
**Fecha:** 19/11/2024  

```assembly
.section .data
msg_set: .asciz "Resultado después de establecer el bit: "
msg_clear: .asciz "Resultado después de borrar el bit: "
msg_toggle: .asciz "Resultado después de alternar el bit: "
newline: .asciz "\n"

.section .text
.global _start

_start:
    // Número inicial
    MOV w0, #42          // Número base (42 en decimal, 0b00101010 en binario)
    MOV w1, #1           // Posición del bit a manipular (bit 1)

    // Establecer el bit
    MOV w2, #1           // Máscara con el bit a establecer
    LSL w2, w2, w1       // Desplazar máscara a la posición deseada
    ORR w3, w0, w2       // Establecer el bit en w3
    LDR x0, =msg_set     // Mensaje para establecer
    BL print_msg
    MOV w0, w3           // Pasar el resultado a w0
    BL print_number

    // Borrar el bit
    MVN w2, #0           // Máscara inicial (todos unos)
    LSL w2, w2, w1       // Desplazar máscara a la posición deseada
    BIC w3, w0, w2       // Borrar el bit en w3
    LDR x0, =msg_clear   // Mensaje para borrar
    BL print_msg
    MOV w0, w3           // Pasar el resultado a w0
    BL print_number

    // Alternar el bit
    MOV w2, #1           // Máscara con el bit a alternar
    LSL w2, w2, w1       // Desplazar máscara a la posición deseada
    EOR w3, w0, w2       // Alternar el bit en w3
    LDR x0, =msg_toggle  // Mensaje para alternar
    BL print_msg
    MOV w0, w3           // Pasar el resultado a w0
    BL print_number

    // Imprimir un salto de línea
    MOV x0, #1
    LDR x1, =newline
    MOV x2, #1
    MOV x8, #64
    SVC #0

    // Finalizar el programa
    MOV x8, #93
    MOV x0, #0
    SVC #0

// Subrutina para imprimir un mensaje
// Entrada: x0 = dirección del mensaje
print_msg:
    MOV x1, x0
    MOV x0, #1
    MOV x2, #38
    MOV x8, #64
    SVC #0
    RET

// Subrutina para convertir un número en w0 a ASCII e imprimirlo
// Entrada: w0 = número a imprimir
print_number:
    SUB sp, sp, #16
    ADD x1, sp, #15
    MOV w2, #10
    MOV w3, w0
    MOV w4, #0

convert_loop:
    UDIV w5, w3, w2
    MADD w6, w5, w2, w3
    ADD w6, w6, #'0'
    STRB w6, [x1, -w4, LSL #0]
    MOV w3, w5
    ADD w4, w4, #1

    CBNZ w3, convert_loop

    SUB x1, x1, w4
    MOV x0, #1
    MOV x2, w4
    MOV x8, #64
    SVC #0
    ADD sp, sp, #16
    RET
