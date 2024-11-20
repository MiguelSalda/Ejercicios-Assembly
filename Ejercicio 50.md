# Contar los bits activados en un número  
**Nombre:** Miguel Guadalupe Saldana Ramirez  
**Número de Control:** 21210426  
**Práctica:** Ejercicios Assembly 21-30  
**Fecha:** 19/11/2024  

```assembly
.section .data
msg_result: .asciz "Número de bits activados: "
newline: .asciz "\n"

.section .text
.global _start

_start:
    // Número inicial
    MOV w0, #42          // Número a analizar (42 en decimal, 0b00101010 en binario)
    MOV w1, #0           // Contador de bits activados

count_bits:
    ANDS w2, w0, #1      // Comprobar el bit menos significativo
    ADD w1, w1, w2       // Incrementar el contador si el bit está activado
    LSR w0, w0, #1       // Desplazar el número hacia la derecha
    CBNZ w0, count_bits  // Repetir mientras queden bits por analizar

    // Imprimir el mensaje
    LDR x0, =msg_result  // Dirección del mensaje
    BL print_msg

    // Imprimir el resultado
    MOV w0, w1           // Pasar el número de bits activados a w0
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
    MOV x2, #27
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
