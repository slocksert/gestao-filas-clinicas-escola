Sistema de Gestão de Filas para
Atendimento em Clínicas-Escola

Especificação de Requisitos

1. Regras de Negócio

1.1 Triagem e priorização

RN-01 | Justificativa para reclassificação de prioridade
Alterações na classificação de prioridade de uma senha já emitida somente poderão ser realizadas mediante registro
de justificativa, permitindo rastrear o motivo da reclassificação (ex.: agravamento identificado após a triagem).

RN-02 | Prioridade legal
Pacientes enquadrados em prioridade legal (idosos, gestantes, lactantes, pessoas com deficiência, entre outros
previstos em norma) devem ter posição preferencial nas filas de atendimento, conforme legislação aplicável.

RN-03 | Urgência clínica
Casos de urgência clínica identificados na triagem poderão furar a fila padrão, desde que respeitado protocolo
específico e registrado o critério clínico que motivou a priorização.

1.2 Composição da fila

RN-04 | Critério de ordenação da fila
A posição do paciente na fila deve ser determinada, nesta ordem de precedência: (1) prioridade da senha, (2) horário
de chegada e (3) existência de agendamento prévio.

1.3 Alocação de recursos

RN-05 | Disponibilidade do estudante/atendente
Um estudante/atendente somente poderá ser alocado para um atendimento quando estiver disponível no turno
correspondente e não possuir outro atendimento ativo simultaneamente.

RN-06 | Compatibilidade do estudante com o atendimento
O estudante/atendente somente poderá ser alocado para atendimentos compatíveis com sua especialidade, nível de
formação, habilitações e procedimentos autorizados.

RN-07 | Disponibilidade do supervisor
Um atendimento que exija supervisão docente somente poderá ser iniciado quando existir supervisor habilitado e
disponível para aquele tipo de atendimento.

RN-08 | Limite de estudantes por supervisor
O sistema deve respeitar o limite máximo de estudantes que podem ser supervisionados simultaneamente por cada
supervisor, conforme configuração da unidade, especialidade ou serviço.

RN-09 | Compatibilidade da sala
A sala somente poderá ser atribuída ao atendimento quando estiver disponível e possuir as características,
equipamentos e recursos necessários para a especialidade ou procedimento.

RN-10 | Bloqueio de chamada por ausência de supervisor
A ausência de supervisor habilitado e disponível para uma especialidade deve impedir a chamada de novas senhas
para essa especialidade até que a situação seja regularizada.

1.4 Atendimento e encerramento

RN-11 | Validação do encerramento pelo supervisor
O encerramento do atendimento deve ser validado pelo supervisor docente responsável sempre que essa validação
for exigida pelo protocolo do serviço correspondente.

1.5 Ausências e desistências

RN-12 | Tratamento de senha não atendida
Uma senha chamada e não atendida dentro do prazo de tolerância deve retornar à fila ou ser marcada como ausência,
conforme política definida pela unidade.

RN-13 | Restrição por reincidência de ausência
A reincidência de ausências por parte de um mesmo paciente pode gerar restrição temporária ou exigência de
confirmação prévia para novos agendamentos, conforme política da unidade.

2. Requisitos Funcionais

2.1 Cadastros base

RF-01 consolida o CRUD das cinco entidades de cadastro base num único requisito genérico — a tabela abaixo
detalha o que varia entre elas. Perfis de usuário (RF-02) ficam à parte por tratarem de permissões, não de dados
operacionais.

RF-01 | Gerenciar cadastros base
O sistema deve permitir realizar o CRUD (criar, consultar, atualizar e excluir) de cada um dos cadastros base
listados na tabela abaixo, garantindo que todo registro esteja devidamente cadastrado antes de ser referenciado pelos
módulos de triagem, filas e alocação de recursos.

Entidades cobertas por RF-01:

Entidade
Paciente

Estudante/Atendente

Supervisor

Sala
Especialidade/Serviço

Dados / campos principais
Identificação, dados de contato e vínculo institucional
(paciente externo, aluno ou servidor)
Especialidade, nível de formação, habilitações e
procedimentos autorizados
Especialidades e procedimentos habilitados,
disponibilidade por turno
Características, equipamentos e recursos disponíveis
Referência para criação de filas, triagem e alocação de
recursos

Implementa
—

RN-06

—

RN-09
—

RF-02 | Gerenciar perfis de usuário e permissões de acesso
O sistema deve permitir realizar o CRUD de perfis de usuário, definindo as operações e informações que cada perfil
pode acessar.
Implementa: RNF-01.

2.2 Triagem e emissão de senha

RF-03 | Gerenciar triagem
O sistema deve permitir registrar a triagem inicial do paciente, coletando o motivo do atendimento e as informações
mínimas necessárias para direcionamento à especialidade ou serviço adequado, tratando dados de saúde como
informação sensível, com acesso restrito a perfis autorizados.
Implementa: RN-01, RN-02, RN-03.

2.3 Gestão de fila

