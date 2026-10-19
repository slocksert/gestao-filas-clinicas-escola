Sistema de Gestão de Filas para Atendimento em Clínicas-
Escola
DAS — Documento de Arquitetura de Software
1

Sumário
1. Introdução ..................................................................................................................................... 4
1.1 Finalidade ................................................................................................................................................... 4
1.2 Escopo ........................................................................................................................................................ 4
1.3 Definições e convenções de identificação ................................................................................................. 4
1.4 Documentos de referência ........................................................................................................................ 4
1.5 Como este documento está organizado .................................................................................................... 5
2. Representação arquitetural ............................................................................................................ 6
2.1 Estilo arquitetural adotado ........................................................................................................................ 6
2.2 Princípio de organização interna ............................................................................................................... 6
3. Metas e restrições de arquitetura ................................................................................................... 7
3.1 Metas ......................................................................................................................................................... 7
3.2 Restrições ................................................................................................................................................... 7
3.3 Premissas transversais herdadas da especificação de interface ............................................................... 7
4. Visão de casos de uso ..................................................................................................................... 9
4.1 Casos de uso arquiteturalmente significativos .......................................................................................... 9
4.2 Fluxo principal do sistema, do ponto de vista arquitetural ....................................................................... 9
5. Visão lógica ...................................................................................................................................11
5.1 As três aplicações ..................................................................................................................................... 11
5.2 Pacotes compartilhados ........................................................................................................................... 11
5.3 Módulos do serviço de negócio ............................................................................................................... 12
5.4 Organização da aplicação operacional ..................................................................................................... 12
5.5 Regra de visibilidade de ação ................................................................................................................... 13
6. Visão de processos e concorrência .................................................................................................14
6.1 Concorrência na chamada de senha ........................................................................................................ 14
6.2 Concorrência em mudança de estado ..................................................................................................... 14
6.3 Canal de eventos em tempo real ............................................................................................................. 14
6.4 Degradação em caso de perda de conexão ............................................................................................. 15
6.5 Rotinas disparadas por tempo ................................................................................................................. 15
6.6 Custo computacional ............................................................................................................................... 15
7. Visão de dados ..............................................................................................................................16
7.1 Convenções .............................................................................................................................................. 16
7.2 Agrupamentos de entidades .................................................................................................................... 16
7.3 Máquina de estados da senha ................................................................................................................. 16
7.4 Invariantes garantidas no banco de dados .............................................................................................. 17
7.5 Índice que implementa a ordenação da fila ............................................................................................ 17
7.6 Parâmetros que dependem da unidade .................................................................................................. 18
7.7 Retenção e proteção de dados pessoais .................................................................................................. 18
8. Visão de implantação ....................................................................................................................19
8.1 Ambientes ................................................................................................................................................ 19
8.2 Unidades implantáveis ............................................................................................................................. 19
8.3 Terminais e clientes ................................................................................................................................. 19
8.4 Saúde e observabilidade .......................................................................................................................... 20
2

9. Mapeamento da interface de programação ....................................................................................21
9.1 Convenções gerais ................................................................................................................................... 21
9.2 Autenticação e sessão .............................................................................................................................. 21
9.3 Perfis e usuários ....................................................................................................................................... 22
9.4 Cadastros base ......................................................................................................................................... 22
9.5 Triagem, senha e prioridade .................................................................................................................... 24
9.6 Fila, chamada e telão ............................................................................................................................... 24
9.7 Alocação de recursos ............................................................................................................................... 25
9.8 Atendimento ............................................................................................................................................ 26
9.9 Encaminhamento ..................................................................................................................................... 26
9.10 Ausência e desistência ........................................................................................................................... 27
9.11 Indicadores, alertas e auditoria ............................................................................................................. 27
9.12 Operações públicas ................................................................................................................................ 28
9.13 Verificações de saúde ............................................................................................................................ 28
9.14 Autorização ............................................................................................................................................ 28
9.15 Projeções de senha e proteção de dado sensível .................................................................................. 29
9.16 Tratamento de erros .............................................................................................................................. 29
9.17 Versionamento do contrato ................................................................................................................... 30
10. Mapeamento das tecnologias ......................................................................................................31
10.1 Decisão estruturante: uma linguagem única ......................................................................................... 31
10.2 Tecnologias por camada ........................................................................................................................ 31
10.3 Alternativas consideradas e recusadas .................................................................................................. 32
10.4 Como a tecnologia atende cada requisito não funcional ...................................................................... 33
11. Decisões arquiteturais registradas ................................................................................................34
11.1 Linguagem única em todas as camadas ................................................................................................. 34
11.2 Navegação por módulos, com painel lateral apenas na fila .................................................................. 34
11.3 Separação de dado sensível no contrato ............................................................................................... 34
12. Estratégia de qualidade ...............................................................................................................36
12.1 Níveis de teste........................................................................................................................................ 36
12.2 Critério de conclusão de uma tarefa ...................................................................................................... 36
12.3 Estados obrigatórios de tela .................................................................................................................. 36
13. Rastreabilidade ...........................................................................................................................37
13.1 Requisitos funcionais e onde são realizados .......................................................................................... 37
13.2 Regras de negócio e onde são garantidas .............................................................................................. 37
13.3 Divisão de responsabilidades pela arquitetura ...................................................................................... 38
14. Riscos e pendências .....................................................................................................................39
14.1 Riscos arquiteturais ................................................................................................................................ 39
14.2 Pendências registradas .......................................................................................................................... 39
14.3 Evolução prevista ................................................................................................................................... 39
3

1. Introdução
1.1 Finalidade
Este documento descreve a arquitetura do Sistema de Gestão de Filas para Atendimento em Clínicas-
Escola. Ele reúne, em um só lugar, as decisões estruturais que orientam a construção do software, a
divisão do sistema em partes, a tecnologia adotada em cada camada, o mapeamento completo das
operações da interface de programação e a forma como cada requisito da especificação encontra
atendimento técnico.
O público-alvo é a própria equipe de desenvolvimento, os avaliadores acadêmicos do projeto e a
coordenação da clínica-escola, que precisa entender o comportamento do sistema no que toca a dado de
saúde, controle de acesso e rastreabilidade.
1.2 Escopo
O sistema cobre o ciclo completo de atendimento de uma clínica-escola: cadastro das entidades base,
triagem do paciente, emissão e ordenação de senha, chamada, alocação de estudante, supervisor e sala,
condução do atendimento com pausa e encerramento, encaminhamento interno e externo, registro de
ausência e desistência, indicadores de gestão, alertas de tempo de espera e trilha de auditoria. Inclui
também as interfaces sem autenticação usadas na sala de espera: o telão de chamada e a consulta da
própria senha pelo paciente.
O sistema realiza vinte casos de uso, dezoito telas, doze requisitos funcionais, treze regras de negócio e
nove requisitos não funcionais, todos definidos nos documentos de especificação de requisitos, de casos
de uso e de interface.
1.3 Definições e convenções de identificação
Prefixo Significado
RF Requisito funcional, numerado de RF-01 a RF-12
RNF Requisito não funcional, numerado de RNF-01 a RNF-09
RN Regra de negócio, numerada de RN-01 a RN-13
UC Caso de uso, numerado de UC-01 a UC-20
W Tela, conforme os wireframes na versão 2, numerada de W-01 a W-18
T Tela, conforme a especificação de interface em texto, numerada de T-01 a T-18
P Premissa transversal da especificação de interface, de P-01 a P-04
Senha Ficha numerada que representa a passagem de um paciente pela fila, do atendimento até o
desfecho
Fila Conjunto ordenado de senhas aguardando atendimento em uma especialidade
A numeração de telas convive em duas séries porque os dois documentos de interface foram produzidos
em momentos diferentes. Este documento usa a série dos wireframes na versão 2, por ser o artefato
mais recente, e cita a série da especificação em texto quando a correspondência é relevante.
1.4 Documentos de referência
4

• Especificação de Requisitos — regras de negócio, requisitos funcionais e não funcionais.
• Especificação de Funcionalidades — os vinte casos de uso em versão enxuta.
• Especificação de Interface de Usuário — premissas transversais, fundamentos visuais, padrões
globais de comportamento e detalhamento por tela.
• Wireframes na versão 2 — arquitetura de navegação por módulos e correção das divergências
apontadas na versão anterior.
• Registros de decisão arquitetural sobre tecnologia, modelo de navegação e separação de dado
sensível, resumidos no capítulo 10 deste documento.
1.5 Como este documento está organizado
Os capítulos 2 e 3 apresentam a forma de representação adotada e as metas e restrições que
condicionam a arquitetura. Os capítulos 4 a 8 descrevem o sistema por pontos de vista complementares:
casos de uso significativos, organização lógica, processos e concorrência, dados e implantação. O capítulo
9 traz o mapeamento completo da interface de programação. O capítulo 10 traz o mapeamento das
tecnologias. Os capítulos 11 a 14 tratam de decisões, atendimento aos requisitos não funcionais,
rastreabilidade, riscos e pendências.
5

2. Representação arquitetural
A arquitetura é descrita por pontos de vista complementares, cada um respondendo a uma pergunta
diferente sobre o mesmo sistema. Nenhum ponto de vista sozinho é suficiente; juntos, cobrem o que a
equipe precisa saber para construir e o que o avaliador precisa saber para julgar.
Ponto de vista Pergunta que responde Capítulo
Casos de uso Quais cenários exercitam a arquitetura até o limite e por quê 4
Lógico Em que partes o sistema se divide e qual a responsabilidade de cada uma 5
Processos O que acontece em paralelo, o que é disparado por tempo e como a 6
concorrência é tratada
Dados Que informação é guardada, sob que invariantes e por quanto tempo 7
Implantação Onde o sistema roda em cada ambiente e com que dependências 8
Interface de Que operações o sistema oferece, sob que permissão e com que contrato 9
programação
Tecnologia Que ferramenta sustenta cada camada e por qual motivo 10
2.1 Estilo arquitetural adotado
O sistema é uma aplicação distribuída em três partes executáveis, com separação clara entre a interface
de usuário e a lógica de negócio. A lógica de negócio é organizada em camadas: uma camada de entrada
que trata protocolo, validação e códigos de resposta; uma camada de serviço, onde todas as regras de
negócio vivem; e uma camada de acesso a dados, sem regra alguma.
A comunicação entre a interface e a lógica de negócio acontece por dois canais: um canal de requisição e
resposta, usado para toda leitura e toda escrita, e um canal de eventos, usado para propagar mudança de
estado às telas que precisam refletir a fila em tempo quase real. O canal de eventos nunca é a única fonte
de verdade — toda tela que o consome revalida periodicamente pelo canal de requisição, de modo que
uma queda de conexão degrade a experiência sem invalidar a informação.
2.2 Princípio de organização interna
Uma regra do projeto atravessa toda a arquitetura: nenhuma regra de negócio é implementada na
camada de entrada nem em componente de interface. Regra de negócio mora na camada de serviço e
tem teste automatizado que cita, no nome do caso de teste, o identificador da regra que verifica.
Validação de formulário na interface existe para dar retorno imediato ao usuário, não para garantir a
regra — a garantia é sempre do servidor, e ambos usam a mesma definição de validação, escrita uma
única vez em um pacote compartilhado.
6

