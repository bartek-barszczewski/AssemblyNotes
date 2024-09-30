EFLAGS:
	CF (carry flag) (bit 0)
```c
section .data
	isc db "Carry flag is set!", 0xA
	len_carry equ $ - isc

	insc db "Carry flag is not set!", 0xA
	len_not_carry equ $ - insc

	OUTPUT 	equ 1
	FDESC	equ 1
	EXIT 	equ 60
			
global _start
section .text

_start:
	mov al, 221
	add al, 43
	
	jc is_carry_set

	jmp is_not_carry_set
	
is_carry_set:
	mov rax, OUTPUT
	mov rdi, FDESC
	mov rsi, isc
	mov rdx, len_carry
	syscall

is_not_carry_set:
	mov rax, OUTPUT
	mov rdi, FDESC
	mov rsi, insc
	mov rdx, len_not_carry
	syscall

_exit:	
	mov rax, EXIT
	xor rdi, rdi
	syscall


```


OF  ( overflow flag) ( bit 11)
```c
section .data
	iof db "Overflow flag is set!", 0xA
	len_of equ $ - iof

	inof db "Overflow flag is not set!", 0xA
	len_nof equ $ - inof

	OUTPUT 	equ 1
	FDESC	equ 1
	EXIT 	equ 60
			
global _start
section .text

_start:
	; max al = 0 - 127
	; min al = -128 - 0
	; SET EFLAGS OF
	; mov al, 0b1111111 ; = 127
	; add al, 0b0000001 ; = 1

	; NOT SET EFLAGS OF  
	; 128 is UNSIGNED
	;
	; mov al, 128 ;
	; add al, 22 ; 

	; but

	; al = 8 bit = 1 byte = -128 <-> 127
	; SET EFLAGS OF
	mov al, 127
	add al, 1 
	
	jo is_of

	jmp is_nof
	
is_of:
	mov rax, OUTPUT
	mov rdi, FDESC
	mov rsi, iof
	mov rdx, len_of
	syscall

is_nof:
	mov rax, OUTPUT
	mov rdi, FDESC
	mov rsi, inof
	mov rdx, len_nof
	syscall

_exit:	
	mov rax, EXIT
	xor rdi, rdi
	syscall



```


Shift to right in loop

```c
section .data
	binary dq 0b10000000
	zero dq 0

section .bss
	num_buffer resb 20
	
global _start
section .text


_start:
	xor rax, rax
	xor rcx, rcx
	
	mov rax, [binary]
	
	_loop:
		shr rax, 1
		inc rcx
		
		cmp rax, [zero]
		jne _loop

	
	mov rax, 60
	xor rdi, rdi
	syscall
```

Floating numbers
```c
section .data
	f1 dd 3.1435
	f2 dd 123.12
	
global _start

section .text

_start:
	movss xmm0, [f1]
	movss xmm1, [f2]

	addss xmm0, xmm1

	mov rax, 60
	xor rdi, rdi
	syscall

```
gdb
```gdb
layout asm
i r $xmm0
p $xmm0.v4_float[0]
```

floating numbers comparsion

```c
section .data
	f1 dd 1.3211
	f2 dd 2.1

	msg_greater db 	"Is greater", 0xA, 0
	lg equ $ - msg_greater
	
	msg_lower 	db	"Is lower", 0xA, 0
	ll equ $ - msg_lower
	
section .bss

global _start

section .text

_start:
	movss xmm0, [f1]
	movss xmm1, [f2]

	ucomiss xmm0, xmm1
	
	ja greater
	jb lower

lower:
	mov rax, 1
	mov rdi, 1
	mov rsi, msg_lower
	mov rdx, ll
	syscall
	jmp exit

greater:
	mov rax, 1
	mov rdi, 1
	mov rsi, msg_greater
	mov rdx, lg
	syscall
	jmp exit
	

exit:
	mov rax, 60
	xor rdi, rdi
	syscall

```