# Diário de Desenvolvimento — Intranet de Tickets e Reserva de Salas

> **Instrução permanente para o agente:** Este arquivo deve ser atualizado ao final de **cada interação** com o aluno, sem exceção. Adicione uma nova seção `## Passo X.Y` com subseções `### Agente — Passo X.Y` e `### Aluno — Passo X.Y` (títulos únicos para evitar MD024). Blocos de código sempre com linguagem declarada (MD040). Nunca pule uma interação. Isso vale para qualquer agente (Claude, Codex, etc.) que der continuidade ao projeto.
>
> **Nunca registre** operações de Git (init, commit, push, etc.) no histórico — são irrelevantes para o propósito de aprendizado deste arquivo.

---

## Passo 1.1 — Pré-requisitos

### Agente — Passo 1.1

Para o projeto funcionar, você vai precisar de três ferramentas instaladas na sua máquina.

**`.NET SDK`** — O SDK do .NET é o que permite criar e rodar projetos C#/ASP.NET Core. É diferente do "Runtime": o SDK inclui as ferramentas de linha de comando (`dotnet`), o compilador e os pacotes. Sem ele, você não consegue nem criar o projeto.

Para verificar:

```bash
dotnet --version
```

O ideal é versão 8.x ou superior.

**`Node.js`** — O ambiente de execução JavaScript fora do browser. O frontend React usa o Node para instalar dependências (via npm) e rodar o servidor de desenvolvimento. O Vite e o npm rodam sobre o Node.

Para verificar:

```bash
node --version
npm --version
```

O ideal é Node 18+.

**`Git`** — O controle de versão. Vamos usar para registrar cada etapa do projeto.

Para verificar:

```bash
git --version
```

Rode esses três comandos no terminal e me passa os resultados.

### Aluno — Passo 1.1

```text
dotnet --version  →  10.0.107
node --version    →  v24.14.0
npm --version     →  11.9.0
git --version     →  git version 2.51.2.windows.1
```

---

## Passo 1.2 — Criar o projeto ASP.NET Core

### Agente — Passo 1.2

Tudo instalado e nas versões mais recentes: .NET 10, Node 24 + npm 11, Git 2.51.

**O que é um projeto ASP.NET Core?** — É o framework da Microsoft para criar APIs com C#. Quando você roda o comando de criação, o .NET gera uma estrutura de pastas e arquivos prontos. Você não precisa configurar nada do zero.

**Por que o template `webapi`?** — O .NET tem vários templates (`mvc`, `blazor`, `webapi`, etc.). Usamos `webapi` porque queremos apenas uma API REST que retorna JSON. O frontend React será separado e vai consumir essa API — não precisamos de HTML gerado pelo backend.

Estrutura de pastas planejada:

```text
PPP/
  backend/     <- projeto ASP.NET Core
  frontend/    <- projeto React (mais pra frente)
```

Comando para criar o projeto:

```bash
dotnet new webapi -n backend --use-controllers
```

- `dotnet new` — cria um novo projeto
- `webapi` — o template de API REST
- `-n backend` — nome do projeto e da pasta criada
- `--use-controllers` — usa Controllers em vez de Minimal APIs, mais organizado para nosso tamanho de projeto

### Aluno — Passo 1.2

```text
dotnet new webapi -n backend --use-controllers

O modelo "API Web do ASP.NET Core" foi criado com êxito.

Processando ações pós-criação...
Restaurando C:\Users\User\Desktop\Coding\XPTO\PPP\backend\backend.csproj:
A restauração foi bem-sucedida.
```

---

## Passo 1.3 — Entender a estrutura de pastas gerada

### Agente — Passo 1.3

Projeto criado com sucesso. Antes de tocar em qualquer arquivo, você precisa saber o que cada coisa é e por que existe.

Arquivos e pastas gerados:

```text
backend/
  Controllers/
    WeatherForecastController.cs
  Properties/
    launchSettings.json
  backend.csproj
  Program.cs
  WeatherForecast.cs
  appsettings.json
  appsettings.Development.json
```

