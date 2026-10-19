# Spec: Implantação e Docker

> Esta spec registra como o sistema é construído e executado. Trechos do DAS (`_md/01-das.md`, cap. 8 e §10) estão copiados com a fonte citada; o que é proposta nossa está marcado como proposta. Ainda é só documentação: não há `Dockerfile` nem arquivo de composição no repositório.

## Restrição de projeto: Docker
**O projeto deve ser construído e executado com Docker (orientação do professor).**

Fonte: feedback do professor, repassado pelo usuário. É registrado como **restrição de projeto**, e não como requisito não funcional novo (a lista RNF-01 a RNF-09 vem inalterada de `_md/03-especificacao-requisitos.md`).

## Ferramentas
Fontes: `_md/01-das.md` §10.2 e `_md/02-tecnologias.md` §3.3.

| Ferramenta | Para que serve |
|---|---|
| Docker Compose, com banco e administrador de banco | Ambiente local idêntico para os cinco integrantes, com banco de dados e administrador de banco; sem instalação manual de dependência |

> Nota: o material do grupo **não especifica** qual ferramenta de administração do banco é usada, nem o desenho do arquivo de composição. A tabela de serviços da seção "Proposta" abaixo é nossa.

## 8.1 Ambientes
Fonte: `_md/01-das.md` §8.1.

| Ambiente | Onde executa | Base de dados | Observação |
|---|---|---|---|
| Desenvolvimento local | Contêineres na máquina de cada integrante, orquestrados por um arquivo único de composição | Base local com carga inicial de dados fictícios | A carga cria perfis, cinco especialidades, salas, supervisores e pacientes fictícios, de modo que todos partem do mesmo estado |
| Homologação | Contêiner único em servidor da instituição | Base de homologação | Usado na validação com a coordenação da clínica; apenas dados fictícios |
| Produção | A definir com a instituição | Base de produção | Exige transporte criptografado, cópia de segurança diária e política de retenção definida |

Nenhum dado real de paciente entra em ambiente local ou de homologação. É restrição do projeto, não recomendação.

## 8.2 Unidades implantáveis
Fonte: `_md/01-das.md` §8.2.

| Unidade | Natureza | Escala |
|---|---|---|
| Serviço de negócio | Processo sem estado próprio de sessão em memória | Replicável horizontalmente; o canal de eventos usa um adaptador externo quando há mais de uma instância |
| Aplicação operacional | Aplicação de interface com renderização no servidor e no cliente | Replicável; sem estado |
| Aplicação pública | Aplicação de interface leve, sem autenticação | Replicável; normalmente exposta apenas na rede interna da clínica |
| Banco de dados | Instância única com cópia de segurança diária | Escala vertical; réplica de leitura é possibilidade futura para os relatórios |

## 8.3 Terminais e clientes
Fonte: `_md/01-das.md` §8.3.

| Cliente | Uso | Requisito específico |
|---|---|---|
| Estação da recepção e da triagem | Operação completa | Sessão curta, por ser terminal compartilhado; sem credencial persistente por padrão |
| Terminal do telão | Somente exibição, sem interação | Tela de 42 polegadas em orientação paisagem, projetada para resolução de 1920 por 1080 |
| Celular do paciente | Consulta da própria senha | Texto ampliável sem quebra de layout e alvos de toque ampliados |
| Estação de gestão | Indicadores, alertas e auditoria | Telas de gestão não são oferecidas abaixo de 768 pixels de largura |

## 8.4 Saúde e observabilidade
Fontes: `_md/01-das.md` §8.4 e §9.13.

O serviço expõe duas verificações distintas. A primeira responde sem tocar o banco de dados e serve para saber se o processo está vivo; a segunda verifica a conexão com o banco e a aplicação das migrações, e serve para saber se o processo está apto a receber tráfego. Toda resposta carrega um identificador de requisição, propagado para o registro de log e para a trilha de auditoria, o que permite ligar um relato de usuário a uma linha específica de registro.

| Operação | Ação | Recurso | Permissão | Uso |
|---|---|---|---|---|
| Verificar se o processo está vivo | Consultar | Estado do serviço | Pública | Responde sem tocar o banco de dados |
| Verificar se o serviço está apto a receber tráfego | Consultar | Prontidão do serviço | Pública | Verifica o banco e a aplicação das migrações |

Atendimento ao RNF-04 (DAS §10.4): "Verificações de vivacidade e de prontidão; reinício automático do contêiner; aviso de perda de conexão com congelamento das ações de escrita".

## Proposta de composição local
> Proposta (não consta no material do grupo): os serviços abaixo, os nomes e os detalhes de configuração são nossos. O material só diz "Docker Compose, com banco e administrador de banco" e "arquivo único de composição".

| Serviço | Papel |
|---|---|
| `banco` | PostgreSQL 16 (versão do DAS §10.2), com volume para persistência |
| `admin-banco` | Ferramenta de administração do banco (a ferramenta específica ainda precisa ser escolhida pelo grupo) |
| `servico` | Serviço de negócio (NestJS) |
| `operacional` | Aplicação operacional (Next.js) |
| `publica` | Aplicação pública (Next.js) |

Diretrizes da proposta:
- Carga inicial conforme o DAS §8.1: "perfis, cinco especialidades, salas, supervisores e pacientes fictícios", executada de forma que todos partam do mesmo estado.
- Política de reinício (`restart`) automático nos contêineres, para atender o "reinício automático do contêiner" do RNF-04.
- Verificações de saúde (`healthcheck`) do contêiner ligadas às duas verificações do serviço (vivacidade e prontidão).
- Nenhum segredo versionado: credenciais e URLs só por variáveis de ambiente fora do repositório.
- Homologação em contêiner único (DAS §8.1) (imagem única que empacota as aplicações; o material do grupo não diz como, e o ambiente local usa mais de uma imagem: pendência do grupo).

Planos de implementação por projeto: [`backend/plano-implementacao.md`](backend/plano-implementacao.md), [`database/plano-implementacao.md`](database/plano-implementacao.md) e [`frontend/plano-implementacao.md`](frontend/plano-implementacao.md).
