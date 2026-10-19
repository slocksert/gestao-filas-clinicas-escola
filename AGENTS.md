# AGENTS.md — Sistema de Gestão de Filas para Atendimento em Clínicas-Escola

Instruções para quem (pessoa ou IA) trabalha neste projeto. Arquivo único para o projeto todo (orientação do professor); não há `AGENTS.md` por módulo. Fontes indicadas como `_md/...` são documentos do grupo; trechos marcados "Proposta" são nossos e não constam no material do grupo.

## 1. Sobre o projeto
O sistema cobre o ciclo completo de atendimento de uma clínica-escola: cadastro das entidades base, triagem do paciente, emissão e ordenação de senha, chamada, alocação de estudante, supervisor e sala, condução do atendimento com pausa e encerramento, encaminhamento interno e externo, registro de ausência e desistência, indicadores de gestão, alertas de tempo de espera e trilha de auditoria. Inclui também as interfaces sem autenticação usadas na sala de espera: o telão de chamada e a consulta da própria senha pelo paciente (`_md/01-das.md` §1.2).

**Estado atual: somente documentação; código ainda não iniciado.** Não há `Dockerfile`, arquivo de composição, gerenciador de pacotes, testes nem aplicação.

## 2. Onde estão as regras
```
AGENTS.md                     # este arquivo
.docs/.ref/
├── README.md                 # estrutura, critério MODULOS × SPECS, ordem de leitura, origem do conteúdo, pendências
├── MODULOS/                  # RF, RN e UC por módulo de negócio (+ acesso-publico, sem RF/UC próprios)
└── SPECS/
    ├── requisitos-nao-funcionais.md
    ├── implantacao.md        # Docker, ambientes
    ├── backend/              # spec.md, api.md, plano-implementacao.md
    ├── database/             # spec.md (dados), plano-implementacao.md
    └── frontend/             # spec.md, plano-implementacao.md
```
- **Critério MODULOS × SPECS:** `MODULOS/` = requisitos funcionais (RF), regras de negócio (RN) e casos de uso (UC), por módulo. `SPECS/` = requisitos não funcionais (RNF) e specs por camada técnica (backend, database e frontend). O conteúdo de arquitetura nunca entra no texto de requisito dos módulos.
- **Precedência quando há conflito:** `MODULOS/<módulo>/spec.md` > `SPECS/` > `.docs/.ref/README.md`. A precedência entre os documentos do grupo está no README.
- **`_md/` é material-fonte bruto, somente leitura, e não é regra.** Não edite nenhum arquivo em `_md/`. O arquivo `_md/00-requisitos-modelo.md` é o modelo de **outro** sistema ("Violência Zero"): serve só de referência de forma, nunca de conteúdo.

## 3. Regra de origem do conteúdo
- O texto de RN, RF, RNF e UC é copiado **sem alteração** do documento do grupo. Nenhum requisito é inventado.
- Texto que é proposta nossa leva o marcador `> Proposta (não consta no material do grupo):`.
- Texto que vem de outro documento do grupo cita a fonte (por exemplo `Fonte: _md/01-das.md §7.3`).
- **Os documentos do repositório não citam nomes de integrantes.** O DAS §13.3 traz nomes por pessoa; eles não são transcritos (use "frente de interface" e "frente de servidor"). Ver o commit `e6e47a9`.
- Divergências internas do material do grupo são registradas como `> Nota de divergência`, nunca corrigidas na fonte nem resolvidas por conta própria.
- Célula ilegível na extração dos arquivos Apple Pages: escrever `[ilegível na extração]`; só completar a partir de outro documento do grupo, citando a fonte.

## 4. Convenções de identificação
Fonte: `_md/01-das.md` §1.3.

| Prefixo | Significado |
|---|---|
| RF | Requisito funcional, numerado de RF-01 a RF-12 |
| RNF | Requisito não funcional, numerado de RNF-01 a RNF-09 |
| RN | Regra de negócio, numerada de RN-01 a RN-13 |
| UC | Caso de uso, numerado de UC-01 a UC-20 |
| W | Tela, conforme os wireframes na versão 2, numerada de W-01 a W-18 |
| T | Tela, conforme a especificação de interface em texto, numerada de T-01 a T-18 |
| P | Premissa transversal da especificação de interface, de P-01 a P-04 |
| Senha | Ficha numerada que representa a passagem de um paciente pela fila, do atendimento até o desfecho |
| Fila | Conjunto ordenado de senhas aguardando atendimento em uma especialidade |

