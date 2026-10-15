# Especificação — TaskFlow Flask

## Objetivo

Construir um gerenciador de tarefas web para portfólio e aprendizado em dupla. O foco será Python, Flask, banco de dados, testes, Git, GitHub e documentação. O HTML e o CSS serão produzidos com auxílio de IA e explicados apenas no nível necessário para integração e manutenção.

## Escopo da primeira versão

A primeira versão será usada por uma pessoa e não terá cadastro ou login. Ela permitirá:

- criar tarefas;
- listar tarefas;
- editar tarefas;
- marcar tarefas como concluídas ou pendentes;
- excluir tarefas mediante confirmação;
- buscar, filtrar e ordenar tarefas.

Cada tarefa terá título, descrição, status, prioridade, categoria, prazo e data de criação.

## Evolução planejada

Uma segunda versão adicionará cadastro, login e separação das tarefas por usuário. Essa funcionalidade não faz parte da primeira entrega.

## Tecnologias

- Python;
- Flask;
- SQLite;
- SQLAlchemy;
- pytest;
- HTML e CSS;
- Git e GitHub.

## Arquitetura

A aplicação será dividida em partes pequenas:

- configuração e inicialização do Flask;
- modelo de dados da tarefa;
- regras e validações das tarefas;
- rotas web;
- páginas HTML;
- arquivos estáticos de CSS;
- testes automatizados.

As regras de negócio não ficarão misturadas ao HTML. O acesso ao banco será feito por meio do SQLAlchemy.

## Fluxo dos dados

1. O usuário envia uma ação pela página web.
2. Uma rota Flask recebe e valida os dados.
3. A aplicação consulta ou altera a tarefa no banco SQLite.
4. A rota apresenta a página atualizada e uma mensagem de sucesso ou erro.

## Validações e erros

- O título será obrigatório.
- Status e prioridade aceitarão apenas valores conhecidos.
- Prazos inválidos serão recusados com mensagem clara.
- A aplicação tratará tentativas de editar ou excluir tarefas inexistentes.
- Exclusões exigirão confirmação na interface.

## Divisão inicial

### Pessoa A

- configuração inicial do Flask;
- modelo da tarefa e banco de dados;
- criação e listagem de tarefas;
- busca, filtros e ordenação;
- testes dessas funcionalidades.

### Pessoa B

- edição de tarefas;
- mudança entre concluída e pendente;
- exclusão com confirmação;
- validações e mensagens de erro;
- testes dessas funcionalidades;
- primeira versão do guia de uso no README.

### Responsabilidades compartilhadas

- criação e organização das issues;
- revisão dos pull requests;
- integração e testes finais;
- documentação e publicação do projeto.

## Fluxo Git e GitHub

1. Criar uma issue pequena e objetiva.
2. Criar uma branch a partir da `main`.
3. Implementar e testar apenas a issue selecionada.
4. Fazer commits curtos e descritivos.
5. Abrir um pull request.
6. Solicitar a revisão do colega.
7. Corrigir problemas e integrar na `main`.

A branch `main` permanecerá estável. Os dois participantes evitarão alterar simultaneamente os mesmos arquivos.

## Estratégia de testes

- testes unitários para validações e regras;
- testes das principais rotas Flask;
- banco temporário separado para os testes;
- verificação manual das páginas antes de cada pull request.

## Critérios de conclusão da primeira versão

- todas as operações planejadas funcionam pela interface web;
- os dados continuam disponíveis após reiniciar a aplicação;
- entradas inválidas mostram mensagens compreensíveis;
- os testes automatizados passam;
- o README ensina a instalar e executar o projeto;
- o histórico Git contém branches, commits e pull requests revisados pela dupla.

## Forma de colaboração com IA

A IA trabalhará em uma tarefa por vez. Cada resposta deverá informar, de maneira curta: objetivo, conceito essencial, arquivos afetados, implementação, teste e sugestão de commit. A IA não deverá gerar o projeto inteiro nem avançar antes da confirmação de que a etapa atual funciona.
