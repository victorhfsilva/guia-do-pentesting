### Orientação a Objetos em Go

Go é uma linguagem que se afasta das convenções tradicionais de orientação a objetos (OO) encontradas em linguagens como Java ou C#. Embora Go não tenha classes, herança ou outros conceitos OO clássicos, ela oferece poderosos mecanismos de composição, interfaces e métodos que permitem a construção de software modular e reutilizável. 


## Structs: A Base para Objetos

### Definindo Structs

Em Go, structs são a base para construir "objetos". Uma struct é uma coleção de campos, onde cada campo pode ter um nome e um tipo. Structs são usadas para agrupar dados relacionados.

```go
package main

import "fmt"

type Pessoa struct {
    Nome  string
    Idade int
}

func main() {
    p := Pessoa{"Alice", 30}
    fmt.Println(p.Nome) // Alice
    fmt.Println(p.Idade) // 30
}
```

### Instanciando Structs

Você pode criar instâncias de structs diretamente ou usando a notação literal:

```go
package main

import "fmt"

type Pessoa struct {
    Nome  string
    Idade int
}

func main() {
    // Usando a notação literal
    p := Pessoa{"Alice", 30}
    
    // Especificando os campos
    q := Pessoa{Nome: "Bob", Idade: 25}
    
    // Campos não especificados recebem o valor zero
    r := Pessoa{Nome: "Carlos"}

    fmt.Println(p) // {Alice 30}
    fmt.Println(q) // {Bob 25}
    fmt.Println(r) // {Carlos 0}
}
```

### Ponteiros para Structs

Ponteiros para structs são comuns em Go, especialmente quando você deseja modificar a struct original em funções ou métodos.

```go
package main

import "fmt"

type Pessoa struct {
    Nome  string
    Idade int
}

func (p *Pessoa) Aniversario() {
    p.Idade++
}

func main() {
    p := Pessoa{"Alice", 30}
    p.Aniversario()
    fmt.Println(p.Idade) // 31
}
```

---

## Métodos: Comportamento Associado a Structs

### Definindo Métodos

Em Go, métodos são funções associadas a tipos de structs. Você pode definir métodos para qualquer tipo, não apenas structs, mas structs são o uso mais comum.

```go
package main

import "fmt"

type Retangulo struct {
    Largura, Altura float64
}

// Método que calcula a área do Retângulo
func (r Retangulo) Area() float64 {
    return r.Largura * r.Altura
}

func main() {
    r := Retangulo{10, 5}
    fmt.Println("Área:", r.Area()) // Área: 50
}
```

### Métodos com Recebedores Ponteiros

Se um método precisa modificar o estado de uma struct, você deve usar um recebedor ponteiro (`*Tipo`).

```go
package main

import "fmt"

type Contador struct {
    Valor int
}

func (c *Contador) Incrementar() {
    c.Valor++
}

func main() {
    c := Contador{}
    c.Incrementar()
    fmt.Println(c.Valor) // 1
}
```

- **Nota:** Quando você chama um método em Go, se a instância não for um ponteiro mas o método requer um ponteiro, Go automaticamente faz a conversão.

## Composição

### Composição Implícita

Go não tem herança como em linguagens OO clássicas. Em vez disso, você pode usar composição para construir tipos complexos. Composição em Go é feita incluindo uma struct dentro de outra.

```go
package main

import "fmt"

type Endereco struct {
    Rua    string
    Numero int
}

type Pessoa struct {
    Nome     string
    Idade    int
    Endereco // Composição: Pessoa "contém" um Endereço
}

func main() {
    p := Pessoa{
        Nome:  "Alice",
        Idade: 30,
        Endereco: Endereco{
            Rua:    "Rua das Flores",
            Numero: 123,
        },
    }
    
    fmt.Println(p.Nome)       // Alice
    fmt.Println(p.Rua)        // Rua das Flores (acesso direto ao campo da struct Endereco)
    fmt.Println(p.Endereco)   // {Rua das Flores 123}
}
```

### Composição Explícita

Você também pode compor structs explicitamente, dando nomes aos campos.

