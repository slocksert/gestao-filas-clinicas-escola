# Mapeamento de Módulos e Documentação — Clínica-Escola

Estrutura de documentação organizada conforme orientação passada em aula (mapear módulos primeiro, depois documentar cada um).

```
.docs/.ref/
├── MODULOS/                          # requisitos funcionais + casos de uso, por módulo de negócio
│   ├── usuarios-e-permissoes/spec.md
│   ├── cadastros-base/spec.md
│   ├── triagem-e-senha/spec.md
│   ├── fila/spec.md
│   ├── chamada-e-alocacao/spec.md
│   ├── atendimento/spec.md
│   ├── encaminhamento/spec.md
│   ├── ausencias-e-desistencias/spec.md
│   └── indicadores-alertas-auditoria/spec.md
└── SPECS/                            # requisitos transversais (não-funcionais) e specs por camada técnica
    ├── requisitos-nao-funcionais.md  # RNF-01 a RNF-09
    └── backend/spec.md               # mapeamento backend ↔ módulos ↔ RNFs
```

## Critério usado para separar MODULOS de SPECS
- **`MODULOS/`** = requisitos **funcionais (RF)**, regras de negócio (RN) e casos de uso (UC) — o "o quê" o sistema faz, agrupado por módulo de negócio.
- **`SPECS/`** = requisitos **não funcionais (RNF)** e specs por camada técnica (ex.: `backend/`) — o "como" o sistema deve se comportar de forma transversal, não amarrado a um módulo só. Bate com o segundo desenho do quadro (`SPECS → BACKEND`).

## Ordem de leitura sugerida
1. `MODULOS/usuarios-e-permissoes` — base de acesso, todo o resto depende dele.
2. `MODULOS/cadastros-base` — entidades usadas por todos os outros módulos.
3. `MODULOS/triagem-e-senha` → `MODULOS/fila` → `MODULOS/chamada-e-alocacao` → `MODULOS/atendimento` → `MODULOS/encaminhamento` — fluxo principal do atendimento, nessa ordem.
4. `MODULOS/ausencias-e-desistencias` — casos alternativos do fluxo acima.
5. `MODULOS/indicadores-alertas-auditoria` — consome dados de todos os anteriores.
6. `SPECS/requisitos-nao-funcionais.md` e `SPECS/backend/spec.md` — requisitos transversais.

## Origem do conteúdo
Todo o texto de RN/RF/RNF/UC foi extraído sem alteração do documento de especificação de requisitos enviado pelo grupo (31/08/2026); apenas foi reorganizado por módulo. Nenhum requisito foi inventado.

## Pendências para confirmar com o professor
- Se cada módulo também precisa de um `agents.md` (foi levantada essa hipótese no grupo, mas não foi confirmada — o quadro só mostrava `spec.md`).
- A "tabela de variações por entidade" citada em `MODULOS/cadastros-base/spec.md` (RF-01) não veio completa no material — falta preencher ator/campos/duplicidade de cada uma das 5 entidades.
- Se `SPECS/` deve ganhar também `frontend/` e `database/`, seguindo o mesmo padrão de `backend/`.
