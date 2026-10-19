# Plano de implementação: Backend (serviço de negócio e pacote de contratos)

> Proposta (não consta no material do grupo): ordem e recorte das etapas. O conteúdo técnico de cada etapa (módulos, mecanismos, regras e seções) vem do DAS (`_md/01-das.md`) e dos demais documentos do grupo, com a fonte citada; a ordem é a "ordem de leitura sugerida" do `../../README.md` (fluxo principal do atendimento), e o recorte em etapas é nosso. Este arquivo só planeja: **não há código, `Dockerfile` nem arquivo de composição** (fora de escopo).

Relacionados: [`spec.md`](spec.md) (decisões de arquitetura), [`api.md`](api.md) (operações), [`../database/spec.md`](../database/spec.md) e [`../database/plano-implementacao.md`](../database/plano-implementacao.md) (dados e plano do banco), [`../implantacao.md`](../implantacao.md) (Docker) e [`../frontend/plano-implementacao.md`](../frontend/plano-implementacao.md) (planos das telas).

## Projetos cobertos
Este plano cobre dois dos projetos definidos pelo DAS (§5.1 e §5.2) e aponta o terceiro (banco de dados), que tem plano próprio em [`../database/plano-implementacao.md`](../database/plano-implementacao.md). Os outros três estão em [`../frontend/plano-implementacao.md`](../frontend/plano-implementacao.md).

| Projeto | Tipo no DAS | Plano |
|---|---|---|
| Serviço de negócio | Aplicação | (a) abaixo |
| Banco de dados | Unidade implantável (DAS §8.2) | (b) abaixo |
| Pacote de contratos | Pacote compartilhado (DAS §5.2) | (c) abaixo |

## Formato de cada etapa
**Entregáveis** | **RF/RN/UC cobertos** | **Seções do DAS** | **Mecanismos críticos** | **Pronto quando**.

"Pronto quando" usa o critério de conclusão do DAS §12.2 ("Uma tarefa só é considerada concluída quando todos os itens abaixo valem ao mesmo tempo"), aplicável a todas as etapas:
- Critérios de aceite verificados manualmente.
- Testes automatizados cobrindo a regra de negócio citada na tarefa.
- Análise estática e formatação sem apontamento.
- Contrato atualizado, quando a operação mudou.
- Estados de carregamento, vazio, erro e ausência de permissão implementados, quando há tela.
- Acessibilidade verificada quando há tela: navegação por teclado, rótulo visível e contraste.
- Auditoria registrada quando a operação é sensível.
- Revisão aprovada por outra pessoa da equipe.
- Integração contínua sem falha.

Em cada etapa abaixo, "Pronto quando" lista só o que é específico dela, além desses itens. Regra do projeto (DAS §2.2): cada caso de teste de regra de negócio cita no nome o identificador da regra (por exemplo `RN-04`).

## Frentes responsáveis (sem nomes)
Fonte: `_md/01-das.md` §13.3. Os nomes de integrantes **não** são transcritos (commit `e6e47a9`); as pessoas são identificadas só pela frente e por um número arbitrário nosso, para distinguir as partes.

| Frente | Partes sob responsabilidade |
|---|---|
| Interface e liderança técnica | Biblioteca de componentes, estrutura da aplicação operacional, fila, alocação, atendimento e encaminhamento; cliente do canal de eventos; revisão de toda mudança de contrato |
| Interface 2 | Acesso, tela inicial por perfil, cadastros base, perfis e permissões, triagem, emissão e reclassificação de senha |
| Interface e qualidade | Aplicação pública, ausências e desistências, indicadores, alertas e auditoria; suíte de testes de ponta a ponta e verificação de acessibilidade |
| Servidor 1 | Autenticação e controle de acesso, cadastros base, triagem e senha, ausências e desistências, auditoria e indicadores |
| Servidor 2 | Fila, chamada, alocação, atendimento, encaminhamento, canal de eventos, alertas de tempo de espera e rotinas agendadas |

