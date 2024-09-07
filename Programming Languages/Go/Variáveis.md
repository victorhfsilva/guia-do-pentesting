# Tipos de Variáveis em Go

## Tipos de Variáveis em Go

Go é uma linguagem fortemente tipada, o que significa que cada variável tem um tipo específico. Existem vários tipos de dados em Go, categorizados da seguinte forma:

### a. Tipos Básicos

1. **Inteiros (Integers)**
   - **int**: Tamanho padrão do inteiro (32 ou 64 bits, dependendo da arquitetura).
   - **int8, int16, int32, int64**: Especificam o número de bits.
   - **uint8, uint16, uint32, uint64**: Versões sem sinal (apenas valores positivos).

2. **Números de Ponto Flutuante (Floating Point)**
   - **float32**: Números de ponto flutuante de 32 bits.
   - **float64**: Números de ponto flutuante de 64 bits (mais preciso).

3. **Números Complexos**
   - **complex64**: Números complexos com partes reais e imaginárias de 32 bits.
   - **complex128**: Números complexos com partes reais e imaginárias de 64 bits.

4. **String**
   - Representa uma sequência de caracteres (UTF-8).
   ```go
   var s string = "Hello, Go!"
   ```

5. **Booleano (Boolean)**
   - **bool**: Pode ter os valores `true` ou `false`.
   ```go
   var isGoAwesome bool = true
   ```

### b. Tipos Compostos

1. **Arrays**
   - Conjunto fixo de elementos do mesmo tipo.
   ```go
   var arr [5]int = [5]int{1, 2, 3, 4, 5}
   ```

2. **Slices**
   - Similar a arrays, mas com tamanho dinâmico.
   ```go
   var slice []int = []int{1, 2, 3}
   ```

3. **Maps**
   - Coleção de pares chave-valor.
   ```go
   var m map[string]int = map[string]int{"one": 1, "two": 2}
   ```

4. **Structs**
   - Coleção de campos com tipos diferentes.
   ```go
   type Person struct {
       Name string
       Age  int
   }
   ```

5. **Pointers**
   - Referência para o endereço de memória de uma variável.
   ```go
   var p *int
   ```

## Declaração de Variáveis

### a. Declaração Explícita

Em Go, você pode declarar uma variável com seu tipo explicitamente:

```go
var x int = 10
var y float64 = 20.5
```

### b. Declaração com Inferência de Tipo

Go permite a inferência de tipos, onde o compilador deduz o tipo da variável com base no valor atribuído:

```go
var x = 10       // inferido como int
var y = 20.5     // inferido como float64
```

### c. Declaração Curta

Uma forma mais concisa de declarar e inicializar variáveis é usando o operador de declaração curta (`:=`):

```go
x := 10          // inferido como int
y := 20.5        // inferido como float64
isGoAwesome := true  // inferido como bool
```

## Regras de Inferência de Tipos

- **Inteiros e Números de Ponto Flutuante:** Se um valor atribuído for um número inteiro, ele será inferido como `int` por padrão. Se for um número com ponto decimal, será inferido como `float64`.
  
- **Constantes:** Constantes não possuem tipo até serem atribuídas a uma variável, permitindo flexibilidade de uso.
  ```go
  const x = 42
  var y int = x       // x é inferido como int
  var z float64 = x   // x é inferido como float64
  ```

- **Strings:** Sempre inferidas como `string` se atribuídas a uma variável.

- **Booleans:** Inferidos como `bool` quando a expressão avaliada retorna `true` ou `false`.

## Boas Práticas

- **Use Inferência Sempre que Possível:** Ela torna o código mais conciso e legível.
- **Declare Tipos Explícitos quando Necessário:** Quando o tipo da variável é crítico ou a inferência não é clara, especifique o tipo explicitamente.
- **Evite o Uso de Inferência em Código Complexo:** Em situações complexas, a inferência pode tornar o código difícil de entender.

## Exemplos Práticos

Aqui estão alguns exemplos práticos para reforçar o conceito:

```go
package main

import "fmt"

func main() {
    // Inferência básica
    a := 10               // int
    b := 3.14             // float64
    c := "Hello, Go!"     // string
    d := true             // bool

    // Uso de inferência com funções
    length := len(c)      // int (resultado da função len)

    fmt.Println(a, b, c, d, length)

    // Inferência em expressões
    sum := a + int(b)     // int (a conversão explícita de b é necessária)
    fmt.Println(sum)
}
```