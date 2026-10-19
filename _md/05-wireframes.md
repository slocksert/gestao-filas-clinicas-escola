E S P E C I F I C A Ç Ã O D E U I — W I R E F R A M E S · V E R S Ã O 2

Sistema de Gestão de Filas
para Atendimento em Clínicas-Escola

20 telas, organizadas pelos mesmos módulos da especiﬁcação de requisitos e casos de uso.

Baixa ﬁdelidade — foco em conteúdo e ﬂuxo, não em estilo visual ﬁnal.

Arquitetura: navegação por módulos (Modelo A), conforme a versão anterior dos wireframes.

Esta versão corrige as divergências apontadas na comparação com a especiﬁcação de UI e
acrescenta as telas dos casos de uso que estavam sem interface (UC-04, UC-07, UC-17).

Cobertura: UC-01 a UC-20 · RF-01 a RF-12 · RN-01 a RN-13 · RNF-01, RNF-02, RNF-05, RNF-06, RNF-07
N O T A D E R E V I S Ã O

Correções aplicadas nesta versão

# D I V E R G Ê N C I A N A V E R S Ã O A N T E R I O R C O R R E Ç Ã O A P L I C A D A O R I G E M 1 Botão "Chamar" em cada linha da ﬁla permitia furar a ordem Ação única "Chamar próxima" no topo da ﬁla (W-08)

RN-04

2 Telão público exibia posição e tempo estimado individuais Telão separado da consulta do paciente (W-09 e W-10) RNF-02, P-03 3 Nome completo do paciente na ﬁla e no telão Nome parcial na ﬁla; telão só com senha,

especialidade e local P-03 4 Sem tela de perﬁs de usuário e permissões Nova tela W-05 UC-04, RF-02 5 Reclassiﬁcação de prioridade só aparecia no log de auditoria Nova tela W-07, com justiﬁcativa obrigatória UC-07, RN-01

6 Desistência voluntária sem interface Nova tela W-16, separada da ausência UC-17, RF-09 7 Sem login nem tela inicial por perﬁl Novas telas W-01 e W-02 RNF-01, RF-02 8 Triagem sem campo de tipo de atendimento

Campo incluído em W-06; conﬁrmação da senha em W-07 UC-06 9 Dashboard sem quantidade de desistências Indicador incluído em W-17 RF-10 10 Atendimento sem os estados iniciar, retomar e pendente de validação

Estados detalhados em W-12 e W-13 UC-11 a UC-13, RN-11

A arquitetura de navegação por módulos foi mantida conforme os wireframes enviados. A especiﬁcação de UI em texto
descrevia o Modelo D (painel contextual); prevalece aqui o Modelo A, e a especiﬁcação deve ser atualizada nesse ponto.

M Ó D U L O · A C E S S O

W-01 · Login
RNF-01 — controle de acesso por perﬁl

Clínica-Escola — Gestão de Filas

Clínica-Escola

1

Sistema de Gestão de Filas

M A T R Í C U L A O U E - M A I L I N S T I T U C I O N A L

S E N H A

Manter conectado

Entrar

Esqueci minha senha

[área de mensagem de erro]

Mensagem de erro genérica: não distingue usuário inexistente de senha incorreta. Bloqueio temporário após 5 tentativas.

Wireframes de baixa ﬁdelidade — Sistema de Gestão de Filas para Clínicas-Escola 2

M Ó D U L O · A C E S S O

W-02 · Tela inicial por perﬁl
RF-02 · RNF-01 — conteúdo varia conforme o perﬁl autenticado

Acesso

Cadastros Triagem

Fila

Chamada

Clínica-Escola — Gestão de Filas Perﬁl ativo: Recepção | [usuário]

Atendimento
Bom dia, [usuário] —
Recepção

12

A G U A R D A N D O

Fila resumida da unidade

3

E M A T E N D I M E N T O

A U S Ê N C I A S H O J E

B L O Q U E I O A T I V O Ação principal

2

Encaminh. Ausências Indicadores Auditoria

S E N H A P A C I E N T E P R I O R I D A D E C H E G A D A E S P E C I A L I D A D

E F014 M. A. S. Urgente 08:12 Fisioterapia F015 J. P. L. Preferencial 08:20

