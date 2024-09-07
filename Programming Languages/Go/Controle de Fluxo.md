#  Controle de Fluxo em Go

## 1. Estruturas de Decisão

### `if` e `else`

O Go usa as palavras-chave `if` e `else` para criar condições:

```go
package main

import "fmt"

func main() {
    x := 10

    if x > 0 {
        fmt.Println("x é positivo")
    } else if x == 0 {
        fmt.Println("x é zero")
    } else {
        fmt.Println("x é negativo")
    }
}
```

- **Nota:** Em Go, parênteses ao redor da condição não são necessários, mas as chaves `{}` são obrigatórias.

### `if` com Inicialização

Você pode declarar e inicializar uma variável dentro da própria declaração `if`:

```go
if y := 42; y > x {
    fmt.Println("y é maior que x")
}
```

### `switch`

O `switch` em Go é uma maneira eficiente de escrever uma série de declarações `if-else` encadeadas. Ele pode ser usado tanto com valores como com expressões.

```go
package main

import "fmt"

func main() {
    day := "terça-feira"

    switch day {
    case "segunda-feira":
        fmt.Println("Hoje é segunda-feira")
    case "terça-feira":
        fmt.Println("Hoje é terça-feira")
    default:
        fmt.Println("Hoje não é segunda nem terça-feira")
    }
}
```

- **Nota:** O Go não precisa da palavra-chave `break` ao final de cada caso, pois ele "quebra" automaticamente após executar um `case`.

### `switch` sem expressão

O `switch` sem expressão é equivalente a uma série de `if-else` e é útil para testar várias condições:

```go
package main

import "fmt"

func main() {
    x := -10

    switch {
    case x > 0:
        fmt.Println("x é positivo")
    case x < 0:
        fmt.Println("x é negativo")
    default:
        fmt.Println("x é zero")
    }
}
```

## 2. Estruturas de Loop

### `for`

O `for` é o único tipo de loop em Go. Ele pode ser usado de várias maneiras:

#### Loop Clássico

Semelhante a outros idiomas, o loop clássico tem uma inicialização, condição e incremento:

```go
package main

import "fmt"

func main() {
    for i := 0; i < 5; i++ {
        fmt.Println(i)
    }
}
```

#### Loop tipo `while`

O `for` pode ser usado como um loop `while` omitindo as partes de inicialização e incremento:

```go
package main

import "fmt"

func main() {
    x := 0
    for x < 5 {
        fmt.Println(x)
        x++
    }
}
```

#### Loop Infinito

O loop infinito é criado omitindo todas as partes do `for`:

```go
package main

import "fmt"

func main() {
    for {
        fmt.Println("loop infinito")
    }
}
```

- **Nota:** Use `break` para sair de loops infinitos.

### Controle de Loop: `break` e `continue`

- **`break`:** Interrompe a execução do loop imediatamente.
- **`continue`:** Pula a iteração atual e continua com a próxima iteração.

```go
package main

import "fmt"

func main() {
    for i := 0; i < 10; i++ {
        if i%2 == 0 {
            continue  // Pula números pares
        }
        if i > 7 {
            break  // Sai do loop quando i for maior que 7
        }
        fmt.Println(i)
    }
}
```

### Iteração com `range`

O `range` é uma construção poderosa em Go usada principalmente para iterar sobre arrays, slices, maps, strings e canais. Dependendo do tipo de coleção, ele retorna diferentes valores durante a iteração.

#### Iterando sobre Arrays e Slices

Quando usado com arrays ou slices, `range` retorna o índice e o valor do elemento.

```go
package main

import "fmt"

func main() {
    nums := []int{2, 4, 6, 8, 10}

    for i, num := range nums {
        fmt.Printf("Índice: %d, Valor: %d\n", i, num)
    }
}
```

- **Ignorando o Índice ou o Valor:** Se você não precisa do índice, pode usar `_` para ignorá-lo.

```go
for _, num := range nums {
    fmt.Println(num)
}
```

#### Iterando sobre Maps

Ao iterar sobre maps, `range` retorna a chave e o valor para cada par.

```go
package main

import "fmt"

func main() {
    scores := map[string]int{"Alice": 90, "Bob": 85, "Charlie": 92}

    for name, score := range scores {
        fmt.Printf("%s tem uma pontuação de %d\n", name, score)
    }
}
```

#### Iterando sobre Strings

Ao iterar sobre strings, `range` retorna o índice do byte e o valor do rune (caractere Unicode) em cada posição.

```go
package main

import "fmt"

func main() {
    s := "Olá, Go!"

    for i, c := range s {
        fmt.Printf("Índice: %d, Caractere: %c\n", i, c)
    }
}
```

