# Terê Verde Online — MVP (FastAPI + SQLite + React)

## Integrantes da equipe: 

- Pedro Henrique Pinheiro Mendes Huck

## Escolha da situação problema 
Plataforma de Ecoturismo do Circuito Terê Verde (Teresópolis)

## Descrição sucinta do MVP 
Terê Verde Online — plataforma web de ecoturismo do Circuito Terê Verde (Teresópolis). O público navega por atrações e eventos da região com busca, filtros e páginas de detalhe; um administrador autenticado cadastra, edita e controla a disponibilidade desses itens. Entregue em pacote local FastAPI + SQLite + React. Sem pagamentos, IA ou redes sociais — MVP de exibição e gestão de conteúdo com dados fictícios para edição. 
---

## Como rodar o projeto (passo a passo)

### O que instalar (apenas uma vez)

1. **Python 3.12 ou mais novo** — baixe em [python.org/downloads](https://www.python.org/downloads/)
   - ⚠️ Antes de clicar em "Install Now", marque a caixa **"Add python.exe to PATH"**
2. **Node.js (versão LTS)** — baixe em [nodejs.org](https://nodejs.org/)
3. **VS Code** — baixe em [code.visualstudio.com](https://code.visualstudio.com/) (opcional, mas recomendado)
   - Extensão sugerida: **Python** (Microsoft)

Depois de instalar, **feche e reabra** o VS Code para que ele encontre os novos programas.

### Passo 1 — Abrir o projeto

- Extraia o ZIP em uma pasta do seu computador
- No VS Code: **File → Open Folder** e selecione a pasta extraída

### Passo 2 — Iniciar o backend (terminal 1)

Abra um terminal no VS Code (menu **Terminal → New Terminal**) e rode, uma linha por vez:

**Windows (PowerShell):**

```powershell
cd backend
py -m venv .venv
.\.venv\Scripts\activate
python -m pip install --upgrade pip
pip install -r requirements.txt
python -m uvicorn app.main:app --reload --port 8000
```

**Linux / macOS:**

```bash
cd backend
python3 -m venv .venv
source .venv/bin/activate
python -m pip install --upgrade pip
pip install -r requirements.txt
python -m uvicorn app.main:app --reload --port 8000
```

Quando aparecer **`INFO: Application startup complete`**, o backend está no ar:

- API interativa (Swagger): **http://localhost:8000/docs**
- Na primeira execução é criado o banco `tereverde.db` e o admin padrão **admin@tereverde.com / admin123** (troque em produção via variáveis `ADMIN_EMAIL`/`ADMIN_PASSWORD`).
- Variáveis opcionais em `.env.example` (`SECRET_KEY` deve ser trocada em produção).

> ⚠️ **Não feche esse terminal** enquanto estiver usando o site.

### Passo 3 — Iniciar o front-end (terminal 2)

Clique no ícone **`+`** na barra de terminais para abrir um segundo terminal (o backend continua rodando no primeiro) e rode:

```powershell
cd frontend
npm install
npm run dev
```

- Se o PowerShell bloquear o `npm`, rode `Set-ExecutionPolicy -Scope CurrentUser -ExecutionPolicy RemoteSigned`, responda `S`, feche e reabra o terminal — ou use `npm.cmd install` e `npm.cmd run dev`
- Abra no navegador: **http://localhost:5173**

### Passo 4 — Entrar no painel administrativo

- No site, vá em **Login** e entre com:
  - **E-mail:** `admin@tereverde.com`
  - **Senha:** `admin123`
- O painel permite cadastrar, editar, excluir e alternar a disponibilidade de atrações e eventos

### Passo 5 — Encerrar

- Pressione **Ctrl+C** em cada terminal (backend e front-end)
- Para rodar de novo depois: abra a pasta no VS Code, um terminal para o backend (Passo 2, a partir do `activate`) e outro para o front (Passo 3, `npm run dev`). **Não é preciso** repetir `pip install` nem `npm install` — isso é feito uma vez só.

---

## Resumo rápido (dia a dia)

| Terminal | Comando | Resultado |
|---|---|---|
| 1 | `cd backend` → `.\.venv\Scripts\activate` → `python -m uvicorn app.main:app --reload --port 8000` | API em http://localhost:8000/docs |
| 2 | `cd frontend` → `npm run dev` | Site em http://localhost:5173 |

---

 

## Estrutura

```
backend/
  app/
    core/       config.py, database.py (SQLite/SQLModel), security.py (JWT + bcrypt)
    models/     User, Atracao, Evento (tabelas)
    schemas/    Schemas Pydantic de entrada/saída
    routers/    auth.py, atracoes.py, eventos.py
    services/   seed.py (admin padrão + dados de exemplo)
    main.py     App FastAPI, CORS, startup
frontend/
  src/
    services/api.js        Axios + interceptor JWT
    context/AuthContext    Login/logout, token no localStorage
    components/            Header, Cards, FormModal
    pages/                 Home, Explorar, Detalhe, Login, Admin
```

## Endpoints

| Método | Rota | Acesso |
|---|---|---|
| POST | /api/auth/login | Público |
| GET | /api/atracoes?tipo=&disponivel= | Público |
| GET | /api/atracoes/{id} | Público |
| POST/PUT/DELETE | /api/atracoes[/{id}] | Admin (JWT) |
| PATCH | /api/atracoes/{id}/disponibilidade | Admin (JWT) |
| GET | /api/eventos?disponivel= | Público |
| GET | /api/eventos/{id} | Público |
| POST/PUT/DELETE | /api/eventos[/{id}] | Admin (JWT) |
| PATCH | /api/eventos/{id}/disponibilidade | Admin (JWT) |

O PATCH de disponibilidade alterna o status atual; opcionalmente aceita `{"disponivel": true|false}`.