3. Metas e restrições de arquitetura
3.1 Metas
Meta Origem Como a arquitetura responde
Nenhum dado de saúde P-03, RNF-02, Projeções distintas por tipo de consumidor; abreviação de nome
vaza para tela pública ou RNF-07 feita no servidor; verificação automática de contrato impedindo
lista campo clínico em resposta de listagem
Nenhuma senha é chamada RN-04, UC-09 Seleção e atualização da próxima senha dentro de uma única
duas vezes transação com bloqueio de linha e salto de linhas já bloqueadas
Toda operação sensível é RNF-05, RNF- Interceptação global de escrita e de leitura sensível, gravando
rastreável 07 em registro imutável, sem depender de chamada explícita em
cada serviço
Permissão nunca é decidida RF-02, RNF-01 Permissão é uma palavra composta de módulo e operação; o
por nome de perfil perfil é um conjunto dessas palavras, editável pela
administração sem tocar em código
A fila reflete a realidade em RNF-03 Canal de eventos por especialidade, com revalidação periódica
segundos, não em minutos de baixa frequência como rede de segurança
O telão é legível a distância P-02, RNF-06 Aplicação pública separada, com escala tipográfica própria e
e por quem tem baixa visão contraste reforçado, sem carregar o código da aplicação
autenticada
3.2 Restrições
• Prazo e escopo de projeto acadêmico, com cinco integrantes e perfis desiguais entre interface e
servidor: três pessoas com foco em interface e duas com foco em servidor.
• Nenhum dado real de paciente pode existir em ambiente local ou de homologação. Toda validação
com a clínica ocorre sobre dados fictícios.
• A infraestrutura de produção depende de definição da instituição e não está sob controle da equipe.
• Cinco valores de comportamento dependem de definição da unidade e não podem ser fixados em
código: prazo de tolerância por especialidade, limite de tempo de espera por fila, número de
reincidências que dispara restrição, limite de estudantes por supervisor e tamanho mínimo da
justificativa de reclassificação.
• O sistema opera em terminais compartilhados na recepção, o que impõe sessão curta e ausência de
credencial persistente por padrão.
3.3 Premissas transversais herdadas da especificação de interface
Premissa Conteúdo Consequência arquitetural
P-01 Oito estados possíveis para uma senha, com Máquina de estados explícita, concentrada em um
rótulo textual sempre presente e cor apenas único módulo do servidor, com teste cobrindo cada
como reforço transição válida e cada transição proibida
P-02 Conformidade com o nível AA das diretrizes de Biblioteca de componentes construída sobre
acessibilidade em todo o sistema, e contraste primitivas acessíveis; verificação automática de
reforçado nas telas públicas acessibilidade na integração contínua
7

Premissa Conteúdo Consequência arquitetural
P-03 Dado de saúde e motivo do atendimento fora Três projeções distintas para senha, escolhidas pelo
de listas, painéis e telão tipo de consumidor, e não por filtro aplicado sobre
um objeto único
P-04 Bloqueio por ausência de supervisor se Um único estado no servidor alimenta as três
manifesta em três lugares ao mesmo tempo manifestações: aviso permanente na fila, recusa da
operação de chamada e item na lista de alertas de
gestão
8

4. Visão de casos de uso
Dos vinte casos de uso, seis exercitam a arquitetura de forma significativa — ou porque envolvem
concorrência, ou porque tocam dado sensível, ou porque atravessam mais de um módulo. Os demais são
variações de leitura e escrita sobre um mesmo padrão e não impõem decisão estrutural própria.
4.1 Casos de uso arquiteturalmente significativos
Caso de uso Por que é significativo Mecanismo envolvido
UC-09 — Chamar Dois atendentes podem acionar a Transação com bloqueio de linha, salto
próxima senha chamada no mesmo instante; a de linhas bloqueadas, chave de
ordenação precisa ser respeitada e idempotência e emissão de evento para
nenhuma senha pode sair duas vezes o telão e para o painel
UC-10 — Alocar recursos Verifica cinco regras de negócio ao Motor de alocação com verificação de
ao atendimento mesmo tempo e pode disparar o disponibilidade e compatibilidade,
bloqueio de uma especialidade inteira restrições únicas parciais no banco e
contagem de supervisão dentro da
transação
UC-05 — Realizar triagem É a porta de entrada do dado de saúde Campos condicionais validados no
no sistema servidor, gravação de justificativa
obrigatória em urgência e marcação do
registro como sensível
UC-13 — Encerrar O desfecho ramifica o fluxo: pode exigir Máquina de estados com estado
atendimento validação de supervisor, pode encadear intermediário de pendência, cópia da
encaminhamento e pode gerar nova exigência de validação no início do
senha atendimento e vínculo entre a senha de
origem e a senha gerada
UC-16 — Registrar Pode ser disparado sem usuário, por Rotina agendada que varre senhas
ausência expiração de prazo chamadas com tolerância vencida, libera
os recursos alocados e alimenta a política
de reincidência
UC-20 — Consultar trilha A própria consulta e a própria exportação Registro append-only garantido por
de auditoria precisam ser registradas, e o registro não permissão do usuário de banco, e não
pode ser alterado apenas por disciplina da aplicação
4.2 Fluxo principal do sistema, do ponto de vista arquitetural
O caminho que atravessa mais módulos é o ciclo de vida de uma senha. Ele começa no módulo de
triagem, passa pelo módulo de fila, entra no módulo de alocação, segue para o módulo de atendimento e
pode terminar no módulo de encaminhamento, gerando uma nova senha e reiniciando o ciclo em outra
especialidade.
Etapa Módulo responsável Estado resultante da senha Regra dominante
Triagem concluída e senha Triagem e senha Aguardando RN-01, RN-02, RN-03,
emitida RN-04
Chamada da próxima senha Fila e chamada Chamada RN-04, RN-10
Alocação de estudante, Alocação Aguardando recurso, quando RN-05 a RN-09
supervisor e sala falta algum
9

Etapa  Módulo responsável  Estado resultante da senha  Regra dominante
| Início do atendimento  | Atendimento  | Em atendimento  | RN-05  |
| ---------------------- | ------------ | --------------- | ------ |
Pausa e retomada  Atendimento  Pausado e de volta a Em  Protocolo do serviço
atendimento
| Encerramento  | Atendimento  | Pendente de validação ou  | RN-11  |
| ------------- | ------------ | ------------------------- | ------ |
Encerrado
Encaminhamento interno  Encaminhamento  Nova senha em Aguardando na  RF-08
especialidade de destino
| Expiração de tolerância  | Ausências  | Ausente      | RN-12, RN-13  |
| ------------------------ | ---------- | ------------ | ------------- |
| Saída voluntária         | Ausências  | Desistência  | RF-09         |

A distinção entre ausência e desistência é mantida em todo o percurso porque alimenta indicadores diferentes: taxa de
ausência de um lado, número de desistências de outro.
|     |     |     |     |
| --- | --- | --- | --- |
10

5. Visão lógica
5.1 As três aplicações
O sistema é composto por três aplicações executáveis e um conjunto de pacotes compartilhados entre
elas.
Aplicação Responsabilidade Telas atendidas
Serviço de negócio Regras de negócio, persistência, controle de acesso, auditoria, Nenhuma diretamente —
canal de eventos e rotinas agendadas. É a única parte que fala atende as três aplicações
com o banco de dados. de interface
Aplicação Toda a operação autenticada da clínica: acesso, cadastros, W-01 a W-08 e W-11 a
operacional triagem, fila, alocação, atendimento, encaminhamento, W-18
ausências, indicadores e auditoria.
Aplicação pública Telão da sala de espera e consulta da própria senha pelo W-09 e W-10
paciente. Não tem autenticação, não tem menu e não
conhece nenhum conceito clínico.
Por que a aplicação pública é separada
O telão e a consulta do paciente exibem apenas número da senha, especialidade e local. Mantê-los
dentro da aplicação autenticada significaria levar até um terminal instalado na sala de espera o código de
sessão, os interceptadores de autenticação e a biblioteca de componentes operacional — tudo material
que não tem função ali e que amplia a superfície por onde dado sensível poderia escapar.
A separação tem ainda um benefício de acessibilidade: a aplicação pública usa uma escala tipográfica
própria, dimensionada para leitura a distância em tela de 42 polegadas, sem que isso contamine a
aplicação operacional, que precisa de densidade de informação.
5.2 Pacotes compartilhados
Pacote Conteúdo Quem consome
Contratos Tipos e regras de validação derivados da especificação da Serviço de negócio,
interface de programação, escritos uma única vez aplicação operacional e
aplicação pública
Biblioteca de Componentes visuais acessíveis: cartão de senha, etiqueta de Aplicação operacional e
componentes status, etiqueta de prioridade, aviso de bloqueio, cronômetro, aplicação pública
janela de confirmação e de justificativa, estado vazio
Padrões de código Configuração de análise estática e bases de configuração da Todas
linguagem
O pacote de contratos é o ponto mais importante da organização interna. Como a definição de validação
nasce nele e é usada tanto pela camada de entrada do servidor quanto pelo formulário da interface,
deixa de existir a divergência de validação entre cliente e servidor — que é a origem mais comum de
defeito em formulário com campo condicional, exatamente o caso da ficha de triagem e da janela de
reclassificação de prioridade.
5.3 Módulos do serviço de negócio
11

