# TaskFlow Flask V1 Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Entregar um gerenciador de tarefas web, sem contas de usuário, com operações completas, filtros, persistência, testes e documentação.

**Architecture:** Uma aplicação Flask com fábrica de aplicação, um módulo `tasks` e persistência SQLite via SQLAlchemy. Rotas recebem formulários, validam dados, alteram o modelo `Task` e renderizam páginas Jinja; testes usam uma base SQLite temporária.

**Tech Stack:** Python 3.11+, Flask, Flask-SQLAlchemy, SQLite, pytest, HTML, CSS, Git e GitHub.

**Spec:** `docs/superpowers/specs/2026-10-15-taskflow-flask-design.md`

## Global Constraints

- A V1 não terá cadastro, login nem separação por usuário.
- O código usa nomes em inglês; textos apresentados ao usuário ficam em português.
- Status aceitos: `pending` e `completed`.
- Prioridades aceitas: `low`, `medium` e `high`.
- `title` é obrigatório e terá no máximo 120 caracteres.
- Cada tarefa contém `title`, `description`, `status`, `priority`, `category`, `due_date` e `created_at`.
- Cada tarefa deve ser implementada em branch própria e integrada por pull request.

Branches planejadas, na ordem: `chore/project-setup`, `feature/task-model`, `feature/create-list-tasks`, `feature/manage-tasks`, `feature/task-validation`, `feature/task-filters`, `style/responsive-interface` e `docs/project-guide`. Antes de cada tarefa, criar a issue correspondente e a branch a partir da `main` atualizada.

## Review Focus

- Título vazio ou somente com espaços deve ser recusado sem criar registro — coberto na Tarefa 3.
- Status e prioridade desconhecidos devem ser recusados — coberto na Tarefa 5.
- Prazo com formato inválido deve exibir erro e preservar os dados digitados — coberto na Tarefa 5.
- Edição, alternância ou exclusão de ID inexistente deve retornar HTTP 404 — coberto na Tarefa 4.
- Filtros combinados sem resultado devem renderizar uma lista vazia sem erro — coberto na Tarefa 6.

---

## Mapa de arquivos

- `pyproject.toml`: dependências e configuração do pytest.
- `.gitignore`: artefatos locais que não entram no Git.
- `run.py`: ponto de entrada local.
- `taskflow/__init__.py`: fábrica `create_app` e registro do módulo de tarefas.
- `taskflow/extensions.py`: instância compartilhada de `SQLAlchemy`.
- `taskflow/tasks/models.py`: modelo persistente `Task`.
- `taskflow/tasks/validation.py`: conversão e validação dos formulários.
- `taskflow/tasks/routes.py`: listagem e operações web sobre tarefas.
- `taskflow/templates/`: páginas Jinja.
- `taskflow/static/css/styles.css`: aparência gerada com auxílio de IA.
- `tests/`: fixtures e testes automatizados.

### Task 1: Base executável e ambiente de testes — Pessoa A

**Files:**
- Create: `pyproject.toml`
- Create: `.gitignore`
- Create: `run.py`
- Create: `taskflow/__init__.py`
- Create: `taskflow/extensions.py`
- Create: `tests/conftest.py`
- Create: `tests/test_app.py`

**Interfaces:**
- Consumes: nenhuma.
- Produces: `create_app(test_config: dict | None = None) -> Flask`, fixture `app` e fixture `client`.

- [ ] **Step 1: Escrever o teste de criação da aplicação**

Criar `test_create_app_uses_test_configuration` e verificar `app.config["TESTING"] is True` e a URI SQLite temporária.

- [ ] **Step 2: Executar o teste e confirmar a falha**

Run: `python -m pytest tests/test_app.py -v`
Expected: FAIL porque o pacote `taskflow` ainda não existe.

- [ ] **Step 3: Implementar a base mínima**

Definir `db = SQLAlchemy()` em `extensions.py`; implementar `create_app(test_config)` com configuração SQLite padrão, pasta de instância, `db.init_app(app)` e criação das tabelas. `run.py` importa e executa a aplicação.

- [ ] **Step 4: Executar o teste completo**

Run: `python -m pytest -v`
Expected: PASS.

- [ ] **Step 5: Commit**

Run: `git commit -m "chore: configura a base do projeto Flask"`

### Task 2: Modelo persistente de tarefa — Pessoa A

**Files:**
- Create: `taskflow/tasks/__init__.py`
- Create: `taskflow/tasks/models.py`
- Create: `tests/test_task_model.py`
- Modify: `taskflow/__init__.py`