A regra de revisão cruza interface e servidor: toda proposta de mudança precisa de aprovação de alguém da outra frente, para que a interpretação do contrato seja verificada das duas pontas. Mudança no contrato exige aprovação da liderança técnica e da pessoa responsável pelo módulo no servidor, e não pode vir acompanhada de implementação.

## Dependências com o frontend
- O contrato (pacote de contratos) de cada módulo precisa estar integrado **antes** de a tela do módulo começar (DAS §9.17): "integrado antes de a implementação começar dos dois lados". A interface constrói a tela sobre uma simulação gerada a partir das definições de validação.
- A etapa do módulo de fila no frontend só começa depois da confirmação do modelo de navegação com a coordenação (DAS §11.2 e §14.2).
- A tabela de correspondência entre etapas do servidor e telas está em cada etapa ("Telas dependentes") e em [`../frontend/spec.md`](../frontend/spec.md) (mapa de telas).

## (a) Serviço de negócio
Módulos do DAS §5.3: Acesso, Cadastros base, Triagem e senha, Fila e chamada, Alocação, Atendimento, Encaminhamento, Ausências, Indicadores, Alertas e Auditoria. Cada módulo segue a divisão interna entrada, serviço, repositório, definições de entrada e saída e eventos de domínio.

### Etapa 0 — Fundação
- **Entregáveis:** arquivo único de composição com `banco`, `admin-banco` e `servico` (ver `../implantacao.md`); esquema base (Unidade, parâmetro de unidade, usuário, perfil, permissão de perfil, registro de auditoria); formato padronizado de erro (DAS §9.16); guarda de permissão declarativa por operação; interceptador global de auditoria; verificações de saúde (vivacidade e prontidão); identificador de requisição propagado para log e auditoria; paginação obrigatória (20 por padrão, teto de 100); pacote de contratos inicial (ver (c)); ambiente de testes (Vitest e Supertest).
- **RF/RN/UC cobertos:** RNF-01, RNF-04, RNF-05 (alicerces); nenhum RF próprio.
- **Seções do DAS:** §2.1, §2.2, §7.1, §7.2 (Acesso e configuração), §8.4, §9.1, §9.13, §9.16.
- **Mecanismos críticos:** interceptação global de escrita e de leitura sensível, "sem depender de chamada explícita em cada serviço" (§3.1); identificadores ordenáveis por tempo (§7.1); recurso inexistente sob permissão negada responde acesso negado (§9.14).
- **Pronto quando:** `docker compose` sobe o serviço e o banco do zero; a verificação de prontidão falha com o banco fora e passa com ele no ar; toda resposta de erro tem código estável e identificador de requisição.
- **Frente:** servidor 1 (acesso, auditoria) e servidor 2 (verificações de saúde), com revisão cruzada. **Telas dependentes:** nenhuma ainda.

### Etapa 1 — Acesso (`MODULOS/usuarios-e-permissoes`)
- **Entregáveis:** autenticação com credencial de acesso (15 min) e de renovação (8 h, em marcador inacessível a código de página); bloqueio após 5 tentativas; catálogo de permissões; CRUD de perfis e usuários; perfis do sistema não editáveis.
- **RF/RN/UC cobertos:** RF-02, UC-04, RNF-01.
- **Seções do DAS:** §5.3 (Acesso), §9.2, §9.3, §9.14; inventário tabelas 6 a 8 (`../database/spec.md`).
- **Mecanismos críticos:** permissão decidida pela lista de permissões, nunca pelo nome do perfil; resposta de credencial inválida idêntica para usuário inexistente e senha incorreta; senha armazenada só em forma irreversível; alteração de permissões informa quantos usuários serão afetados; exclusão de perfil recusada com usuário vinculado.
- **Pronto quando:** testes de integração (Supertest) cobrem acesso negado neutro, bloqueio após a quinta tentativa e renovação de sessão.
- **Frente:** servidor 1. **Telas dependentes:** W-01, W-02, W-05.

