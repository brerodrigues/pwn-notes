---
layout: post
tags: pwn
categories: pwntools
---

# Shellcode

Criação e utilização de shellcodes com pwntools é simples e evita o trabalho cansativo de usar várias ferramentas para escrever, compilar e gerar shellcode.

## Gerando shellcode do assembly
Com o módulo `asm` é simples gerar shellcode:

```python
context.arch = "x86_64"
assembly = """
jmp executable
main:
	pop rdi
	xor rsi, rsi
	xor rdx, rdx
	push 59
	pop rax
	syscall
executable:
    call main
	.ascii "c"
"""

shellcode = asm(assembly)
print(shellcode)
```

E simples fazer o disassembly de shellcodes

```python
context.arch = "x86_64"
assembly = """
jmp executable
main:
	pop rdi
	xor rsi, rsi
	xor rdx, rdx
	push 59
	pop rax
	syscall
executable:
    call main
	.ascii "c"
"""

shellcode = asm(assembly)
print(disasm(shellcode))
```

## Shellcodes inclusos no pwntools
Há um monte de shellcodes prontos para serem gerados automaticamente no módulo `shellcraft`.

```python
shellcode = asm(shellcraft.amd64.linux.cat('/flag'))
print(disasm(shellcode))
```

```python
shellcode = asm(shellcraft.amd64.linux.sh())
print(disasm(shellcode))
```