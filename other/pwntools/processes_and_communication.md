---
layout: post
tags: pwn
categories: pwntools
---

# Processos e Comunicação

## Processos

Um `process` é a forma principal de interação usada pelo pwntools.

```python
p = process('./vulnerable_binary')
```

É possível iniciar um "processo **remoto**" e se conectar usando `remote`.

```python
p = remote('endereco.ip.ou.url', port)
```

## Enviando dados para processos

### p.send\(data\)

Envia `data` para o processo `p`. `data`pode ser tanto uma `string` quando um `bytes-like-object`, pwntools lida com a conversão.

### p.sendline\(data\)

Envia `data` para o processo seguido de um **newline** `\n`. Algums programas requerem um `\n` quando estão recebendo uma entrada \(lembre daqueles programas que precisam de um enter após a inserção dos dados\).

`p.sendline(data)` é equivalente a `p.send(data + '\n')`  


{% hint style="danger" %}
A falta de um **newline** ou a inserção não necessária pode ser a causa de um exploit que não funciona. Verifique qual deve ser usado.
{% endhint %}

## Recebendo dados de processos

### p.recv\(quantidade\)

Recebe determinada `quantidade` de bytes do processo.

### p.recvuntil\(delimitador, drop=False\)

Retorna todos dados até que encontre o `delimitador`. Se a propriedade `drop` for `True`, remove dos dados retornados o `delimitador`.

### p.recvline\(keepends=True\)

O equivalente a `p.recvuntil('\n', drop=keepends)`. Ou seja, recebe dados até encontrar um caracter **new line**. Se `keepends` estiver setado como `True`, mantém o caracter **new line** `\n`.

### p.clean\(timeout=0.02\)

Recebe todos os dados até o tempo setado no `timeout` e limpa o buffer.

### Timeout

Todas as funções de recebimento de dados contém um parametro `timeout` opcional. Por exemplo `p.recv(1024, timeout=1)` tentará receber 1024 bytes mas se 1 segundo se passar e esse montante não for atingido, os dados ficarão no buffer e uma string vazia será retornada.
