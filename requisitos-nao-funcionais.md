# Requisitos Não Funcionais — nassauTickets

## 1. Segurança

| ID | Requisito |
|----|-----------|
| RNF01 | As senhas dos atendentes devem ser armazenadas apenas como hash forte (bcrypt com custo ≥ 12 ou argon2id), nunca em texto puro. |
| RNF02 | A autenticação deve usar token JWT de curta duração (ex.: 15 min) com renovação, enviado em cookie `httpOnly` e `SameSite=Strict` ou no cabeçalho `Authorization`. |
| RNF03 | O acesso deve ser controlado por perfil: relatórios e cadastros só para o gestor; chamada e atendimento só para atendentes autenticados; emissão de senha e painel são públicos. |
| RNF04 | Em produção, todo o tráfego deve usar HTTPS/TLS, com HSTS. |
| RNF05 | Todas as consultas ao banco devem usar *prepared statements* (prevenção de SQL Injection). Entradas devem ser validadas no backend. |
| RNF06 | O backend deve usar cabeçalhos de segurança (`helmet`), CORS restrito às origens do sistema e limite de tamanho do corpo das requisições. |
| RNF07 | Os endpoints públicos (emissão) e o login devem ter limite de requisições por IP, com bloqueio temporário após várias tentativas de login inválidas. |
| RNF08 | Segredos (banco, JWT) devem ficar em variáveis de ambiente, fora do Git. O usuário do banco deve ter privilégios mínimos (SELECT, INSERT, UPDATE, DELETE). |
| RNF09 | Mensagens de erro enviadas ao cliente não devem expor detalhes internos (stack trace, SQL). |

## 2. LGPD (Lei nº 13.709/2018)

| ID | Requisito |
|----|-----------|
| RNF10 | **Minimização:** o totem não coleta nem armazena dados pessoais do cliente. A senha é anônima e não deve ser vinculada a dados de saúde. |
| RNF11 | Os únicos dados pessoais tratados são os dos atendentes (nome e login), com finalidade definida (operação e auditoria) e base legal documentada. |
| RNF12 | Deve existir política de retenção: prazo para dados detalhados de atendimento (sugestão: 12 meses) e descarte ou anonimização depois dele. Indicadores agregados podem ser mantidos. |
| RNF13 | Logs e relatórios não devem conter dados pessoais além do identificador do atendente necessário à auditoria. |
| RNF14 | O sistema deve permitir atender pedidos do titular (acesso, correção e eliminação) sobre o cadastro do atendente. |
| RNF15 | A documentação deve identificar o controlador, a finalidade do tratamento e o canal de contato do encarregado (DPO). |

## 3. Acessibilidade (Lei nº 13.146/2015 — LBI, e-MAG e WCAG 2.1 AA)

| ID | Requisito |
|----|-----------|
| RNF16 | As telas devem seguir o WCAG 2.1 nível AA: contraste mínimo de 4,5:1, texto ampliável até 200% e informação nunca transmitida só por cor. |
| RNF17 | Toda a navegação deve funcionar pelo teclado, com foco visível, ordem lógica e HTML semântico (ARIA apenas quando necessário). |
| RNF18 | O painel deve combinar informação visual e sonora, usar fonte grande legível à distância e região `aria-live="polite"` para leitores de tela. |
| RNF19 | O totem deve ter botões grandes (alvo mínimo de 44 × 44 px), textos curtos, rótulos claros e opção de leitura em voz. A altura física do equipamento deve atender a cadeirantes. |
| RNF20 | As telas devem respeitar `prefers-reduced-motion` e não ter conteúdo que pisque mais de 3 vezes por segundo. |
| RNF21 | As páginas devem declarar `lang="pt-BR"`, associar rótulos aos campos de formulário e identificar erros por texto. |

## 4. Concorrência

| ID | Requisito |
|----|-----------|
| RNF22 | A escolha da próxima senha deve ser atômica: transação MySQL que trava a linha de controle da fila (`SELECT … FOR UPDATE`), de modo que chamadas simultâneas sejam processadas em série e nunca recebam a mesma senha. |
| RNF23 | O sequencial por tipo e dia deve ser gerado com contador travado em transação e garantido por chave única `(data_ref, tipo, sequencia)`. |
| RNF24 | Mudanças de estado devem conferir o estado de origem (`UPDATE … WHERE estado = ?`). Se a condição falhar, a API responde `409 Conflict`. |
| RNF25 | O frontend deve desabilitar o botão enquanto a requisição estiver em andamento, evitando duplo clique. |
| RNF26 | Deve haver teste de concorrência (N chamadas paralelas) comprovando a ausência de senhas duplicadas. |