O serviço é dividido em módulos que espelham os módulos do menu da aplicação operacional. Cada
módulo segue a mesma divisão interna: uma camada de entrada, que trata protocolo e serialização; uma
camada de serviço, onde vivem as regras; uma camada de repositório, que fala com o banco; um
conjunto de definições de entrada e saída; e os eventos de domínio que o módulo emite.
Módulo Responsabilidade principal Requisitos Regras
Acesso Autenticação, sessão, catálogo de permissões, perfis RF-02 —
e usuários
Cadastros base Paciente, estudante ou atendente, supervisor, sala e RF-01 RN-06, RN-08,
especialidade ou serviço RN-09
Triagem e senha Ficha de triagem, emissão de senha e reclassificação RF-03, RF-04 RN-01, RN-02,
de prioridade RN-03, RN-04
Fila e chamada Filas por especialidade, quadro por status, chamada, RF-04, RF-05 RN-04, RN-10
rechamada, cancelamento e bloqueio
Alocação Sugestão e confirmação de estudante, supervisor e RF-06 RN-05 a RN-09
sala
Atendimento Início, pausa, retomada, encerramento e validação do RF-07 RN-11
supervisor
Encaminhamento Encaminhamento interno, que gera nova senha, e RF-08 —
externo, apenas registrado
Ausências Ausência automática ou manual, desistência RF-09 RN-12, RN-13
voluntária e política de reincidência
Indicadores Agregações de tempo de espera, tempo de RF-10 —
atendimento, volume, taxa de ausência e desistências
Alertas Monitoramento de tempo de espera, geração de RF-11 —
alerta, reconhecimento e escalonamento
Auditoria Registro imutável de operação e consulta a RF-12 —
informação sensível
5.4 Organização da aplicação operacional
A navegação é por módulos: um menu lateral fixo lista os módulos disponíveis, e cada módulo tem tela
própria. O menu é filtrado por permissão, de modo que o usuário só vê os módulos a que seu perfil tem
acesso. A tela inicial muda de conteúdo conforme o perfil — indicadores, lista principal e ação em
destaque variam —, o que reproduz o efeito de uma área de trabalho por perfil sem exigir a manutenção
de seis layouts distintos.
Um único elemento do modelo alternativo de navegação foi preservado: dentro do módulo de fila, a ação
de detalhe de uma senha abre um painel lateral contextual com as ações permitidas ao perfil, como
reclassificar prioridade, registrar ausência ou registrar desistência. Ações que envolvem formulário longo
— alocação, atendimento e encaminhamento — permanecem como telas próprias, porque um painel
lateral não comporta a densidade delas sem prejudicar o uso por teclado.
5.5 Regra de visibilidade de ação
Ação para a qual o usuário não tem permissão não é renderizada. Nunca é renderizada em estado
desabilitado, porque um botão desabilitado informa que a funcionalidade existe e que outra pessoa
12

poderia usá-la, o que já é uma revelação. O estado de ausência de permissão usa mensagem neutra, sem
indicar existência ou volume de dados por trás dela.
13

6. Visão de processos e concorrência
6.1 Concorrência na chamada de senha
É o ponto de maior risco de concorrência do sistema. Dois atendentes da mesma especialidade podem
acionar a chamada da próxima senha no mesmo segundo. Sem tratamento, ambos leriam a mesma
primeira senha da fila e ambos a marcariam como chamada — o paciente seria chamado duas vezes e a
senha seguinte seria pulada.
A operação roda dentro de uma única transação, com bloqueio da linha selecionada e salto das linhas já
bloqueadas por outra transação. A ordenação segue a precedência definida na regra de negócio: primeiro
a prioridade da senha, depois o horário de chegada e, por último, a existência de agendamento prévio.
Cada requisição concorrente recebe uma senha diferente, e nenhuma senha é chamada duas vezes.
A operação de chamada e a de alocação aceitam ainda uma chave de idempotência informada pela
interface. Repetição da mesma chave dentro de dez minutos devolve a resposta original sem executar de
novo, o que cobre duplo clique e reenvio automático após reconexão.
6.2 Concorrência em mudança de estado
Toda operação que altera o estado de uma senha ou de um atendimento exige que o cliente informe a
versão do registro que ele tinha em mãos. Se a versão estiver desatualizada, a operação é recusada com
um código específico de conflito e o estado atual acompanha a resposta, de modo que a tela possa
recarregar mostrando exatamente o que mudou. É o caso, por exemplo, de um estudante tentando
encerrar um atendimento que o supervisor acabou de devolver.
6.3 Canal de eventos em tempo real
O canal de eventos é dividido em três espaços independentes, com regras de autenticação distintas. A
separação existe para que o espaço público seja incapaz, por construção, de transportar informação
clínica.
Espaço de Autenticação Agrupamento Eventos publicados
eventos
Filas Obrigatória, com Por Senha criada; senha chamada; status de senha
verificação de especialidade alterado; senha reclassificada; fila bloqueada; fila
permissão desbloqueada; métricas da fila
Painel público Nenhuma Por unidade Exibir chamada, contendo apenas número,
especialidade, local e sequência; estado do painel no
momento da conexão, para reconstruir a tela após
reconexão
Alertas Obrigatória, com Por unidade Tempo de espera excedido; tempo de espera
permissão de normalizado
consulta de alertas
O espaço público não conhece identificador de paciente, prioridade clínica nem qualquer dado de saúde.
Não se trata de um filtro aplicado antes do envio, e sim de um conjunto de campos que simplesmente
não existe naquele caminho.
6.4 Degradação em caso de perda de conexão
14

Toda tela que consome o canal de eventos também revalida a informação pelo canal de requisição em
intervalo curto. A perda da conexão de eventos, portanto, não deixa a tela cega: ela passa a atualizar por
consulta periódica. Quando a conexão cai por completo, a aplicação exibe um aviso permanente, congela
as ações de escrita e mantém a leitura do último estado conhecido com indicação explícita de que a
informação pode estar desatualizada. O telão mantém a última chamada visível, com uma indicação
discreta da queda, porque apagar a tela de uma sala de espera é pior do que exibir informação
levemente atrasada.
6.5 Rotinas disparadas por tempo
Rotina O que faz Regra ou requisito
Expiração de Varre as senhas chamadas cujo prazo de tolerância venceu, marca RN-12, UC-16
tolerância ausência ou devolve à fila conforme a política da unidade, e libera
os recursos alocados
Varredura de tempo Compara o tempo de espera de cada fila ativa com o limite RF-11, UC-19
de espera configurado, gera alerta quando ultrapassa e normaliza o alerta
quando o tempo volta ao limite
Cópia de segurança Executa a extração diária dos dados, com objetivo de ponto de RNF-09
do banco recuperação de vinte e quatro horas e objetivo de tempo de
recuperação de quatro horas
6.6 Custo computacional
A plataforma de execução do servidor é orientada a entrada e saída e não lida bem com processamento
pesado de processador dentro do mesmo processo que atende requisições. Por isso, os indicadores de
gestão são calculados por agregação no próprio banco de dados, com funções de janela, e não montados
em memória a partir de listas carregadas. A mesma preocupação sustenta a exigência de paginação
obrigatória em toda listagem, sem exceção.
15

7. Visão de dados
7.1 Convenções
Identificadores são valores únicos universais na variante ordenável por tempo, o que preserva a
localidade de inserção nos índices e evita a fragmentação típica de identificadores aleatórios. Nomes de
tabela seguem o padrão de palavras separadas por sublinhado; nomes de campo do domínio
permanecem em português, na mesma terminologia da especificação de requisitos, para que a distância
entre o texto do requisito e o código seja a menor possível.
7.2 Agrupamentos de entidades
| Agrupamento  | Entidades  | Observação  |
| ------------ | ---------- | ----------- |
Acesso e  Unidade, parâmetro de unidade, usuário,  A unidade existe desde o início, ainda
configuração  perfil, permissão de perfil e registro de  que só haja uma, para que o suporte a
|     | auditoria  | múltiplas unidades não exija migração  |
| --- | ---------- | -------------------------------------- |
futura
Cadastros base  Paciente, estudante, supervisor, sala e  Habilitação, procedimento e recurso são
|     | especialidade, com suas tabelas de vínculo  | tabelas de apoio, e não texto livre,  |
| --- | ------------------------------------------- | ------------------------------------- |
para habilitação, procedimento, recurso e  porque a verificação de compatibilidade
|     | disponibilidade por turno  | compara conjuntos  |
| --- | -------------------------- | ------------------ |
Fila e senha  Fila, triagem, senha, reclassificação de  A senha é o agregado central do sistema;
|     | prioridade e chamada  | quase todo fluxo converge nela  |
| --- | --------------------- | ------------------------------- |
Alocação e  Alocação, atendimento, pausa de  A pausa é uma linha por ocorrência, para
atendimento  atendimento e encaminhamento  que o tempo efetivo de atendimento
desconte a soma das pausas nos
indicadores
Ausência e gestão  Ausência, desistência, alerta e escalonamento  Ausência e desistência são tabelas
separadas porque alimentam indicadores
distintos

7.3 Máquina de estados da senha
A senha percorre oito estados possíveis. A máquina de estados fica concentrada em um único módulo do
servidor e tem teste de unidade cobrindo cada transição válida e cada transição proibida. Qualquer
tentativa de transição não prevista é recusada com um código específico de conflito.
| Estado  | Como se chega  | Para onde pode ir  |
| ------- | -------------- | ------------------ |
Aguardando  Emissão da senha, ou devolução após  Chamada, Desistência
tolerância vencida
Chamada
Acionamento da chamada da próxima  Aguardando recurso, Em atendimento,
|     | senha  | Ausente, Desistência  |
| --- | ------ | --------------------- |
Aguardando  Alocação impossível por falta de estudante,  Em atendimento, Aguardando, Desistência
| recurso  | supervisor ou sala  |     |
| -------- | ------------------- | --- |
Em atendimento  Início do atendimento com recursos  Pausado, Pendente de validação, Encerrado,
|     | alocados  | Desistência  |
| --- | --------- | ------------ |
Pausado  Pausa registrada durante o atendimento  Em atendimento
16