### Etapa 2 — Cadastros base (`MODULOS/cadastros-base`)
- **Entregáveis:** operações de listar, cadastrar, consultar, editar e excluir das cinco entidades; tabelas de apoio (habilitação, procedimento, recurso, disponibilidade por turno); verificação de duplicidade; exclusão recusada com vínculo ativo, sugerindo inativar.
- **RF/RN/UC cobertos:** RF-01, UC-01 a UC-03, RN-06, RN-09 (RN-08 por nota, ver D-06 em `MODULOS/cadastros-base/spec.md`).
- **Seções do DAS:** §5.3 (Cadastros base), §7.2, §9.4; inventário tabelas 1 a 5.
- **Mecanismos críticos:** comparação de conjuntos para compatibilidade (RN-06, RN-09); duplicidade devolve o identificador do registro existente; as colunas "Duplicidade?" e "Implementa" da tabela de variações por entidade ainda são pendência do autor do documento de funcionalidades (`MODULOS/cadastros-base/spec.md`), então a regra de duplicidade por entidade só é fechada depois dessa definição.
- **Pronto quando:** testes citam `RN-06` e `RN-09` no nome; código de erro "Vínculo ativo" e "Duplicidade" cobertos.
- **Frente:** servidor 1. **Telas dependentes:** W-03, W-04.

### Etapa 3 — Triagem e senha (`MODULOS/triagem-e-senha`)
- **Entregáveis:** registro de triagem com campos condicionais; emissão de senha; reclassificação de prioridade com justificativa; histórico de reclassificações; projeções de senha (pública, resumo, detalhe); aviso de reincidência na busca de paciente.
- **RF/RN/UC cobertos:** RF-03 (e RF-04 pelo DAS, ver D-07), UC-05, UC-06, UC-07, RN-01, RN-02, RN-03, RN-04.
- **Seções do DAS:** §5.3, §9.5, §9.10 (aviso de reincidência), §9.15, §11.3, §7.4 (invariantes RN-01 e RN-03), §7.5; inventário tabelas 9, 11, 12 e 23.
- **Mecanismos críticos:** restrição de verificação no banco para justificativa (tamanho mínimo e justificativa clínica em prioridade urgente); evento de consulta a informação sensível gravado antes de responder; nome abreviado no servidor; verificação automática de contrato que reprova campo clínico em listagem; "Sem fila configurada" quando a especialidade não tem fila ativa.
- **Pronto quando:** testes citam `RN-01`, `RN-02`, `RN-03` e `RN-04`; a integração contínua reprova resposta de listagem com motivo, justificativa clínica, observações ou nome completo.
- **Frente:** servidor 1. **Telas dependentes:** W-06, W-07.

### Etapa 4 — Fila e chamada (`MODULOS/fila`, parte de `MODULOS/chamada-e-alocacao`)
- **Entregáveis:** CRUD de filas por especialidade; quadro de senhas por status; chamada da próxima senha, rechamada e cancelamento; estado de bloqueio da fila; espaços de eventos (Filas, Painel público); operações públicas (telão e consulta de posição).
- **RF/RN/UC cobertos:** RF-04, RF-05, UC-08, UC-09, RN-04, RN-10, premissa P-04.
- **Seções do DAS:** §5.3 (Fila e chamada), §6.1, §6.2, §6.3, §6.4, §7.4 (fila ativa única), §7.5, §9.6, §9.12; inventário tabelas 10, 11 e 13.
- **Mecanismos críticos:** chamada em uma única transação, com bloqueio da linha e salto de linhas bloqueadas; chave de idempotência (repetição dentro de 10 minutos devolve a resposta original); versão do registro em toda mudança de estado (código de conflito); estado único de bloqueio que alimenta aviso, recusa e alerta; o espaço público de eventos "simplesmente não tem" campos clínicos; limite de 30 requisições por minuto por origem nas operações públicas.
- **Pronto quando:** teste de concorrência prova que duas chamadas simultâneas recebem senhas diferentes; teste da ordenação cita `RN-04`; teste da recusa cita `RN-10`; testes de integração cobrem os três espaços de eventos (mitigação de risco do DAS §14.1).
- **Frente:** servidor 2. **Telas dependentes:** W-08 (só depois da confirmação do modelo de navegação), W-09, W-10.

