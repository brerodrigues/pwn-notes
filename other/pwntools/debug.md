---
layout: post
tags: pwn
categories: pwntools
---

# Debug

Para agilizar o desenvolvimento de exploits, pwntools oferece facilidades para debuggar binários usando o GDB.

## Anexando o debugger a um processo

O módulo `gdb` tem três funções úteis:

`attach()` para se anexar a um processo:

```python
p = process('./vulnerable_program')
gdb.attach(p, '''
break _start
continue
''')
```

`debug()` para criar um novo processo e parar na primeira instrução:

```python
io = gdb.debug('./vulnerable_program', '''
break main
continue
''')
```

e `debug_shellcode()` para depurar os shellcodes criados com o próprio pwntools:

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
	.ascii "/bin/sh"
"""
shellcode = asm(assembly)
debug_shellcode(shellcode)
```