Estado Como se chega Para onde pode ir
Pendente de Encerramento em serviço cujo protocolo Encerrado, ou permanece no mesmo estado
validação exige validação do supervisor em caso de recusa, com parecer registrado
Encerrado Encerramento sem exigência de validação, Estado final
ou validação concedida
Ausente Expiração do prazo de tolerância, ou Estado final
marcação manual
Desistência Saída voluntária solicitada pelo paciente ou Estado final
registrada pela equipe
7.4 Invariantes garantidas no banco de dados
Regra que depende apenas da camada de serviço quebra no dia em que alguém esquece de chamar a
verificação. As invariantes abaixo são garantidas por restrição do próprio banco, e não por disciplina da
aplicação.
Invariante Mecanismo Regra
Um atendimento por senha Restrição de unicidade sobre a referência à senha RF-07
Estudante sem duas alocações Índice único parcial, considerando apenas alocações RN-05
ativas ao mesmo tempo ainda não liberadas
Sala sem duas alocações ativas ao Índice único parcial, considerando apenas alocações RN-09
mesmo tempo ainda não liberadas
Justificativa presente em toda Restrição de verificação sobre o tamanho mínimo do RN-01
reclassificação de prioridade texto
Justificativa clínica presente em Restrição de verificação condicional à prioridade RN-03
prioridade urgente
Uma única fila ativa por Índice único parcial sobre filas ativas RF-04
especialidade
Trilha de auditoria imutável Revogação das permissões de alteração e remoção RNF-05
para o usuário de banco da aplicação
Limite de estudantes por Verificado na camada de serviço, dentro da mesma RN-08
supervisor transação que cria a alocação, com bloqueio na linha
do supervisor
O limite de supervisão é o único item que não vira restrição declarativa, porque depende de uma
contagem sobre linhas relacionadas. A garantia vem de mantê-lo dentro da transação de alocação, com a
linha do supervisor bloqueada durante a verificação.
7.5 Índice que implementa a ordenação da fila
A regra de ordenação da fila é traduzida diretamente em um índice composto sobre a tabela de senhas,
combinando fila, status, prioridade em ordem decrescente, horário de chegada em ordem crescente e
existência de agendamento prévio. A consulta do quadro de filas e a seleção da próxima senha usam esse
mesmo índice, de modo que a ordenação exibida na tela e a ordenação aplicada na chamada não podem
divergir.
17

7.6 Parâmetros que dependem da unidade
Os cinco valores que dependem de definição da clínica não são constantes no código. Ficam em tabela
própria, editáveis pela administração, com a possibilidade de um valor específico por especialidade
sobrepor o valor geral da unidade.
Parâmetro Onde afeta Valor provisório
Prazo de tolerância por especialidade Expiração automática de senha chamada 5 minutos
Limite de tempo de espera por fila Geração de alerta para a gestão 25 minutos
Reincidências que disparam restrição Aviso na triagem e no registro de ausência 3 ausências em 90 dias
Limite de estudantes por supervisor Verificação na alocação 3
Tamanho mínimo da justificativa de Validação da reclassificação de prioridade 20 caracteres
reclassificação
Os valores provisórios servem para que o sistema funcione em ambiente de desenvolvimento e homologação. A definição
final é pendência registrada e cabe à coordenação da clínica-escola.
7.7 Retenção e proteção de dados pessoais
Categoria de dado Retenção Observação
Triagem e atendimento Conforme política da Base legal e prazo a confirmar com a coordenação
instituição
Trilha de auditoria Mínimo de cinco anos Nunca removida por rotina automática
Senha, chamada e Dois anos Depois do prazo, o vínculo com o paciente é
alocação anonimizado, preservando o agregado para indicadores
Dados de indicador Indefinida Já agregados, sem identificação individual
A anonimização substitui o vínculo com o paciente por um identificador irreversível. Isso preserva a
capacidade de calcular indicadores históricos sem manter dado pessoal por mais tempo do que o
necessário.
18

8. Visão de implantação
8.1 Ambientes
| Ambiente  | Onde executa  | Base de dados  |     | Observação  |
| --------- | ------------- | -------------- | --- | ----------- |
Desenvolvimento  Contêineres na máquina de cada  Base local com carga  A carga cria perfis, cinco
local
integrante, orquestrados por um  inicial de dados  especialidades, salas,
arquivo único de composição  fictícios  supervisores e pacientes
fictícios, de modo que
todos partem do mesmo
estado
Homologação  Contêiner único em servidor da  Base de homologação  Usado na validação com a
instituição
coordenação da clínica;
apenas dados fictícios
Produção  A definir com a instituição  Base de produção  Exige transporte
criptografado, cópia de
segurança diária e
política de retenção
definida

Nenhum dado real de paciente entra em ambiente local ou de homologação. É restrição do projeto, não
recomendação.
8.2 Unidades implantáveis
| Unidade  | Natureza  |     | Escala  |     |
| -------- | --------- | --- | ------- | --- |
Serviço de negócio  Processo sem estado próprio de sessão  Replicável horizontalmente; o canal de
em memória
eventos usa um adaptador externo quando
há mais de uma instância
Aplicação operacional  Aplicação de interface com renderização  Replicável; sem estado
no servidor e no cliente
Aplicação pública  Aplicação de interface leve, sem  Replicável; normalmente exposta apenas na
|     | autenticação  |     | rede interna da clínica  |     |
| --- | ------------- | --- | ------------------------ | --- |
Banco de dados
|     | Instância única com cópia de segurança  |     | Escala vertical; réplica de leitura é    |     |
| --- | --------------------------------------- | --- | ---------------------------------------- | --- |
|     | diária                                  |     | possibilidade futura para os relatórios  |     |

8.3 Terminais e clientes
| Cliente  | Uso  | Requisito específico  |     |     |
| -------- | ---- | --------------------- | --- | --- |
Estação da recepção e da  Operação completa  Sessão curta, por ser terminal compartilhado; sem
| triagem  |     | credencial persistente por padrão  |     |     |
| -------- | --- | ---------------------------------- | --- | --- |
Terminal do telão  Somente exibição, sem  Tela de 42 polegadas em orientação paisagem, projetada
|     | interação  | para resolução de 1920 por 1080  |     |     |
| --- | ---------- | -------------------------------- | --- | --- |
Celular do paciente  Consulta da própria  Texto ampliável sem quebra de layout e alvos de toque
|     | senha  | ampliados  |     |     |
| --- | ------ | ---------- | --- | --- |
19

| Cliente  | Uso  | Requisito específico  |
| -------- | ---- | --------------------- |
Estação de gestão
|     | Indicadores, alertas e  | Telas de gestão não são oferecidas abaixo de 768 pixels  |
| --- | ----------------------- | -------------------------------------------------------- |
|     | auditoria               | de largura                                               |

8.4 Saúde e observabilidade
O serviço expõe duas verificações distintas. A primeira responde sem tocar o banco de dados e serve para
saber se o processo está vivo; a segunda verifica a conexão com o banco e a aplicação das migrações, e
serve para saber se o processo está apto a receber tráfego. Toda resposta carrega um identificador de
requisição, propagado para o registro de log e para a trilha de auditoria, o que permite ligar um relato de
usuário a uma linha específica de registro.
|     |     |     |
| --- | --- | --- |
20

9. Mapeamento da interface de programação
Este capítulo cumpre a exigência de mapeamento completo da interface de programação do sistema. As
operações estão descritas por nome de negócio, ação, recurso sobre o qual atuam, permissão exigida e
rastreabilidade ao caso de uso ou requisito de origem. A notação técnica de endereçamento fica no
artefato formal de contrato, mantido pela equipe; aqui interessa o mapeamento conceitual, que é o que
orienta a divisão de trabalho e a avaliação de cobertura dos requisitos.
9.1 Convenções gerais
Item Definição
Formato de troca Notação de objetos em texto, codificada em UTF-8
Nomes de campo Palavras compostas iniciando em minúscula, sem separador
Datas e horas Formato internacional com fuso horário explícito, sempre
Durações Número inteiro de minutos, com a unidade indicada no nome do campo
Identificadores Valor único universal na variante ordenável por tempo
Idioma Mensagens de erro em português, redigidas para exibição direta ao usuário
Paginação Obrigatória em toda listagem, sem exceção, com tamanho padrão de vinte itens e
teto de cem
Ações utilizadas no mapeamento
Ação Significado
Consultar Leitura de um recurso ou de uma coleção, sem efeito colateral
Criar Criação de um novo recurso, ou registro de um evento de negócio
Substituir Atualização completa de um recurso existente
Alterar Atualização parcial de um recurso existente
Remover Exclusão de um recurso, sempre condicionada à ausência de vínculo ativo
9.2 Autenticação e sessão
A autenticação devolve uma credencial de acesso de curta duração, entregue no corpo da resposta, e
uma credencial de renovação de duração maior, entregue em um marcador de navegador inacessível a
código de página. A credencial de acesso vale quinze minutos; a de renovação, oito horas — a duração de
um turno da clínica. A escolha reflete o fato de que as estações da recepção são compartilhadas.
A credencial de acesso carrega o identificador do usuário, o nome, o perfil ativo, a unidade e a lista de
permissões. A interface nunca decide permissão por nome de perfil, e sim pela lista de permissões,
porque perfis são editáveis pela administração e qualquer nome fixado em código quebraria no primeiro
perfil novo criado pela clínica.
Cinco tentativas de autenticação malsucedidas bloqueiam temporariamente o acesso. A resposta de
credencial inválida é sempre a mesma, sem distinguir usuário inexistente de senha incorreta.
21

Operação Ação Recurso Permissão Rastreabilidade
Autenticar usuário Criar Sessão Pública W-01, RNF-01
Renovar credencial de Criar Renovação de sessão Sessão válida RNF-01
acesso
Encerrar sessão Criar Encerramento de Autenticado RNF-01
sessão
Consultar dados da sessão Consultar Sessão Autenticado W-02, RNF-01
atual
Consultar catálogo de Consultar Catálogo de permissões Consulta de perfis UC-04, RF-02, W-
permissões 05
O catálogo de permissões alimenta a matriz da tela de perfis e a filtragem do menu lateral. Módulos que envolvem dado de
saúde ou registro de auditoria vêm marcados no próprio catálogo.
9.3 Perfis e usuários
Operação Ação Recurso Permissão Rastreabilidade
Listar perfis Consultar Perfis Consulta de perfis UC-04, RF-02
Criar perfil Criar Perfis Criação de perfil UC-04, RF-02
Consultar perfil Consultar Perfil Consulta de perfis UC-04, RF-02
Atualizar perfil e suas Substituir Perfil Edição de perfil UC-04, RF-02, W-
permissões 05
Excluir perfil Remover Perfil Exclusão de perfil UC-04, RF-02
Listar usuários Consultar Usuários Consulta de perfis RF-02
Criar usuário e vincular perfil Criar Usuários Criação de perfil RF-02
A alteração das permissões de um perfil informa, na resposta, quantos usuários serão afetados pela
mudança, de modo que a tela possa apresentar essa consequência antes da confirmação. A exclusão de
um perfil é recusada enquanto houver usuário vinculado, e perfis do sistema não são editáveis nem
removíveis.
9.4 Cadastros base
As cinco entidades base compartilham o mesmo conjunto de operações. O que varia entre elas são os
campos, a verificação de duplicidade e o perfil responsável, e essa variação está no formulário, não na
estrutura das operações.
Operação Ação Recurso Permissão Rastreabilidade
Listar pacientes Consultar Pacientes Consulta de UC-03, RF-01
cadastros
Cadastrar paciente Criar Pacientes Criação em UC-01, RF-01
cadastros
Consultar paciente Consultar Paciente Consulta de UC-03, RF-01
cadastros
22

