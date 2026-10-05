# Requisitos Funcionais — nassauTickets

Agentes: **AS** (Sistema), **AA** (Atendente), **AC** (Cliente).
Prioridade: **E** = essencial, **I** = importante, **D** = desejável.
As regras citadas (RNxx) estão em [regras-de-negocio.md](regras-de-negocio.md).

## 1. Totem (AC)

| ID | Requisito | Prior. |
|----|-----------|:------:|
| RF01 | O totem deve permitir ao cliente, sem identificação, escolher o tipo de senha: SP, SG ou SE. | E |
| RF02 | O sistema deve gerar a senha no formato `YYMMDD-PPSQ` (RN02) e exibi-la ao cliente logo após a emissão. | E |
| RF03 | O totem deve exibir o comprovante com número, tipo e data/hora da emissão, com opção de impressão. | I |
| RF04 | O sistema deve recusar emissões fora do expediente (7h às 17h) e informar o motivo no totem. | E |
| RF05 | Toda senha emitida deve entrar na fila do seu tipo, passando de `EMITIDA` para `AGUARDANDO`. | E |

## 2. Painel de chamadas

| ID | Requisito | Prior. |
|----|-----------|:------:|
| RF06 | O painel deve exibir as 5 últimas senhas chamadas, com número, tipo e guichê, destacando a mais recente. | E |
| RF07 | O painel não deve exibir a próxima senha da fila (RN14). | E |
| RF08 | O painel deve se atualizar automaticamente, sem ação do usuário (meta: até 3 s). | E |
| RF09 | A cada chamada, o painel deve reproduzir áudio informando a prioridade, o sequencial da senha e o guichê. | E |
| RF10 | Em "Chamar Novamente", o painel deve exibir o indicador "Última chamada" e repetir o áudio precedido por "Última chamada". | E |

## 3. Atendente (AA)

| ID | Requisito | Prior. |
|----|-----------|:------:|
| RF11 | O atendente deve poder **chamar o próximo**: o sistema escolhe a senha pela regra de intercalação (RN05), associa ao guichê do atendente e muda o estado de `AGUARDANDO` para `CHAMADA`. | E |
| RF12 | Se dois ou mais atendentes chamarem ao mesmo tempo, cada um deve receber uma senha diferente; a mesma senha nunca pode ir para dois guichês. | E |
| RF13 | O atendente deve poder **iniciar o atendimento** (`CHAMADA` ou `CHAMADA_NOVAMENTE` → `EM_ATENDIMENTO`), registrando o horário. | E |
| RF14 | O atendente deve poder **finalizar o atendimento** (`EM_ATENDIMENTO` → `ATENDIDA`), registrando o horário e liberando o guichê. | E |
| RF15 | O atendente deve poder **chamar novamente** a senha (`CHAMADA` → `CHAMADA_NOVAMENTE`), no máximo uma vez por senha. | E |
| RF16 | Após duas chamadas sem comparecimento, a senha deve passar a `NAO_COMPARECEU` e o sistema deve seguir para a próxima prioridade (RN08). | E |
| RF17 | O atendente deve informar o guichê ao iniciar a sessão. Cada guichê pode ter apenas um atendente ativo e uma senha ativa por vez (RN07). | E |
| RF18 | A tela do atendente deve mostrar a senha atual, seu estado, o número de chamadas realizadas e a quantidade de senhas aguardando por tipo. | I |
| RF19 | No fim do expediente, o sistema deve bloquear novas chamadas, permitir concluir os atendimentos em andamento e descartar as senhas que sobraram na fila (RN12 e RN13). | E |

## 4. Relatórios (gestor)

| ID | Requisito | Prior. |
|----|-----------|:------:|
| RF20 | **Relatório diário** com: total de senhas emitidas, total de atendidas, emitidas por prioridade e atendidas por prioridade. | E |
| RF21 | **Relatório mensal** com os mesmos indicadores do diário, consolidados no mês. | E |
| RF22 | **Relatório detalhado das senhas**: número, tipo, data/hora da emissão, data/hora do atendimento e guichê. Para senhas não atendidas, os campos de atendimento ficam em branco. | E |
| RF23 | **Relatório de tempo médio de atendimento (TM)** por tipo de senha, calculado do início ao fim de cada atendimento. | E |
| RF24 | **Relatório de auditoria**: atendente, guichê, senha, horário da 1ª chamada, horário da 2ª chamada (se houver), início e fim do atendimento. | E |
| RF25 | Os relatórios devem permitir filtro por data ou mês e exportação em CSV. | D |
| RF26 | **Acompanhamento de desempenho**: TM por tipo, tempo médio de espera, senhas por hora, taxa de não comparecimento (referência de 5%) e produtividade por guichê e atendente. | I |

## 5. Login e gestão

| ID | Requisito | Prior. |
|----|-----------|:------:|
| RF27 | O sistema deve ter login (usuário e senha) para os atendentes. | E |
| RF28 | Um único atendente deve ter o perfil adicional de **gestor**, com acesso a cadastros e relatórios. | E |
| RF29 | O gestor deve cadastrar, editar e inativar atendentes. | E |
| RF30 | O gestor deve cadastrar, editar e inativar guichês. | E |
| RF31 | O sistema deve permitir logout manual e encerrar a sessão por inatividade. | I |
| RF32 | O totem e o painel devem funcionar sem login; o cliente interage de forma anônima apenas com o totem. | E |

## 6. Sistema (AS)

| ID | Requisito | Prior. |
|----|-----------|:------:|
| RF33 | Cada senha deve seguir a máquina de estados `EMITIDA → AGUARDANDO → CHAMADA → CHAMADA_NOVAMENTE → EM_ATENDIMENTO → ATENDIDA`, com a saída alternativa para `NAO_COMPARECEU`. Transições inválidas devem ser rejeitadas (RN16). | E |
| RF34 | Cada mudança de estado deve ser registrada com data/hora, atendente e guichê, formando o histórico usado na auditoria. | E |
| RF35 | O sequencial da senha deve ser contado por tipo e reiniciado a cada dia (RN03). | E |
| RF36 | O sistema deve ter um modo de simulação, que gere atendimentos com os tempos médios da RN10 e a taxa de 5% de não comparecimento, para demonstração e testes. | D |