**Interfaces:**
- Consumes: `db` de `taskflow.extensions`.
- Produces: classe `Task` com método `toggle_status() -> None`.

- [ ] **Step 1: Escrever os testes do modelo**

Testar os valores padrão `pending` e `medium`, a presença automática de `created_at`, a persistência dos sete campos após fechar e reabrir um contexto da aplicação e duas chamadas de `toggle_status()`.

- [ ] **Step 2: Executar e confirmar a falha**

Run: `python -m pytest tests/test_task_model.py -v`
Expected: FAIL porque `Task` não existe.

- [ ] **Step 3: Implementar `Task`**

Criar colunas tipadas para `id`, `title`, `description`, `status`, `priority`, `category`, `due_date` e `created_at`; limitar `title` a 120 e usar horário UTC em `created_at`.

- [ ] **Step 4: Executar os testes**

Run: `python -m pytest -v`
Expected: PASS.

- [ ] **Step 5: Commit**

Run: `git commit -m "feat: adiciona o modelo de tarefa"`

### Task 3: Criação e listagem — Pessoa A

**Files:**
- Create: `taskflow/tasks/routes.py`
- Create: `taskflow/templates/base.html`
- Create: `taskflow/templates/tasks/index.html`
- Create: `tests/test_create_and_list.py`
- Modify: `taskflow/__init__.py`

**Interfaces:**
- Consumes: `Task`, `db` e `create_app`.
- Produces: blueprint `tasks_bp`; rotas `GET /` e `POST /tasks`.

- [ ] **Step 1: Escrever testes de criação e listagem**

Testar página vazia, criação válida com redirecionamento, tarefa exibida após criação e título vazio ou somente com espaços recusado sem inserir registro.

- [ ] **Step 2: Executar e confirmar a falha**

Run: `python -m pytest tests/test_create_and_list.py -v`
Expected: FAIL porque as rotas não existem.

- [ ] **Step 3: Implementar as rotas e páginas mínimas**

Registrar `tasks_bp`; `GET /` consulta tarefas por criação mais recente; `POST /tasks` normaliza o título, cria a tarefa e usa mensagens flash. Manter o HTML funcional e sem estilização avançada nesta tarefa.

- [ ] **Step 4: Executar os testes**

Run: `python -m pytest -v`
Expected: PASS.

- [ ] **Step 5: Commit**

Run: `git commit -m "feat: permite criar e listar tarefas"`

### Task 4: Editar, concluir e excluir — Pessoa B

**Files:**
- Create: `taskflow/templates/tasks/edit.html`
- Create: `tests/test_manage_tasks.py`
- Modify: `taskflow/tasks/routes.py`
- Modify: `taskflow/templates/tasks/index.html`

**Interfaces:**
- Consumes: `Task.toggle_status()`, `tasks_bp` e `db`.
- Produces: rotas `GET|POST /tasks/<int:task_id>/edit`, `POST /tasks/<int:task_id>/toggle` e `POST /tasks/<int:task_id>/delete`.

- [ ] **Step 1: Escrever testes das três operações**

Testar edição válida, alternância nos dois sentidos, exclusão e retorno 404 para ID inexistente em cada operação.

- [ ] **Step 2: Executar e confirmar a falha**

Run: `python -m pytest tests/test_manage_tasks.py -v`
Expected: FAIL porque as rotas não existem.

- [ ] **Step 3: Implementar as operações**

Buscar tarefas com `db.get_or_404`; salvar edição por POST; alternar via `toggle_status`; excluir somente por POST. Adicionar botões e confirmação de exclusão na página.

- [ ] **Step 4: Executar os testes**

Run: `python -m pytest -v`
Expected: PASS.

- [ ] **Step 5: Commit**

Run: `git commit -m "feat: permite gerenciar tarefas existentes"`

### Task 5: Validação completa dos formulários — Pessoa B

**Files:**
- Create: `taskflow/tasks/validation.py`
- Create: `tests/test_validation.py`
- Modify: `taskflow/tasks/routes.py`

**Interfaces:**
- Consumes: dados compatíveis com `Mapping[str, str]`.
- Produces: `validate_task_form(data: Mapping[str, str]) -> tuple[dict[str, object], dict[str, str]]`.

- [ ] **Step 1: Escrever testes de validação**

Testar conversão de prazo ISO válido, título com mais de 120 caracteres, status inválido, prioridade inválida e prazo inválido. Confirmar que erros usam as chaves dos campos e que um formulário com prazo inválido preserva os demais valores digitados.

