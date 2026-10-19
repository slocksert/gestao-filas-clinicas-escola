# Mapeamento de Módulos e Documentação — Clínica-Escola

Estrutura de documentação organizada conforme orientação passada em aula (mapear módulos primeiro, depois documentar cada um). As instruções para quem trabalha no projeto (pessoa ou IA) estão no [`AGENTS.md`](../../AGENTS.md) da raiz do repositório, que é único para o projeto todo.

```
AGENTS.md                             # instruções do projeto todo (único, na raiz)
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
│   ├── indicadores-alertas-auditoria/spec.md
│   └── acesso-publico/spec.md        # aplicação pública (telão e consulta de senha); sem RF/UC próprios
└── SPECS/                            # requisitos transversais (não-funcionais) e specs por camada técnica
    ├── requisitos-nao-funcionais.md  # RNF-01 a RNF-09 (com o atendimento técnico do DAS)
    ├── implantacao.md                # Docker (restrição de projeto), ambientes e unidades implantáveis
    ├── backend/
    │   ├── spec.md                   # mapeamento backend ↔ módulos ↔ RNFs e decisões de arquitetura
    │   ├── api.md                    # mapeamento da interface de programação (DAS cap. 9)
    │   └── plano-implementacao.md    # planos: serviço de negócio e pacote de contratos
    ├── database/
    │   ├── spec.md                   # visão de dados e inventário de dados (DAS cap. 7 e pages-inv)
    │   └── plano-implementacao.md    # plano: banco de dados (instância, migrações, carga inicial)
    └── frontend/
        ├── spec.md                   # aplicações de interface, premissas P-01..P-04, mapa de telas
        └── plano-implementacao.md    # planos: biblioteca de componentes, app operacional, app pública
```

## Critério usado para separar MODULOS de SPECS
- **`MODULOS/`** = requisitos **funcionais (RF)**, regras de negócio (RN) e casos de uso (UC) — o "o quê" o sistema faz, agrupado por módulo de negócio. O módulo `acesso-publico` é a exceção explicada: não tem RF/UC próprios, só referencia UC-08 e UC-09.
- **`SPECS/`** = requisitos **não funcionais (RNF)** e specs por camada técnica — o "como" o sistema deve se comportar de forma transversal, não amarrado a um módulo só. SPECS por camada: backend, database e frontend (backend e frontend por orientação do professor; database por decisão do usuário).
- O conteúdo de arquitetura (DAS) vai para `SPECS/`, nunca para dentro do texto de requisito dos módulos; nos módulos entram só notas e referências: nas seções "Entidades relacionadas" e "Dependências / Observações" e como `> Nota` junto ao requisito afetado. O módulo `acesso-publico` cita operações do DAS por não ter RF/UC próprios.

## Ordem de leitura sugerida
1. `MODULOS/usuarios-e-permissoes` — base de acesso, todo o resto depende dele.
2. `MODULOS/cadastros-base` — entidades usadas por todos os outros módulos.
3. `MODULOS/triagem-e-senha` → `MODULOS/fila` → `MODULOS/chamada-e-alocacao` → `MODULOS/atendimento` → `MODULOS/encaminhamento` — fluxo principal do atendimento, nessa ordem.
4. `MODULOS/ausencias-e-desistencias` — casos alternativos do fluxo acima.
5. `MODULOS/indicadores-alertas-auditoria` — consome dados de todos os anteriores.
6. `SPECS/requisitos-nao-funcionais.md`, `SPECS/backend/spec.md` (e `api.md`), `SPECS/database/spec.md`, `SPECS/frontend/spec.md` e `SPECS/implantacao.md` — requisitos transversais e decisões por camada. Os planos de implementação (`plano-implementacao.md`, de cada camada) por último.

## Origem do conteúdo
Todo o texto de RN/RF/RNF (da Especificação de Requisitos, 31/08/2026) e de UC (da Especificação de Funcionalidades) foi extraído sem alteração; apenas foi reorganizado por módulo. Nenhum requisito foi inventado.

Fontes usadas (material-fonte bruto em `_md/`, somente leitura; não são regras, servem de origem):

