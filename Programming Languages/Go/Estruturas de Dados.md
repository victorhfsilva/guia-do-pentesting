# Estruturas de Dados em Go

## Arrays

### Introdução aos Arrays

Um array em Go é uma sequência de elementos de um tipo específico, com tamanho fixo. Todos os elementos de um array devem ser do mesmo tipo, e o tamanho do array faz parte do seu tipo.

```go
package main

import "fmt"

func main() {
    var a [5]int // Declara um array de 5 inteiros
    fmt.Println(a) // [0 0 0 0 0]

    b := [3]string{"Go", "Python", "Java"}
    fmt.Println(b) // [Go Python Java]
}
```

### Acessando e Modificando Elementos

Você pode acessar e modificar elementos de um array usando índices, que começam em zero.

```go
package main

import "fmt"

func main() {
    a := [5]int{1, 2, 3, 4, 5}
    fmt.Println(a[2]) // 3

    a[2] = 10
    fmt.Println(a) // [1 2 10 4 5]
}
```

### Percorrendo Arrays com `for` e `range`

```go
package main

import "fmt"

func main() {
    a := [3]int{1, 2, 3}
    
    for i := 0; i < len(a); i++ {
        fmt.Println(a[i])
    }

    // Usando range
    for index, value := range a {
        fmt.Printf("Index: %d, Value: %d\n", index, value)
    }
}
```

## Slices

### Introdução aos Slices

Slices são uma camada mais poderosa e flexível que os arrays. Diferente dos arrays, slices têm tamanho dinâmico e apontam para uma estrutura subjacente de array.

```go
package main

import "fmt"

func main() {
    s := []int{1, 2, 3, 4, 5} // Declara um slice
    fmt.Println(s) // [1 2 3 4 5]
}
```

### Manipulação de Slices

Slices podem ser manipulados facilmente:

- **Acesso e Modificação:** Funciona de maneira similar aos arrays.
- **Append:** Adiciona elementos ao final do slice.
- **Copy:** Copia o conteúdo de um slice para outro.

```go
package main

import "fmt"

func main() {
    s := []int{1, 2, 3}
    s = append(s, 4, 5)
    fmt.Println(s) // [1 2 3 4 5]

    t := make([]int, len(s))
    copy(t, s)
    fmt.Println(t) // [1 2 3 4 5]
}
```

### Sub-slices

Você pode criar sub-slices a partir de slices existentes:

```go
package main

import "fmt"

func main() {
    s := []int{1, 2, 3, 4, 5}
    sub := s[1:3]
    fmt.Println(sub) // [2 3]
}
```

### Métodos Úteis para Slices

- **Capacidade (`cap`)**: Retorna a capacidade do slice.
- **Comprimento (`len`)**: Retorna o comprimento do slice.

```go
package main

import "fmt"

func main() {
    s := []int{1, 2, 3, 4, 5}
    fmt.Println("Length:", len(s)) // 5
    fmt.Println("Capacity:", cap(s)) // 5

    s = append(s, 6, 7)
    fmt.Println("New Length:", len(s)) // 7
    fmt.Println("New Capacity:", cap(s)) // 10
}
```

## Maps

### Introdução aos Maps

Maps em Go são coleções de pares chave-valor. As chaves em um map devem ser únicas e do mesmo tipo, assim como os valores.

```go
package main

import "fmt"

func main() {
    m := make(map[string]int)
    m["Alice"] = 25
    m["Bob"] = 30
    fmt.Println(m) // map[Alice:25 Bob:30]
}
```

### Acessando e Modificando Valores

```go
package main

import "fmt"

func main() {
    m := map[string]int{"Alice": 25, "Bob": 30}
    fmt.Println(m["Alice"]) // 25

    m["Alice"] = 26
    fmt.Println(m) // map[Alice:26 Bob:30]

    delete(m, "Bob")
    fmt.Println(m) // map[Alice:26]
}
```

### Verificando a Existência de uma Chave

Você pode verificar se uma chave existe em um map usando a seguinte sintaxe:

```go
package main

import "fmt"

func main() {
    m := map[string]int{"Alice": 25}

    if idade, existe := m["Alice"]; existe {
        fmt.Printf("Alice tem %d anos.\n", idade)
    } else {
        fmt.Println("Alice não foi encontrada.")
    }
}
```

### Percorrendo Maps com `range`

```go
package main

import "fmt"

func main() {
    m := map[string]int{"Alice": 25, "Bob": 30}
    
    for chave, valor := range m {
        fmt.Printf("%s tem %d anos.\n", chave, valor)
    }
}
```

### Métodos Úteis para Maps

Embora maps em Go não tenham métodos associados diretamente, as operações padrão são altamente eficazes:
- **`delete(map, key)`:** Remove uma chave de um map.
- **Verificação de Existência:** Usando a sintaxe `valor, existe := map[key]`.


## Structs

### a. Introdução às Structs

Structs são coleções compostas de campos. Elas permitem agrupar dados de diferentes tipos em uma única entidade, tornando-as fundamentais para modelar objetos e entidades em Go.

```go
package main

import "fmt"

type Pessoa struct {
    Nome  string
    Idade int
}

func main() {
    p := Pessoa{"Alice", 30}
    fmt.Println(p) // {Alice 30}
}
```

### Acessando e Modificando Campos

```go
package main

import "fmt"

type Pessoa struct {
    Nome  string
    Idade int
}

func main() {
    p := Pessoa{"Bob", 25}
    fmt.Println(p.Nome) // Bob

    p.Idade = 26
    fmt.Println(p) // {Bob 26}
}
```

### Structs Anônimas

Você pode criar structs anônimas, úteis para estruturas de dados temporárias:

```go
package main

import "fmt"

func main() {
    pessoa := struct {
        Nome  string
        Idade int
    }{
        Nome:  "Charlie",
        Idade: 28,
    }
    fmt.Println(pessoa) // {Charlie 28}
}
```

### Métodos em Structs

Você pode associar métodos a structs, permitindo que elas tenham comportamento associado aos seus dados.

```go
package main

import "fmt"

type Pessoa struct {
    Nome  string
    Idade int
}

// Método associado à struct Pessoa
func (p Pessoa) Saudacao() string {
    return fmt.Sprintf("Olá, meu nome é %s e eu tenho %d anos.", p.Nome, p.Idade)
}

func main() {
    p := Pessoa{"Alice", 30}
    fmt.Println(p.Saudacao()) // Olá, meu nome é Alice e eu tenho 30 anos.
}
```

### Ponteiros em Structs

Você pode usar ponteiros em structs para modificar o estado da instância original:

```go
package main

import "fmt"

type Pessoa struct {
    Nome  string
    Idade int
}

// Método que modifica a struct
func (p *Pessoa) Aniversario() {
    p.Idade++
}

func main() {
    p := Pessoa{"Bob", 25}
    p.Aniversario()
    fmt.Println(p.Idade) // 26
}
```