### Etapa 5 — Alocação (`MODULOS/chamada-e-alocacao`)
- **Entregáveis:** consulta de sugestões e alternativas (estudante, supervisor, sala) com o motivo de inaptidão vindo do servidor; confirmação; troca de recurso; manutenção da senha em "Aguardando recurso"; bloqueio da fila quando não há supervisor.
- **RF/RN/UC cobertos:** RF-06, UC-10, RN-05 a RN-09 (e RN-10 no bloqueio).
- **Seções do DAS:** §5.3 (Alocação), §7.4, §9.7, §6.1 (idempotência da alocação), §13.2; inventário tabela 14.
- **Mecanismos críticos:** índices únicos parciais para estudante e sala (alocações não liberadas); contagem de supervisão dentro da transação, com bloqueio na linha do supervisor; recurso que deixa de estar disponível entre sugestão e confirmação recusa com código específico; bloco de supervisor omitido quando a especialidade não exige supervisão.
- **Pronto quando:** testes citam `RN-05` a `RN-09`; teste de duas confirmações simultâneas para o mesmo estudante recusa a segunda.
- **Frente:** servidor 2. **Telas dependentes:** W-11.

### Etapa 6 — Atendimento (`MODULOS/atendimento`)
- **Entregáveis:** início, pausa, retomada, encerramento com desfecho, validação ou recusa do supervisor; listar e consultar atendimentos.
- **RF/RN/UC cobertos:** RF-07, UC-11, UC-12, UC-13, RN-11.
- **Seções do DAS:** §5.3, §7.3, §7.4 (um atendimento por senha), §9.8; inventário tabelas 15 e 16.
- **Mecanismos críticos:** máquina de estados da senha concentrada em um único módulo, com teste de cada transição válida e proibida; exigência de validação copiada da especialidade no início; recusa mantém "Pendente de validação" com parecer; pausa como uma linha por ocorrência (tempo efetivo desconta pausas); consulta de atendimento é sensível.
- **Pronto quando:** teste de unidade cobre as transições da máquina de estados (nove estados) e cita `RN-11`; "Desfecho obrigatório" coberto.
- **Frente:** servidor 2. **Telas dependentes:** W-12, W-13.

### Etapa 7 — Encaminhamento (`MODULOS/encaminhamento`)
- **Entregáveis:** registro de encaminhamento interno (gera nova senha vinculada) ou externo (só registrado).
- **RF/RN/UC cobertos:** RF-08, UC-14, UC-15.
- **Seções do DAS:** §5.3, §9.9; inventário tabela 17.
- **Mecanismos críticos:** `senhaOrigem` / `senhaGerada`; especialidade de destino inativa recusa com código específico; destino com bloqueio ativo não impede o encaminhamento, só informa a suspensão.
- **Pronto quando:** teste do encaminhamento interno confere a nova senha em "Aguardando" na especialidade de destino.
- **Frente:** servidor 2. **Telas dependentes:** W-14.

### Etapa 8 — Ausências e desistências (`MODULOS/ausencias-e-desistencias`)
- **Entregáveis:** registro manual de ausência (com confirmação antes do vencimento da tolerância); desistência voluntária; rotina agendada de expiração de tolerância; política de reincidência.
- **RF/RN/UC cobertos:** RF-09, UC-16, UC-17, RN-12, RN-13.
- **Seções do DAS:** §5.3 (Ausências), §6.5, §9.10, §7.2; inventário tabelas 18 e 19 e parâmetros (tabela 8).
- **Mecanismos críticos:** rotina sem usuário que marca ausência ou devolve à fila conforme a política da unidade e libera os recursos alocados; reincidência é aviso e não bloqueia a triagem; ausência e desistência em tabelas separadas.
- **Pronto quando:** testes citam `RN-12` e `RN-13`; teste da rotina com tolerância vencida libera a alocação.
- **Frente:** servidor 1 (módulo) com a rotina agendada do servidor 2. **Telas dependentes:** W-15, W-16.