Fisioterapia F016 R. C. M. Normal 08:25 Fonoaudiologia

1

Nova triagem

Avisos

■ Fisioterapia sem supervisor

O mesmo layout serve a todos os perﬁs; mudam os indicadores, a lista principal e a ação em destaque.

M Ó D U L O · C A D A S T R O S B A S E

W-03 · Lista de registros (genérica)
UC-03 · RF-01 — tela reutilizada para Paciente, Estudante/Atendente, Supervisor, Sala e Especialidade

Wireframes de baixa ﬁdelidade — Sistema de Gestão de Filas para Clínicas-Escola 3

Clínica-Escola — Gestão de Filas

Acesso

Cadastros Triagem

Fila

Chamada

Atendimento Encaminh. Ausências Indicadores Auditoria

Entidade: Paciente Buscar por nome, documento ou vínculo + Novo Paciente

N O M E C O N T A T O V Í N C U L O S I T U A Ç Ã O A Ç Õ E S [nome do registro] [telefone / e-mail] [externo]

Ativo Editar [nome do registro] [telefone / e-mail] [aluno] Ativo Editar [nome do registro] [telefone / e-mail]

Situação "Restrição" indica reincidência de ausências (RN-13).

[servidor] Restrição Editar [nome do registro] [telefone / e-mail] [externo] Ativo Editar ‹ 1 2 3 ... 8 ›

Colunas e ﬁltros mudam conforme a entidade selecionada. "Excluir" só aparece quando o registro não tem vínculo ativo.

M Ó D U L O · C A D A S T R O S B A S E

W-04 · Formulário de cadastro e edição
UC-01, UC-02 · RF-01 · RN-06, RN-08, RN-09

Wireframes de baixa ﬁdelidade — Sistema de Gestão de Filas para Clínicas-Escola 4

Clínica-Escola — Gestão de Filas

Acesso

Cadastros Triagem

Fila

Chamada

Registro já cadastrado — abrir existente

E S P E C I A L I D A D E *

Atendimento Encaminh. Ausências Indicadores Auditoria

N Í V E L D E F O R M A Ç Ã O *

Novo Estudante/Atendente

N O M E C O M P L E T O *

* campo obrigatório — "Salvar" ﬁca desabilitado enquanto houver
obrigatório vazio

H A B I L I T A Ç Õ E S ( S E L E Ç Ã O M Ú L T I P L A ) *

P R O C E D I M E N T O S A U T O R I Z A D O S *

D I S P O N I B I L I D A D E P O R T U R N O *

Supervisor tem, ainda, o campo "Limite de estudantes simultâneos" (RN-08); Sala tem equipamentos e recursos (RN-09).

Cancelar Salvar

Campos variam por entidade. Veriﬁcação de duplicidade exibida abaixo do campo Nome, com link para o registro existente.

Wireframes de baixa ﬁdelidade — Sistema de Gestão de Filas para Clínicas-Escola 5

M Ó D U L O · C A D A S T R O S B A S E

W-05 · Perﬁs de usuário e permissões
UC-04 · RF-02 · RNF-01

Clínica-Escola — Gestão de Filas

Acesso

Cadastros Triagem

Fila

Chamada

Atendimento Encaminh. Ausências Indicadores Auditoria

Perﬁs

Administrador (sistema) Recepção

Triagem

Estudante

Supervisão

Gestão

+ Novo perﬁl
Editando: Recepção

N O M E D O P E R F I L *

Recepção

Matriz de permissões

M Ó D U L O C O N S U L T A R C R I A R E D I T A R E X C L U I R Cadastros

base ✓ ✓ ✓ — Triagem ● sensível ✓ ✓ — — Fila e chamada ✓ ✓

✓ — Atendimento ● sensível ✓ — — — Auditoria ● restrito — — —

—

● módulos que envolvem dado de saúde ou registro de auditoria

» Tela nova — o caso de uso UC-04 não tinha interface na versão anterior.
Módulos com dado de saúde ou auditoria recebem marcação. Exclusão bloqueada quando há usuários vinculados.

M Ó D U L O · T R I A G E M

W-06 · Buscar paciente e registrar triagem
UC-05 · RF-03 · RN-01, RN-02, RN-03 · RNF-02, RNF-07