| Operação         | Ação        | Recurso   | Permissão  | Rastreabilidade  |
| ---------------- | ----------- | --------- | ---------- | ---------------- |
| Editar paciente  | Substituir  | Paciente  |            | UC-02, RF-01     |
Edição em
cadastros
| Excluir paciente  | Remover  | Paciente  | Exclusão em  | RF-01  |
| ----------------- | -------- | --------- | ------------ | ------ |
cadastros
Listar estudantes ou atendentes  Consultar  Estudantes  Consulta de  UC-03, RF-01
cadastros
|                         | Criar  | Estudantes  |             |                |
| ----------------------- | ------ | ----------- | ----------- | -------------- |
| Cadastrar estudante ou  |        |             | Criação em  | UC-01, RF-01,  |
| atendente               |        |             | cadastros   | RN-06          |
Consultar estudante  Consultar  Estudante  Consulta de  UC-03, RF-01
cadastros
Editar estudante  Substituir  Estudante  Edição em  UC-02, RF-01,
|                    |          |            | cadastros  | RN-06  |
| ------------------ | -------- | ---------- | ---------- | ------ |
| Excluir estudante  | Remover  | Estudante  |            | RF-01  |
Exclusão em
cadastros
Listar supervisores  Consultar  Supervisores  Consulta de  UC-03, RF-01,
|     |     |     | cadastros  | RN-08  |
| --- | --- | --- | ---------- | ------ |
Cadastrar supervisor  Criar  Supervisores  Criação em  UC-01, RF-01,
|                       |            |             | cadastros  | RN-08         |
| --------------------- | ---------- | ----------- | ---------- | ------------- |
| Consultar supervisor  | Consultar  | Supervisor  |            | UC-03, RF-01  |
Consulta de
cadastros
Editar supervisor  Substituir  Supervisor  Edição em  UC-02, RF-01,
|                     |          |             | cadastros    | RN-07, RN-08  |
| ------------------- | -------- | ----------- | ------------ | ------------- |
| Excluir supervisor  | Remover  | Supervisor  | Exclusão em  | RF-01         |
cadastros
| Listar salas  | Consultar  | Salas  |     | UC-03, RF-01  |
| ------------- | ---------- | ------ | --- | ------------- |
Consulta de
cadastros
| Cadastrar sala  | Criar      | Salas  | Criação em   | UC-01, RF-01,  |
| --------------- | ---------- | ------ | ------------ | -------------- |
|                 |            |        | cadastros    | RN-09          |
| Consultar sala  | Consultar  | Sala   | Consulta de  | UC-03, RF-01   |
cadastros
| Editar sala   | Substituir  | Sala  |              |                |
| ------------- | ----------- | ----- | ------------ | -------------- |
|               |             |       | Edição em    | UC-02, RF-01,  |
|               |             |       | cadastros    | RN-09          |
| Excluir sala  | Remover     | Sala  | Exclusão em  | RF-01          |
cadastros
Listar especialidades ou serviços  Consultar  Especialidades  Consulta de  UC-03, RF-01
cadastros
|                             | Criar  | Especialidades  |             | UC-01, RF-01  |
| --------------------------- | ------ | --------------- | ----------- | ------------- |
| Cadastrar especialidade ou  |        |                 | Criação em  |               |
| serviço                     |        |                 | cadastros   |               |
Consultar especialidade  Consultar  Especialidade  Consulta de  UC-03, RF-01
cadastros
Editar especialidade  Substituir  Especialidade  Edição em  UC-02, RF-01,
|     |     |     | cadastros  | RN-07, RN-11  |
| --- | --- | --- | ---------- | ------------- |
23

| Operação               | Ação     | Recurso        | Permissão  | Rastreabilidade  |
| ---------------------- | -------- | -------------- | ---------- | ---------------- |
| Excluir especialidade  | Remover  | Especialidade  |            | RF-01            |
Exclusão em
cadastros

Toda tentativa de exclusão de um registro com vínculo ativo em fila, alocação ou atendimento é recusada
com um código específico, e a resposta orienta a alternativa de inativar o registro em vez de removê-lo. A
verificação de duplicidade, quando existe para a entidade, devolve o identificador do registro já
cadastrado, para que a tela ofereça o link de abertura direta.
9.5 Triagem, senha e prioridade
| Operação  | Ação  | Recurso  | Permissão  | Rastreabilidade  |
| --------- | ----- | -------- | ---------- | ---------------- |
Registrar triagem  Criar  Triagens  Criação em triagem  UC-05, RF-03, RN-
01 a RN-03
Listar triagens  Consultar  Triagens  Consulta em triagem  RF-03
| Consultar triagem completa  | Consultar  | Triagem  |                       |                    |
| --------------------------- | ---------- | -------- | --------------------- | ------------------ |
|                             |            |          | Consulta sensível em  | UC-05, RF-03, RNF- |
|                             |            |          | triagem               | 07                 |
Emitir senha  Criar  Senhas  Criação de senha  UC-06, RF-04, RN-
04
Consultar senha  Consultar  Senha  Consulta de senha;  UC-08, RF-04, RNF-
07
consulta sensível
quando inclui a
triagem
Reclassificar prioridade  Alterar  Prioridade da  Reclassificação de  UC-07, RF-04, RN-
|     |     | senha  | senha  | 01  |
| --- | --- | ------ | ------ | --- |
Consultar histórico de  Consultar  Reclassificações da  Consulta de senha  UC-07, RNF-05
| reclassificações  |     | senha  |     |     |
| ----------------- | --- | ------ | --- | --- |

A consulta da triagem completa e a consulta da senha com o conteúdo clínico incluído são operações
marcadas como sensíveis: registram um evento de consulta a informação sensível antes de responder,
com o identificador do registro consultado. É esse registro que torna verdadeira a trilha exigida pela
conformidade legal — um evento desse tipo significa que alguém de fato abriu o conteúdo clínico, e não
apenas que o campo trafegou junto de outra informação.
A reclassificação de prioridade exige justificativa. Justificativa ausente ou abaixo do tamanho mínimo
definido pela unidade resulta em recusa com código específico, e a mensagem devolvida já é o texto que
a tela exibe abaixo do campo.
9.6 Fila, chamada e telão
| Operação      | Ação       | Recurso  | Permissão         | Rastreabilidade  |
| ------------- | ---------- | -------- | ----------------- | ---------------- |
| Listar filas  | Consultar  | Filas    | Consulta de fila  | RF-04            |
| Criar fila    | Criar      | Filas    | Criação de fila   | RF-04            |
Consultar fila, com o estado de  Consultar  Fila  Consulta de fila  RF-04, RN-10, P-04
bloqueio
24

| Operação      | Ação        | Recurso  | Permissão         | Rastreabilidade  |
| ------------- | ----------- | -------- | ----------------- | ---------------- |
| Editar fila   | Substituir  | Fila     | Edição de fila    | RF-04            |
| Excluir fila  | Remover     | Fila     | Exclusão de fila  | RF-04            |
Consultar quadro de senhas da  Consultar  Senhas da fila  Consulta de fila  UC-08, W-08, RN-
| fila, por status  |     |     |     | 04  |
| ----------------- | --- | --- | --- | --- |
Chamar próxima senha  Criar  Chamadas da fila  Chamada de  UC-09, RF-05, RN-
|                 |        |            | senha       | 04, RN-10     |
| --------------- | ------ | ---------- | ----------- | ------------- |
| Rechamar senha  | Criar  | Rechamada  | Chamada de  | UC-09, RF-05  |
senha
| Cancelar chamada  | Remover  | Chamada  | Chamada de  | RF-05  |
| ----------------- | -------- | -------- | ----------- | ------ |
senha

A consulta da fila devolve o estado de bloqueio como parte do recurso, contendo se está ativo, o motivo
e desde quando. É esse mesmo estado que faz a tela exibir o aviso permanente, que faz a operação de
chamada ser recusada com código próprio e que faz o bloqueio aparecer na lista de alertas de gestão —
as três manifestações exigidas pela premissa, todas derivadas de uma única fonte no servidor.
A operação de chamada aceita chave de idempotência e é a única do sistema que roda com bloqueio de
linha e salto de linhas bloqueadas.
9.7 Alocação de recursos
| Operação  | Ação  | Recurso  | Permissão  | Rastreabilidade  |
| --------- | ----- | -------- | ---------- | ---------------- |
Consultar sugestões e alternativas  Consultar  Sugestões de alocação  Consulta de  UC-10, RF-06, RN-
| de alocação                     |        | da senha           | alocação    | 05 a RN-09        |
| ------------------------------- | ------ | ------------------ | ----------- | ----------------- |
| Confirmar alocação de recursos  | Criar  | Alocação da senha  |             |                   |
|                                 |        |                    | Criação de  | UC-10, RF-06, RN- |
|                                 |        |                    | alocação    | 05 a RN-10        |
Trocar recurso de uma alocação  Substituir  Alocação da senha  Edição de  UC-10, RF-06
alocação
Manter senha aguardando recurso  Criar  Pendência de recurso  Edição de  UC-10, RF-06
|     |     | da senha  | alocação  |     |
| --- | --- | --------- | --------- | --- |

A consulta de sugestões devolve três blocos — estudante, supervisor e sala —, cada um com a sugestão
do sistema e a lista de alternativas. Cada alternativa indica se está apta e, quando não está, o motivo em
texto: já possui atendimento ativo, especialidade incompatível, fora do turno, limite de supervisão
atingido ou recurso indisponível na sala. O motivo vem do servidor porque é resultado da mesma
verificação que decidirá a confirmação; deixar a interface deduzi-lo abriria espaço para divergência entre
o que a tela explica e o que o sistema recusa.
Quando a especialidade não exige supervisão docente, o bloco de supervisor simplesmente não vem na
resposta, e a tela o omite. Quando não há nenhum supervisor habilitado disponível, a confirmação é
recusada, a senha permanece aguardando recurso, a fila da especialidade é bloqueada e a gestão é
notificada — tudo na mesma operação.
Recurso que deixa de estar disponível entre a sugestão e a confirmação produz recusa com código
específico, sem confirmar a alocação, e a tela recarrega as alternativas.
25