- [ ] **Step 2: Executar e confirmar a falha**

Run: `python -m pytest tests/test_validation.py -v`
Expected: FAIL porque `validate_task_form` não existe.

- [ ] **Step 3: Implementar e integrar a validação**

Retornar separadamente dados normalizados e erros; usar a função nas rotas de criação e edição; em erro, responder HTTP 400 e renderizar novamente o formulário com os valores enviados.

- [ ] **Step 4: Executar os testes**

Run: `python -m pytest -v`
Expected: PASS.

- [ ] **Step 5: Commit**

Run: `git commit -m "feat: valida os dados das tarefas"`

### Task 6: Busca, filtros e ordenação — Pessoa A

**Files:**
- Create: `tests/test_task_filters.py`
- Modify: `taskflow/tasks/routes.py`
- Modify: `taskflow/templates/tasks/index.html`

**Interfaces:**
- Consumes: parâmetros de query `q`, `status`, `priority`, `category` e `order`.
- Produces: listagem filtrada; ordens `created_desc`, `due_asc` e `priority_desc`.

- [ ] **Step 1: Escrever testes das consultas**

Testar busca parcial por título, cada filtro isolado, combinação sem resultado e as três ordenações com tarefas conhecidas.

- [ ] **Step 2: Executar e confirmar a falha**

Run: `python -m pytest tests/test_task_filters.py -v`
Expected: FAIL porque os parâmetros ainda são ignorados.

- [ ] **Step 3: Implementar consultas e controles**

Compor a consulta apenas com parâmetros reconhecidos, fazer busca de título sem diferenciar maiúsculas de minúsculas, ignorar valores desconhecidos e manter os parâmetros selecionados no formulário. Em `due_asc`, prazos vazios ficam por último; em `priority_desc`, a ordem é `high`, `medium`, `low`.

- [ ] **Step 4: Executar os testes**

Run: `python -m pytest -v`
Expected: PASS.

- [ ] **Step 5: Commit**

Run: `git commit -m "feat: adiciona busca filtros e ordenacao"`

### Task 7: Interface responsiva gerada com IA — Pessoa B com revisão da Pessoa A

**Files:**
- Create: `taskflow/static/css/styles.css`
- Modify: `taskflow/templates/base.html`
- Modify: `taskflow/templates/tasks/index.html`
- Modify: `taskflow/templates/tasks/edit.html`

**Interfaces:**
- Consumes: nomes de campos, URLs e mensagens definidos nas Tarefas 3 a 6.
- Produces: interface responsiva em português, sem alterar contratos das rotas.

- [ ] **Step 1: Registrar uma lista de verificação visual**

Verificar desktop e celular para: formulário, lista vazia, tarefa atrasada, tarefa concluída, mensagens, filtros e confirmação de exclusão.

- [ ] **Step 2: Gerar e revisar HTML/CSS em uma branch própria**

Solicitar à IA somente alterações nos três templates e no CSS; preservar nomes de inputs, métodos HTTP e URLs.

- [ ] **Step 3: Executar testes e verificação manual**

Run: `python -m pytest -v`
Expected: todos os testes passam; a lista visual não apresenta item quebrado.

- [ ] **Step 4: Commit**

Run: `git commit -m "style: cria a interface responsiva do TaskFlow"`

### Task 8: Documentação e preparação para o GitHub — Ambos

**Files:**
- Modify: `README.md`
- Create: `CONTRIBUTING.md`
- Create: `.github/pull_request_template.md`
- Create: `docs/images/.gitkeep`

**Interfaces:**
- Consumes: aplicação final e comandos confirmados nas tarefas anteriores.
- Produces: instruções reproduzíveis de instalação, execução, testes e colaboração.

- [ ] **Step 1: Validar instalação limpa**

Criar um ambiente virtual novo, instalar o projeto e executar os testes seguindo apenas o README.

- [ ] **Step 2: Documentar o projeto**

Adicionar objetivo, funcionalidades, tecnologias, demonstração, instalação Windows, execução, testes, estrutura, autores e evolução com autenticação.

- [ ] **Step 3: Documentar colaboração**

Explicar issues, branches `feature/<descricao>`, commits, pull requests e revisão; criar checklist de PR para testes, documentação e screenshots.

- [ ] **Step 4: Executar verificação final**

Run: `python -m pytest -v`
Expected: todos os testes passam.

- [ ] **Step 5: Commit**

Run: `git commit -m "docs: completa a documentacao do projeto"`