Wireframes de baixa ﬁdelidade — Sistema de Gestão de Filas para Clínicas-Escola 6

Clínica-Escola — Gestão de Filas

Acesso

Cadastros Triagem

Fila

Chamada

Atendimento

Buscar paciente por nome ou documento Buscar + Cadastrar paciente [nome do paciente]

· vínculo: [aluno / servidor / externo] · histórico: 2 ausências

■ Paciente com restrição por reincidência de ausências (RN-13) — triagem
permitida

Encaminh. Ausências Indicadores Auditoria

E S P E C I A L I D A D E D E D E S T I N O *

T I P O D E A T E N D I M E N T O * ( 1 ª C O N S U L T A / R E T O R N O / E N
C A I X E )
M O T I V O D O A T E N D I M E N T O * ( D A D O S E N S Í V E L )

C L A S S I F I C A Ç Ã O D E P R I O R I D A D E *

Normal Preferencial legal Urgência clínica

J U S T I F I C A T I V A * ( O B R I G A T Ó R I A S E P R I O R I D A D E ≠ N O

R M A L ) Cancelar Concluir triagem

» Incluído o campo "Tipo de atendimento", que faltava para o ﬂuxo de emissão da senha (UC-06).
Motivo do atendimento é dado sensível: não aparece em listas nem no telão público (RNF-02).

M Ó D U L O · T R I A G E M

W-07 · Senha emitida e reclassiﬁcação de prioridade
UC-06, UC-07 · RF-03, RF-04 · RN-01, RN-04

Wireframes de baixa ﬁdelidade — Sistema de Gestão de Filas para Clínicas-Escola 7

Clínica-Escola — Gestão de Filas

Acesso

Cadastros Triagem

Fila

Chamada

Fisioterapia · 1ª consulta
URGENTE

Posição estimada na ﬁla: 1ª

Atendimento Encaminh. Ausências Indicadores Auditoria

Senha emitida

Imprimir senha Nova triagem
Reclassiﬁcar prioridade [modal]

F014

Senha F014 · prioridade atual: Urgente

N O V A P R I O R I D A D E *

Normal Preferencial Urgente

J U S T I F I C A T I V A D A R E C L A S S I F I C A Ç Ã O *

Histórico: nenhuma reclassiﬁcação anterior

Cancelar Conﬁrmar

"Conﬁrmar" desabilitado enquanto a justiﬁcativa estiver vazia (RN-01)

» Tela de reclassiﬁcação criada — antes o UC-07 só existia como linha no log de auditoria.
Reclassiﬁcação só é oferecida para senha ainda não atendida; a justiﬁcativa vai para a trilha de auditoria (RNF-05).

Wireframes de baixa ﬁdelidade — Sistema de Gestão de Filas para Clínicas-Escola 8

M Ó D U L O · F I L A

W-08 · Painel de ﬁla (equipe)
UC-08, UC-09 · RF-04, RF-05 · RN-04, RN-10

Clínica-Escola — Gestão de Filas

Acesso

Cadastros Triagem Fila

disponível — chamadas bloqueadas (RN-10)

Especialidade: Fisioterapia Tipo de atendimento Chamar próxima ■ Fisioterapia sem supervisor

Chamada

Atendimento Encaminh.

12

A G U A R D A N D O

3

18 min

E M A T E N D I M E N T O

T E M P O M É D I O D E E S P E R A

Ausências Indicadores Auditoria

S E N H A P A C I E N T E P R I O R I D A D E C H E G A D A S T A T U S A Ç Õ E S F014 M. A. S.

R. C. M. Normal 08:25 Aguardando Detalhes F013 A. B. T. Normal 07:58 Em atendimento
Detalhes Nome exibido de forma parcial; motivo do atendimento não aparece na ﬁla (RNF-02).
"Detalhes" abre a senha com as ações permitidas ao perﬁl: reclassiﬁcar, registrar ausência ou
desistência.

Urgente 08:12 Aguardando Detalhes F015 J. P. L. Preferencial 08:20 Aguardando Detalhes F016

» Removido o botão "Chamar" por linha; a chamada agora é uma ação única que respeita a ordenação da ﬁla.
Ordenação ﬁxa: prioridade, depois horário de chegada, depois agendamento prévio (RN-04).