9.8 Atendimento
| Operação  | Ação  | Recurso  | Permissão  | Rastreabilidade  |
| --------- | ----- | -------- | ---------- | ---------------- |
Listar atendimentos  Consultar  Atendimentos  Consulta de  RF-07
atendimento
Consultar atendimento  Consultar  Atendimento  Consulta sensível  RF-07, RNF-07
de atendimento
| Iniciar atendimento  | Criar  |                 |              |                   |
| -------------------- | ------ | --------------- | ------------ | ----------------- |
|                      |        | Atendimento da  | Criação de   | UC-11, RF-07, RN- |
|                      |        | senha           | atendimento  | 05                |
Pausar atendimento  Criar  Pausa do  Edição de  UC-12, RF-07
|     |     | atendimento  | atendimento  |     |
| --- | --- | ------------ | ------------ | --- |
Retomar atendimento  Criar  Retomada do  Edição de  UC-12, RF-07
|     |     | atendimento  | atendimento  |     |
| --- | --- | ------------ | ------------ | --- |
Criar
Encerrar atendimento com  Encerramento do  Edição de  UC-13, RF-07, RN-
| desfecho  |     | atendimento  | atendimento  | 11  |
| --------- | --- | ------------ | ------------ | --- |
Validar ou recusar  Criar  Validação do  Validação de  UC-13, RF-07, RN-
| encerramento  |     | atendimento  | atendimento  | 11  |
| ------------- | --- | ------------ | ------------ | --- |

A exigência de validação do supervisor é copiada da especialidade para o atendimento no momento em
que ele começa. Isso evita que uma alteração posterior no cadastro da especialidade mude a regra de um
atendimento já em curso.
A recusa da validação não devolve o atendimento ao estado anterior: ele permanece pendente de
validação, com o parecer do supervisor registrado. Encerramento sem desfecho é recusado com código
próprio.
9.9 Encaminhamento
| Operação  | Ação  | Recurso  | Permissão  | Rastreabilidade  |
| --------- | ----- | -------- | ---------- | ---------------- |
Registrar encaminhamento interno  Criar  Encaminhamentos do  Criação de  UC-14, UC-15,
| ou externo  |     | atendimento  | encaminhamento  | RF-08  |
| ----------- | --- | ------------ | --------------- | ------ |

A mesma operação atende às duas variantes, distinguidas pelo tipo informado. O encaminhamento
interno gera uma nova senha na especialidade de destino, vinculada à senha de origem, e a resposta
informa o número gerado para que a tela possa exibi-lo. O encaminhamento externo é apenas registrado,
sem gerar entrada de fila. Especialidade de destino inativa produz recusa com código específico.
Especialidade de destino com bloqueio ativo por ausência de supervisor não impede o encaminhamento
— apenas informa que a chamada está suspensa naquela fila.
9.10 Ausência e desistência
| Operação  | Ação  | Recurso  | Permissão  | Rastreabilidade  |
| --------- | ----- | -------- | ---------- | ---------------- |
Registrar ausência  Criar  Ausência da senha  Criação em  UC-16, RF-09, RN-
|     |     |     | ausências  | 12, RN-13  |
| --- | --- | --- | ---------- | ---------- |
26

| Operação  | Ação  | Recurso  | Permissão  | Rastreabilidade  |
| --------- | ----- | -------- | ---------- | ---------------- |
Registrar desistência voluntária  Criar  Desistência da senha  UC-17, RF-09
Criação em
ausências

O registro manual de ausência antes do vencimento da tolerância exige confirmação explícita, e a
resposta informa o tempo restante. A ausência automática por expiração não passa por esta operação: é
produzida pela rotina agendada, sem intervenção de usuário, e gera notificação no painel e registro no
histórico do paciente.
A reincidência de ausências não bloqueia a triagem. Ela aparece como aviso na resposta da busca de
paciente, indicando a restrição aplicável, porque a política da unidade prevê restrição ou exigência de
confirmação prévia para novos agendamentos, e não impedimento de atendimento.
9.11 Indicadores, alertas e auditoria
| Operação  | Ação  | Recurso  | Permissão  | Rastreabilidade  |
| --------- | ----- | -------- | ---------- | ---------------- |
Consultar indicadores consolidados  Consultar  Resumo de  Consulta de  UC-18, RF-10
| do período                        |            | indicadores    | indicadores  |               |
| --------------------------------- | ---------- | -------------- | ------------ | ------------- |
|                                   | Consultar  |                |              | UC-18, RF-10  |
| Consultar volume de atendimentos  |            | Volume por     | Consulta de  |               |
| por especialidade                 |            | especialidade  | indicadores  |               |
Exportar indicadores  Consultar  Exportação de  Exportação de  UC-18, RF-10,
|     |     | indicadores  | indicadores  | RNF-05  |
| --- | --- | ------------ | ------------ | ------- |
Listar alertas ativos e histórico  Consultar  Alertas  Consulta de  UC-19, RF-11
alertas
Reconhecer alerta  Criar  Reconhecimento do  Reconhecimento  UC-19, RF-11
|     |     | alerta  | de alerta  |     |
| --- | --- | ------- | ---------- | --- |
Acionar medida de escalonamento  Criar  Escalonamento do  Escalonamento de  UC-19, RF-11
|     |     | alerta  | alerta  |     |
| --- | --- | ------- | ------- | --- |
Consultar trilha de auditoria  Consultar  Eventos de auditoria  Consulta de  UC-20, RF-12,
|                                 |            |                      | auditoria    | RNF-05        |
| ------------------------------- | ---------- | -------------------- | ------------ | ------------- |
|                                 | Consultar  | Evento de auditoria  |              | UC-20, RF-12  |
| Consultar detalhe de evento de  |            |                      | Consulta de  |               |
| auditoria                       |            |                      | auditoria    |               |
Exportar trilha filtrada  Consultar  Exportação de  Exportação de  UC-20, RF-12,
|     |     | auditoria  | auditoria  | RNF-05  |
| --- | --- | ---------- | ---------- | ------- |

Os indicadores têm granularidade mínima de especialidade e período; nenhuma resposta expõe dado
individual identificável de paciente. Toda exportação, tanto de indicador quanto de auditoria, gera ela
própria um evento na trilha — inclusive a exportação da trilha.
O reconhecimento de um alerta muda sua apresentação sem removê-lo da lista. A normalização é
automática, quando o tempo de espera volta ao limite, e registra o horário. O bloqueio de fila por
ausência de supervisor aparece nessa mesma lista, como alerta de origem distinta.
9.12 Operações públicas
27

| Operação  | Ação       | Recurso            | Permissão  | Rastreabilidade     |
| --------- | ---------- | ------------------ | ---------- | ------------------- |
|           | Consultar  | Painel da unidade  | Pública    | UC-09, W-09, RF-05  |
Consultar estado do telão da sala
de espera
Consultar posição de uma senha  Consultar  Senha pública  Pública  UC-08, W-10, RF-04

As operações públicas não exigem autenticação e servem apenas ao telão e à consulta do paciente. Estão
sujeitas a limite de trinta requisições por minuto por origem. Nunca devolvem nome de paciente,
prioridade clínica ou dado de saúde. Senha inexistente ou de outro dia produz mensagem neutra, sem
revelar se aquele número já existiu.
9.13 Verificações de saúde
| Operação  | Ação  | Recurso  | Permissão  | Uso  |
| --------- | ----- | -------- | ---------- | ---- |
Verificar se o processo está vivo  Consultar  Estado do serviço  Pública  Responde sem tocar o
banco de dados
|     | Consultar  |     | Pública  |     |
| --- | ---------- | --- | -------- | --- |
Verificar se o serviço está apto a  Prontidão do  Verifica o banco e a
| receber tráfego  |     | serviço  |     | aplicação das migrações  |
| ---------------- | --- | -------- | --- | ------------------------ |

9.14 Autorização
Cada operação exige uma permissão formada por módulo e operação. O catálogo completo é fornecido
pelo próprio sistema e alimenta a matriz de permissões da tela de perfis, de modo que a criação de um
perfil novo pela administração nunca dependa de alteração em código.
| Módulo  Operações disponíveis                                     |     |     |     |     |
| ----------------------------------------------------------------- | --- | --- | --- | --- |
| Cadastros  consultar, criar, editar, excluir                      |     |     |     |     |
| Perfis  consultar, criar, editar, excluir                         |     |     |     |     |
| Triagem  consultar, criar, editar, consultar informação sensível  |     |     |     |     |
| Senha  consultar, criar, reclassificar                            |     |     |     |     |
| Fila  consultar, criar, editar, excluir, chamar                   |     |     |     |     |
| Alocação  consultar, criar, editar                                |     |     |     |     |
Atendimento  consultar, criar, editar, validar, consultar informação sensível
| Encaminhamento  consultar, criar           |     |     |     |     |
| ------------------------------------------ | --- | --- | --- | --- |
| Ausência  consultar, criar                 |     |     |     |     |
| Indicadores  consultar, exportar           |     |     |     |     |
| Alertas  consultar, reconhecer, escalonar  |     |     |     |     |
| Auditoria  consultar, exportar             |     |     |     |     |

A falta de permissão produz resposta de acesso negado com corpo neutro, sem revelar existência ou
volume de dados. Recurso inexistente sob permissão negada também responde acesso negado, e nunca
recurso não encontrado, porque a segunda resposta revelaria que o recurso existe.
28

9.15 Projeções de senha e proteção de dado sensível
A senha tem três projeções distintas e mutuamente exclusivas. A escolha da projeção é feita pelo tipo de
consumidor, e não por filtragem aplicada sobre um objeto único. A diferença é decisiva: um objeto
filtrado ainda carrega os campos que foram omitidos em algum ponto do caminho, e basta um
componente novo esquecido para expô-los.
Projeção Onde é usada Campos Auditoria
Pública Telão e consulta do Número, especialidade e local Nenhuma
paciente
Resumo Listagens, painel de fila e Número, iniciais do paciente, especialidade, Nenhuma
indicadores tipo, prioridade, status e horários
Detalhe Ficha individual e tela de Tudo do resumo, mais triagem completa, Registra evento
atendimento motivo, justificativa e observações de consulta a
informação
sensível
A abreviação do nome do paciente acontece no servidor. O nome completo não sai do sistema em
nenhuma operação de listagem — dado que não sai do servidor não vaza nas ferramentas de inspeção do
navegador. A projeção pública é servida por um caminho que não passa pelo interceptador de sessão e
que usa um serializador que conhece somente os três campos previstos; não é filtro por omissão, é um
tipo que não tem os demais campos.
A integração contínua reprova a construção se qualquer resposta de listagem contiver os campos de
motivo, justificativa clínica, observações ou nome completo. É a verificação automática que transforma o
requisito de privacidade em algo checável, em vez de depender de revisão humana a cada mudança.
9.16 Tratamento de erros
Todos os erros seguem o mesmo formato padronizado de detalhes de problema, com dois campos
adicionais: um código estável e legível por máquina, e a lista de campos com problema, presente apenas
em erro de validação. A mensagem de cada campo é escrita para ser exibida diretamente abaixo do
campo no formulário, e descreve a causa e o próximo passo, nunca apenas que houve erro.
Código Situação Regra de origem
Validação Campo obrigatório ausente ou inválido —
Duplicidade Registro já cadastrado; a resposta traz o identificador do UC-01
registro existente
Vínculo ativo Exclusão de cadastro com vínculo ativo; a resposta sugere RF-01
inativar
Transição inválida Mudança de estado não prevista na máquina de estados da P-01
senha
Versão desatualizada Conflito de edição concorrente; a resposta traz o estado —
atual
Fila bloqueada Chamada em fila sem supervisor disponível RN-10
Fila vazia Chamada da próxima senha sem nenhuma senha UC-09
aguardando
29