## 5. Disponibilidade

| ID | Requisito |
|----|-----------|
| RNF27 | Meta de disponibilidade de 99,5% no horário de expediente (7h às 17h). Manutenções devem ocorrer fora dele. |
| RNF28 | A API deve ter o endpoint `GET /api/health`, que verifica também a conexão com o banco. |
| RNF29 | O processo do backend deve reiniciar automaticamente após falha (PM2, systemd ou Docker com `restart: unless-stopped`). |
| RNF30 | Backup diário do MySQL, com teste periódico de restauração. Metas propostas: RPO ≤ 24 h e RTO ≤ 1 h. |
| RNF31 | O backend deve usar pool de conexões e se reconectar sozinho ao banco após queda. |

## 6. Tratamento de falhas e recuperação de desastres

| ID | Requisito |
|----|-----------|
| RNF32 | **Banco indisponível:** o backend responde `503` com mensagem padronizada, desfaz a transação (*rollback*) para não deixar estados parciais e registra o erro em log. |
| RNF33 | **Totem:** com o backend ou o banco fora, desabilita a emissão, mostra aviso acessível ("Sistema temporariamente indisponível. Dirija-se à recepção.") e tenta reconectar automaticamente. Não gera senha localmente, para evitar numeração duplicada. |
| RNF34 | **Painel:** mantém a última lista conhecida, mostra o aviso "Sem conexão — as informações podem estar desatualizadas" e continua tentando. Ao reconectar, reproduz áudio apenas de chamadas novas. |
| RNF35 | **Atendente:** bloqueia as ações, informa a falha e preserva a senha atual na tela. Ao reconectar, sincroniza o estado com o servidor, que é a fonte da verdade. |
| RNF36 | O backend deve ser *stateless*: após reiniciar, recupera todo o estado do banco, sem perder atendimentos `EM_ATENDIMENTO`. |
| RNF37 | Deve existir um roteiro de recuperação documentado, e o ambiente deve ser reproduzível por `schema.sql` e `.env.example`. |

## 7. Desempenho

| ID | Requisito |
|----|-----------|
| RNF38 | Em carga normal, a emissão de senha deve responder em até 1 s e a chamada do próximo em até 500 ms (percentil 95). |
| RNF39 | Uma chamada deve aparecer no painel em até 3 s. |
| RNF40 | O sistema deve suportar pelo menos 10 guichês e 500 senhas por dia, com índices em `senhas (data_ref, estado, tipo, sequencia)`. |
| RNF41 | O relatório mensal deve ser gerado em até 5 s, usando consultas agregadas e índices. |

## 8. Auditoria

| ID | Requisito |
|----|-----------|
| RNF42 | Os eventos de senha devem ser *append-only*: a aplicação não faz UPDATE nem DELETE neles. Cada evento guarda atendente, guichê e horário com precisão de milissegundos. |
| RNF43 | Eventos de segurança (login, falha de login, acesso a relatórios, alteração de cadastros) devem ser registrados. |

## 9. Manutenibilidade, interoperabilidade e qualidade

| ID | Requisito |
|----|-----------|
| RNF44 | Stack: Node.js 22 LTS com Express, React 19 e MySQL 8.0. |
| RNF45 | A comunicação deve ser por API REST em JSON, documentada no README. |
| RNF46 | O backend deve ser organizado em camadas (`routes → controllers → services → banco`), e o frontend em `pages`, `components`, `services` e `utils`. |
| RNF47 | As regras de negócio (fila, numeração, expediente, máquina de estados) devem ter testes automatizados. |
| RNF48 | O fuso horário deve ser configurável (padrão `America/Recife`) e aplicado de forma consistente no expediente, na numeração e nos relatórios. |
| RNF49 | O frontend deve funcionar nas versões atuais de Chrome, Edge e Firefox e ser responsivo. |