| Arquivo em `_md/` | Documento do grupo | Usado para |
|---|---|---|
| `03-especificacao-requisitos.md` | Especificação de Requisitos | Texto de RN, RF e RNF |
| `pages-func.txt` | Especificação de Funcionalidades — versão enxuta | Texto dos casos de uso (UC-01..UC-20) e tabela de variações por entidade |
| `01-das.md` | DAS — Documento de Arquitetura de Software | Arquitetura, API, dados, implantação, tecnologias, ADRs, qualidade e rastreabilidade (`SPECS/`) |
| `02-tecnologias.md` | Tecnologias do projeto (recorte do DAS, cap. 10) | Tecnologias, Docker Compose |
| `04-especificacao-ui.md` | Especificação de Interface de Usuário (versão em texto) | Premissas P-01..P-04, fundamentos visuais, padrões globais, telas T-01..T-18 |
| `05-wireframes.md` | Wireframes — versão 2 | Telas W-01..W-18, menu por módulos |
| `pages-inv.txt` | Inventário de Dados | `SPECS/database/spec.md` |
| `pages-arq.txt` | Arquitetura geral (texto da Figura 1) | Contagem de módulos e o módulo de acesso público |
| `00-requisitos-modelo.md` | Modelo de outro sistema | **Não é conteúdo** do projeto; só serve de referência de forma |

Trechos que não vêm do material do grupo (tabelas de correspondência, planos de implementação, propostas de composição e de pastas) levam o marcador `> Proposta (não consta no material do grupo):`. Notas de divergência do material do grupo são registradas, nunca resolvidas por nós. Os documentos do repositório não citam nomes de integrantes.

### Precedência entre documentos do grupo
Proposta (não consta no material do grupo): quando dois documentos do grupo divergem.
1. Texto de RN/RF/RNF: `03-especificacao-requisitos.md`.
2. Texto de UC: `pages-func.txt`.
3. Arquitetura, contrato e dados: o DAS (`01-das.md`), por ser o mais recente e consolidar os outros (§1.3 e §11.2). Entre o DAS e o `pages-inv.txt`, nenhum prevalece: registrar os dois e a divergência.
4. Navegação: DAS §11.2 > `05-wireframes.md` > `04-especificacao-ui.md`.
5. Detalhe de tela sem conflito: `04` e `05` valem juntos.
6. Conflito de regra de negócio nunca é resolvido por conta própria: é registrado como pendência e levado ao grupo/professor.

## Pendências
Resolvidas ou reformuladas:
- **`AGENTS.md` por módulo — resolvido.** O professor indicou um `AGENTS.md` único para o projeto todo, na raiz. Nada por módulo.
- **Tabela de variações por entidade — reformulada.** A tabela está transcrita em `MODULOS/cadastros-base/spec.md` (colunas legíveis). Completar as colunas ilegíveis ("Duplicidade?" e "Implementa") e validar a transcrição é responsabilidade do autor do documento de funcionalidades (orientação do professor).
- **`SPECS/frontend/` — resolvido** (criado).
- **`SPECS/database/` — resolvido** (criado, decisão do usuário): a documentação de banco de dados é uma camada própria, no mesmo nível de `backend/` e `frontend/`; o backend a referencia.
- **Docker — registrado** como restrição de projeto em `SPECS/implantacao.md` (não é RNF novo).

Novas (para o grupo):
- Pendências registradas no DAS §14.2, copiadas sem alteração:
  - Confirmação do modelo de navegação com a coordenação da clínica-escola.
  - Definição dos cinco valores de parametrização da unidade.
  - Definição da base legal e do prazo de retenção de triagem e atendimento.
  - Definição do ambiente de produção pela instituição, com transporte criptografado, cópia de segurança diária e política de retenção.
  - Atualização da seção de navegação da especificação de interface, para refletir o mapa de módulos por permissão no lugar do mapa por área de trabalho.
- **Status de senha: "oito" × nove.** Os textos (P-01 de `04`, DAS §3.3 e §7.3) dizem oito, mas as tabelas listam nove. Documentamos os nove; o autor da especificação de interface corrige a contagem.
- **Exportar em PDF os arquivos Apple Pages** (`pages-func`, `pages-inv`, `pages-arq`) para recuperar as células perdidas na extração (marcadas `[ilegível na extração]`) e a Figura 1 de `pages-arq`, que não está no material.
- **Cinco × seis parâmetros da unidade** (DAS §7.6 × tabela 8 do inventário, com `janelaReincidenciaDias`) e **retenção de "Cadastros base"** (só no inventário): o grupo decide (ver `SPECS/database/spec.md`).
- **Consulta pública de senha (D-31):** a T-12 de `04` pede número, especialidade, posição, tempo médio estimado e status; o DAS §9.15 limita a projeção pública a número, especialidade e local. O grupo define se a consulta pública usa uma quarta variante de projeção ou se a projeção pública ganha posição e status (ver `SPECS/backend/api.md` §9.15).
- **Escolha da ferramenta de administração do banco** do ambiente Docker, não especificada no material (ver `SPECS/implantacao.md`).