Código Situação Regra de origem
Sem fila configurada Emissão de senha para especialidade sem fila ativa UC-06
Justificativa obrigatória Reclassificação de prioridade sem justificativa RN-01
Recurso indisponível Recurso deixou de estar disponível entre a sugestão e a RN-05, RN-09
confirmação
Limite de supervisão Supervisor no limite de estudantes simultâneos RN-08
atingido
Supervisor indisponível Nenhum supervisor habilitado; dispara o bloqueio da fila RN-07, RN-10
Desfecho obrigatório Encerramento de atendimento sem desfecho informado UC-13
Especialidade inativa Encaminhamento interno para especialidade inativa UC-14
Paciente com restrição Reincidência de ausências; vem como aviso, não como erro, RN-13
porque não bloqueia a triagem
9.17 Versionamento do contrato
A versão é indicada no endereçamento do serviço. Mudança compatível — campo novo opcional,
operação nova — não altera a versão. Mudança incompatível — remoção de campo, mudança de tipo,
mudança de significado — exige nova versão e um período de convivência entre as duas.
O contrato é alterado em revisão própria, separada da implementação, aprovada pela liderança técnica e
pela pessoa responsável pelo módulo no servidor, e integrado antes de a implementação começar dos
dois lados. Com o contrato pronto, a equipe de interface constrói a tela sobre uma simulação gerada a
partir das próprias definições de validação, sem esperar o servidor ficar pronto. Quando o servidor
entrega, a troca é de destino apenas. Se a tela quebra nesse momento, quem se afastou do contrato está
identificado, e a verificação automática de contrato aponta o lado.
30

10. Mapeamento das tecnologias
Este capítulo cumpre a exigência de mapeamento das tecnologias empregadas. Cada escolha está
associada à camada que atende, à versão de referência e ao motivo que a sustenta, sempre ligado a um
requisito ou a uma restrição do projeto.
10.1 Decisão estruturante: uma linguagem única
A decisão de maior alcance foi adotar a mesma linguagem tipada em todas as camadas, do banco à tela.
Três consequências a justificam no contexto deste projeto.
• As definições de validação são escritas uma única vez em um pacote compartilhado e usadas tanto
pela camada de entrada do servidor quanto pelo formulário da interface. Deixa de existir a
divergência de validação entre cliente e servidor, que é a origem mais comum de defeito em
formulário com campo condicional — precisamente o caso da ficha de triagem e da reclassificação
de prioridade.
• Com apenas duas pessoas trabalhando no servidor, as três pessoas de interface conseguem ler e
revisar o código do servidor, e vice-versa. Numa equipe de cinco, isso é a diferença entre uma
revisão real e uma aprovação de cortesia.
• A plataforma escolhida para o servidor oferece guardas e interceptadores como recurso de primeira
classe, que é exatamente a forma de implementar controle de acesso e auditoria implícita sem
espalhar condicionais por toda a camada de entrada.
O custo é conhecido e aceito: a plataforma de execução do servidor exige cuidado com processamento
pesado de processador, e por isso os relatórios são agregados no banco; e o ecossistema muda rápido,
razão pela qual as versões são fixadas e só sobem por decisão registrada.
10.2 Tecnologias por camada
Camada Tecnologia Versão de Justificativa
referência
Linguagem TypeScript 5 Tipagem estática compartilhada entre
servidor e interface, permitindo que o
contrato seja verificado em tempo de
compilação nos dois lados
Interface de usuário Next.js com React 15 e 19 Renderização no servidor reduz o volume
de código enviado a terminais modestos
da clínica; o roteamento por segmento
espelha diretamente a navegação por
módulos adotada
Estado de servidor na TanStack Query 5 Cache, revalidação por intervalo e
interface invalidação disparada por evento do canal
de tempo real, exatamente o
comportamento exigido pelo painel de fila
Formulários React Hook Form com Zod — Validação idêntica no cliente e no
servidor, reaproveitando as definições do
pacote de contratos
Estilo e componentes Tailwind CSS com 4 As primitivas entregam foco preso em
primitivas Radix janela modal, atributos de acessibilidade
corretos e navegação por teclado, tudo
31

Camada Tecnologia Versão de Justificativa
referência
exigido pelo requisito de acessibilidade,
sem reimplementação própria
Serviço de negócio NestJS 11 Módulos, guardas e interceptadores
mapeiam diretamente o controle de
acesso por permissão e a auditoria
implícita
Mapeamento objeto- Prisma 6 Migrações versionadas, tipagem forte e
relacional transação interativa, esta última
necessária na chamada da próxima senha
Banco de dados PostgreSQL 16 Bloqueio de linha com salto de linhas
bloqueadas, índices parciais e funções de
janela para os indicadores
Canal de tempo real Socket.IO 4 Reconexão automática e agrupamento por
especialidade; adaptador externo opcional
quando houver mais de uma instância do
serviço
Agendamento de Módulo de agendamento — Expiração de tolerância e varredura de
rotinas do NestJS tempo de espera
Autenticação Credencial de acesso e de — Sessão curta, adequada a terminal
renovação em marcador compartilhado na recepção
de navegador protegido
Testes Vitest, Supertest e — Unidade, integração da camada de
Playwright entrada e ponta a ponta, incluindo
verificação automática de acessibilidade
Qualidade de código ESLint, Prettier, Husky, lint- — Padrão único de código e de mensagem de
staged e Commitlint registro de alteração, verificado antes do
envio
Empacotamento do pnpm com Turborepo — Repositório único com as três aplicações e
repositório os pacotes compartilhados, com
construção incremental
Ambiente local Docker Compose, com — Ambiente idêntico para os cinco
banco e administrador de integrantes, sem instalação manual de
banco dependência
Integração contínua GitHub Actions — Análise estática, testes, construção e
verificação do contrato a cada revisão
proposta
10.3 Alternativas consideradas e recusadas
Alternativa Motivo da recusa
Spring Boot no servidor com Duas linguagens e duas cadeias de construção; com apenas duas pessoas no
React na interface servidor, o custo de troca de contexto não compensa
Plataforma .NET com Angular Nenhum integrante declarou familiaridade; a curva de aprendizado não cabe
no prazo
32

Alternativa Motivo da recusa
FastAPI no servidor com React na Boa opção para os relatórios, mas o canal de tempo real e o controle de
interface acesso por permissão exigiriam mais código próprio do que na plataforma
escolhida
10.4 Como a tecnologia atende cada requisito não funcional
Requisito Atendimento técnico
RNF-01 Segurança e controle Guarda de permissão por operação, declarada de forma explícita em cada ponto
de acesso de entrada; ação sem permissão não é renderizada na interface; resposta de
negação neutra
RNF-02 Proteção de dado Projeções distintas para senha; transporte criptografado; colunas de dado
sensível clínico com acesso auditado; nome abreviado no servidor
RNF-03 Desempenho Índice composto que implementa a ordenação da fila; canal de eventos no lugar
de consulta periódica pesada; paginação obrigatória em toda listagem
RNF-04 Disponibilidade Verificações de vivacidade e de prontidão; reinício automático do contêiner;
aviso de perda de conexão com congelamento das ações de escrita
RNF-05 Rastreabilidade Registro de auditoria imutável, alimentado por interceptador global e protegido
por revogação de permissão no banco
RNF-06 Usabilidade e Biblioteca de componentes sobre primitivas acessíveis; conformidade com o
acessibilidade nível AA no sistema e contraste reforçado nas telas públicas; verificação
automática na integração contínua
RNF-07 Conformidade legal Trilha de consulta a informação clínica; aviso de registro exibido uma vez por
sessão nas telas que abrem prontuário; política de retenção configurável
RNF-08 Escalabilidade Serviço sem estado em memória; adaptador externo para o canal de eventos;
filtro por unidade em todas as consultas
RNF-09 Cópia de segurança Extração diária agendada do banco, com objetivos de ponto e de tempo de
recuperação documentados no manual de operação
33

11. Decisões arquiteturais registradas
Três decisões foram registradas formalmente por afetarem estrutura, custo de mudança ou
conformidade legal. Cada uma traz o contexto que a motivou, a decisão tomada e as consequências
assumidas.
11.1 Linguagem única em todas as camadas
Aspecto Conteúdo
Situação Aceita
Contexto Equipe de cinco pessoas, com três em interface e duas no servidor; vinte casos de uso, dezoito
telas e prazo de projeto acadêmico; telas de fila e telão exigindo atualização em tempo quase
real; controle de acesso e trilha de auditoria exigidos em todo o sistema
Decisão Uma única linguagem tipada em todas as camadas, com o pacote de contratos como fonte
única de validação
A favor Validação escrita uma vez; revisão cruzada viável entre interface e servidor; guardas e
interceptadores nativos para acesso e auditoria; tempo real sem infraestrutura adicional
Contra Cuidado necessário com processamento pesado de processador, resolvido agregando
relatórios no banco; ecossistema volátil, resolvido fixando versões e subindo-as apenas por
decisão registrada
11.2 Navegação por módulos, com painel lateral apenas na fila
Os dois documentos de interface divergiam entre si, e a divergência estava declarada no próprio material.
A especificação em texto descrevia uma navegação por área de trabalho de perfil, com painel lateral
conduzindo todo o ciclo de atendimento sem troca de tela. Os wireframes na versão 2, mais recentes,
mantinham a navegação por módulos e afirmavam explicitamente que a especificação deveria ser
atualizada nesse ponto.
Foi adotada a navegação por módulos, por ser a definição do artefato mais recente e por ser a que
orienta a atualização do outro. Um único elemento do modelo alternativo foi preservado: dentro do
módulo de fila, o detalhe de uma senha abre painel lateral contextual com as ações permitidas ao perfil.
Ações com formulário longo permanecem como telas próprias.
A área de trabalho por perfil não foi perdida: virou filtragem do menu por permissão, somada a uma tela
inicial cujo conteúdo muda por perfil. O comportamento observado pelo usuário é próximo, sem o custo
de manter seis layouts distintos.
Esta decisão tem pendência: confirmar o modelo com a coordenação da clínica-escola antes do início da
etapa que constrói o módulo de fila. Se a coordenação preferir o modelo alternativo, o impacto se
concentra nesse módulo, e os demais não mudam.
11.3 Separação de dado sensível no contrato
Se um mesmo objeto de senha fosse devolvido por todas as operações, bastaria uma inspeção de rede
aberta na sala de espera, um registro de depuração esquecido ou um componente novo distraído para
expor dado de saúde. Pior: a trilha de consulta exigida pela conformidade legal ficaria impossível de
determinar, porque não haveria como distinguir o campo clínico efetivamente consultado do campo que
apenas trafegou junto.
34