Wireframes de baixa ﬁdelidade — Sistema de Gestão de Filas para Clínicas-Escola 9

M Ó D U L O · F I L A

W-09 · Telão público da sala de espera
UC-09 · RF-05 · RNF-02, RNF-06

Clínica-Escola — Gestão de Filas

CHAMANDO

F014

Fisioterapia — Sala 03

F013
Sala 02

CHAMADAS ANTERIORES

Não exibe nome do paciente, prioridade clínica
nem qualquer dado de saúde.

F012 Sala 01
F011 Sala 03

F010 Sala 02

» Separado da consulta do paciente. Exibe apenas senha, especialidade e local — sem nome nem posição individual.
Fonte grande, contraste alto, sinal sonoro a cada chamada. Projetado para 1920×1080 em orientação paisagem.

Wireframes de baixa ﬁdelidade — Sistema de Gestão de Filas para Clínicas-Escola 10

M Ó D U L O · F I L A

W-10 · Consulta de senha pelo paciente
UC-08 (visão do paciente) · RF-04 · RNF-06

Clínica-Escola — Gestão de Filas

Consultar minha senha

Número da senha Consultar

F015

Fisioterapia

~22 min

T E M P O E S T I M A D O

4ª

S U A P O S I Ç Ã O

Senha inexistente exibe mensagem neutra, sem revelar dados. Sem dados para estimativa, exibe apenas a posição.

Aguarde ser chamado no painel da sala de espera.

» Tela nova, separada do telão — a posição e o tempo estimado são informação individual do paciente.
Uso principal em celular. Texto ampliável sem quebra de layout; posição atualizada e anunciada por leitor de tela.

M Ó D U L O · C H A M A D A E A L O C A Ç Ã O

W-11 · Alocação de recursos ao atendimento
UC-10 · RF-06 · RN-05 a RN-10

Wireframes de baixa ﬁdelidade — Sistema de Gestão de Filas para Clínicas-Escola 11

Clínica-Escola — Gestão de Filas

Acesso

Cadastros

Triagem

Fila

Senha F014 · chamada às 08:31 ·
Fisioterapia

■ [nome] — atendimento ativo
■ [nome] — fora do turno

[nome sugerido]

Chamada Atendimento Encaminh. Ausências

Indicadores Auditoria

1 · Estudante/atendente

[nome sugerido]

Disponível e compatível (RN-05, RN-06)

Alternativas
■ [nome] — apto

Trocar sugestão
2 · Supervisor

Equipamentos compatíveis (RN-09)

Alternativas
■ [nome] — apto
■ [nome] — atendimento ativo ■ [nome] — fora do
turno

Trocar sugestão

Habilitado — 2 de 3 estudantes (RN-07, RN-08)

Alternativas
■ [nome] — apto
■ [nome] — atendimento ativo
■ [nome] — fora do turno

Trocar sugestão
3 · Sala

[Sala 03]

Se o atendimento não exige supervisão docente, o bloco 2 desaparece do layout.

Sem supervisor disponível: senha ﬁca "aguardando recurso", chamadas da especialidade são bloqueadas e a gestão é notiﬁcada (RN-10).

Manter aguardando Cancelar Conﬁrmar alocação

Cada alternativa indica por que está indisponível: atendimento ativo, especialidade incompatível, fora do turno ou limite atingido.

M Ó D U L O · A T E N D I M E N T O

W-12 · Atendimento em andamento
UC-11, UC-12 · RF-07

Wireframes de baixa ﬁdelidade — Sistema de Gestão de Filas para Clínicas-Escola 12

Clínica-Escola — Gestão de Filas

Acesso

Cadastros

Triagem

Fila

Chamada

Atendimento Encaminh. Ausências

Indicadores Auditoria

O B S E R V A Ç Õ E S D O A T E N D I M E N T O

Senha F014 · [nome do paciente] · Sala 03

Estudante: [nome] · Supervisor: [nome] · Início: 08:33 · Duração: 12
min

Dados da triagem · acesso restrito, consulta registrada em

auditoria (RNF-07) [motivo do atendimento]

Barra de ações — varia conforme o estado da senha
EM ATENDIMENTO