A numeração de telas convive em duas séries porque os dois documentos de interface foram produzidos em momentos diferentes. O DAS usa a série dos wireframes na versão 2, por ser o artefato mais recente, e cita a série da especificação em texto quando a correspondência é relevante. O mapa W ↔ T está em `.docs/.ref/SPECS/frontend/spec.md`.

## 5. Arquitetura em uma página
Fonte: `_md/01-das.md` §2.1, §5.1 e §5.2 (detalhes em `.docs/.ref/SPECS/backend/spec.md` e `.docs/.ref/SPECS/frontend/spec.md`).

- **Três aplicações:** serviço de negócio (regras de negócio, persistência, controle de acesso, auditoria, canal de eventos e rotinas agendadas; é a única parte que fala com o banco), aplicação operacional (toda a operação autenticada, telas W-01 a W-08 e W-11 a W-18) e aplicação pública (telão e consulta de senha, W-09 e W-10, sem autenticação).
- **Três pacotes compartilhados:** contratos (tipos e regras de validação escritos uma única vez), biblioteca de componentes (componentes visuais acessíveis) e padrões de código (análise estática e bases de configuração).
- **Estilo em camadas:** a lógica de negócio é organizada em camada de entrada (protocolo, validação e códigos de resposta), camada de serviço (todas as regras) e camada de acesso a dados (sem regra alguma). A interface e o servidor se comunicam por um canal de requisição e resposta e por um canal de eventos, que nunca é a única fonte de verdade.
- **Princípio de organização (DAS §2.2):** "nenhuma regra de negócio é implementada na camada de entrada nem em componente de interface. Regra de negócio mora na camada de serviço e tem teste automatizado que cita, no nome do caso de teste, o identificador da regra que verifica."

## 6. Stack
Fonte: `_md/01-das.md` §10.2. As versões são fixadas e só sobem por decisão registrada (§10.1).

| Camada | Tecnologia | Versão de referência |
|---|---|---|
| Linguagem | TypeScript | 5 |
| Interface de usuário | Next.js com React | 15 e 19 |
| Estado de servidor na interface | TanStack Query | 5 |
| Formulários | React Hook Form com Zod | — |
| Estilo e componentes | Tailwind CSS com primitivas Radix | 4 |
| Serviço de negócio | NestJS | 11 |
| Mapeamento objeto-relacional | Prisma | 6 |
| Banco de dados | PostgreSQL | 16 |
| Canal de tempo real | Socket.IO | 4 |
| Agendamento de rotinas | Módulo de agendamento do NestJS | — |
| Autenticação | Credencial de acesso e de renovação em marcador de navegador protegido | — |
| Testes | Vitest, Supertest e Playwright | — |
| Qualidade de código | ESLint, Prettier, Husky, lint-staged e Commitlint | — |
| Empacotamento do repositório | pnpm com Turborepo | — |
| Ambiente local | Docker Compose, com banco e administrador de banco | — |
| Integração contínua | GitHub Actions | — |

## 7. Organização do código (monorepo)
Fonte: `_md/02-tecnologias.md` §3.3: pnpm com Turborepo, "Repositório único com as três aplicações e os pacotes compartilhados, com construção incremental".

> Proposta (não consta no material do grupo): nomes de pasta do monorepo.
> - `apps/servico` — serviço de negócio
> - `apps/operacional` — aplicação operacional
> - `apps/publica` — aplicação pública
> - `packages/contratos` — pacote de contratos
> - `packages/componentes` — biblioteca de componentes
> - `packages/config` — padrões de código

Divisão interna de cada módulo do serviço de negócio (`_md/01-das.md` §5.3): uma camada de entrada, que trata protocolo e serialização; uma camada de serviço, onde vivem as regras; uma camada de repositório, que fala com o banco; um conjunto de definições de entrada e saída; e os eventos de domínio que o módulo emite.