Por isso o contrato define três projeções mutuamente exclusivas, descritas no capítulo 9. A consequência
assumida é ter mais tipos para manter e algumas telas precisarem de duas requisições — resumo na lista,
detalhe ao abrir a ficha. Em troca, a trilha passa a ser verdadeira, e a exigência de privacidade ganha
verificação automática, em vez de depender de revisão humana em cada mudança.
35

12. Estratégia de qualidade
12.1 Níveis de teste
Nível O que cobre Ferramenta
Unidade Regras de negócio na camada de serviço; cada caso de teste cita Vitest
no nome a regra que verifica
Integração Camada de entrada do servidor: códigos de resposta, validação, Supertest
permissão e formato de erro
Ponta a ponta Fluxos críticos completos, do acesso ao encerramento do Playwright
atendimento
Acessibilidade Verificação automatizada das telas, integrada aos testes de ponta Ferramenta de auditoria
a ponta de acessibilidade
Contrato Conformidade das respostas com o contrato, incluindo a Verificação própria na
proibição de campo clínico em listagem integração contínua
12.2 Critério de conclusão de uma tarefa
Uma tarefa só é considerada concluída quando todos os itens abaixo valem ao mesmo tempo.
• Critérios de aceite verificados manualmente.
• Testes automatizados cobrindo a regra de negócio citada na tarefa.
• Análise estática e formatação sem apontamento.
• Contrato atualizado, quando a operação mudou.
• Estados de carregamento, vazio, erro e ausência de permissão implementados, quando há tela.
• Acessibilidade verificada quando há tela: navegação por teclado, rótulo visível e contraste.
• Auditoria registrada quando a operação é sensível.
• Revisão aprovada por outra pessoa da equipe.
• Integração contínua sem falha.
12.3 Estados obrigatórios de tela
Toda tela de listagem ou consulta especifica quatro estados além do estado normal: carregando,
apresentado como esqueleto do conteúdo e nunca como indicador isolado em tabela; vazio, com ícone,
frase explicativa e ação primária quando aplicável; erro de carga, com mensagem e opção de nova
tentativa; e ausência de permissão, com mensagem neutra que não revela existência nem volume de
dados.
36

13. Rastreabilidade
13.1 Requisitos funcionais e onde são realizados
| Requisito  | Módulo do servidor  | Telas  | Casos de uso  |
| ---------- | ------------------- | ------ | ------------- |
RF-01 Cadastros base  Cadastros base  W-03, W-04  UC-01 a UC-03
| RF-02 Perfis e permissões  | Acesso           | W-02, W-05  | UC-04  |
| -------------------------- | ---------------- | ----------- | ------ |
| RF-03 Triagem              | Triagem e senha  | W-06        | UC-05  |
RF-04 Fila de atendimento  Fila e chamada; Triagem e  W-07, W-08, W-10  UC-06, UC-08
senha
| RF-05 Chamadas de senha     | Fila e chamada  | W-08, W-09  | UC-09          |
| --------------------------- | --------------- | ----------- | -------------- |
| RF-06 Alocação de recursos  | Alocação        | W-11        | UC-10          |
| RF-07 Atendimentos          | Atendimento     | W-12, W-13  | UC-11 a UC-13  |
| RF-08 Encaminhamentos       | Encaminhamento  | W-14        | UC-14, UC-15   |
| RF-09 Ausências e           | Ausências       | W-15, W-16  | UC-16, UC-17   |
desistências
| RF-10 Indicadores  | Indicadores  | W-17  | UC-18  |
| ------------------ | ------------ | ----- | ------ |
| RF-11 Alertas e    | Alertas      | W-18  | UC-19  |
escalonamento
| RF-12 Trilha de auditoria  | Auditoria  | W-18  | UC-20  |
| -------------------------- | ---------- | ----- | ------ |

13.2 Regras de negócio e onde são garantidas
| Regra  | Garantia técnica  |     |     |
| ------ | ----------------- | --- | --- |
RN-01 Justificativa para  Restrição de verificação no banco sobre o tamanho mínimo, somada à validação
| reclassificação  | compartilhada entre servidor e formulário  |     |     |
| ---------------- | ------------------------------------------ | --- | --- |
RN-02 Prioridade legal  Enquadramento legal registrado na triagem sinaliza automaticamente a prioridade
preferencial; a etiqueta aparece no cartão da senha
RN-03 Urgência clínica  Restrição de verificação condicional exigindo justificativa clínica quando a
prioridade é urgente
RN-04 Ordenação da fila  Índice composto que ordena por prioridade, horário de chegada e existência de
agendamento prévio; a mesma ordenação serve à consulta do quadro e à seleção
da próxima senha
RN-05 Disponibilidade do  Índice único parcial impedindo duas alocações não liberadas para o mesmo
| estudante  | estudante  |     |     |
| ---------- | ---------- | --- | --- |
RN-06 Compatibilidade do  Comparação de conjuntos entre as habilitações e procedimentos do estudante e
| estudante  | os exigidos pela especialidade  |     |     |
| ---------- | ------------------------------- | --- | --- |
RN-07 Disponibilidade do  Verificação de habilitação e de turno na alocação; ausência total dispara o
| supervisor  | bloqueio da fila  |     |     |
| ----------- | ----------------- | --- | --- |
RN-08 Limite de estudantes  Contagem dentro da transação de alocação, com bloqueio na linha do supervisor
por supervisor
37

Regra Garantia técnica
RN-09 Compatibilidade da Comparação entre os recursos da sala e os recursos exigidos pela especialidade,
sala mais índice único parcial impedindo dupla alocação
RN-10 Bloqueio por Estado único de bloqueio na fila, alimentando o aviso na tela, a recusa da chamada
ausência de supervisor e o alerta de gestão
RN-11 Validação do Exigência copiada da especialidade no início do atendimento, com estado
encerramento intermediário de pendência de validação
RN-12 Senha não atendida Rotina agendada de expiração de tolerância, que marca ausência ou devolve à fila
conforme a política
RN-13 Restrição por Contagem de ausências dentro da janela configurada, apresentada como aviso na
reincidência busca de paciente e no registro de ausência, sem bloquear a triagem
13.3 Divisão de responsabilidades pela arquitetura
Integrante Frente Partes sob responsabilidade
Caio Renato dos Santos Interface e liderança Biblioteca de componentes, estrutura da aplicação
Claudino técnica operacional, fila, alocação, atendimento e
encaminhamento; cliente do canal de eventos; revisão de
toda mudança de contrato
Caio Gabriel Pereira de Interface Acesso, tela inicial por perfil, cadastros base, perfis e
Menezes Correia permissões, triagem, emissão e reclassificação de senha
Thalita Pereira de Andrade Interface e Aplicação pública, ausências e desistências, indicadores,
qualidade alertas e auditoria; suíte de testes de ponta a ponta e
verificação de acessibilidade
Rildo Silva de Lima Junior Servidor Autenticação e controle de acesso, cadastros base,
triagem e senha, ausências e desistências, auditoria e
indicadores
Nathan Rodrigues da Costa Servidor Fila, chamada, alocação, atendimento, encaminhamento,
Silva canal de eventos, alertas de tempo de espera e rotinas
agendadas
A regra de revisão cruza interface e servidor: toda proposta de mudança precisa de aprovação de alguém
da outra frente, para que a interpretação do contrato seja verificada das duas pontas. Mudança no
contrato exige aprovação da liderança técnica e da pessoa responsável pelo módulo no servidor, e não
pode vir acompanhada de implementação.
38

14. Riscos e pendências
14.1 Riscos arquiteturais
Risco Impacto Mitigação
A coordenação da clínica Retrabalho concentrado no Confirmar o modelo antes do início da etapa que
preferir a navegação por módulo de fila constrói o módulo de fila; os demais módulos
área de trabalho de perfil não mudam em nenhum dos cenários
Ambiente de produção Impossibilidade de validar Manter homologação equivalente à produção
indefinido transporte criptografado, em tudo que está sob controle da equipe, e
cópia de segurança e retenção registrar como pendência os itens que
em condições reais dependem da instituição
Valores de comportamento Sistema funcionando com Nenhum valor está fixado em código; todos são
ainda não definidos pela números provisórios, que parâmetros editáveis, com possibilidade de valor
unidade podem não refletir a prática da específico por especialidade
clínica
Volume de escrita Contenção na seleção da A seleção já usa bloqueio de linha com salto de
concorrente maior que o próxima senha linhas bloqueadas, que degrada distribuindo
previsto na fila senhas diferentes em vez de serializar as
requisições
Equipe com uma única Dependência de pessoa em Revisão cruzada obrigatória e testes de
pessoa dominando o canal uma parte crítica integração cobrindo os três espaços de eventos
de eventos
14.2 Pendências registradas
• Confirmação do modelo de navegação com a coordenação da clínica-escola.
• Definição dos cinco valores de parametrização da unidade.
• Definição da base legal e do prazo de retenção de triagem e atendimento.
• Definição do ambiente de produção pela instituição, com transporte criptografado, cópia de
segurança diária e política de retenção.
• Atualização da seção de navegação da especificação de interface, para refletir o mapa de módulos
por permissão no lugar do mapa por área de trabalho.
14.3 Evolução prevista
Três caminhos de evolução estão contemplados pela arquitetura, sem exigir reestruturação. O suporte a
múltiplas unidades já está no modelo de dados e no filtro de todas as consultas, restando apenas
exercitá-lo. A execução do serviço em mais de uma instância depende apenas de habilitar o adaptador
externo do canal de eventos. E os relatórios podem migrar para uma réplica de leitura do banco, já que
são agregações e não dependem de escrita.
39