---
layout: post
tags: pwn
categories: pwntools
---

# Packing

Packing com o modulo `struct` é burocrático e exige setups repetitivos.
Com pwntools tudo é configurável via a váriabel global `context` e utilizado para os cálculos.

## p64\(addr\)

Packs `addr` de acordo com a endianness configurada em `context`, por default esse valor é: **little-endian**.  


```python
context.arch = 'x86_64'

p64(0x04030201) == b'\x01\x02\x03\x04\x00\x00\x00\x00'
True

context.endian = 'big'
p64(0x04030201) == b'\x00\x00\x00\x00\x04\x03\x02\x01'
True
```

## u64\(data\)

Unpacks `data` de acordo com a endianness configurada em `context`; o oposto do que faz `p64()` (duh!).

```python
hex(u64(p64(0x04030201)))                                                                       
'0x4030201'
```

## flat\(\*args\)

Pega vários valores como argumento e packs de acordo com a configuração do `context`.

```python
payload = flat(
    0x01020304,
    0x59549342,
    0x12186354
)
```

é o equivalente a:

```python
payload = p64(0x01020304) + p64(0x59549342) + p64(0x12186354)
```