## 8. Regras que nenhum código pode quebrar
1. **Permissão é decidida pela lista de permissões, nunca pelo nome do perfil** (DAS §9.2): perfis são editáveis pela administração.
2. **Ação sem permissão não é renderizada**, nunca em estado desabilitado (DAS §5.5).
3. **Três projeções de senha** (pública, resumo, detalhe), escolhidas pelo tipo de consumidor, sem dado clínico em listagem (DAS §9.15, premissa P-03). A projeção pública tem número, especialidade e local; a consulta do paciente (T-12 de `04`) pede mais campos, divergência D-31 em aberto.
4. **Os valores de comportamento que dependem da unidade nunca ficam fixos no código** (cinco no DAS §3.2 e §7.6; seis campos no inventário, ver divergência D-13): prazo de tolerância, limite de tempo de espera, reincidências, limite de estudantes por supervisor e tamanho mínimo da justificativa. São parâmetros editáveis.
5. **Nenhum dado real de paciente em ambiente local ou de homologação** (DAS §3.2 e §8.1): é restrição do projeto, não recomendação.
6. **Paginação obrigatória em toda listagem**: 20 itens por padrão, teto de 100 (DAS §9.1).
7. **Trilha de auditoria imutável**: as permissões de alteração e remoção são revogadas do usuário de banco da aplicação (DAS §7.4).
8. **A chamada da próxima senha roda em uma única transação, com bloqueio de linha e salto das linhas já bloqueadas** (DAS §6.1).

## 9. Docker
**O projeto deve ser construído e executado com Docker (orientação do professor).** É restrição de projeto, e não RNF novo. O ambiente local é um arquivo único de composição com os contêineres do projeto, banco e administrador de banco, e uma carga inicial de dados fictícios; a homologação roda em contêiner único. Detalhes, ambientes e a proposta de composição: [`.docs/.ref/SPECS/implantacao.md`](.docs/.ref/SPECS/implantacao.md).

## 10. Fluxo de trabalho
- **Contrato primeiro** (DAS §9.17): o contrato é alterado em revisão própria, separada da implementação, aprovada pela liderança técnica e pela pessoa responsável pelo módulo no servidor, e integrado antes de a implementação começar dos dois lados. A interface constrói a tela sobre uma simulação gerada a partir das definições de validação.
- **Revisão cruzada entre frentes** (DAS §13.3, sem nomes): toda proposta de mudança precisa de aprovação de alguém da outra frente (interface ou servidor). Mudança no contrato exige aprovação da liderança técnica e da pessoa responsável pelo módulo no servidor, e não pode vir acompanhada de implementação.
- **Critério de conclusão de uma tarefa** (DAS §12.2): "Uma tarefa só é considerada concluída quando todos os itens abaixo valem ao mesmo tempo."
  - Critérios de aceite verificados manualmente.
  - Testes automatizados cobrindo a regra de negócio citada na tarefa.
  - Análise estática e formatação sem apontamento.
  - Contrato atualizado, quando a operação mudou.
  - Estados de carregamento, vazio, erro e ausência de permissão implementados, quando há tela.
  - Acessibilidade verificada quando há tela: navegação por teclado, rótulo visível e contraste.
  - Auditoria registrada quando a operação é sensível.
  - Revisão aprovada por outra pessoa da equipe.
  - Integração contínua sem falha.
- **Estados obrigatórios de tela** (DAS §12.3): toda tela de listagem ou consulta especifica quatro estados além do estado normal — carregando (esqueleto do conteúdo, nunca indicador isolado em tabela), vazio (ícone, frase explicativa e ação primária quando aplicável), erro de carga (mensagem e opção de nova tentativa) e ausência de permissão (mensagem neutra que não revela existência nem volume de dados).

## 11. Instruções específicas para agentes de IA
> Proposta (não consta no material do grupo): regras de trabalho para ferramentas de IA.
- Antes de mexer em qualquer coisa, leia nesta ordem: este `AGENTS.md` → `.docs/.ref/README.md` → a `spec.md` do módulo ou da camada envolvida.
- Não edite nada em `_md/`.
- Cite o identificador da regra (por exemplo `RN-04`) no nome do caso de teste, conforme o DAS §2.2.
- Não invente regra de negócio. Se faltar definição ou houver conflito entre documentos, registre como pendência (README) e pergunte; não escolha sozinho.
- Não transcreva nomes de integrantes do material do grupo.
- A documentação de trabalho de IA (specs de tarefa, notas, relatórios) fica em `tmp/docs/`; a documentação do projeto fica em `.docs/.ref/` e na raiz.
- **Operações de git (`add`, `commit`, `push`) ficam com o integrante; a IA não as executa.** Leitura de histórico é permitida.
