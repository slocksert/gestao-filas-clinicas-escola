Sistema de Gestão de Filas para Atendimento em
Clínicas-Escola

Especiﬁcação de Interface de Usuário (UI)

Modelo  de  arquitetura  aprovado:  Modelo  D  —  Híbrido. Workspace por perﬁl na entrada, painel de
ﬁla  com  painel  lateral  contextual  para  o  ciclo  senha–atendimento,  CRUD  tradicional  para cadastros
base, e painéis públicos como aplicações separadas.

Este documento antecede os wireframes. Cada tela é descrita em nível suﬁciente para prototipação sem
ambiguidade.

1. Premissas e decisões transversais

As quatro decisões levantadas antes da aprovação foram resolvidas conforme abaixo. Estão marcadas
como premissas por dependerem de validação ﬁnal da unidade.

P-01 | Nomenclatura e sinalização de status da senha

Oito status, com rótulo textual sempre presente. Cor é reforço, nunca o único portador de signiﬁcado
(exigência de RNF-06).

Status

Rótulo na UI

Cor de apoio

Ícone

Ocorre a partir de

aguardando

Aguardando

Cinza-azulado

Relógio

UC-06

chamada

Chamada

Azul

Alto-falante

UC-09

aguardando alocação

Aguardando recurso

Âmbar

Ampulheta

UC-10 (exceção)

em atendimento

Em atendimento

pausado

Pausado

Verde

Âmbar

pendente de validação

Pendente de validação

Roxo

Círculo preenchido

UC-11

Pausa

Escudo

UC-12

UC-13 (exceção)

encerrado

ausente

Encerrado

Ausente

Cinza

Veriﬁcação

Vermelho

X

UC-13

UC-16

desistência

Desistência

Cinza escuro

Seta de saída

UC-17

Prioridade  é  atributo  separado  do  status  e  nunca  compartilha a mesma cor. São três níveis: Urgente
(RN-03), Preferencial (RN-02) e Normal. Urgente e Preferencial recebem etiqueta textual à esquerda
do número da senha.

P-02 | Nível de acessibilidade alvo

WCAG 2.1 nível AA para todo o sistema. Para o painel público (T-11) e a consulta do paciente (T-12),
requisitos adicionais: contraste mínimo 7:1, corpo de texto do número da senha com no mínimo 96 px
em tela de 42", navegação completa por teclado, rótulos ARIA em regiões dinâmicas e anúncio sonoro
conﬁgurável a cada nova chamada.

P-03 | Exibição de dados de triagem em listas

1
Dados de saúde e motivo do atendimento não aparecem em listas, painéis ou no painel público. Listas
exibem apenas número da senha, iniciais ou nome parcial do paciente, especialidade, prioridade, status
e horário. O conteúdo clínico é acessível apenas na ﬁcha individual, mediante perﬁl autorizado, e cada
abertura gera registro de auditoria (RNF-02, RNF-05, RNF-07).

O painel público exibe exclusivamente número da senha, especialidade e local de atendimento. Nunca
nome completo.

P-04 | Manifestação do bloqueio por ausência de supervisor (RN-10)

Combinação de três elementos, e não apenas um botão desabilitado:

1. Banner persistente no topo da ﬁla afetada, com texto explicativo e nome da especialidade

bloqueada.

2. Botão "Chamar Próxima" desabilitado, com tooltip e texto acessível informando a causa.

3. Notiﬁcação automática à gestão da unidade, visível em T-17.

O bloqueio é por especialidade, não global. Demais ﬁlas seguem operando normalmente.

2. Fundamentos visuais

2.1 Grid e layout

Grid de 12 colunas, largura máxima de conteúdo 1440 px, calha de 24 px. Espaçamento em múltiplos
de 8 px. Área de toque mínima de 44 × 44 px em qualquer alvo interativo.

2.2 Tipograﬁa

Fonte  sem  serifa  de  alta  legibilidade.  Escala:  corpo  16  px,  secundário  14 px, rótulo 13 px, título de
seção 20 px, título de página 28 px. Corpo nunca abaixo de 16 px em telas operacionais, dado o perﬁl
de usuários previsto em RNF-06. Painéis públicos usam escala própria, deﬁnida em T-11.

2.3 Cores funcionais

Além das cores de status de P-01: primária de ação, superfície neutra, superfície elevada, borda, texto
primário,  texto  secundário,  e  as  quatro  semânticas  (sucesso,  alerta,  erro,  informação).  Todo  par
texto/fundo respeita 4.5:1 no mínimo.

2.4 Componentes compartilhados

Barra  superior  com  identiﬁcação  da  unidade,  seletor  de  contexto  de  perﬁl  e  menu  do  usuário.
Navegação lateral colapsável. Cartão de senha. Etiqueta de status. Etiqueta de prioridade. Tabela com
paginação  e  ﬁltros  persistentes.  Painel  lateral  contextual  (drawer).  Modal  de conﬁrmação. Modal de
justiﬁcativa.  Notiﬁcação  transitória  (toast).  Banner  de  bloqueio.  Cronômetro de espera. Estado vazio
ilustrado.

3. Padrões globais de comportamento

3.1 Estados obrigatórios de toda tela

2
Toda  tela  de  listagem  ou  consulta  especiﬁca  quatro  estados  além  do  estado  padrão:  carregando
(esqueleto  de  conteúdo,  nunca  spinner  isolado  em  tabelas),  vazio  (ícone,  frase  explicativa  e  ação
primária quando aplicável), erro de carga (mensagem e botão "Tentar novamente") e sem permissão
(mensagem neutra, sem revelar existência ou volume de dados — exigência de RNF-01).