### Etapa 9 — Indicadores, Alertas e Auditoria (`MODULOS/indicadores-alertas-auditoria`)
- **Entregáveis:** indicadores por agregação no banco; exportação (gera evento na trilha); varredura de tempo de espera, alertas, reconhecimento e escalonamento; espaço de eventos "Alertas"; consulta, detalhe e exportação da trilha de auditoria.
- **RF/RN/UC cobertos:** RF-10, RF-11, RF-12, UC-18, UC-19, UC-20, RNF-05, RNF-07.
- **Seções do DAS:** §5.3 (Indicadores, Alertas, Auditoria), §6.3, §6.5, §6.6, §7.4 (trilha imutável), §9.11; inventário tabelas 20 a 22.
- **Mecanismos críticos:** funções de janela no banco; granularidade mínima de especialidade e período, sem dado individual identificável; alerta de bloqueio de fila como origem distinta; normalização automática; trilha append-only por revogação de permissão no banco; a consulta e a exportação da trilha também são registradas.
- **Pronto quando:** teste confirma que o usuário de banco da aplicação não consegue alterar nem remover a auditoria; teste do alerta cobre disparo e normalização.
- **Frente:** servidor 1 (indicadores e auditoria) e servidor 2 (alertas). **Telas dependentes:** W-17, W-18.

## (b) Banco de dados
Plano em [`../database/plano-implementacao.md`](../database/plano-implementacao.md) (camada `database/`). As migrações acompanham as etapas 0 a 9 do serviço de negócio acima, conforme a tabela de correspondência desse plano.

## (c) Pacote de contratos
Pacote compartilhado, "fonte única de validação" (DAS §5.2 e §11.1), consumido pelo serviço de negócio, pela aplicação operacional e pela aplicação pública. Estrutura de pasta proposta no `AGENTS.md` da raiz.

### Etapa C0 — Estrutura e base
- **Entregáveis:** pacote no monorepo; tipo de erro padronizado (§9.16) com código estável e lista de campos; paginação (20 padrão, teto 100); catálogo de permissões como tipo (§9.14); versão do contrato; geração da simulação (mock) a partir das definições de validação.
- **RF/RN/UC cobertos:** RNF-01, RNF-03.
- **Seções do DAS:** §5.2, §9.1, §9.14, §9.16, §9.17, §11.1.
- **Mecanismos críticos:** definição escrita uma única vez e usada pela camada de entrada e pelo formulário; verificação automática de contrato na integração contínua (§12.1).
- **Pronto quando:** servidor e interface importam o mesmo pacote; a verificação de contrato roda na integração contínua.

### Etapas C1 a C9 — Contrato por módulo
Uma etapa por módulo do servidor, na mesma ordem das etapas 1 a 9 do serviço: tipos de entrada e saída, validações condicionais (ficha de triagem e reclassificação, DAS §5.2), as **três projeções de senha** (§9.15) como tipos mutuamente exclusivos, e os tipos de evento dos espaços Filas, Painel público e Alertas (§6.3).
- **Fluxo de mudança (DAS §9.17):** o contrato muda em revisão própria, separada da implementação, aprovada pela liderança técnica e pela pessoa responsável pelo módulo no servidor; é integrado antes de a implementação começar dos dois lados; mudança compatível não altera a versão, mudança incompatível exige nova versão e período de convivência.
- **Pronto quando:** o contrato do módulo está integrado antes de o servidor e a tela começarem; a verificação de contrato reprova campo clínico em listagem.