- `Program.cs` — ponto de entrada da aplicação. É onde ela "nasce": você configura os serviços e define como as requisições HTTP são processadas.
- `backend.csproj` — arquivo de projeto do C#. Funciona como o `package.json` do Node: lista dependências (pacotes NuGet) e configurações. Raramente editado na mão.
- `appsettings.json` — configuração da aplicação (string de conexão com o banco, chave secreta do JWT, etc.).
- `appsettings.Development.json` — sobrescreve o `appsettings.json` quando rodando em modo Development.
- `Controllers/` — pasta dos Controllers. Cada Controller agrupa endpoints relacionados (ex.: `UserController`, `TicketController`, `RoomController`).
- `WeatherForecastController.cs` e `WeatherForecast.cs` — exemplos do template. Serão deletados em breve.
- `Properties/launchSettings.json` — define como o projeto é iniciado localmente (porta, se abre o browser, etc.).

Próxima pergunta: abra o arquivo `backend/Program.cs` no editor e me diz — o que você consegue entender do que está escrito lá? Não precisa entender tudo, me fala o que faz sentido e o que parece confuso.

### Aluno — Passo 1.3

Aluno leu o arquivo e reportou o que entendeu:

- `builder` recebe o retorno de uma função desconhecida
- `builder` usado em mais duas chamadas desconhecidas
- `app` criado a partir de `builder` com função desconhecida
- Condicional que parece verificar ambiente de desenvolvimento
- Algo relacionado a redirecionamento HTTPS
- Algo relacionado a autorização
- Uma linha desconhecida
- Inicio da aplicação

---

## Passo 1.4 — Entender o Program.cs em detalhes

### Agente — Passo 1.4

O `Program.cs` tem duas fases bem distintas: tudo antes do `Build()` é configuração de serviços, tudo depois é configuração do pipeline de requisições.

```csharp
var builder = WebApplication.CreateBuilder(args);
```

Cria o "construtor" da aplicação. Pense nele como uma prancheta onde você declara tudo antes de abrir as portas: quais serviços existem, como a aplicação se comporta. O `args` são argumentos de linha de comando, raramente usados diretamente.

```csharp
builder.Services.AddControllers();
builder.Services.AddOpenApi();
```

Ambas seguem o padrão `builder.Services.AddAlgumaCoisa()` — esse é o sistema de **injeção de dependência** do .NET. Você registra serviços aqui e o framework os disponibiliza para o resto da aplicação automaticamente.

- `AddControllers()` — ativa o sistema de Controllers
- `AddOpenApi()` — registra suporte nativo a OpenAPI (novo no .NET 10, substitui o Swagger de versões anteriores). Gera um documento JSON descrevendo toda a API.

```csharp
var app = builder.Build();
```

Encerra a fase de configuração e constrói a aplicação. A partir daqui você define como as requisições passam pela aplicação.

```csharp
if (app.Environment.IsDevelopment())
{
    app.MapOpenApi();
}
```

Acertou na condicional. `MapOpenApi()` expõe o documento OpenAPI em uma rota — útil para testar a API durante o desenvolvimento. Fica restrito ao ambiente de desenvolvimento propositalmente: não deve ficar exposto em produção.

```csharp
app.UseHttpsRedirection();
```

Acertou. Qualquer requisição HTTP é redirecionada para HTTPS automaticamente.

```csharp
app.UseAuthorization();
```

Acertou. Ativa o sistema que verifica se o usuário tem permissão para acessar cada rota. Sem essa linha, as proteções que vamos criar com JWT não funcionariam.

```csharp
app.MapControllers();
```

Conecta as rotas definidas nos Controllers à aplicação. Sem ela, os Controllers existem mas nunca são chamados — nenhuma requisição chegaria neles.

```csharp
app.Run();
```

Acertou. Inicia o servidor e fica aguardando requisições. É sempre a última linha.

### Aluno — Passo 1.4

Entendeu a explicação. Sem dúvidas adicionais.

---

## Passo 1.5 — Instalar pacotes do Entity Framework

### Agente — Passo 1.5

O .NET usa pacotes NuGet (equivalente ao npm do Node). Três pacotes necessários para o Entity Framework com SQLite:

- `Microsoft.EntityFrameworkCore.Sqlite` — driver que conecta o EF ao SQLite
- `Microsoft.EntityFrameworkCore.Design` — ferramentas de design, necessário para rodar migrations pela linha de comando
- `Microsoft.EntityFrameworkCore.Tools` — habilita os comandos `dotnet ef` para criar e aplicar migrations

