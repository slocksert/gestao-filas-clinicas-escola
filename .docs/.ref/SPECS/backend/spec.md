# Spec: Backend

> Este arquivo é o mapeamento de alto nível pedido pelo professor no quadro (`SPECS → BACKEND`). Ainda é só documentação/mapeamento — nenhuma decisão de código, framework ou banco foi tomada aqui.

## Papel deste documento
Consolidar, do ponto de vista do backend, quais módulos de negócio (`MODULOS/`) e quais requisitos não funcionais (`requisitos-nao-funcionais.md`) impactam a camada de servidor/API, servindo de ponto de partida para quando a equipe for desenhar a arquitetura.

## Módulos que o backend precisa expor
| Módulo | Pasta | Principais RF |
|---|---|---|
| Usuários e Permissões | `MODULOS/usuarios-e-permissoes` | RF-02 |
| Cadastros Base | `MODULOS/cadastros-base` | RF-01 |
| Triagem e Senha | `MODULOS/triagem-e-senha` | RF-03 |
| Fila | `MODULOS/fila` | RF-04 |
| Chamada e Alocação | `MODULOS/chamada-e-alocacao` | RF-05, RF-06 |
| Atendimento | `MODULOS/atendimento` | RF-07 |
| Encaminhamento | `MODULOS/encaminhamento` | RF-08 |
| Ausências e Desistências | `MODULOS/ausencias-e-desistencias` | RF-09 |
| Indicadores, Alertas e Auditoria | `MODULOS/indicadores-alertas-auditoria` | RF-10, RF-11, RF-12 |

## Requisitos não funcionais que o backend precisa garantir
- **RNF-01** — Controle de acesso por perfil em toda operação exposta.
- **RNF-02** — Proteção de dados sensíveis (saúde) em trânsito e em repouso.
- **RNF-03** — Desempenho nas operações de fila.
- **RNF-04** — Disponibilidade durante o horário de funcionamento.
- **RNF-05** — Auditoria de toda operação relevante.
- **RNF-07** — Conformidade com a LGPD.
- **RNF-08** — Suporte a múltiplas especialidades/unidades simultâneas.
- **RNF-09** — Backup e recuperação de desastres.

(Detalhamento completo de cada um em `../requisitos-nao-funcionais.md`.)

## Em aberto (não decidido ainda)
- Stack/framework do backend.
- Modelagem de banco de dados.
- Design de endpoints/API.
- Estratégia de autenticação/autorização (só sabemos que RNF-01 exige controle por perfil).

> Quando essas decisões forem tomadas, documentar aqui ou quebrar em sub-specs (ex.: `SPECS/backend/api.md`, `SPECS/backend/dados.md`), seguindo o mesmo padrão. Se o professor confirmar que cada módulo também precisa de um `agents.md` (instruções para trabalhar com IA), replicar o padrão usado em `spec.md` dentro de cada pasta de `MODULOS/`.
