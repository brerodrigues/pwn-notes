---
layout: post
tags: pwn
categories: pwntools
---

# ELF

A classe `ELF` do pwntools expõe funcionalidades para a interação com arquivos ELF. Compreende-la irá facilitar a vida. Essencialmente, a classe permite a busca de informações em tempo de execução. Chega de endereços hardcoded (:

## Criando um objeto ELF

```python
elf = ELF('./vulnerable_program')
```

## Criando um processo a partir de um objeto ELF

```python
p = elf.process()
```

## PLT e GOT

Quer tentar um `ret2plt`? Fácil. Busque endereços direto no ELF.

```python
puts_plt = elf.plt['puts']
puts_got = elf.got['puts']
```

## Funções

Precisa retornar para uma função chamada `vuln`? Não precisa mais abrir o disassembler ou debuggar.

```python
vuln_address = elf.functions['vuln']
```

`elf.functions` retornará um objeto `Function`, caso precise apenas do endereço em forma de `int`, use `elf.symbols`:

```python
vuln_address = elf.symbols['vuln']
```

## elf.libc

Localmente podemos pegar o binário da libc que o elf está usando.

```python
libc = elf.libc
```

## elf.search\(needle, writable=False\)

Busque no binário por uma sequência especifica de caracteres. Útil quando se está tentando um `ret2libc`. Se `writable` for setado para `true`, a busca será feita apenas em seções de memória onde a escrita é permitida. Note que o retorno é um **generator**, então caso deseje o primeiro resultado, use `next()`.

```python
binsh = next(libc.search(b'/bin/sh\x00'))
```

## elf.address

`elf.address` é o endereço base do binário. Se o binário não tiver PIE habilitado, esse endereço será absoluto; se tiver, todos endereços serão relativos \(nesses casos, a base será `0x0`\).

Atualizar o valor de `address`, automaticamente atualiza o endereço de `symbols`, `got`, `plt` e `functions`, o que é útil quando ajustes são necessários em casos de proceções PIE ou ASLR.

Suponhamos que você vazou o endereço base da `libc` e ASLR está habilitado; com pwntools, é ridicularmente fácil de obter o endereço de `system` para um `ret2libc`.

```python
libc = elf.libc
libc.address = 0xf7f23000           # Você 'vazou' esse endereço

system = libc.symbols['system']
binsh = next(libc.search(b'/bin/sh\x00'))
exit_addr = libc.symbols['exit']

# E agora você pode ret2libc
```