3.2 Padrão de mensagens

Validação  de  campo aparece abaixo do campo, em vermelho, com ícone, e o campo recebe borda de
erro e aria-invalid. O foco vai para o primeiro campo inválido. Mensagens de sistema aparecem como
toast de 5 segundos, exceto erros irreversíveis, que exigem descarte manual. Toda mensagem descreve
a causa e o próximo passo, nunca apenas "erro".

3.3 Conﬁrmação e destrutividade

Exclusão de cadastro base, cancelamento de chamada e registro de ausência manual exigem modal de
conﬁrmação com nome do registro afetado. Reclassiﬁcação de prioridade exige modal de justiﬁcativa
com  campo  obrigatório  (RN-01).  Ações  destrutivas  usam  botão  vermelho,  sempre  à  direita,  com  o
botão neutro à esquerda.

3.4 Auditoria implícita

Toda tela que exibe ou altera dado sensível registra a operação em segundo plano. A UI não conﬁrma
esse  registro  ao  usuário,  mas  as  telas  que  abrem  prontuário  ou  triagem  exibem,  uma  única  vez  por
sessão, um aviso discreto de que consultas são registradas (RNF-07).

3.5 Responsividade

Três breakpoints: ≥1280 px (operacional completo), 768–1279 px (navegação colapsada, painel lateral
vira  tela  cheia),  <768  px  (somente  consulta e ações simples; alocação de recursos e conﬁguração de
perﬁs não são oferecidas em mobile). O painel público tem tratamento próprio.

4. Mapa de navegação por workspace

Workspace

Perﬁs

Telas acessíveis

Recepção

Recepcionista, Atendente administrativo

T-02, T-03/T-04 (Paciente), T-06, T-07, T-09, T-10, T-12, T-15

Triagem

Proﬁssional de triagem

T-02, T-06, T-07, T-08

Estudante

Estudante/Atendente

T-02, T-09 (leitura), T-13, T-14, T-15

Supervisão

Supervisor

T-02, T-08, T-09, T-13, T-14, T-17

Gestão

Gestão da unidade

T-02, T-09, T-16, T-17, T-18

Administração   Administrador, Coordenação, Secretaria

T-02, T-03, T-04, T-05, T-18

Público

Sem autenticação

T-11, T-12

Usuários com mais de um perﬁl usam o seletor de contexto na barra superior. A troca é explícita e o
workspace ativo aparece rotulado na barra, evitando ação em contexto errado.

5. Especiﬁcação por tela

T-01 | Login e recuperação de acesso

3

Objetivo. Autenticar o usuário e direcioná-lo ao workspace correspondente ao seu perﬁl.

Perﬁs. Todos os perﬁs autenticados.

Estrutura. Tela centrada em coluna única. Identiﬁcação da instituição no topo, formulário ao centro,
link de recuperação e informação de suporte abaixo. Sem menu lateral.

Campos.

Campo

Tipo

Obrigatório

Observações

Identiﬁcação

Texto

Senha

Senha com alternador de
visibilidade

Sim

Sim

Matrícula ou e-mail institucional

—

Manter conectado

Checkbox

Não

Desabilitado em terminais compartilhados

Ações. "Entrar" (primária, desabilitada até ambos os campos preenchidos, com estado de
carregamento). "Esqueci minha senha" (link secundário).

Validações  e  mensagens.  Credencial  inválida  retorna  mensagem  genérica  única,  sem  distinguir
usuário inexistente de senha incorreta. Após cinco tentativas, bloqueio temporário com tempo restante
informado. Conta sem perﬁl vinculado informa que o acesso precisa ser liberado pela administração.

Estados especiais. Sessão expirada retorna a esta tela com aviso e preserva a rota de destino para
redirecionamento após novo login.

Responsivo. Coluna única em todos os breakpoints.

Acessibilidade. Rótulos visíveis, não apenas placeholder. Erros anunciados via região aria-live. Foco
inicial no primeiro campo.

Rastreabilidade. RNF-01, RNF-02.

T-02 | Home / painel inicial por perﬁl

Objetivo. Dar ao usuário, em uma tela, a situação atual do que lhe compete e o acesso direto à sua

tarefa principal.

Perﬁs. Todos, com conteúdo variável.

Estrutura. Faixa de saudação e contexto de perﬁl ativo. Faixa de indicadores resumidos (3 a 4
cartões). Área principal com a lista da tarefa do momento. Coluna lateral com alertas e avisos.

Conteúdo por perﬁl.

Perﬁl

Cartões

Área principal

Coluna lateral

Recepçã
o

Aguardando, Chamadas ativas, Ausências hoje

Fila resumida da unidade

Bloqueios
ativos

Triagem

Triagens hoje, Aguardando emissão

Pacientes em triagem

—

Estudant
e

Supervisã
o

Meu atendimento atual, Concluídos hoje

Senha alocada a mim

Estudantes supervisionados / limite, Pendentes de validação

Atendimentos sob supervisão

Avisos do
supervisor

Encerramentos
pendentes

4

Perﬁl

Cartões

Área principal

Coluna lateral

Gestão

Tempo médio de espera, Filas em SLA excedido, Taxa
de ausência

Filas por especialidade

Alertas de SLA

Admini
stra ção

Usuários ativos, Cadastros pendentes

Atalhos de cadastro

—

Ações. Cada cartão é clicável e leva à tela ﬁltrada correspondente. Botão primário contextual por perﬁl
("Nova triagem", "Chamar próxima", "Iniciar atendimento").

