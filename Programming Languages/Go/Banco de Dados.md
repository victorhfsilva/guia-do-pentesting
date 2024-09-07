# Lidando com Bancos de Dados com Go

Go é uma linguagem poderosa e eficiente para trabalhar com bancos de dados. Usando o pacote `database/sql` juntamente com drivers específicos para cada banco de dados, você pode realizar operações como conectar, consultar, inserir, atualizar e excluir dados de forma robusta. 

## Configuração Inicial

### Importando Pacotes Necessários

Para começar a trabalhar com bancos de dados em Go, você precisará importar o pacote `database/sql` e o driver específico para o banco de dados que você está utilizando. Por exemplo, para trabalhar com MySQL:

```go
import (
    "database/sql"
    "fmt"
    _ "github.com/go-sql-driver/mysql" // Import necessário para o driver MySQL
)
```

### Conectando ao Banco de Dados

A primeira coisa que você precisa fazer é estabelecer uma conexão com o banco de dados. Isso é feito usando a função `sql.Open`.

```go
func conectar() (*sql.DB, error) {
    db, err := sql.Open("mysql", "user:password@tcp(127.0.0.1:3306)/dbname")
    if err != nil {
        return nil, err
    }

    // Verifica se a conexão foi bem-sucedida
    if err := db.Ping(); err != nil {
        return nil, err
    }

    return db, nil
}
```

- **Nota:** Substitua `"user:password@tcp(127.0.0.1:3306)/dbname"` pelas suas credenciais de conexão.

### Fechando a Conexão

É uma boa prática fechar a conexão ao banco de dados quando não for mais necessária.

```go
db, err := conectar()
if err != nil {
    panic(err)
}
defer db.Close()
```

## Executando Queries Simples

### Selecionando Dados (`SELECT`)

Para realizar uma query de seleção, você pode usar `db.Query` para múltiplas linhas ou `db.QueryRow` para uma única linha.

#### Exemplo com `db.Query`

```go
func buscarUsuarios(db *sql.DB) {
    rows, err := db.Query("SELECT id, nome, email FROM usuarios")
    if err != nil {
        fmt.Println("Erro ao buscar usuários:", err)
        return
    }
    defer rows.Close()

    for rows.Next() {
        var id int
        var nome, email string
        err := rows.Scan(&id, &nome, &email)
        if err != nil {
            fmt.Println("Erro ao ler linha:", err)
            return
        }
        fmt.Printf("ID: %d, Nome: %s, Email: %s\n", id, nome, email)
    }

    if err = rows.Err(); err != nil {
        fmt.Println("Erro ao iterar sobre as linhas:", err)
    }
}
```

#### Exemplo com `db.QueryRow`

Use `db.QueryRow` quando espera exatamente uma linha de resultado.

```go
func buscarUsuarioPorID(db *sql.DB, id int) {
    var nome, email string
    err := db.QueryRow("SELECT nome, email FROM usuarios WHERE id = ?", id).Scan(&nome, &email)
    if err != nil {
        if err == sql.ErrNoRows {
            fmt.Println("Nenhum usuário encontrado")
        } else {
            fmt.Println("Erro ao buscar usuário:", err)
        }
        return
    }
    fmt.Printf("Nome: %s, Email: %s\n", nome, email)
}
```

- **Nota:** `?` é um placeholder seguro para argumentos na query. Ele evita injeção de SQL, substituindo-o pelos valores passados na função.


## Inserindo Dados (`INSERT`)

Para inserir dados em uma tabela, use `db.Exec`. Você pode também obter o ID do último registro inserido usando `result.LastInsertId`.

```go
func inserirUsuario(db *sql.DB, nome string, email string) {
    result, err := db.Exec("INSERT INTO usuarios (nome, email) VALUES (?, ?)", nome, email)
    if err != nil {
        fmt.Println("Erro ao inserir usuário:", err)
        return
    }

    id, err := result.LastInsertId()
    if err != nil {
        fmt.Println("Erro ao obter ID do último inserido:", err)
        return
    }

    fmt.Printf("Usuário inserido com ID: %d\n", id)
}
```

## Atualizando Dados (`UPDATE`)

A atualização de dados em uma tabela é feita também com `db.Exec`.

```go
func atualizarUsuario(db *sql.DB, id int, novoNome string) {
    result, err := db.Exec("UPDATE usuarios SET nome = ? WHERE id = ?", novoNome, id)
    if err != nil {
        fmt.Println("Erro ao atualizar usuário:", err)
        return
    }

    linhasAfetadas, err := result.RowsAffected()
    if err != nil {
        fmt.Println("Erro ao obter número de linhas afetadas:", err)
        return
    }

    fmt.Printf("Linhas afetadas: %d\n", linhasAfetadas)
}
```