```bash
dotnet add package Microsoft.EntityFrameworkCore.Sqlite
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet add package Microsoft.EntityFrameworkCore.Tools
```

Verificação: os três aparecem no `backend.csproj` na versão 10.0.7, alinhada com o .NET 10.

### Aluno — Passo 1.5

Pacotes instalados com sucesso. Verificados no `backend.csproj`.

---

## Passo 1.6 — Dúvida: o que são migrations?

### Agente — Passo 1.6

Migrations são o sistema de controle de versão do banco de dados.

O Git controla as mudanças no código. As migrations controlam as mudanças na estrutura do banco. Cada migration é um arquivo que descreve o que mudou — "crie a tabela Users com essas colunas", "adicione a coluna Status na tabela Tickets", etc.

O banco não sabe nada sobre suas classes C#. Quando você cria uma classe `User`, o banco não cria automaticamente uma tabela `Users`. A migration é o passo que sincroniza os dois mundos.

O fluxo sempre será:

1. Criar ou alterar uma classe C# (entidade)
2. Rodar um comando que gera a migration automaticamente
3. Rodar outro comando que aplica a migration no banco

Motivo para não editar o banco diretamente: outros desenvolvedores não teriam essas mudanças. Com migrations, qualquer pessoa que clonar o projeto e rodar um único comando tem o banco idêntico.

### Aluno — Passo 1.6

Dúvida esclarecida.

---

## Passo 1.7 — Criar pastas Models e Data

### Agente — Passo 1.7

Dentro da pasta `backend`, criar duas pastas: `Models` e `Data`.

- `Models/` — vai guardar as entidades: classes C# que representam as tabelas do banco (User, Ticket, Room, etc.)
- `Data/` — vai guardar o `DbContext`, que é o objeto central do Entity Framework: representa o banco de dados dentro do C# e é por onde todas as operações de leitura e escrita passam

Essa separação é uma convenção de organização — o projeto funcionaria sem ela, mas misturar tudo numa pasta só dificulta a navegação conforme o projeto cresce.

### Aluno — Passo 1.7

Pastas `Models` e `Data` criadas dentro de `backend/`.

---

## Passo 1.8 — Criar a entidade User

### Agente — Passo 1.8

Dentro de `Models/`, criar `User.cs`. A classe representa a tabela de usuários — cada propriedade vira uma coluna no banco.

Propriedades necessárias:

- `Id` (`int`) — chave primária
- `Name` (`string`) — nome do usuário
- `Email` (`string`) — usado no login
- `PasswordHash` (`string`) — nunca guardar senha em texto puro
- `CreatedAt` (`DateTime`) — data de criação do registro

Padrão de propriedade em C#:

```csharp
public Tipo NomeDaPropriedade { get; set; }
```

Erro encontrado após a primeira tentativa: `CS8618` — com `<Nullable>enable</Nullable>` ativo no projeto, o compilador exige explicitação de nulabilidade em `string`. Solução: usar o modificador `required` nas propriedades obrigatórias. `int` e `DateTime` não precisam porque nunca são `null` por padrão.

### Aluno — Passo 1.8

```csharp
namespace backend.Models;

public class User
{
    public int Id { get; set; }
    public required string Name { get; set; }
    public required string Email { get; set; }
    public required string PasswordHash { get; set; }
    public DateTime CreatedAt { get; set; }
}
```

---

## Passo 1.9 — Criar o AppDbContext

### Agente — Passo 1.9

Dentro de `Data/`, criar `AppDbContext.cs`. É a classe central do Entity Framework — toda comunicação com o banco passa por ela.

Três elementos obrigatórios:

1. Herdar de `DbContext` (namespace `Microsoft.EntityFrameworkCore`)
2. Construtor que recebe `DbContextOptions<AppDbContext>` e repassa para a classe pai via `: base(options)`
3. Propriedade `DbSet<User> Users` — representa a tabela de usuários

Dificuldade encontrada: sintaxe de herança, construtor com `base()` e `DbSet`. Resolvido explicando cada parte separadamente.

### Aluno — Passo 1.9

```csharp
using Microsoft.EntityFrameworkCore;
using backend.Models;

namespace backend.Data;

public class AppDbContext : DbContext
{
    public AppDbContext(DbContextOptions<AppDbContext> options) : base(options)
    {
    }
    public DbSet<User> Users { get; set; }
}
```