Iniciar atendimento
Estado: Em atendimento

Pausar Encerrar atendimento
Estado: Pausado Retomar

Estado: Alocado

Pausa exige motivo quando o protocolo do serviço determinar; sem o motivo, a conﬁrmação é bloqueada.

» Estados "iniciar" e "retomar" explicitados; a versão anterior mostrava apenas pausar e encerrar.
Cronômetro em texto, não apenas cor. Recurso indisponível no início ou na retomada devolve o ﬂuxo para a alocação.

M Ó D U L O · A T E N D I M E N T O

W-13 · Encerramento e validação do supervisor
UC-13 · RF-07 · RN-11

Wireframes de baixa ﬁdelidade — Sistema de Gestão de Filas para Clínicas-Escola 13

Clínica-Escola — Gestão de Filas

Acesso

Cadastros

Triagem

Fila

Chamada

Atendimento Encaminh. Ausências

Indicadores Auditoria

Modal de encerramento

Encerrar atendimento — senha F014

D E S F E C H O *

Alta

Retorno agendado

Encaminhamento interno

Encaminhamento externo

Recusa mantém o atendimento como "pendente de validação" e registra o
parecer para auditoria.
Validação do supervisor (se o protocolo exigir)

P A R E C E R D O S U P E R V I S O R

O B S E R V A Ç Õ E S D O D E S F E C H O

PENDENTE DE VALIDAÇÃO

Recusar validação Validar

Cancelar Encerrar

Senha F014 · desfecho: Alta

Responsável: [estudante] · encerrado às 09:02

» Incluído o estado "pendente de validação" e a tela do supervisor, que faltavam (RN-11).
Sem exigência de validação, o encerramento é imediato. Com exigência, a senha ﬁca "pendente de validação".

Wireframes de baixa ﬁdelidade — Sistema de Gestão de Filas para Clínicas-Escola 14

M Ó D U L O · E N C A M I N H A M E N T O

W-14 · Registrar encaminhamento
UC-14, UC-15 · RF-08

Clínica-Escola — Gestão de Filas

Acesso

Cadastros Triagem

Fila

Chamada

T I P O D E E N C A M I N H A M E N T O *

Atendimento Encaminh. Ausências Indicadores Auditoria

Interno (dentro da clínica-escola) Externo

Atendimento de origem: F014 · Fisioterapia ·
encerrado às 09:02

Se interno

E S P E C I A L I D A D E D E D E S T I N O *

M O T I V O D O E N C A M I N H A M E N T O

→ gera nova entrada de ﬁla automaticamente

Se externo

I N S T I T U I Ç Ã O / S E R V I Ç O D E D E S T I N O *

M O T I V O *

Cancelar Conﬁrmar encaminhamento

→ registrado sem gerar entrada de ﬁla

Especialidade de destino inativa impede o encaminhamento. Após conﬁrmar, a mensagem informa o número da nova senha gerada.

Encaminhamento interno gera nova entrada de ﬁla vinculada ao atendimento original; o externo é apenas registrado.

Wireframes de baixa ﬁdelidade — Sistema de Gestão de Filas para Clínicas-Escola 15

M Ó D U L O · A U S Ê N C I A S E D E S I S T Ê N C I A S W-15 ·
Registrar ausência (no-show) UC-16 ·
RF-09 · RN-12, RN-13

Clínica-Escola — Gestão de Filas

Acesso

Cadastros Triagem Fila

Chamada
Senhas chamadas com tolerância em curso

Atendimento Encaminh. Ausências Indicadores Auditoria

Modal de ausência manual

Marcar F013 como ausente

A tolerância ainda não expirou (restam 02:14).

Os recursos alocados serão liberados para a ﬁla.

O B S E R V A Ç Ã O

S E N H A P A C I E N T E C H A M A D A T O L E R Â N C I A S T A T U S A Ç Õ E S F013 A. B. T. 08:31 restam

02:14 Chamada Marcar ausente F012 L. F. R. 08:26 expirada Ausente (auto) Detalhes

Cancelar Conﬁrmar ausência
Aviso de reincidência

■ Paciente atingiu o limite de ausências da política (RN-13)

Efeito aplicado:

– restrição temporária para novos agendamentos
– ou exigência de conﬁrmação prévia,
– conforme a política deﬁnida pela unidade.

O aviso também aparece na triagem seguinte do mesmo paciente
(W-06).

Ausência automática ao expirar a tolerância ocorre sem modal, apenas com notiﬁcação no painel e registro no histórico.

Wireframes de baixa ﬁdelidade — Sistema de Gestão de Filas para Clínicas-Escola 16

M Ó D U L O · A U S Ê N C I A S E D E S I S T Ê N C I A S

W-16 · Registrar desistência voluntária
UC-17 · RF-09

Clínica-Escola — Gestão de Filas

Acesso

Cadastros Triagem

Fila

Chamada

Atendimento Encaminh. Ausências Indicadores Auditoria

Modal de desistência

Registrar desistência — senha F016

Status atual: aguardando · chegada 08:25

S O L I C I T A D O P O R

Paciente Equipe

Distinção nos indicadores

S I T U A Ç Ã O O R I G E M I N D I C A D O R Ausência não compareceu taxa

M O T I V O D A D E S I S T Ê N C I A ( O P C I O N A L )

de ausência

Desistência saída voluntária n° de desistências

A senha sai da ﬁla ativa e os recursos são liberados.

Cancelar Conﬁrmar

Motivos frequentes de desistência alimentam a

análise de tempo de espera junto aos alertas de SLA.

» Tela nova — o UC-17 não tinha interface própria; havia apenas "marcar ausente".
Desistência é contabilizada separadamente da ausência nos indicadores (RF-10).

Wireframes de baixa ﬁdelidade — Sistema de Gestão de Filas para Clínicas-Escola 17

M Ó D U L O · I N D I C A D O R E S

W-17 · Painel de indicadores
UC-18 · RF-10

Clínica-Escola — Gestão de Filas

Acesso

Triagem

Fila

Cadastros
Período: 01/09 a 30/09 Especialidade: todas Turno: todos Exportar

Chamada

Atendimento

21 min

T E M P O M É D I O E S P E R A

14 min

T E M P O M É D I O A T E N D I M .

8%

11

142

T A X A D E A U S Ê N C I A

D E S I S T Ê N C I A S

A T E N D I M E N T O S

tabela equivalente acessível; séries distinguidas por padrão além de tom (RNF-06).

Encaminh. Ausências Indicadores Auditoria

Volume de atendimentos por especialidade

[espec. 1] [espec. 2] [espec. 3] [espec. 4] [espec. 5] [espec. 6] Todo gráﬁco acompanha

» Incluído o indicador "desistências", exigido pelo RF-10 e ausente na versão anterior.
Granularidade mínima é especialidade e período — nenhum indicador expõe dado individual de paciente (RNF-02).

M Ó D U L O · A L E R T A S E A U D I T O R I A

W-18 · Alertas de SLA e trilha de auditoria
UC-19, UC-20 · RF-11, RF-12 · RN-10 · RNF-05, RNF-07

Wireframes de baixa ﬁdelidade — Sistema de Gestão de Filas para Clínicas-Escola 18

Clínica-Escola — Gestão de Filas

Acesso

Cadastros Triagem

Fila

Chamada

Atendimento Encaminh. Ausências Indicadores Auditoria

Alertas ativos

Trilha de auditoria

Usuário Período Tipo de operação Módulo

■ Fisioterapia — espera acima do SLA (32 min > 25 min)

Ver ﬁla Acionar reforço Reconhecer

 ■

Fisioterapia — chamadas bloqueadas por ausência de supervisor (RN-10)

U S U Á R I O P E R F I L O P E R A Ç Ã O R E G I S T R O D A T A / H O R A [usuário] Triagem Reclassiﬁcou prioridade

— justiﬁcativa registrada F014 01/09 08:15 [usuário] Supervisão Validou encerramento F009 01/09 07:58

[usuário] Recepção Consultou prontuário ● F011 01/09 07:44 ● consultas a dados de prontuário recebem
marcação distinta, atendendo à trilha exigida pelo RNF-07.

Registros de auditoria são somente leitura. A própria exportação é registrada como evento.

Wireframes de baixa ﬁdelidade — Sistema de Gestão de Filas para Clínicas-Escola 19

