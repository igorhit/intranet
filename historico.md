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

*(aguardando resposta)*