```go
package main

import "fmt"

type Endereco struct {
    Rua    string
    Numero int
}

type Pessoa struct {
    Nome    string
    Idade   int
    Casa    Endereco
    Trabalho Endereco
}

func main() {
    p := Pessoa{
        Nome:  "Bob",
        Idade: 25,
        Casa: Endereco{
            Rua:    "Rua A",
            Numero: 10,
        },
        Trabalho: Endereco{
            Rua:    "Avenida B",
            Numero: 20,
        },
    }
    
    fmt.Println(p.Casa.Rua)      // Rua A
    fmt.Println(p.Trabalho.Rua)  // Avenida B
}
```

## Interfaces: Polimorfismo em Go

### Definindo Interfaces

Uma interface em Go é uma coleção de métodos que um tipo deve implementar para "satisfazer" essa interface. Interfaces permitem polimorfismo, onde diferentes tipos podem ser tratados da mesma forma se implementarem a mesma interface.

```go
package main

import "fmt"

type Forma interface {
    Area() float64
}

type Retangulo struct {
    Largura, Altura float64
}

func (r Retangulo) Area() float64 {
    return r.Largura * r.Altura
}

type Circulo struct {
    Raio float64
}

func (c Circulo) Area() float64 {
    return 3.14 * c.Raio * c.Raio
}

func calcularArea(f Forma) {
    fmt.Println("Área:", f.Area())
}

func main() {
    r := Retangulo{10, 5}
    c := Circulo{7}
    
    calcularArea(r) // Área: 50
    calcularArea(c) // Área: 153.86
}
```

#### Interfaces Implícitas

Em Go, a implementação de interfaces é implícita. Um tipo não precisa declarar explicitamente que implementa uma interface; ele apenas precisa ter os métodos exigidos pela interface.

### Interfaces Compostas

Você pode combinar várias interfaces menores em uma maior.

```go
package main

import "fmt"

type Leitor interface {
    Ler() string
}

type Escritor interface {
    Escrever(string)
}

type LeitorEscritor interface {
    Leitor
    Escritor
}

type Arquivo struct {
    conteudo string
}

func (a *Arquivo) Ler() string {
    return a.conteudo
}

func (a *Arquivo) Escrever(texto string) {
    a.conteudo = texto
}

func main() {
    a := &Arquivo{}
    var leEscr LeitorEscritor = a
    
    leEscr.Escrever("Texto em Go")
    fmt.Println(leEscr.Ler()) // Texto em Go
}
```

### Tipo Interface Vazia

A interface vazia (`interface{}`) pode representar qualquer tipo em Go. Isso é útil para funções que precisam aceitar diferentes tipos.

```go
package main

import "fmt"

func imprimirQualquerCoisa(i interface{}) {
    fmt.Println(i)
}

func main() {
    imprimirQualquerCoisa(42)
    imprimirQualquerCoisa("Go é incrível")
    imprimirQualquerCoisa(3.14)
}
```

- **Nota:** Embora a interface vazia seja poderosa, seu uso deve ser ponderado para evitar perda de tipagem e segurança.


## Encapsulamento e Visibilidade

### Visibilidade de Campos e Métodos

Em Go, a visibilidade é determinada pela capitalização dos nomes:
- Nomes que começam com letra maiúscula são exportados (públicos) e podem ser acessados por outros pacotes.
- Nomes que começam com letra minúscula são não exportados (privados) e só podem ser acessados dentro do mesmo pacote.

```go
package main

import "fmt"

type Pessoa struct {
    Nome  string // Público
    idade int    // Privado
}

func (p *Pessoa) SetIdade(novaIdade int) {
    p.idade = novaIdade
}

func (p *Pessoa) GetIdade() int {
    return p.idade
}

func main() {
    p := Pessoa{Nome: "Alice"}
    p.SetIdade(30)
    fmt.Println(p.Nome)    // Alice
    fmt.Println(p.GetIdade()) // 30
}
```

### Construtores

Go não tem construtores como em outras linguagens OO, mas você pode criar funções que retornam instâncias de structs.

```go
package main

import "fmt"

type Pessoa struct {
    Nome  string
    Idade int
}

func NovaPessoa(nome string, idade int) *Pessoa {
    return &Pessoa{Nome

: nome, Idade: idade}
}

func main() {
    p := NovaPessoa("Bob", 25)
    fmt.Println(p.Nome) // Bob
    fmt.Println(p.Idade) // 25
}
```