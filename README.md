# Atividade 4 - Criar o restante do CRUD de projetos, testando-o no Insomnia (SENAI)

Esta atividade consiste na implementação completa das operações **CRUD (Create, Read, Update e Delete)** e busca por ID para a entidade `Projeto` no projeto **Exo.WebApi**, utilizando **.NET 6.0**, **Entity Framework Core** e **SQL Server**, com validação das rotas HTTP através do **Insomnia**.

---

## 📁 Estrutura do Projeto

```plaintext
├── cria-db.sql                          # Script SQL para criação do banco ExoApi e tabelas Projetos/Usuarios
├── Insomnia_ExoApi_Collection.json      # Coleção pronta para importação no Insomnia com todas as requisições CRUD
├── Exo.WebApi/
│   ├── Contexts/
│   │   └── ExoContext.cs                # Contexto do Entity Framework Core (conexão SQL Server)
│   ├── Controllers/
│   │   └── ProjetosController.cs        # Controller REST (GET, GET/{id}, POST, PUT/{id}, DELETE/{id})
│   ├── Models/
│   │   └── Projeto.cs                   # Entidade Projeto (Id, NomeDoProjeto, Area, Status)
│   ├── Repositories/
│   │   └── ProjetoRepository.cs         # Camada Repository com Listar, Cadastrar, BuscarporId, Atualizar e Deletar
│   ├── Properties/
│   │   └── launchSettings.json          # Configurações de execução e portas (https://localhost:7154)
│   ├── appsettings.json                 # Configurações da aplicação
│   ├── Exo.WebApi.csproj                # Arquivo de projeto .NET 6.0 e dependências NuGet
│   └── Program.cs                       # Configuração de DI (ExoContext, ProjetoRepository) e pipeline HTTP
└── README.md
```

---

## 🛠️ Tecnologias Utilizadas

- **C# / .NET 6.0 (ou .NET 7.0)**
- **ASP.NET Core Web API**
- **Entity Framework Core 6.0 (`Microsoft.EntityFrameworkCore.SqlServer`)**
- **SQL Server / SQL Server Management Studio (SSMS)**
- **Insomnia / Postman** (Testes de integração dos endpoints REST)

---

## 🗄️ 1. Configuração do Banco de Dados (SSMS)

1. Abra o **SQL Server Management Studio (SSMS)**.
2. Abra o arquivo [`cria-db.sql`](cria-db.sql) e clique em **Executar** (`F5`).
3. O script criará o banco de dados `ExoApi`, a tabela `Projetos` (com 3 projetos iniciais) e a tabela `Usuarios`.
4. Verifique a string de conexão em [`Exo.WebApi/Contexts/ExoContext.cs`](Exo.WebApi/Contexts/ExoContext.cs) de acordo com sua instância local do SQL Server (ex.: `Server=localhost\SQLEXPRESS;Database=ExoApi;Trusted_Connection=True;`).

---

## 💻 2. Métodos Implementados no CRUD

### `ProjetoRepository.cs`
- **`Listar()`**: retorna `List<Projeto>` com todos os registros (`_context.Projetos.ToList()`).
- **`Cadastrar(Projeto projeto)`**: adiciona um novo projeto (`_context.Projetos.Add(projeto)`) e salva as alterações (`_context.SaveChanges()`).
- **`BuscarporId(int id)`**: busca um projeto pela chave primária (`_context.Projetos.Find(id)`).
- **`Atualizar(int id, Projeto projeto)`**: localiza o projeto existente pelo `id`, atualiza `NomeDoProjeto`, `Area` e `Status`, marca com `_context.Projetos.Update(projetoBuscado)` e executa `_context.SaveChanges()`.
- **`Deletar(int id)`**: localiza o projeto pelo `id`, remove via `_context.Projetos.Remove(projetoBuscado)` e executa `_context.SaveChanges()`.

### `ProjetosController.cs` (`api/projetos`)
| Método HTTP | Rota | Método no Controller | Status de Sucesso | Descrição |
| :--- | :--- | :--- | :---: | :--- |
| `GET` | `/api/projetos` | `Listar()` | `200 OK` | Lista todos os projetos cadastrados |
| `POST` | `/api/projetos` | `Cadastrar(Projeto projeto)` | `201 Created` | Cadastra um novo projeto |
| `GET` | `/api/projetos/{id}` | `BuscarPorId(int id)` | `200 OK` / `404 Not Found` | Busca um projeto específico pelo ID |
| `PUT` | `/api/projetos/{id}` | `Atualizar(int id, Projeto projeto)` | `204 No Content` | Atualiza os dados de um projeto pelo ID |
| `DELETE` | `/api/projetos/{id}` | `Deletar(int id)` | `204 No Content` / `400 Bad Request` | Remove um projeto pelo ID |

---

## 🚀 3. Executando a Aplicação

No terminal, dentro da pasta `Exo.WebApi`:

```bash
cd Exo.WebApi
dotnet restore
dotnet build
dotnet run
```

A aplicação ficará disponível em:
- `https://localhost:7154/api/projetos`
- `http://localhost:5038/api/projetos`

---

## 🧪 4. Roteiro de Testes no Insomnia

Você pode importar diretamente o arquivo [`Insomnia_ExoApi_Collection.json`](Insomnia_ExoApi_Collection.json) no **Insomnia** ou executar manualmente as requisições abaixo:

### 4.1. Listar Projetos (`GET`)
- **URL:** `https://localhost:7154/api/projetos/`
- **Resposta esperada (`200 OK`):** Lista com os projetos iniciais (IDs 1, 2 e 3).

### 4.2. Cadastrar Novo Projeto (`POST`)
- **URL:** `https://localhost:7154/api/projetos/`
- **Body (JSON):**
  ```json
  {
      "nomeDoProjeto": "Projeto D - Senai FIT",
      "area": "Saúde",
      "status": true
  }
  ```
- **Resposta esperada:** `201 Created`

### 4.3. Buscar Projeto por ID (`GET`)
- **URL:** `https://localhost:7154/api/projetos/4`
- **Resposta esperada (`200 OK`):**
  ```json
  {
      "id": 4,
      "nomeDoProjeto": "Projeto D - Senai FIT",
      "area": "Saúde",
      "status": true
  }
  ```

### 4.4. Atualizar Projeto (`PUT`)
- **URL:** `https://localhost:7154/api/projetos/4`
- **Body (JSON):**
  ```json
  {
      "nomeDoProjeto": "Projeto D - Senai Games",
      "area": "Entretenimento",
      "status": false
  }
  ```
- **Resposta esperada:** `204 No Content`

### 4.5. Deletar Projeto (`DELETE`)
- **URL:** `https://localhost:7154/api/projetos/4`
- **Resposta esperada:** `204 No Content`
