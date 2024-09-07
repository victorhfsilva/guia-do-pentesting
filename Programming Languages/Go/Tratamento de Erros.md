# Tratamento de Erros em Go

Go é uma linguagem que valoriza a simplicidade e a clareza, e isso se reflete na maneira como a linguagem lida com erros. Em vez de usar exceções como muitas outras linguagens, Go adota um modelo de tratamento de erros baseado em retornos explícitos e verificação de erros. Além disso, para situações críticas, Go oferece as funções `panic` e `recover`, que permitem lidar com condições anômalas e recuperar o controle do fluxo do programa. 

## Errors

### O Tipo `error`

Em Go, os erros são valores e implementam a interface `error`. A interface `error` é definida da seguinte forma:

```go
type error interface {
    Error() string
}
```

Qualquer tipo que tenha um método `Error()` que retorne uma string é considerado um erro.

### Criando Erros

Você pode criar erros usando a função `errors.New` do pacote `errors`:

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

- **Nota:** No exemplo acima, o erro é tratado verificando se a variável `err` não é `nil`.

### Função `fmt.Errorf`

Go também permite criar erros formatados usando `fmt.Errorf`, que funciona de maneira semelhante a `fmt.Sprintf`:

```go
import "fmt"

func dividir(a, b int) (int, error) {
    if b == 0 {
        return 0, fmt.Errorf("não é possível dividir %d por zero", a)
    }
    return a / b, nil
}
```

### Retornando Erros

Quando uma função pode falhar, o padrão em Go é retornar um valor normal e um valor de erro. Quem chama a função deve verificar o erro antes de usar o valor retornado.

```go
import (
    "fmt"
    "os"
)

func lerArquivo(nome string) ([]byte, error) {
    conteudo, err := os.ReadFile(nome)
    if err != nil {
        return nil, err
    }
    return conteudo, nil
}

func main() {
    conteudo, err := lerArquivo("arquivo.txt")
    if err != nil {
        fmt.Println("Erro ao ler o arquivo:", err)
        return
    }
    fmt.Println(string(conteudo))
}
```

## Pânico (`panic`)

### Introdução ao `panic`

O `panic` é usado em Go para situações em que o programa não pode continuar a execução. Ele é semelhante a lançar uma exceção em outras linguagens. Quando ocorre um `panic`, a execução da função atual para, e o controle é passado para as funções `defer` que foram empilhadas até o ponto em que o programa encerra.

```go
import "fmt"

func causaPanic() {
    panic("ocorreu um erro grave!")
}

func main() {
    fmt.Println("Iniciando...")
    causaPanic()
    fmt.Println("Este código não será executado")
}
```

- **Nota:** O programa imprimirá "Iniciando..." e, em seguida, entrará em pânico, o que faz com que o programa pare imediatamente e não execute o código subsequente.

### Quando Usar `panic`

O uso de `panic` é reservado para situações graves, como:
- Erros que indicam um estado de programa inaceitável (como falhas em invariantes).
- Condições que não devem ser tratadas durante a execução normal do programa, como erros internos que o usuário do pacote não pode prever.

### Função `defer` e `panic`

A palavra-chave `defer` é útil para garantir que operações de limpeza sejam realizadas, mesmo quando ocorre um pânico.

```go
import "fmt"

func main() {
    defer fmt.Println("Executando defer antes da finalização")
    fmt.Println("Iniciando...")
    panic("ocorreu um erro grave!")
    fmt.Println("Este código não será executado")
}
```

- **Nota:** No exemplo acima, a função `defer` será executada antes que o programa seja finalizado pelo pânico.

---

## Recuperação (`recover`)

### Introdução ao `recover`

O `recover` é usado para capturar um pânico em andamento e permitir que o programa continue sua execução. Ele só funciona dentro de uma função `defer`.

```go
import "fmt"

func causarPanic() {
    defer func() {
        if r := recover(); r != nil {
            fmt.Println("Recuperado do pânico:", r)
        }
    }()
    panic("erro crítico")
}

func main() {
    causarPanic()
    fmt.Println("Programa continua após o pânico")
}
```

- **Nota:** A função `recover` captura o valor passado para `panic` e permite que o programa continue sua execução.

### Limitações do `recover`

- **Dentro de `defer`:** O `recover` só funciona dentro de funções `defer`. Fora delas, `recover` não tem efeito.
- **Captura Limitada:** O `recover` só pode capturar um pânico que ocorre na mesma cadeia de chamadas onde o `recover` está. Ele não pode capturar pânicos em goroutines separadas, a menos que o `recover` esteja diretamente dentro dessa goroutine.

## Melhores Práticas

- **Evite `panic` para Erros Comuns:** Use `panic` apenas para situações excepcionais. Para erros comuns, como entradas inválidas ou falhas de I/O, retorne um erro.
- **Limite o Uso de `recover`:** Use `recover` apenas para situações onde você pode lidar com o erro de maneira significativa e onde é essencial que o programa continue executando.
- **Trate Erros Imediatamente:** Verifique os valores de erro imediatamente após a chamada de função. Isso melhora a clareza e a segurança do código.
