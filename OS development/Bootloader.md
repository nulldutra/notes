
## Theory

......


## Version 1

```asm
ORG 0x7c00
BITS 16

start:
	mov ah, 0eh
	mov al, 'A'
	mov bx, 0
	int 0x10     ; call BIOS video interrupt

	jmp $

; it says that we need to fill at least 510 bytes of data.
; boot signature needs to be at byte 511 and 512
times 510-($ - $$) db 0

dw 0xAA55
```

## Version 2

```
ORG 0x7c00
BITS 16

message: db 'Hello World!', 0

start:
	mov si, message
	call print
	jmp $

print:
	mov bx, 0

.loop:
	lodsb
	cmp al, 0
	je .done
	call print_char
	jmp .loop

.done:
	ret

print_char:
	mov ah, 0eh
	int 0x10
	ret

; it says that we need to fill at least 510 bytes of data.
; boot signature needs to be at byte 511 and 512
times 510-($ - $$) db 0

dw 0xAA55
```