Estados especiais. Sem dados no dia exibe estado vazio com a ação primária em destaque. Nenhum
indicador exibe dado de outra unidade ou especialidade fora da permissão do usuário.

Responsivo. Cartões empilham em coluna única abaixo de 768 px; coluna lateral vai para o ﬁm da
página.

Acessibilidade. Cartões são elementos focáveis com rótulo descritivo completo, incluindo valor e
unidade.

Rastreabilidade. RF-02, RF-10, RF-11, RNF-01.

T-03 | Lista de cadastro base

Objetivo. Consultar e ﬁltrar registros de qualquer das cinco entidades base, com layout único e
conteúdo parametrizado.

Perﬁs.  Conforme  tabela  de  variações  do  UC-01  a  UC-03:  Recepcionista  (Paciente),  Coordenação  e
Secretaria
e
Especialidade/Serviço),  Administração  da  unidade  (Sala).  Perﬁs  de  alocação  têm acesso somente de
consulta.

(Estudante/Atendente),  Coordenação

(Supervisor

acadêmica

acadêmica

Estrutura. Cabeçalho com título da entidade e botão "Novo [Entidade]". Barra de busca e ﬁltros.

Tabela paginada. Rodapé com contagem e controle de página.

Colunas e ﬁltros por entidade.

Entidade

Colunas

Filtros

Paciente

Nome, contato, vínculo institucional, situação

Nome, vínculo, situação de restrição

Estudante/
Aten dente

Nome, especialidade, nível de formação, disponibilidade

Especialidade, nível, disponibilidade

Supervisor

Nome, especialidades habilitadas, turnos, ocupação atual

Especialidade, turno, disponibilidade

Sala

Identiﬁcação, características, equipamentos, situação

Recurso, situação

Especialidad
e/S erviço

Nome, exige supervisão, ﬁlas vinculadas, situação

Situação, exige supervisão

Ações por linha. Editar (T-04), Consultar detalhe, Excluir. Excluir só é oferecida quando o registro
não possui vínculo ativo com ﬁlas, alocações ou atendimentos.

Validações  e  mensagens.  Tentativa  de  excluir  registro  vinculado  exibe  mensagem  explicando  o
vínculo  e  oferece a alternativa de inativar. A coluna de disponibilidade de supervisor exibe ocupação
sobre o limite conﬁgurado (RN-08), por exemplo "2 de 3".

5
Estados especiais. Filtro sem resultado exibe mensagem especíﬁca com opção de limpar ﬁltros. Filtros
persistem na sessão do usuário.

Responsivo. Abaixo de 1280 px a tabela reduz para colunas essenciais; abaixo de 768 px vira lista de
cartões.

Acessibilidade. Tabela com cabeçalhos associados, ordenação anunciada, e resultado de ﬁltro
comunicado por região aria-live.

Rastreabilidade. UC-03, RF-01, RN-06, RN-08, RN-09.

T-04 | Formulário de cadastro base (criar e editar)

Objetivo. Criar e alterar registros das cinco entidades base com um único padrão de formulário.

Perﬁs. Mesmos de T-03, com permissão de escrita.

Estrutura. Cabeçalho com título contextual ("Novo Paciente" / "Editar Paciente"). Formulário em
coluna única com agrupamento por seção. Barra de ações ﬁxa no rodapé com "Cancelar" e "Salvar".

Campos por entidade.

Entidade

Campos

Paciente

Nome, documento, contato, vínculo institucional (externo / aluno / servidor)

Veriﬁc
açã  o
de
dupli
cidade

Sim

Estudant
e/A
tendente

Nome, especialidade, nível de formação, habilitações, procedimentos autorizados, disponibilidade
por turno

Superviso
r

Nome, especialidades habilitadas, procedimentos habilitados, disponibilidade por turno, limite
de estudantes simultâneos

Sala

Identiﬁcação, características, equipamentos, recursos, situação

Nome, descrição, exige supervisão docente (sim/não), exige validação de encerramento
(sim/não), política de tolerância

Especia
lida
de/Servi
ço

Sim

Não

Não

Sim

Ações e estados. "Salvar" desabilitado enquanto houver campo obrigatório vazio e durante o envio.
"Cancelar" com alterações não salvas dispara conﬁrmação de descarte.

Validações  e  mensagens.  Campo  obrigatório  vazio  marca  o  campo  e  impede  o  envio.  Duplicidade
detectada  exibe  mensagem  indicando  o  registro  existente  e  oferece  link  para  abri-lo.  Limite  de
estudantes por supervisor aceita apenas inteiro positivo. Campos de habilitação e procedimento usam
seleção múltipla com busca.

Estados especiais. Em edição, campos que afetam alocações em curso exibem aviso de impacto antes
de salvar.

Responsivo. Coluna única sempre; barra de ações ﬁxa ao rodapé da viewport em telas pequenas.

Acessibilidade. Agrupamentos com ﬁeldset e legenda. Campos obrigatórios marcados textualmente,
não apenas por asterisco colorido.

Rastreabilidade. UC-01, UC-02, RF-01, RN-06, RN-08, RN-09.

T-05 | Perﬁs de usuário e permissões

Objetivo. Criar perﬁs e deﬁnir o conjunto de operações e informações que cada um acessa.

6

Perﬁs. Administrador do sistema.

Estrutura. Lista de perﬁs à esquerda, editor do perﬁl selecionado à direita. O editor apresenta a matriz
de permissões agrupada por módulo, com uma linha por operação.

Campos. Nome do perﬁl (obrigatório, único), descrição, e a matriz de permissões com quatro colunas
por  módulo:  consultar,  criar,  editar,  excluir.  Módulos  sensíveis  (Triagem,  Atendimento,  Auditoria)
possuem marcação visual adicional indicando que envolvem dado de saúde ou registro de auditoria.

Ações. "Salvar", "Cancelar", "Duplicar perﬁl", "Excluir perﬁl". Exclusão bloqueada quando há
usuários vinculados.

Validações e mensagens. Nome duplicado exibe mensagem de duplicidade. Perﬁl salvo sem nenhuma
permissão  de  consulta  gera  aviso  de  conﬁrmação,  por  resultar  em  acesso  inútil.  Alteração  de
permissões informa quantos usuários serão afetados.

Estados especiais. Perﬁs do sistema (Administrador) não são editáveis nem excluíveis; aparecem em

modo leitura com indicação.

Responsivo. Não oferecido abaixo de 768 px.

Acessibilidade. A matriz é uma tabela real com cabeçalhos de linha e coluna associados; cada caixa
tem rótulo acessível no formato "Módulo — operação".

Rastreabilidade. UC-04, RF-02, RNF-01.

T-06 | Busca de paciente e ﬁcha de triagem

Objetivo. Localizar o paciente e registrar a triagem inicial, direcionando-o à especialidade adequada.

Perﬁs. Proﬁssional de triagem.

Estrutura.  Duas  etapas  em  sequência  na  mesma  tela.  Etapa  1:  busca  de  paciente  com  resultado  e
resumo. Etapa 2: formulário de triagem, revelado após seleção do paciente. Indicador de etapa no topo.
Aviso de registro de consulta exibido uma vez por sessão (item 3.4).

Campos da etapa 2.

Campo

Tipo

Obrigatório   Observações

Motivo do atendimento

Texto longo

Sim

Dado sensível; não replicado em listas

Especialidade de destino

Seleção

Tipo de atendimento

Seleção

Classiﬁcação de prioridade

Seleção

Enquadramento legal

Seleção
múltipla

Justiﬁcativa clínica

Texto longo

Sim

Sim

Sim

Condiciona
l

Condiciona
l

Apenas especialidades ativas com ﬁla conﬁgurada

Primeira consulta, retorno, encaixe

Normal, Preferencial, Urgente

Habilitado quando prioridade Preferencial

Obrigatório quando prioridade Urgente

Observações

Texto longo

Não

—

Ações e estados. "Concluir Triagem" (primária, leva a T-07), "Cancelar", "Cadastrar novo paciente" a
partir da busca sem resultado.

7
Validações  e  mensagens.  Campo  obrigatório  vazio  bloqueia  a  conclusão.  Prioridade  Urgente  sem
justiﬁcativa  bloqueia  (RN-03).  Enquadramento  legal  marcado  sinaliza  automaticamente  prioridade
preferencial  e  exibe  aviso  informativo  (RN-02).  Paciente  com  restrição  ativa  por  reincidência  de
ausência exibe banner de alerta com a política aplicável, sem impedir a triagem (RN-13).

Estados especiais. Busca sem resultado oferece cadastro direto. Paciente com triagem em aberto exibe
aviso e oferece retomar em vez de duplicar.

Responsivo. Coluna única; etapas viram telas sequenciais abaixo de 768 px.

Acessibilidade. Campos condicionais anunciados ao aparecer. Mudança de etapa move o foco para o
título da nova etapa.

Rastreabilidade. UC-05, RF-03, RN-01, RN-02, RN-03, RNF-02, RNF-07.

T-07 | Emissão e conﬁrmação de senha

Objetivo. Conﬁrmar a triagem e gerar a senha posicionada na ﬁla correta.

Perﬁs. Proﬁssional de triagem; geração automática pelo sistema.

Estrutura.  Resumo  da  triagem  em  cartão  de  leitura  (sem  exibir  o  motivo  clínico  por  extenso,
conforme  P-03),  seleção  ﬁnal  do tipo de atendimento, e área de conﬁrmação. Após a emissão, a tela
apresenta a senha gerada em destaque, com opção de impressão.

Campos. Tipo de atendimento (editável antes de conﬁrmar), especialidade (leitura), prioridade
(leitura, com etiqueta).

Ações. "Conﬁrmar emissão" (primária), "Voltar à triagem", "Imprimir senha" e "Nova triagem" após a
emissão.

Validações  e  mensagens.  Especialidade  sem  ﬁla  conﬁgurada  bloqueia  a  emissão,  com  mensagem
indicando  a  causa  e  orientando  acionar  a  administração.  Especialidade  com  bloqueio  ativo  por
ausência de supervisor permite a emissão, mas informa que a chamada está suspensa (RN-10).

Estados especiais. Após emitir, a senha aparece com número em corpo grande, prioridade,
especialidade e posição inicial estimada na ﬁla.

Responsivo. Coluna única.

Acessibilidade. Número da senha anunciado como região aria-live após a geração; contraste reforçado
no bloco da senha.

Rastreabilidade. UC-06, RF-04, RN-04, RN-10.

T-08 | Reclassiﬁcação de prioridade

Objetivo. Alterar a prioridade de uma senha já emitida, com justiﬁcativa obrigatória e rastreável.

Perﬁs. Proﬁssional de triagem, Supervisor.

Estrutura. Modal aberto a partir do cartão da senha em T-09 ou da ﬁla. Cabeçalho com identiﬁcação
da  senha  e  prioridade  atual,  campo  de  nova  prioridade,  campo  de  justiﬁcativa,  e  histórico  de
reclassiﬁcações anteriores da mesma senha.

Campos. Nova prioridade (seleção, obrigatória, diferente da atual), Justiﬁcativa (texto longo,
obrigatória, mínimo deﬁnido pela unidade).

8

Ações. "Conﬁrmar reclassiﬁcação" (primária, desabilitada até justiﬁcativa preenchida), "Cancelar".

Validações e mensagens. Justiﬁcativa vazia impede a conﬁrmação, com mensagem explícita de que a
alteração exige registro do motivo (RN-01). Após conﬁrmar, toast informa a nova posição da senha na
ﬁla.

Estados especiais. Senha já chamada ou em atendimento não permite reclassiﬁcação; a ação não é

oferecida. Histórico vazio exibe "Nenhuma reclassiﬁcação anterior".

Responsivo. Modal ocupa a tela inteira abaixo de 768 px.

Acessibilidade. Foco preso ao modal, retorno ao elemento de origem ao fechar, título do modal
associado por aria-labelledby.

Rastreabilidade. UC-07, RF-03, RN-01, RN-04, RNF-05.

T-09 | Painel de ﬁlas (tela central do sistema)

Objetivo. Concentrar a operação: visualizar as ﬁlas, chamar a próxima senha e conduzir todo o ciclo
do atendimento pelo painel lateral, sem trocar de tela. É o núcleo do Modelo D.

Perﬁs. Atendente e Recepcionista (operação completa), Supervisor (operação e validação), Gestão e
Estudante (consulta).

Estrutura em quatro regiões.

1. Barra de contexto. Seletor de especialidade ou visão consolidada, seletor de turno, e contadores

de aguardando, chamadas, em atendimento e ausências do dia.

2. Faixa de avisos. Banners de bloqueio por ausência de supervisor (P-04) e de SLA excedido,

empilhados, com a especialidade nomeada.

3. Quadro de ﬁlas. Colunas por status (Aguardando, Chamada, Em atendimento, Pausado, Pendente

de validação), cada uma com cartões de senha ordenados segundo RN-04: prioridade, depois
horário de chegada, depois existência de agendamento prévio.

4. Painel lateral contextual. Abre ao selecionar um cartão e concentra as ações do ciclo. Cartão de
senha. Número da senha em destaque, etiqueta de prioridade quando não for Normal, nome parcial do
paciente, especialidade, tipo de atendimento, cronômetro de espera, e etiqueta de status. Não exibe

conteúdo clínico (P-03). Cartões com SLA excedido recebem borda de alerta e ícone, além do banner.

Ações da tela. "Chamar Próxima" (primária, por especialidade), "Rechamar", "Filtrar", "Atualizar". A
atualização é automática em intervalo curto, com indicação do horário da última sincronização.

Estados do painel lateral, por status da senha selecionada.

Status

Conteúdo do painel

Ações disponíveis

Aguardando

Dados da senha, posição, tempo de espera

Chamada

Dados da senha, cronômetro de tolerância

Reclassiﬁcar prioridade (T-08), Registrar
desistência (T-15)

Alocar recursos (T-10), Rechamar, Registrar
ausência (T-15)

Status

Conteúdo do painel

Ações disponíveis

9

Aguardando
recurso

Em
atendimento

Motivo da pendência, recurso faltante

Reavaliar alocação, Devolver à ﬁla

Recursos alocados, horário de início, duração

Pausar (T-13), Encerrar (T-13)

Pausado

Motivo e horário da pausa

Retomar (T-13)

Pendente de
validação

Desfecho registrado, responsável

Validar encerramento (T-13, perﬁl Supervisor)

Validações  e  mensagens.  "Chamar  Próxima"  desabilitado  quando  a  ﬁla  está  vazia  ou  quando  há
bloqueio  por  ausência  de  supervisor,  sempre  com  a  causa  em  texto  acessível (RN-10). Chamada de
senha  urgente  exibe  conﬁrmação  informando  que  a  ordem  padrão  foi  alterada  por  urgência  clínica
(RN-03). Tentativa de ação sem permissão não exibe o botão, em vez de exibi-lo desabilitado.

Estados especiais. Fila vazia exibe "Nenhuma senha aguardando" por coluna. Perda de conexão exibe
banner  persistente  e  congela  ações  de  escrita,  mantendo  a  leitura  do  último  estado  com  aviso  de
desatualização.

Responsivo. Entre 768 e 1279 px, o quadro reduz a duas colunas com seletor de status; o painel lateral
ocupa a tela. Abaixo de 768 px, apenas consulta e rechamada.

Acessibilidade. Cada coluna é uma região rotulada. Cartões são focáveis e operáveis por teclado. Nova
chamada é anunciada por aria-live educado. Cronômetros usam texto, não apenas cor.

Rastreabilidade. UC-08, UC-09, RF-04, RF-05, RN-03, RN-04, RN-10, RNF-03.

T-10 | Alocação de estudante, supervisor e sala

Objetivo. Associar os três recursos ao atendimento, com validação prévia de disponibilidade e
compatibilidade.

Perﬁs. Atendente; veriﬁcação automática pelo sistema.

Estrutura. Painel lateral (ou tela cheia abaixo de 1280 px) com três blocos sequenciais e verticais —
Estudante/Atendente,  Supervisor, Sala — cada um exibindo a sugestão automática do sistema, a lista
de alternativas compatíveis e o resultado da veriﬁcação. Rodapé com resumo e ação de conﬁrmação.

Conteúdo por bloco.

Bloco

Exibe

Estudante/
Aten dente

Nome, especialidade, nível, habilitações, situação atual

Regra veriﬁcada

RN-05, RN-06

Supervisor

Nome, especialidades habilitadas, ocupação sobre o limite ("2 de 3")

RN-07, RN-08

Sala

Identiﬁcação, características e equipamentos exigidos pelo procedimento

RN-09

Cada  item  da  lista  de  alternativas  indica  se  está  apto, e quando não está, o motivo textual: já possui
atendimento ativo, especialidade incompatível, fora do turno, limite de supervisão atingido, ou recurso
indisponível na sala.

Ações. "Conﬁrmar alocação" (primária, desabilitada enquanto houver bloco pendente), "Trocar
recurso" por bloco, "Manter aguardando recurso", "Cancelar".

10

Validações  e  mensagens.  Nenhum  supervisor  disponível  para  a especialidade dispara o bloqueio de
P-04, notiﬁca a gestão e mantém a senha em "Aguardando recurso". Nenhuma sala compatível mantém
a senha em "Aguardando recurso" com indicação do recurso faltante. Atendimento cuja especialidade
não exige supervisão docente oculta o bloco de supervisor e informa que a etapa não se aplica.

Estados especiais. Recurso que deixa de estar disponível entre a sugestão e a conﬁrmação exibe erro e
recarrega as alternativas, sem conﬁrmar a alocação.

Responsivo. Não oferecido abaixo de 768 px.

Acessibilidade. Cada bloco é um ﬁeldset com legenda. O motivo da inaptidão de um recurso está no
texto do item, não apenas em tooltip.

Rastreabilidade. UC-10, RF-06, RN-05, RN-06, RN-07, RN-08, RN-09, RN-10.

T-11 | Painel público de chamada

Objetivo. Exibir as chamadas em curso na sala de espera, de forma legível a distância e acessível.

Perﬁs. Sem autenticação. Terminal dedicado, sem interação.

Estrutura.  Área  principal  com  a  chamada  atual:  número  da  senha  em  corpo  muito  grande,
especialidade  e  local  de  atendimento.  Faixa  lateral  ou  inferior  com  as  últimas  quatro  chamadas
anteriores. Rodapé com relógio e identiﬁcação da unidade.

Conteúdo exibido. Somente número da senha, especialidade e local (P-03). Nunca nome completo,
prioridade clínica ou qualquer dado de saúde.

Comportamento. Nova chamada substitui a atual com transição breve e destaque temporário. Sinal
sonoro conﬁgurável acompanha cada chamada. Rechamada repete o destaque e o sinal.

Estados especiais. Sem chamadas ativas exibe mensagem neutra e o relógio. Perda de conexão exibe
indicação discreta e mantém a última chamada visível.

Responsivo. Projetado para 1920 × 1080 em orientação paisagem. Escala tipográﬁca proporcional à
altura da tela, com número da senha em no mínimo 96 px em 42".

Acessibilidade. Contraste mínimo 7:1. Sem dependência de cor. Chamada anunciada por aria-live
assertivo e por áudio. Sem animação piscante.

Rastreabilidade. UC-09, RF-05, RNF-06, RNF-02.

T-12 | Consulta de senha pelo paciente

Objetivo. Permitir ao paciente conferir sua posição na ﬁla e o tempo estimado de espera.

Perﬁs. Paciente, sem autenticação, mediante número da senha.

Estrutura. Coluna única. Campo de consulta pelo número da senha. Resultado com número,
especialidade, posição atual, tempo médio estimado e status.

Ações. "Consultar", "Atualizar". Atualização automática em intervalo deﬁnido.

Validações e mensagens. Senha inexistente ou de outro dia exibe mensagem neutra, sem revelar
dados. Dados insuﬁcientes para estimar tempo exibe apenas a posição na ﬁla, com nota explicativa.

Estados especiais. Senha já chamada exibe orientação de dirigir-se ao local indicado. Senha ausente
ou desistente exibe o status e orienta procurar a recepção.

11

Responsivo. Prioridade para uso em celular; alvos de toque ampliados.

Acessibilidade. Texto ampliável sem quebra de layout, corpo mínimo 18 px, contraste 7:1, mudança
de posição anunciada por aria-live.

Rastreabilidade. UC-08, RF-04, RNF-06.

T-13 | Tela de atendimento

Objetivo. Conduzir o atendimento do início ao encerramento, incluindo pausa, retomada e validação
do supervisor.

Perﬁs. Estudante/Atendente (condução), Supervisor (validação).

Estrutura.  Cabeçalho  com  senha,  paciente,  especialidade  e  recursos  alocados.  Cronômetro  de
duração.  Corpo  com  os  dados  da  triagem  (acesso  restrito,  com  registro de auditoria) e o registro do
atendimento. Barra de ações ﬁxa conforme o estado.

Ações por estado.

Estado

Alocado

Ações

Iniciar atendimento

Em atendimento

Pausar, Encerrar

Pausado

Retomar

Pendente de validação

Validar encerramento, Recusar validação (Supervisor)

Campos.  Motivo  da  pausa  (obrigatório  quando  exigido  pelo  protocolo),  Desfecho  no  encerramento
(obrigatório: alta, retorno agendado, encaminhamento interno, encaminhamento externo), Observações
do atendimento, Parecer do supervisor na validação.

Validações e mensagens. Recurso alocado indisponível no início impede a ação e devolve à alocação
(T-10).  Motivo  de  pausa  exigido  e  não  informado  impede  a  conﬁrmação.  Desfecho  não  informado
impede o encerramento. Quando o protocolo do serviço exige validação, o encerramento leva a senha
ao  status  "Pendente  de  validação"  e  notiﬁca  o  supervisor;  recusa  da  validação  mantém  o  status  e
registra o parecer (RN-11). Quando não exige, o encerramento é imediato.

Estados especiais. Recurso indisponível na retomada solicita nova alocação. Atendimento encerrado
com desfecho de encaminhamento encadeia diretamente para T-14.

Responsivo. Coluna única abaixo de 1280 px, barra de ações ﬁxa ao rodapé.

Acessibilidade. Cronômetro com texto acessível. Mudanças de status anunciadas. Área de dados
clínicos com aviso de acesso registrado.

Rastreabilidade. UC-11, UC-12, UC-13, RF-07, RN-11, RNF-02, RNF-05, RNF-07.

T-14 | Encaminhamento interno e externo

Objetivo. Registrar o encaminhamento deﬁnido como desfecho do atendimento.

Perﬁs. Estudante/Atendente, Supervisor.

12

Estrutura. Painel com duas variantes, determinadas pelo desfecho registrado em T-13. Cabeçalho
comum identiﬁcando o atendimento de origem.

Campos por variante.

Variante   Campos

Interno

Especialidade de destino (obrigatória), motivo, prioridade sugerida

Externo

Instituição ou serviço de destino (obrigatório), motivo
(obrigatório), observações

Efeito

Gera nova entrada de ﬁla vinculada
ao atendimento original

Registra sem gerar entrada de ﬁla

Ações. "Conﬁrmar encaminhamento" (primária), "Cancelar".

Validações e mensagens. Especialidade de destino inexistente ou inativa impede o encaminhamento,
com mensagem explícita. Campos obrigatórios do encaminhamento externo bloqueiam a conﬁrmação.
Após conﬁrmar encaminhamento interno, a mensagem informa o número da nova senha gerada e a ﬁla
de destino.

Estados especiais. Especialidade de destino com bloqueio ativo por ausência de supervisor permite o
encaminhamento, mas avisa que a chamada está suspensa.

Responsivo. Coluna única; painel vira tela cheia abaixo de 1280 px.

Acessibilidade. Nova senha gerada anunciada por aria-live.

Rastreabilidade. UC-14, UC-15, RF-08, RN-10.

T-15 | Ausência e desistência

Objetivo. Registrar a não realização do atendimento, distinguindo ausência de desistência para efeito
de indicadores.

Perﬁs. Atendente e Recepcionista; registro automático de ausência pelo sistema.

Estrutura. Modal com duas variantes, acionado a partir do painel lateral de T-09.

Campos.

Variante

Ausência

Campos

Obrigatório

Conﬁrmação, observação

Observação opcional

Desistência

Motivo da desistência

Opcional, conforme UC-17

Ações. "Conﬁrmar" (destrutiva, à direita), "Cancelar".

Validações  e  mensagens.  Registro  manual  de  ausência  antes  do  ﬁm  do  prazo  de  tolerância  exige
conﬁrmação explícita e informa o tempo restante. Ao conﬁrmar, o sistema libera os recursos alocados
e  informa  isso  na  mensagem de sucesso. Paciente que atinge o número de reincidências deﬁnido em
política  exibe  aviso  indicando  a  restrição  aplicada  e  o  que  ela  implica  para  novos  agendamentos
(RN-13).

Estados especiais. Ausência automática por expiração de tolerância ocorre sem modal, gerando
notiﬁcação no painel e registro no histórico do paciente (RN-12).

13
Responsivo. Modal em tela cheia abaixo de 768 px.

Acessibilidade. Foco preso ao modal; aviso de restrição em região aria-live.

Rastreabilidade. UC-16, UC-17, RF-09, RN-12, RN-13.

T-16 | Indicadores

Objetivo. Consultar os indicadores de ﬁla e atendimento por período e especialidade.

Perﬁs. Gestão da unidade.

Estrutura. Barra de ﬁltros no topo (período, especialidade, turno, unidade quando aplicável). Faixa de
indicadores em cartões. Área de gráﬁcos. Tabela detalhada com exportação.

Indicadores exibidos. Tempo médio de espera, tempo médio de atendimento, quantidade de pacientes
atendidos, taxa de ausência, quantidade de desistências e volume de atendimentos por especialidade e
período.

Ações. Aplicar e limpar ﬁltros, alternar visualização entre gráﬁco e tabela, exportar resultado.

Validações  e  mensagens.  Período  sem  dados suﬁcientes exibe "Sem dados disponíveis" no lugar do
gráﬁco, mantendo os ﬁltros. Período com volume muito baixo exibe nota indicando que a média pode
não ser representativa.

Estados especiais. Nenhum indicador exibe dado individual identiﬁcável de paciente; a granularidade
mínima é a especialidade e o período.

Responsivo. Cartões empilham; gráﬁcos rolam horizontalmente abaixo de 768 px.

Acessibilidade. Todo gráﬁco acompanha tabela equivalente acessível. Séries distinguidas por padrão
além de cor.

Rastreabilidade. UC-18, RF-10, RNF-02.

T-17 | Alertas de SLA e escalonamento

Objetivo. Apresentar à gestão as ﬁlas com tempo de espera acima do limite e permitir acionar
medidas de escalonamento.

Perﬁs. Gestão da unidade; Supervisor em modo consulta.

Estrutura.  Lista  de  alertas  ativos  ordenada  por  gravidade,  cada  item  com  especialidade,  tempo  de

espera  atual,  limite  deﬁnido,  número  de  senhas  afetadas  e  horário  de  disparo.  Abaixo,  histórico  de
alertas do período.

Ações por alerta. "Reforçar atendentes", "Abrir vaga extra", "Reconhecer alerta", "Ver ﬁla" (leva a
T-09 ﬁltrado). Cada ação de escalonamento registra autor e horário.

Validações  e  mensagens.  Alerta  reconhecido  muda  de  aparência  e  sai  do  topo,  sem  desaparecer.
Alerta  resolvido  automaticamente  quando  o  tempo  volta  ao  limite,  com  registro  do  horário  de
normalização.  Bloqueio  por  ausência  de  supervisor  (P-04) aparece nesta lista como alerta de origem
distinta.

Estados especiais. Sem alertas ativos exibe estado vazio positivo, mantendo o histórico acessível.

Responsivo. Lista em coluna única abaixo de 768 px; ações de escalonamento em menu por item.

14

Acessibilidade. Novos alertas anunciados por aria-live educado. Gravidade indicada por texto além de
cor.

Rastreabilidade. UC-19, RF-11, RN-10.

T-18 | Trilha de auditoria

Objetivo. Consultar os registros de operações realizadas pelos usuários, restrito a perﬁs autorizados.

Perﬁs. Administrador do sistema, Gestão com permissão de auditoria.

Estrutura.  Barra  de  ﬁltros  (usuário,  período,  tipo  de  operação,  módulo,  registro  afetado).  Tabela
cronológica com identiﬁcação do usuário, operação, data e horário, módulo e identiﬁcação do registro.
Painel lateral de detalhe do evento selecionado.

Conteúdo do detalhe. Usuário, perﬁl no momento da ação, operação, data e horário, registro afetado
e, quando aplicável, justiﬁcativa informada (caso de reclassiﬁcação de prioridade) e origem do acesso.

Ações. Aplicar e limpar ﬁltros, exportar resultado ﬁltrado, abrir detalhe.

Validações e mensagens. Filtro sem resultado exibe mensagem correspondente. Exportação registra a
própria exportação como evento de auditoria. Registros de auditoria são somente leitura; não há ação
de edição ou exclusão em nenhuma circunstância.

Estados especiais. Consultas a dados de prontuário aparecem com marcação distinta, atendendo à
exigência de trilha de consultas do RNF-07.

Responsivo. Não oferecido abaixo de 768 px.

Acessibilidade. Tabela com cabeçalhos associados e ordenação anunciada.

Rastreabilidade. UC-20, RF-12, RNF-01, RNF-05, RNF-07.

6. Matriz de rastreabilidade

6.1 Casos de uso para telas

UC

Telas

UC-01 Cadastrar registro base

UC-02 Editar registro base

UC-03 Consultar/listar registros base

UC-04 Cadastrar perﬁl e permissões

UC-05 Realizar triagem

UC-06 Emitir senha

UC-07 Reclassiﬁcar prioridade

UC-08 Consultar ﬁla e posição

UC-09 Chamar próxima senha

UC-10 Alocar recursos

UC-11 Iniciar atendimento

UC

UC-12 Pausar / retomar

UC-13 Encerrar atendimento

UC-14 Encaminhamento interno

UC-15 Encaminhamento externo

UC-16 Registrar ausência

UC-17 Registrar desistência

UC-18 Consultar indicadores

UC-19 Visualizar alertas de SLA

UC-20 Consultar trilha de auditoria

15

T-04

T-04

T-03

T-05

T-06

T-07

T-08

T-09, T-12

T-09, T-11

T-10

T-13

Telas

T-13

T-13

T-14

T-14

T-15

T-15

T-16

T-17

T-18

6.2 Regras de negócio para pontos de interface

RN

Onde se manifesta na UI

RN-01

T-08: justiﬁcativa obrigatória bloqueando a conﬁrmação

RN-02

T-06: enquadramento legal sinaliza prioridade preferencial; T-09: etiqueta no cartão

RN-03

T-06: justiﬁcativa clínica obrigatória; T-09: conﬁrmação ao furar a ﬁla

RN-04

T-07, T-09: ordenação dos cartões e cálculo de posição

RN-05,
RN-06

RN-07,
RN-08

T-10: bloco Estudante com motivo de inaptidão

T-10: bloco Supervisor com ocupação sobre o limite

RN-09

T-10: bloco Sala com recursos exigidos

RN-10

T-09: banner e botão desabilitado; T-10: origem do bloqueio; T-17: alerta à gestão

RN-11

T-13: status "Pendente de validação" e parecer do supervisor

RN-12

T-15: ausência automática por tolerância

RN-13

T-06 e T-15: aviso de restrição por reincidência

6.3 Requisitos não funcionais para decisões de interface

RNF

Decisão correspondente

RNF-01

Workspaces por perﬁl; ação sem permissão não é exibida; estado "sem permissão" neutro

RNF-02

P-03: dado clínico fora de listas e do painel público

RNF-03

T-09: atualização automática com indicação de sincronização

RNF-04

Banner de perda de conexão com congelamento de escrita e leitura preservada

RNF-05

Item 3.4: auditoria implícita em todas as telas sensíveis

RNF-06

P-02: WCAG AA no sistema, contraste 7:1 e escala ampliada em T-11 e T-12

RNF-07

Aviso de consulta registrada em T-06 e T-13; marcação distinta em T-18

RNF-08

T-09: visão consolidada multiespecialidade e ﬁltro por unidade

RNF-09

Sem manifestação direta de interface

7. Pontos que dependem de deﬁnição da unidade

16
Os  itens  abaixo estão especiﬁcados na estrutura, mas seus valores precisam ser parametrizados antes
do wireframe de alta ﬁdelidade: prazo de tolerância por especialidade, limite de SLA por ﬁla, número
de  reincidências  que  dispara  restrição,  limite  de  estudantes  por  supervisor,  e  tamanho  mínimo  da
justiﬁcativa de reclassiﬁcação.

17

