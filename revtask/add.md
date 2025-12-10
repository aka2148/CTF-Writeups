
# Assembly to add to numbers
```
section .data
    num1 dd 5
    num2 dd 7
    msg db "Result: ", 0x0A
    msg_len equ $ - msg

section .bss
    result resb 2

section .text
    global _start

_start:
    mov eax, [num1]
    mov ebx, [num2]
    add eax, ebx
    add al, '0'
    mov [result], al

    mov rax, 1
    mov rdi, 1
    mov rsi, msg
    mov rdx, msg_len
    syscall

    mov rax, 1
    mov rdi, 1
    mov rsi, result
    mov rdx, 1
    syscall

    mov rax, 60
    xor rdi, rdi
    syscall

```

Alright so basically initialise two numbers in data section and bss(uninitialised data) has the result
We use mov to assign value of num1 and num2 to eax and abx
Add them with add and mov them to result

The tricky part was displaying them, rsi is the address of the data
First part is just making a new line
Second part moves the result to the addresss
Then we end the program with rax,60 and xor to get code 0
