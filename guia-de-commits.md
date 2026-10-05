# Guia de Commits — nassauTickets (Fase 1)

Padrão: [Conventional Commits](https://www.conventionalcommits.org/pt-br/) — `tipo(escopo opcional): descrição no imperativo, em minúsculas, até ~72 caracteres`.

| Tipo | Quando usar |
|------|-------------|
| `chore` | Estrutura, configuração, dependências, arquivos de apoio |
| `docs` | README, requisitos, diagramas, mockups |
| `feat` | Nova funcionalidade |
| `fix` | Correção de erro ou de regra |
| `test` | Testes automatizados (tipo padrão do Conventional Commits, além dos quatro principais) |

> Estas mensagens são um **roteiro**. Os commits devem acontecer conforme o trabalho for realmente feito, cada integrante com a própria conta do GitHub (`git config user.name` e `user.email`). Não altere datas de commits.

## 1. Preparação (Scrum Master)

1. No GitHub, crie o repositório `nassauTickets`: **Public**, com **README**, **.gitignore: Node** e **License: MIT**.
2. Em *Settings → Collaborators*, adicione Thiago e Emanuel.
3. Crie a branch `dev` e use-a para todo o desenvolvimento:

```bash
git clone https://github.com/<usuario>/nassauTickets.git
cd nassauTickets
git switch -c dev
git push -u origin dev
```

## 2. Sequência de commits na `dev`

| # | Mensagem | Sugestão de responsável |
|---|----------|-------------------------|
| 1 | `chore: cria estrutura inicial do projeto` (pastas + `.gitkeep`) | Nícolas |
| 2 | `chore: ajusta .gitignore para projetos Node.js` | Nícolas |
| 3 | `docs: adiciona README com visão geral e membros` | Emanuel |
| 4 | `docs: adiciona requisitos funcionais do sistema` | Emanuel |
| 5 | `docs: adiciona requisitos não funcionais` | Emanuel |
| 6 | `docs: adiciona regras de negócio` | Emanuel |
| 7 | `docs: adiciona diagrama de casos de uso` (`docs/models/uml`) | Emanuel |
| 8 | `docs: adiciona modelo entidade-relacionamento` (`docs/mer`) | Emanuel |
| 9 | `docs: adiciona mockups do totem, painel e atendente` | Emanuel |
| 10 | `chore(backend): inicializa projeto Node.js com Express` | Nícolas |
| 11 | `feat(backend): configura pool de conexões com o MySQL` | Nícolas |
| 12 | `feat(backend): cria script de criação do banco` | Nícolas |
| 13 | `feat(backend): adiciona rota de health check` | Thiago |
| 14 | `feat(backend): implementa numeração YYMMDD-PPSQ` | Thiago |
| 15 | `test(backend): adiciona testes da numeração e do expediente` | Thiago |
| 16 | `feat(backend): implementa emissão de senha` | Nícolas |
| 17 | `feat(backend): implementa fila de atendimento com intercalação` | Thiago |
| 18 | `test(backend): adiciona testes da regra de prioridade` | Thiago |
| 19 | `feat(backend): implementa endpoint do painel de chamadas` | Nícolas |
| 20 | `chore(frontend): inicializa projeto React com Vite` | Emanuel |
| 21 | `feat(frontend): cria camada de serviços para consumo da API` | Emanuel |
| 22 | `feat(frontend): cria componente de emissão de senha` | Emanuel |
| 23 | `feat(frontend): implementa painel de chamadas` | Nícolas |
| 24 | `feat(frontend): adiciona áudio das chamadas no painel` | Nícolas |
| 25 | `feat(frontend): cria esqueleto da tela do atendente` | Emanuel |
| 26 | `fix(backend): corrige regra de prioridade com fila vazia` | Thiago |
| 27 | `fix(frontend): trata indisponibilidade do backend no totem e no painel` | Thiago |
| 28 | `docs: atualiza README com instruções de execução` | Emanuel |
| 29 | `docs: atualiza documentação` | Emanuel |

Exemplo de commit com corpo, quando precisar explicar o motivo:

```bash
git commit -m "fix(backend): corrige regra de prioridade com fila vazia" \
           -m "Pula o tipo sem senhas e segue o ciclo SP -> SE -> SG (RN06)."
```

## 3. Merge da `dev` para a `main`

Use `--no-ff` para que o histórico mostre claramente o merge:

```bash
git switch dev && git pull
git switch main && git pull
git merge --no-ff dev -m "chore: integra dev na main (entrega da Fase 1)"
git push origin main
```

Confira no GitHub (*Insights → Network* ou *Commits*) se o commit de merge aparece na `main` e se a `dev` continua existindo.