RF-04 | Gerenciar fila de atendimento
O sistema deve permitir realizar o CRUD das filas de atendimento por especialidade ou serviço, incluindo criação,
consulta, atualização e exclusão de filas, além de permitir visualizar pacientes aguardando chamados, em
atendimento e demais status aplicáveis.
Implementa: RN-04.

2.4 Chamada e alocação

RF-05 | Gerenciar chamadas de senha
O sistema deve permitir realizar o CRUD das chamadas de senha, possibilitando registrar, consultar, atualizar e
cancelar chamadas, considerando automaticamente os critérios de prioridade, horário de chegada, tipo de
atendimento e disponibilidade dos recursos necessários.
Implementa: RN-10.

RF-06 | Gerenciar alocação de recursos
O sistema deve permitir realizar o CRUD das alocações de atendimento, possibilitando registrar, consultar, atualizar
e remover a associação entre estudante/atendente, supervisor e sala, validando previamente a disponibilidade e
compatibilidade dos recursos.
Implementa: RN-05, RN-06, RN-07, RN-08, RN-09.

2.5 Atendimento e encaminhamento

RF-07 | Gerenciar atendimentos
O sistema deve permitir realizar o CRUD dos registros de atendimento, possibilitando criar, consultar, atualizar e
excluir registros conforme as permissões do usuário, além de permitir registrar o início, pausa, retomada,
encaminhamento e encerramento do atendimento.
Implementa: RN-11.

RF-08 | Gerenciar encaminhamentos
O sistema deve permitir registrar encaminhamentos internos e externos ao final do atendimento. Encaminhamentos
internos entre especialidades da clínica-escola devem gerar automaticamente uma nova entrada de fila vinculada ao
atendimento original; encaminhamentos externos devem ser registrados separadamente.

2.6 Ausências e desistências

RF-09 | Gerenciar ausências e desistências
O sistema deve permitir registrar a ausência (no-show) de pacientes chamados e não atendidos dentro do prazo de
tolerância, bem como registrar desistências voluntárias com o motivo informado, distinguindo-se de ausências para
fins de indicadores.
Implementa: RN-12, RN-13.

2.7 Indicadores, alertas e auditoria

RF-10 | Gerenciar indicadores e consultas
O sistema deve permitir realizar consultas aos dados de atendimento e gerar indicadores relacionados às filas,
incluindo tempo médio de espera, tempo médio de atendimento, quantidade de pacientes atendidos, taxa de
ausência, quantidade de desistências e volume de atendimentos por especialidade e período.

RF-11 | Gerenciar alertas de SLA e escalonamento
O sistema deve monitorar o tempo de espera por fila e gerar alerta para a gestão da unidade quando esse tempo
ultrapassar o limite definido (SLA), permitindo o acionamento de medidas de escalonamento, como abertura de vaga
extra ou reforço de atendentes.

RF-12 | Consultar trilha de auditoria
O sistema deve permitir a consulta aos registros de auditoria das operações realizadas pelos usuários, incluindo
identificação do usuário, operação realizada, data e horário, restringindo esse acesso a perfis autorizados.

3. Requisitos Não Funcionais

RNF-01 | Segurança e controle de acesso
O sistema deve garantir controle de acesso baseado em perfis de usuário, permitindo que cada usuário visualize e
execute somente as operações autorizadas para sua função, especialmente no acesso a dados pessoais e informações
de saúde.

RNF-02 | Proteção de dados sensíveis
O sistema deve proteger os dados pessoais e informações de saúde armazenados e transmitidos, utilizando
mecanismos adequados de segurança e restringindo o acesso a informações sensíveis somente a usuários
autorizados.

RNF-03 | Desempenho
O sistema deve responder às operações relacionadas à consulta, atualização e gerenciamento das filas em condições
normais de utilização.

RNF-04 | Disponibilidade
O sistema deve permanecer disponível durante o horário de funcionamento da clínica-escola, com mecanismos de
recuperação em caso de falhas que possam interromper o gerenciamento das filas e dos atendimentos.

RNF-05 | Rastreabilidade e auditoria
O sistema deve manter registros de auditoria das operações relevantes realizadas pelos usuários, incluindo
identificação do usuário, operação realizada, data e horário.

RNF-06 | Usabilidade e acessibilidade
O sistema deve fornecer interfaces de exibição e chamada de senha acessíveis, considerando as necessidades de
pacientes idosos e pessoas com deficiência.

RNF-07 | Conformidade legal (LGPD)
O sistema deve tratar dados pessoais e de saúde em conformidade com a Lei Geral de Proteção de Dados (LGPD),
incluindo trilha de consultas a informações de prontuário.

RNF-08 | Escalabilidade
O sistema deve suportar o funcionamento simultâneo de múltiplas especialidades e, quando aplicável, múltiplas
unidades, sem degradação perceptível de desempenho.

RNF-09 | Backup e recuperação de desastres
O sistema deve realizar backup periódico dos dados e definir objetivos de tempo e ponto de recuperação
(RTO/RPO).