## Excluindo Dados (`DELETE`)

Excluir dados também é simples usando `db.Exec`.

```go
func excluirUsuario(db *sql.DB, id int) {
    result, err := db.Exec("DELETE FROM usuarios WHERE id = ?", id)
    if err != nil {
        fmt.Println("Erro ao excluir usuário:", err)
        return
    }

    linhasAfetadas, err := result.RowsAffected()
    if err != nil {
        fmt.Println("Erro ao obter número de linhas afetadas:", err)
        return
    }

    fmt.Printf("Linhas afetadas: %d\n", linhasAfetadas)
}
```

## Transações

Go permite a execução de transações com o pacote `sql`. Isso é útil quando você precisa garantir que um conjunto de operações seja executado com sucesso, ou seja, todas ou nenhuma.

### Iniciando uma Transação

Use `db.Begin` para iniciar uma transação.

```go
func transacaoExemplo(db *sql.DB) {
    tx, err := db.Begin()
    if err != nil {
        fmt.Println("Erro ao iniciar transação:", err)
        return
    }

    defer func() {
        if err != nil {
            tx.Rollback()
            fmt.Println("Transação revertida")
        } else {
            err = tx.Commit()
            if err != nil {
                fmt.Println("Erro ao confirmar transação:", err)
            }
        }
    }()

    _, err = tx.Exec("UPDATE contas SET saldo = saldo - 100 WHERE id = 1")
    if err != nil {
        return
    }

    _, err = tx.Exec("UPDATE contas SET saldo = saldo + 100 WHERE id = 2")
    if err != nil {
        return
    }
}
```

### b. Confirmando ou Revertendo uma Transação

- **`tx.Commit()`:** Confirma a transação, aplicando todas as mudanças.
- **`tx.Rollback()`:** Reverte a transação, desfazendo todas as mudanças.

## Preparando Statements

Se você precisar executar a mesma query várias vezes, é eficiente preparar o statement com `db.Prepare`.

```go
func inserirUsuariosEmLote(db *sql.DB, usuarios []string) {
    stmt, err := db.Prepare("INSERT INTO usuarios (nome) VALUES (?)")
    if err != nil {
        fmt.Println("Erro ao preparar statement:", err)
        return
    }
    defer stmt.Close()

    for _, nome := range usuarios {
        _, err := stmt.Exec(nome)
        if err != nil {
            fmt.Println("Erro ao inserir usuário:", err)
        }
    }
}
```


## Tratamento de Erros

Sempre verifique e trate erros ao trabalhar com operações de banco de dados. Isso é crucial para garantir a robustez do seu código.

```go
func exemplo(db *sql.DB) {
    _, err := db.Query("SELECT * FROM usuarios")
    if err != nil {
        if err == sql.ErrNoRows {
            fmt.Println("Nenhum registro encontrado")
        } else {
            fmt.Println("Erro ao executar query:", err)
        }
        return
    }
}
```

## Usando ORM com Go

Embora o pacote `database/sql` seja poderoso, trabalhar diretamente com SQL pode ser tedioso para aplicações complexas. Para facilitar a manipulação de banco de dados, você pode usar um ORM (Object-Relational Mapping), como:

- **GORM:** Um dos ORMs mais populares para Go.
- **SQLBoiler:** Gera código Go a partir do esquema do banco de dados.
- **Ent:** Um ORM focado em tipos.

### Exemplo simples com GORM

```go
import (
    "gorm.io/driver/mysql"
    "gorm.io/gorm"
)

type Usuario struct {
    ID    uint   `gorm:"primaryKey"`
    Nome  string
    Email string
}

func main() {
    dsn := "user:password@tcp(127.0.0.1:3306)/dbname"
    db, err := gorm.Open(mysql.Open(dsn), &gorm.Config{})
    if err != nil {
        panic("Erro ao conectar ao banco de dados")
    }

    db.AutoMigrate(&Usuario{})

    // Inserir um novo usuário
    db.Create(&Usuario{Nome: "Alice", Email: "alice@example.com"})

    // Buscar usuários
    var usuarios []Usuario
    db.Find(&usuarios)
    for _, usuario := range usuarios {
        fmt.Println(usuario.Nome, usuario.Email)
    }
}
```
