---
date: 2018-02-28
draft: true
linktitle: Escrevendo testes unitários com Golang
title: Escrevendo testes unitários com Golang
categories: ["dev", "2018"]
tags: ["unit tests", "golang", "testes unitários"]
weight: 10
---


Para escrever testes unitários com Golang tudo o que precisamos é importar o pacote `testing` e começar a escrever os testes.

### Exemplo

Temos um arquivo chamado `main.go` com uma função chamada `Soma()` que realiza a soma de dois números:

```
package main

func Soma(x,y int) int{
  return x + y
}
...

```

Agora vamos escrever um teste para verificar a função `Soma()`, em um arquivo chamado `main_test.go`:

```
package main

func TestSoma(t *testing.T){
  resultado := Soma(2,2)
  if resultado != 4{
    t.Errorf("Soma esperada: %d, obtida: %d", 4, resultado)
  }
}
```

A função de teste acima, testa apenas **UM** valor por vez, para múltiplas entrada (e multiplas saída), tem-se o conceito de *test tables*:
```
package main

import "testing"

func TestSoma(t *testing.T) {
	tabelas := []struct {
		x int
		y int
		n int
	}{
		{1, 2, 3},
		{2, 3, 5},
		{0, 1, 1},
	}

	for _, tabela := range tabelas {
		total := Soma(tabela.x, tabela.y)
		if total != tabela.n {
			t.Errorf("Soma de (%d+%d) incorreta, obtido: %d, esperado: %d.", tabela.x, tabela.y, total, tabela.n)
		}
	}
}
```

Pronto, já temos o teste relacionado a função de soma. Alguma considerações gerais:

* O único parâmetro requerido pela função de teste é `t *testing.T`
* O nome da função de testes segue o padrão: `Test` + nome da função com primeira letra maiúscula (e.g `TestSoma`)
* O nome do arquivo de testes segue padrão `algumacoisa_test.go` (e.g `main_test.go`)

Para executar os testes, podemos fazer:

* no mesmo diretório: `go test`
* explicitando o diretório: `go test github.com/edsoncelio/golangtest`

Esse é o básico para começar a escrever testes unitários, deixo algumas leituras recomendadas para tópicos mais avançados.

Leituras recomendadas: 
1. [Golang basics - writing unit tests](https://blog.alexellis.io/golang-writing-unit-tests/)
2. [Testing](https://golang.org/pkg/testing/)

