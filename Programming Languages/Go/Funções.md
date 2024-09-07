# Funções em Go

## Definindo Funções

### Estrutura Básica

A definição de uma função em Go segue a seguinte estrutura:

```go
package main

import "fmt"

func nomeDaFuncao(parâmetro1 tipo1, parâmetro2 tipo2) tipoRetorno {
    // corpo da função
    return valor
}

func main() {
    resultado := nomeDaFuncao(3, 4)
    fmt.Println(resultado)
}
```

Exemplo de uma função simples que soma dois números:

```go
func soma(a int, b int) int {
    return a + b
}
```

- **Parâmetros:** Definidos entre parênteses, com o tipo especificado após o nome da variável.
- **Tipo de Retorno:** O tipo do valor que a função retorna é definido após os parênteses dos parâmetros.
- **Retorno:** A palavra-chave `return` é usada para devolver um valor da função.

### Função sem Retorno

Se a função não retorna um valor, o tipo de retorno pode ser omitido:

```go
func saudacao(nome string) {
    fmt.Println("Olá,", nome)
}
```

### Múltiplos Retornos

Em Go, uma função pode retornar múltiplos valores:

```go
func dividir(a int, b int) (int, int) {
    quociente := a / b
    resto := a % b
    return quociente, resto
}

func main() {
    q, r := dividir(10, 3)
    fmt.Println("Quociente:", q)
    fmt.Println("Resto:", r)
}
```

- **Nota:** Esta funcionalidade é frequentemente usada para retornar valores junto com um possível erro.

## Argumentos Nomeados e Omissão de Tipos

### Argumentos Nomeados

Se vários parâmetros da função compartilham o mesmo tipo, você pode omitir o tipo para os parâmetros anteriores:

```go
func subtrair(a, b int) int {
    return a - b
}
```

### Retornos Nomeados

Você pode nomear os valores de retorno dentro da própria assinatura da função:

```go
func operacoes(a, b int) (soma, subtracao int) {
    soma = a + b
    subtracao = a - b
    return
}
```

- **Nota:** Quando valores de retorno são nomeados, a palavra-chave `return` pode ser usada sem argumentos, e os valores atuais das variáveis de retorno serão devolvidos.


## Funções Variádicas

Funções variádicas podem receber um número indefinido de argumentos. Em Go, você pode definir uma função variádica usando `...` antes do tipo do último parâmetro:

```go
func somarTudo(numeros ...int) int {
    total := 0
    for _, numero := range numeros {
        total += numero
    }
    return total
}

func main() {
    resultado := somarTudo(1, 2, 3, 4, 5)
    fmt.Println("Total:", resultado)
}
```

- **Nota:** A função `somarTudo` pode ser chamada com qualquer número de inteiros.


## Funções Anônimas e Closures

### Funções Anônimas

Em Go, é possível definir funções sem nome, conhecidas como funções anônimas. Elas são frequentemente usadas como valores de função ou passadas como argumentos.

```go
func main() {
    func() {
        fmt.Println("Esta é uma função anônima.")
    }()
}
```

- **Nota:** No exemplo acima, a função anônima é executada imediatamente após a sua definição.

### Closures

Closures são funções que referenciam variáveis do escopo externo. Quando você cria uma função dentro de outra função, a função interna tem acesso às variáveis da função externa.

```go
func contador() func() int {
    i := 0
    return func() int {
        i++
        return i
    }
}

func main() {
    contar := contador()
    fmt.Println(contar()) // Saída: 1
    fmt.Println(contar()) // Saída: 2
    fmt.Println(contar()) // Saída: 3
}
```

- **Nota:** A função `contador` retorna uma função que incrementa e retorna o valor de `i`. A variável `i` é preservada entre as chamadas da função retornada.

---

## Funções Como Valores e Argumentos

Em Go, as funções são tratadas como valores de primeira classe, o que significa que podem ser atribuídas a variáveis e passadas como argumentos para outras funções.

### Funções Como Valores

```go
func multiplicar(a, b int) int {
    return a * b
}

func main() {
    operacao := multiplicar
    resultado := operacao(5, 6)
    fmt.Println("Resultado:", resultado)
}
```

### Funções Como Argumentos

```go
func aplicarOperacao(a int, b int, operacao func(int, int) int) int {
    return operacao(a, b)
}

func main() {
    soma := func(a, b int) int {
        return a + b
    }
    resultado := aplicarOperacao(3, 4, soma)
    fmt.Println("Resultado:", resultado)
}
```

- **Nota:** No exemplo acima, a função `aplicarOperacao` recebe outra função como argumento e a utiliza para processar os valores.

## Recursão

Funções em Go podem ser recursivas, ou seja, podem chamar a si mesmas. Um exemplo clássico é o cálculo do fatorial de um número:

```go
func fatorial(n int) int {
    if n == 0 {
        return 1
    }
    return n * fatorial(n-1)
}

func main() {
    resultado := fatorial(5)
    fmt.Println("Fatorial de 5 é:", resultado)
}
```

- **Nota:** A recursão deve ser usada com cuidado para evitar loops infinitos e estouros de pilha.


## Manipulação de Erros em Funções

Go usa um padrão simples e explícito para manipulação de erros, retornando valores de erro como parte do retorno da função.

### a. Retorno de Erros

```go
import (
    "errors"
    "fmt"
)

func dividir(a, b int) (int, error) {
    if b == 0 {
        return 0, errors.New("divisão por zero")
    }
    return a / b, nil
}

func main() {
    resultado, err := dividir(10, 0)
    if err != nil {
        fmt.Println("Erro:", err)
        return
    }
    fmt.Println("Resultado:", resultado)
}
```

- **Nota:** O tratamento de erros em Go é feito através de uma verificação explícita do valor de retorno de erro.


## Defer, Panic e Recover

### `defer`

O `defer` adia a execução de uma função até que a função que a contém termine. É útil para garantir que certos passos sejam realizados, como fechar arquivos ou liberar recursos.

```go
import "fmt"

func main() {
    defer fmt.Println("Esta mensagem será impressa por último")
    fmt.Println("Esta mensagem será impressa primeiro")
}
```

### `panic` e `recover`

- **`panic`:** Interrompe a execução normal do programa. É usado para situações inesperadas.
- **`recover`:** Permite capturar o erro de uma `panic` e restaurar a execução do programa.

```go
import "fmt"

func causaPanic() {
    defer func() {
        if r := recover(); r != nil {
            fmt.Println("Recuperado de um pânico:", r)
        }
    }()
    panic("algo deu muito errado")
}

func main() {
    causaPanic()
    fmt.Println("Após o pânico")
}
```

- **Nota:** Use `panic` e `recover` com moderação, preferindo retornar erros sempre que possível.
