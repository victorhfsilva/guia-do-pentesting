# Iniciando no Golang

## Instalação do Go

### a. Instalação no Windows:

1. **Download:**
   - Acesse [go.dev/dl](https://go.dev/dl/) e baixe o instalador adequado para o Windows.
   
2. **Instalação:**
   - Execute o instalador baixado e siga as instruções.
   - Verifique a instalação abrindo o `Prompt de Comando` e digitando:
     ```bash
     go version
     ```

### b. Instalação no macOS:

1. **Download:**
   - Acesse [go.dev/dl](https://go.dev/dl/) e baixe o instalador adequado para o macOS.

2. **Instalação:**
   - Execute o instalador e siga as instruções.
   - Verifique a instalação abrindo o `Terminal` e digitando:
     ```bash
     go version
     ```

3. **Alternativa via Homebrew:**
   - Instale o Go com:
     ```bash
     brew install go
     ```

### c. Instalação no Linux:

1. **Download e Extração:**
   - No terminal, execute:
     ```bash
     wget https://go.dev/dl/go1.20.7.linux-amd64.tar.gz
     sudo tar -C /usr/local -xzf go1.20.7.linux-amd64.tar.gz
     ```
   
2. **Configuração da variável de ambiente:**
   - Adicione o Go ao seu `PATH`:
     ```bash
     echo "export PATH=$PATH:/usr/local/go/bin" >> ~/.profile
     source ~/.profile
     ```
   
3. **Verificação:**
   - Verifique a instalação com:
     ```bash
     go version
     ```


## Configuração do Workspace

### a. Estrutura de Diretórios

O workspace do Go é composto por três diretórios principais: `src`, `pkg` e `bin`.

- **`src`**: Onde o código-fonte reside.
- **`pkg`**: Onde bibliotecas compiladas são armazenadas.
- **`bin`**: Onde os executáveis são colocados.

### b. Configuração do `GOPATH` e `GOROOT`

1. **`GOPATH`:** Define a raiz do seu workspace.
   - No Linux/macOS:
     ```bash
     export GOPATH=$HOME/go
     ```
   - No Windows:
     - Defina a variável de ambiente `GOPATH` para o diretório desejado.

2. **`GOROOT`:** Aponta para o diretório onde o Go foi instalado.
   - Normalmente, o `GOROOT` é configurado automaticamente durante a instalação.

3. **Verificação:**
   - Para confirmar, execute:
     ```bash
     go env
     ```


## Criando e Compilando um Arquivo Go

### a. Criando um Programa Simples

1. Navegue até o diretório `src` do seu `GOPATH`.
   ```bash
   cd $GOPATH/src
   ```
   
2. Crie um novo diretório para o seu projeto:
   ```bash
   mkdir hello
   cd hello
   ```

3. Crie um arquivo `main.go` com o seguinte conteúdo:
   ```go
   package main

   import "fmt"

   func main() {
       fmt.Println("Hello, World!")
   }
   ```

### Compilação e Execução

1. **Compilar o Programa:**
   - No terminal, dentro do diretório do projeto, execute:
     ```bash
     go build
     ```
   - Isso criará um executável chamado `hello` (ou `hello.exe` no Windows).

2. **Executar o Programa:**
   - Execute o programa compilado:
     ```bash
     ./hello
     ```
   - Você verá a saída:
     ```
     Hello, World!
     ```

---

## Executando Arquivos Go sem Compilação Manual

1. Para rodar diretamente um arquivo Go sem a necessidade de compilar manualmente, use:
   
```bash
go run main.go
```

2. Isso compila e executa o arquivo em um único passo.

## Criando um módulo Go

```bash
go mod init bank
```

Isso cria um arquivo go.mod com o nome do módulo bank. Certifique-se de que o nome do módulo no go.mod corresponda ao caminho que você está usando para importar os pacotes.

```go
import (
   "bank/utils"
)
```