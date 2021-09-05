---
layout: post
tags: pwn
categories: pwntools
---

# Logging and Context

## Logging

Logging é um recurso bastante útil e oferece a opção de logar informações de diferentes formas.

### log.info\(text\)

```text
>>> log.info('Base do binário está em 0x400000')
[*] Base do binário está em 0x400000
```

### log.success\(text\)

```text
>>> log.success('ASLR bypassed! Libc base está em 0xf7653000')
[+] ASLR bypassed! Libc base está em 0xf7653000
```

### log.error\(text\)

```text
>>> log.error('O payload é muito longo')
[-] O payload é muito longo
```

## Context (Contexto)

`context` é uma 'variável global' do pwntools que permite definir certos valores de configuração. Como por exemplo a arquitetura e sistema operacional, informações cruciais para a geração de shellcodes corretamente.

```python
context.arch = 'i386'
context.os = 'linux'
context.endian = 'little'
context.bits = 64
```

Após essa definição, sempre que forem usadas funções como `p64()` para fazer o packing de um endereço, serão usadas as informações do `context`. 