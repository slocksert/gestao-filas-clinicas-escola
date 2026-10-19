Sistema de Gestão de Filas para Atendimento em Clínicas-
Escola

Tecnologias do Projeto

1

1. Objetivo deste documento

Este documento apresenta, de forma resumida, as tecnologias escolhidas para construir o sistema e o
motivo de cada escolha. Ele é um recorte do Documento de Arquitetura de Software, feito para consulta
rápida por quem precisa saber com o que o projeto é construído sem percorrer o documento completo.

2. A decisão que organiza todas as outras

A escolha de maior alcance foi usar a mesma linguagem tipada em todas as camadas, da tela ao banco de
dados. Três razões a sustentam no contexto deste projeto.

•  As regras de validação são escritas uma única vez, em um pacote compartilhado, e usadas tanto pelo
servidor quanto pelo formulário na tela. Deixa de existir a divergência de validação entre cliente e
servidor, que é a origem mais comum de defeito em formulário com campo condicional —
exatamente o caso da ficha de triagem e da reclassificação de prioridade.

•  A equipe tem cinco pessoas, sendo três voltadas à interface e duas ao servidor. Com uma linguagem

só, quem trabalha na interface consegue ler e revisar o código do servidor, e vice-versa. Numa
equipe desse tamanho, isso é a diferença entre uma revisão real e uma aprovação de cortesia.

•  A plataforma escolhida para o servidor oferece guardas e interceptadores como recurso nativo, que
é a forma direta de implementar o controle de acesso por permissão e o registro automático de
auditoria, sem espalhar condicionais por todo o código.

O custo é conhecido e foi aceito: a plataforma de execução do servidor não lida bem com processamento
pesado de processador, por isso os relatórios de indicadores são calculados por agregação dentro do
próprio banco de dados; e o ecossistema muda rápido, por isso as versões são fixadas e só sobem por
decisão registrada da equipe.

3. Tecnologias por camada

3.1 Interface de usuário

Tecnologia

TypeScript

Versão

Para que serve

5

Linguagem usada em todas as camadas; garante que o contrato entre tela
e servidor seja verificado antes de rodar

Next.js com React

15 e 19

TanStack Query

React Hook Form com
Zod

Tailwind CSS com
primitivas Radix

5

—

4

3.2 Servidor e dados

Base das duas aplicações de tela; a renderização no servidor reduz o
volume de código enviado aos terminais mais modestos da clínica, e o
roteamento por segmento espelha a navegação por módulos adotada

Controla o estado vindo do servidor: cache, revalidação por intervalo e
atualização disparada por evento de tempo real, que é o comportamento
exigido pelo painel de fila

Formulários e validação, reaproveitando as mesmas regras usadas pelo
servidor

Estilo e componentes; as primitivas já entregam foco preso em janela
modal, atributos de acessibilidade corretos e navegação por teclado,
exigidos pelo requisito de acessibilidade

2

Tecnologia

NestJS

Prisma

PostgreSQL

Socket.IO

Módulo de
agendamento do NestJS

Credencial de acesso e
de renovação

Versão

Para que serve

11

6

16

4

—

—

Estrutura do serviço de negócio; módulos, guardas e interceptadores
mapeiam diretamente o controle de acesso e a auditoria automática

Acesso ao banco com tipagem forte, migrações versionadas e transação
interativa, esta última necessária na chamada da próxima senha

Banco de dados; oferece bloqueio de linha com salto de linhas já
bloqueadas, índices parciais e funções de janela usadas nos indicadores

Canal de tempo real do painel de fila, do telão e dos alertas; reconexão
automática e agrupamento por especialidade

Rotinas disparadas por tempo: expiração do prazo de tolerância e
varredura do tempo de espera das filas

Autenticação com sessão curta, adequada aos terminais compartilhados da
recepção

3.3 Qualidade e apoio ao desenvolvimento

Tecnologia

Vitest

Supertest

Playwright

Ferramenta de auditoria de
acessibilidade

Para que serve

Testes de unidade das regras de negócio; cada caso de teste cita no nome a
regra que verifica

Testes de integração da camada de entrada do servidor: códigos de resposta,
validação, permissão e formato de erro

Testes de ponta a ponta dos fluxos críticos, do acesso ao encerramento do
atendimento

Verificação automática das telas, integrada aos testes de ponta a ponta

ESLint e Prettier

Padrão único de código, verificado automaticamente

Husky, lint-staged e
Commitlint

pnpm com Turborepo

Docker Compose

GitHub Actions

Verificação antes do envio de alterações e padronização das mensagens de
registro

Repositório único com as três aplicações e os pacotes compartilhados, com
construção incremental

Ambiente local idêntico para os cinco integrantes, com banco de dados e
administrador de banco

Integração contínua: análise estática, testes, construção e verificação do
contrato a cada alteração proposta

4. Alternativas consideradas e recusadas

Alternativa

Motivo da recusa

Spring Boot no servidor com
React na interface

Duas linguagens e duas cadeias de construção; com apenas duas pessoas no
servidor, o custo de troca de contexto não compensa

Plataforma .NET com Angular

Nenhum integrante declarou familiaridade; a curva de aprendizado não cabe
no prazo do projeto

3

Alternativa

Motivo da recusa

FastAPI no servidor com React na
interface

Boa opção para os relatórios, mas o tempo real e o controle de acesso por
permissão exigiriam mais código próprio do que na plataforma escolhida

5. Onde cada tecnologia atende um requisito

Nenhuma escolha foi feita por preferência isolada: cada uma responde a um requisito da especificação.

Requisito

Tecnologia responsável pelo atendimento

Segurança e controle de
acesso

Guardas de permissão do NestJS, com a decisão baseada na lista de permissões
e não no nome do perfil

Proteção de dado sensível

Projeções distintas de senha definidas no pacote de contratos, mais verificação
automática na integração contínua

Desempenho

Disponibilidade

Índices do PostgreSQL que implementam a ordenação da fila, canal de tempo
real no lugar de consulta periódica pesada e paginação obrigatória

Verificações de saúde do serviço, reinício automático de contêiner e aviso de
perda de conexão na interface

Rastreabilidade e
conformidade legal

Interceptador global do NestJS gravando em tabela protegida contra alteração e
remoção pelo próprio banco

Usabilidade e acessibilidade

Primitivas Radix, escala tipográfica própria da aplicação pública e verificação
automática de acessibilidade

Escalabilidade

Cópia de segurança

Serviço sem estado em memória e adaptador externo do canal de tempo real
quando houver mais de uma instância

Extração diária agendada do PostgreSQL, com objetivos de ponto e de tempo de
recuperação definidos

4

