# Leitura e Escrita de Arquivos de Texto em Go

Manipular arquivos de texto é uma tarefa comum em muitas aplicações. Em Go, a leitura e escrita de arquivos é simples e direta, graças ao pacote `os` e outros pacotes complementares como `bufio` e `io/ioutil`. 

## Abrindo Arquivos

### Abrindo um Arquivo para Leitura

Para abrir um arquivo para leitura, você pode usar a função `os.Open`. Ela retorna um ponteiro para o arquivo (`*os.File`) e um erro caso algo dê errado.

```go
package main

import (
    "fmt"
    "os"
)

func main() {
    file, err := os.Open("arquivo.txt")
    if err != nil {
        fmt.Println("Erro ao abrir o arquivo:", err)
        return
    }
    defer file.Close()
    // O arquivo está aberto e pronto para ser lido
}
```

### Abrindo um Arquivo para Escrita

Para abrir um arquivo para escrita, ou criar um novo arquivo se ele não existir, você pode usar `os.Create` ou `os.OpenFile` com as permissões apropriadas.

```go
package main

import (
    "fmt"
    "os"
)

func main() {
    file, err := os.Create("novo_arquivo.txt")
    if err != nil {
        fmt.Println("Erro ao criar o arquivo:", err)
        return
    }
    defer file.Close()
    // O arquivo está aberto e pronto para escrita
}
```

Se você deseja abrir um arquivo existente para escrita sem sobrescrevê-lo, use `os.OpenFile`:

```go
package main

import (
    "fmt"
    "os"
)

func main() {
    file, err := os.OpenFile("arquivo_existente.txt", os.O_RDWR|os.O_APPEND, 0666)
    if err != nil {
        fmt.Println("Erro ao abrir o arquivo:", err)
        return
    }
    defer file.Close()
    // O arquivo está aberto para leitura e escrita
}
```

- **Nota:** Os modos de abertura comuns são `os.O_RDONLY` (somente leitura), `os.O_WRONLY` (somente escrita), `os.O_RDWR` (leitura e escrita), `os.O_APPEND` (adicionar ao final do arquivo), e `os.O_CREATE`(criar o arquivo caso este não exista).


## Leitura de Arquivos

### Lendo Todo o Conteúdo de um Arquivo

Para ler todo o conteúdo de um arquivo de uma só vez, você pode usar `ioutil.ReadFile`, que retorna o conteúdo do arquivo como um slice de bytes.

```go
package main

import (
    "fmt"
    "io/ioutil"
)

func main() {
    conteudo, err := ioutil.ReadFile("arquivo.txt")
    if err != nil {
        fmt.Println("Erro ao ler o arquivo:", err)
        return
    }
    fmt.Println(string(conteudo)) // Converte o slice de bytes para string e imprime
}
```

### Lendo Arquivos Linha por Linha

Para ler um arquivo linha por linha, o pacote `bufio` é ideal. Você cria um scanner que lê cada linha do arquivo.

```go
package main

import (
    "bufio"
    "fmt"
    "os"
)

func main() {
    file, err := os.Open("arquivo.txt")
    if err != nil {
        fmt.Println("Erro ao abrir o arquivo:", err)
        return
    }
    defer file.Close()

    scanner := bufio.NewScanner(file)
    for scanner.Scan() {
        linha := scanner.Text()
        fmt.Println(linha)
    }

    if err := scanner.Err(); err != nil {
        fmt.Println("Erro ao ler o arquivo:", err)
    }
}
```

Outra forma de ler um arquivo linha por linha é através da função abaixo:

```go
func readFromFile(path string) []string {
	var lines []string
	file, error := os.Open(path)
	if error != nil {
		fmt.Println("File Error:", error)
		return lines
	}
	reader := bufio.NewReader(file)
	for {
		line, error := reader.ReadString('\n')
		line = strings.TrimSpace(line)
		lines = append(lines, line)
		if error == io.EOF {
			break
		}
	}
	file.Close()
	return lines
}
```


## Escrita em Arquivos

### Escrevendo em um Arquivo de uma Só Vez

Para escrever uma string ou dados em um arquivo de uma só vez, você pode usar `ioutil.WriteFile`.

```go
package main

import (
    "io/ioutil"
    "fmt"
)

func main() {
    conteudo := []byte("Este é o conteúdo a ser escrito no arquivo.")
    err := ioutil.WriteFile("arquivo.txt", conteudo, 0644)
    if err != nil {
        fmt.Println("Erro ao escrever no arquivo:", err)
    }
}
```

### Escrevendo em um Arquivo Linha por Linha

Para adicionar conteúdo ao final de um arquivo ou escrever linha por linha, você pode usar `bufio.Writer`.

```go
package main

import (
    "bufio"
    "fmt"
    "os"
)

func main() {
    file, err := os.OpenFile("arquivo.txt", os.O_WRONLY|os.O_APPEND, 0644)
    if err != nil {
        fmt.Println("Erro ao abrir o arquivo:", err)
        return
    }
    defer file.Close()

    writer := bufio.NewWriter(file)
    _, err = writer.WriteString("Adicionando uma nova linha ao arquivo.\n")
    if err != nil {
        fmt.Println("Erro ao escrever no arquivo:", err)
    }
    writer.Flush() // Garante que todos os dados sejam realmente escritos
}
```

## Manipulação de Arquivos

### Renomeando e Movendo Arquivos

Você pode renomear ou mover arquivos usando `os.Rename`.

```go
package main

import (
    "fmt"
    "os"
)

func main() {
    err := os.Rename("arquivo.txt", "novo_nome.txt")
    if err != nil {
        fmt.Println("Erro ao renomear o arquivo:", err)
    }
}
```

### Excluindo Arquivos

Para excluir um arquivo, use `os.Remove`.

```go
package main

import (
    "fmt"
    "os"
)

func main() {
    err := os.Remove("arquivo.txt")
    if err != nil {
        fmt.Println("Erro ao excluir o arquivo:", err)
    }
}
```

### Verificando se um Arquivo Existe

Você pode verificar se um arquivo existe tentando abri-lo e verificando o erro retornado.

```go
package main

import (
    "fmt"
    "os"
)

func arquivoExiste(nome string) bool {
    _, err := os.Stat(nome)
    return !os.IsNotExist(err)
}

func main() {
    if arquivoExiste("arquivo.txt") {
        fmt.Println("O arquivo existe.")
    } else {
        fmt.Println("O arquivo não existe.")
    }
}
```

## Considerações de Segurança

### Manipulação de Permissões de Arquivos

Ao criar ou abrir arquivos, é importante considerar as permissões de arquivo que você está configurando. Em Go, você pode definir permissões usando um valor octal ao criar arquivos.

```go
package main

import (
    "os"
)

func main() {
    file, err := os.OpenFile("arquivo_secreto.txt", os.O_CREATE|os.O_WRONLY, 0600)
    if err != nil {
        panic(err)
    }
    defer file.Close()
}
```

- **Nota:** As permissões `0600` permitem leitura e escrita apenas pelo proprietário do arquivo.
