SEPREV/AL - Secretaria de Estado de Prevenção à

Violência CENTRO UNIVERSITÁRIO CESMAC

Izaac Duarte de Alencar

DOCUMENTO DE REQUISITOS DE SISTEMA
Aplicação Violência Zero

Maceió/AL

2019
Lista de ilustrações

Figura 1 – Arquitetura Geral Proposta. . . . . . . . . . . . . . . . . . . . . . . . . . . 16 Figura 2 –

Diagrama de caso de uso. . . . . . . . . . . . . . . . . . . . . . . . . . . . 17 Figura 3 – Diagrama

de  Classes  (BackEnd).  .  .  .  .  .  .  .  .  .  .  .  .  . . . . . . . . . . . 18 Figura 4 – Diagrama de

Atividade (Módulo Denúncia). . . . . . . . . . . . . . . . . . 19 Figura 5 – Diagrama ER banco

interno APP. . . . . . . . . . . . . . . . . . . . . . . . 20 Figura 6 – Diagrama ER banco servidor

e contingencial. . . . . . . . . . . . . . . . . 21 Figura 7 – Telas Principais. . . . . . . . . . . . . . . .

. . . . . . . . . . . . . . . . . 26 Figura 8 – Telas Denúncia. . . . . . . . . . . . . . . . . . . . . . . . . . . .

. . . . . 26 Figura 9 – Telas Informações Gerais. . . . . . . . . . . . . . . . . . . . . . . . . . . . 27

Figura 10 – Telas Dicas de Especialistas. . . . . . . . . . . . . . . . . . . . . . . . . . 28 Figura 11

–  Telas  módulos  informações  e  dicas.  .  . . . . . . . . . . . . . . . . . . . . . 30 Figura 12 –

Telas  módulo  denúncia.  .  .  .  .  . . . . . . . . . . . . . . . . . . . . . . . . 31 Figura 13 – Telas

módulo contato. . . . . . . . . . . . . . . . . . . . . . . . . . . . . . 32

Sumário

1 Disposições Preliminares . . . . . . . . . . . . . . . . . . . . . . . . . . . . . 3 1.1 Visão geral

do documento . . . . . . . . . . . . . . . . . . . . . . . . 3 1.2 Prioridades e status dos

requisitos . . . . . . . . . . . . . . . . . . . . 3

2 Descrição Geral do Sistema . . . . . . . . . . . . . . . . . . . . . . . . . . . 5 3 Requisitos
funcionais (casos de uso) . . . . . . . . . . . . . . . . . . . . . . 5 3.1 [RF01] Informações

gerais . . . . . . . . . . . . . . . . . . . . . . . 5 3.2 [RF02] Dicas de procedimento . . . . . . . .

. . . . . . . . . . . . 5 3.3 [RF03] Canal de Denuncias . . . . . . . . . . . . . . . . . . . . . . 6 3.4

[RF04] Canais de Contato . . . . . . . . . . . . . . . . . . . . . . . 6 4 Requisitos não

funcionais . . . . . . . . . . . . . . . . . . . . . . . . . . . . 6 4.1 [RNF01] Interoperabilidade . . .

. . . . . . . . . . . . . . . . . . . 6 4.2 [RNF02] Banco Dados . . . . . . . . . . . . . . . . . . . . . . . .

. 6 4.3 [RNF03] Contingência . . . . . . . . . . . . . . . . . . . . . . . . . 6 4.4 [RNF04]

Apresentação . . . . . . . . . . . . . . . . . . . . . . . . . 7 4.5 [RNF05] Segurança . . . . . . . .

. . . . . . . . . . . . . . . . . . 7 5 Inventário de Dados . . . . . . . . . . . . . . . . . . . . . . . . . . . .

. . . 8 6 Especificação de requisitos do sistema . . . . . . . . . . . . . . . . . . . . 12 7

Arquitetura Geral Proposta . . . . . . . . . . . . . . . . . . . . . . . . . . . 16 7.1 Diagrama de

caso de uso . . . . . . . . . . . . . . . . . . . . . . . . 17 7.2 Diagrama de Classes . . . . . . . .

. . . . . . . . . . . . . . . . . . . 18 7.3 Diagrama de Atividade (Módulo Denúncia) . . . . . . .

. . . . . . . 19 7.4 Diagrama Entidade Relacionamento Banco Interno . . . . . . . . . 20

7.5 Diagrama Entidade Relacionamento Banco Servidor e Contingencial 21 8

Disposições Finais . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . 22

Apêndices 23 APÊNDICE A PROJETO GRÁFICO DE INTERFACE . . . . . . . . . . . . . . . . . .
24 1 Arquitetura de Informação . . . . . . . . . . . . . . . . . . . . . . . . . . . 24 2 Inventário de

conteúdo . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . 24 3 Fluxos de navegação . . . . . . . . .

. . . . . . . . . . . . . . . . . . . . . . 26 4 WireFrames - proposta de telas . . . . . . . . . . . . . . . . .

. . . . . . . . . 29

3

1 Disposições Preliminares

Este documento especifica os requisitos do Aplicativo Móvel “Violência Zero”,
fornecendo  aos  projetistas  e  desenvolvedores  as  informações  necessárias  para  o
projeto  e  implementação,  assim  como  para  a  realização  dos  testes e homologação
do sistema.

1.1 Visão geral do documento

Além desta seção introdutória, as seções seguintes estão organizadas como

descrito abaixo.

• Seção 2 Descrição geral do sistema: apresenta uma visão geral do sistema,

caracterizando qual é o seu escopo.

• Seção 3 Requisitos funcionais (casos de uso): especifica brevemente os casos

de uso do sistema.

• Seção 4 Requisitos não funcionais: especifica brevemente os requisitos não

funcionais do sistema.

• Seção 5 Inventário de dados: aponta os dados utilizados coletados para o

funcionamento do sistema.

• Seção 6 Especificação de requisitos do sistema: descreve requisitos funcionais

e  não  funcionais  mais  detalhadamente.  No  caso  de  requisitos  funcionais,
descreve  os  fluxos  de  eventos  principal,  alternativos,  atores,  entradas  e
saídas de cada caso de uso a ser implementado.

• Seção 7 Arquitetura geral do sistema: apresenta uma visão geral de alto nível
da  arquitetura  prevista inicialmente no sistema, mostrando a distribuição das
funções nos módulos do sistema.

1.2 Prioridades e status dos requisitos

Para melhor compreensão da prioridade e status dos requisitos nas seções 3
e  4,  foram  adotadas  as  denominações  “essencial”,  “importante”  e  “desejável” para
prioridade e “em análise”, “em negociação” e “validado” para status.

Em relação à prioridade do requisito:

Essencial : requisito fundamental, sem o qual o sistema não entrará em funcionamento.

Importante : requisito sem o qual o sistema entrará em funcionamento, porém

não de forma satisfatória.

4

Desejável  :  requisito  que  não  compromete  o  funcionamento  satisfatório  do
sistema,  isto  é,  não  obta  as  funcionalidades  básicas  da  aplicação.  Estes
requisitos podem ter sua implementação deixada para versões posteriores do
sistema.

Em relação ao status do requisito:

Em análise : requisto que está em fase de concepção. Este requisito não foi

ainda apresentado como parte possível de ser integrada no sistema.

Em negociação : requisito que está em fase discussão. Este requisito foi
apresentado como parte possível de ser integrada no sistema.

Validado : requisito que foi aprovado para integração ao sistema.

5

2 Descrição Geral do Sistema

O  objetivo  do  aplicativo  é  prevenir  e  romper  o  ciclo  da violência em Alagoas,

inicialmente  nas  Escolas,  com  foco  maior  na  Criança  e  Adolescente.  O  projeto

incentiva  ainda  a  discussão  e  o  debate  junto  aos  sistemas  de  ensino  para  que

definam  um  fluxo  de  notificação  e  encaminhamento  das  situações  de  violência

identificadas ou vivenciadas na escola.

Pretende-se, portanto, a criação de um aplicativo de celular com informações e
orientações  sobre  ações  de  combate  à  violência,  de  ajuda  ao  cidadão  e  apoio
técnico.  O  app  será  usado  também  para  mensurar  as  estatísticas  dos  casos
registrados  pela  rede  pública  estadual  e  privada  de  ensino  e  possibilitar  assim

trabalhar diretamente onde está o problema.

Neste  aplicativo,  deve  conter  informações  básicas sobre violência, quais são
os  tipos  de  violência  mais  comuns  e  possíveis  de  acontecer na escola, bem como
dicas  de  como  se  portar  diante  de  possíveis  violações  de  direitos  e  canais  de
denúncia.

Deve  conter  ainda  um  canal  direto  para denúncias, vinculado à Seprev, que
será a responsável por receber a denúncia e fazer os devidos encaminhamentos. A
atuação  posterior  às  denúncias  serão  tratadas  por  profissionais  da  Seprev  e  por
docentes e discentes do Centro Universitário Cesmac.

3 Requisitos funcionais (casos de uso)

Esta seção é responsável pela apresentação das requisitos funcionais. Para

isto, cada requisito encontra-se com seu código (RF), seguido de seu título,
descrição, status e prioridade.

3.1 [RF01] Informações gerais

O sistema fornecerá conhecimento sobre o domínio da ferramenta, abarcando
o  conceito  de  violência,  tipos  de  violência,  bullying,  exploração  sexual,  vendas  de

bebidas alcoolicas para crianças e adolescentes, trabalho infantil, automotição.

Status : Em negociação.

Prioridade : Essencial.

3.2 [RF02] Dicas de procedimento

O sistema fornecerá conhecimento sobre como se portar diante de casos de
violência, a partir de informações dadas por equipe interdisciplicar de profissionais.

Status : Em negociação.

Prioridade : Essencial.

3.3 [RF03] Canal de Denuncias

6

O sistema fornecerá uma interface para que o usuário noticie possível caso de

violência através de formulário, com os seguintes dados (inicialmente):

Status : Em negociação.

Prioridade : Essencial.

3.4 [RF04] Canais de Contato

O sistema fornecerá canais de contato: telefones - disque 100, Seprev, polícia

e formulário de contado para e-mail específico na Seprev.

Status : Em negociação.

Prioridade : Essencial.

4 Requisitos não funcionais

Esta seção é responsável pela apresentação das requisitos não funcionais.
Para isto, cada requisito encontra-se com seu código (RNF), seguido de seu título,

descrição, status e prioridade.

4.1 [RNF01] Interoperabilidade

O sistema funcionará para dispositivos Android.

Status : Em negociação.

Prioridade : Essencial.

4.2 [RNF02] Banco Dados

O sistema terá conectibilidade com um banco de dados centralizado.

Status : Em negociação.

Prioridade : Essencial.

4.3 [RNF03] Contingência

O sistema salvará os dados localmente até estabelecimento e transferência

dos dados para o servidor de banco de dados.

Status : Em negociação.

Prioridade : Desejável.

4.4 [RNF04] Apresentação

7

O sistema terá simplicidade, usabilidade e intuitividade na interface gráfica com

o usuário. Projeto gráfico em separado.

Status : Em negociação.

Prioridade : Desejável.

4.5 [RNF05] Segurança

O sistema considerará a condifencialidade, integridade e disponibilidade dos

dados coletados e tratados pelo app.

Status : Em negociação.

Prioridade : Desejável.

5 Inventário de Dados

8

Este  seção  destina-se  a  listar  e  descrever  os  dados  relevantes  para  a
realização do objetivo da aplicação. Além disto, servirá de guia para a determinação
de métricas que substanciarão análise estatística para tomada de decisão baseadas
em  evidência.  A  tabela  1 mostra a lista de dados que serão utilizadas na denúncia.
Os dados serão coletados pela aplicação e enviados para armazenamento em banco

de dados apropriado em um servidor on-line.

Tabela 1 – Dados essenciais para o módulo denúncia.

ID Dado Descrição Possíveis Respostas 01 classificacao destina-se a

classificação

do denunciante entre duas

denunciante

categorias definidas no
módulo de denúncia.

está vinculado. Este dado
depende da indicação positiva

02 identificacao destina-se a

apontar se o denunciantes se
identificou na

realização da denúnica.

da referência ID03.
• Vítima

• Testemunha

03 vinculoInstituicao destina-se a
informar se o denunciante possui

algum vínculo com alguma

instituição de ensino.

04 tipoInstituicao destina-se a
informar qual a natureza da
instituição de

ensino que o denunciantes

está vinculado. Este dado
depende da indicação positiva

da referência ID03.
05 instituicao destina-se a
inficação de qual instituição o

• Identificado • Anônimo

• Sim

• Não

• Pública

• Privada

texto livre.

9

06 tipoViolacao destina-se a classificar o
tipo de violação que será
denunciada no módulo de
denúncia.

08 dataViolacao destina-se a indicar a
data da violação (referência ID03)

motivo da denúncia.

09 estabelecimento destina-se a
informar o nome do estabelecimento
onde

ocorreu a violação (referência
ID03) motivo da denúncia.
Este dado somente será
considerado para o item
Venda de bebibas alcoólicas
(crianças ou adolescentes).

07 localViolacao destina-se a apontar
onde ocorreu a violação (referência

ID03) motivo da denúncia.

10 grauRelacionamento destina-se a
obter o grau de relacionamento que o

denunciante possui com a

vítima. Somente quando da
classificação (referência ID01)

resultar em "testemunha".

11 descricao destina-se a descrever
como se deu a violação (referência

ID03) motivo da denúncia.

12 midiaAnexada destina-se a apontar
se a denúncia foi acompanhada

por algum arquivo de mídia.
• Bullying

• Amigo

• Professor

• Familiar

• Outros

texto livre.

• Exploração sexual

• Trabalho infantil

• Venda de bebibas alcoólicas (crianças
ou adolescentes)

• Sim

• Não

• Automutilação

texto livre.

texto formatado: __/__/____. texto livre.

10

13 tipoMidia destina-se a identificar qual
tipo de mídia foi anexada a
denúncia.

encontra no servidor depois de

realizado o upload.
• Fotografia • Vídeo

14 pathMidia destina-se a indicar
qual o caminho em que a mídia
se

texto livre.

A  tabela  2  mostra  os  dados  que  serão  utilizados  para  a  identificação  do

denunciante. Estes dados, a princípio serão diretamente direcionado para o servidor,
não sendo guardado localmente. Somente em denúncia por contigência estes dados
deverão  permanecer  no  aparelho  para  posterior  envio  assim  que  o  sistema  estiver

on-line e o servidor disponível.

Tabela 2 – Dados de identificação do denunciante.

ID Dado Descrição Possíveis Respostas

01 nome destina-se a informar o nome
do denunciante.

texto livre.

02 RG destina-se a informar o
documento de identidade do

denunciante.

texto livre.

03 CPF destina-se a informar o CPF do
denunciante.

04 rua_av destina-se a informar a
Rua/Av/etc. da residência do

denunciante.

05 número destina-se a informar o
número da residência do

denunciante.

06 bairro destina-se a informar o bairro
da residência do denunciante.

07 cidade destina-se a informar a
cidade da residência do denunciante.

08 estado destina-se a informar o
estado da residência do denunciante.

09 país destina-se a informar o país da
residência do denunciante.

texto livre.

texto livre.

texto livre.

texto livre.
11

10 complemento destina-se a informar
o
complemento da residência
do denunciante.

texto livre.

texto livre.

texto formatado (numérico).

texto formatado: . . -

6 Especificação de requisitos do sistema

12

RF01 Informações gerais

Sumário O caso de uso é responsável por apresentar informações gerais
aos usuários

Pré-condição Não há

Atores Usuário, App

Descrição

1. O usuário iniciará o sistema.

2. O sistema exibirá o menu geral de opções.

3. O usuário escolherá o item de informações gerais.

4. O sistema exibirá as opções de tipos de violênicas

cadastradas.

5. O usuário escolhera o tipo de violência que deseja

informação.

6. O sistema exibirá o conteúdo escolhido ao usuário.

RF02 Dicas de procedimento

Sumário O caso de uso é responsável por apresentar dicas de
procedimentos de especialistas.

Pré-condição Não há

Atores Usuário, App

Descrição

1. O usuário iniciará o sistema.

2. O sistema exibirá o menu geral de opções.

3. O usuário escolherá o item de dicas de procedimento de

especialistas.

4. O sistema exibirá as opções de dicas de procedimentos

cadastrados.

5. O usuário escolhera a dica de procedimento que deseja.

6. O sistema exibirá o conteúdo escolhido ao usuário.

13

RF03 Canal de Denuncias
Sumário O caso de uso é responsável por apresentar formulário para

envio de denuncias de violência contra criança ou

adolescente.

Pré-condição Não há

Atores Usuário, App, Servidor Online

Descrição

1. O usuário iniciará o sistema.

2. O sistema exibirá o menu geral de opções.

3. O usuário escolherá o item de canal de denuncias.

4. O sistema exibirá formulário de preenchimento de

Alternativas

denuncia.

5. O usuário irá preenchar o formulário contendo os

seguintes dados: 1) indicará ser vítima ou testemunha;
2) indicar a opção de identificação ou anonimato e; 3)
indicará se tem vínculo com alguma instituição de ensino.

6. O usuário identificará o tipo de violência: 1) Bullying;

2) Exploração sexual; 3) Trabalho infantil; 4) Venda
de bebida alcoólica para criança ou adolescente e; 5)

Automutilação.

7. O usuário informará o local e data da ocorrência da

violência.

8. O usuário indicará se deseja anexar mídia a denuncia: 1)

não anexar e; 2) anexar.

9. O usuário enviará a denuncia.

10. O sistema confirmará o envio.

14

• No passo 5, caso o usuário aponte seguir com
sua identificação deverá: 3) fornecer documento de
identidade ou CPF; 4)fornecer endereço completo; 5)
fornecer telefone e; 6) fornecer e-mail (não obrigatório).

• No passo 5, caso o usuário aponte ser testemunha: o

usuário irá indicar o grau de relação com a vítima: 1)

amigo; 2) professor; 3) família e; 4) outros.

• No passo 5, caso o usuário aponte ter vínculo com

instituição de ensino: 1) identificará se a instituição é
pública ou privada e; 2) indicará o nome da instituição;

• No passo 6, caso o usuário aponte o tipo 4: o usuário

informará o nome do esbalecimento em que a violação

ocorreu.

• No passo 8, caso o usuário escolha por anexar mídia: o

sistema solicitará que o usuário indique onde está a mídia

no dispositivo.

Exceção

• O sistema guardará localmente a denuncia até a
conexão com a internet e dispobibilidade do servidor
ser estabelecida.

RF04 Canais de contato

Sumário O caso de uso é responsável por apresentar canais de contatos
com a Seprev/AL e demais contatos de interesse.

Pré-condição Não há

Atores Usuário, App

Descrição

15

1. O usuário iniciará o sistema.

2. O sistema exibirá o menu geral de opções.

3. O usuário escolherá o item de contatos.

4. O sistema exibirá as opções de contatos com telefones e

e-mail diretos de canais de interesse.

5. O sistema fornecerá formulário para contato direto com a

Seprev.

6. O usuário fornecerá: 1) Nome; 2) e-mail de contato ou

telefone; 3) conteúdo do contato.

7. O usuário enviará o conteúdo.

8. O sistema confirmará o envio.

Exceção

• Na falta de conexão com a internet, o sistema exibirá

mensagem informando o erro.

Arquitetura Geral Proposta 7

A arquitetura apresentada na Figura 1 representa hierarquicamente como os

módulos dos requisitos fun

igualmente, usá-la como referência para a eleboração da arquitetura de informação

s
Figura 1 – Arquitetura Geral Proposta.
destinada a camada de apre

17

7.1 Diagrama de caso de uso

A Figura 2 apresenta o diagrama de casos de uso do sistema, identificando

os atores principais do sistema e as funcionalidades principais.

Figura 2 – Diagrama de caso de uso.

7.2 Diagrama de Classes

O diagrama apresentado na Figura 3 expõem as classes dos sistema. Estas

classes são abstraídas das telas e mostram a organização estrutural do sistema.

Figura 3 – Diagrama de Classes (BackEnd).

18

19

7.3 Diagrama de Atividade (Módulo Denúncia)

Figura 4 – Diagrama de Atividade (Módulo Denúncia).

Diagrama Entidade Relacionamento Banco Interno 7.4
Figura 5 – Diagrama ER banco interno APP.

Diagrama Entidade Relacionamento Banco Servidor e Contingencial 7.5

Figura 6 – Diagrama ER banco servidor e contingencial.

22

8 Disposições Finais

O  presente  artefato  visa  documentar  a  proposta  de  desenvolvimento,
apresentado  pelo  Centro  Universitário CESMAC, através de Analista externo e pelo
Núcleo de Robótica e aceita pela Secretaria de Estado de Prevenção à Violência do
Estado  de  Alagoas  -  Seprev,  contendo  todas  as informações acerca dos requisitos
do sistema, dos recursos necessários para execução do serviço.

Justo  e  acordado  o  presente  instrumento  de  documentação,  o  Centro
Universitário  CESMAC  e  a  Secretaria  de  Estado  de  Prevenção  à  Violência  do
Estado  de  Alagoas  -  Seprev,  assinam  o  presente  instrumento  em  2  (duas)  vias  de
igual teor e forma.

Maceió, ___ de _________ de 2019.

___________________________________________

RESPONSÁVEL CESMAC

___________________________________________

RESPONSÁVEL SEPREV-AL

___________________________________________

Izaac Duarte de Alencar

Analista de Sistemas
Apêndices

APÊNDICE A – PROJETO GRÁFICO DE INTERFACE

24

Este projeto gráfico é complementar ao projeto principal de análise e

modelagem de requisitos, cujo objetivo principal é o desenvolvimento de uma
interface gráfica para o usuário.

1 Arquitetura de Informação

Esta  seção  destina-se  a  apresentar  fluxos  de  informação para navegação e
melhor  experiência  para  o  usuário  do  aplicativo.  Para  isto,  se  faz  uso  de  fluxos  de
navegação, inventário de conteúdo e wireframes (projetos de telas).

2 Inventário de conteúdo

Esta subseção tem com objetivo principal apresentar o conteúdo de forma

sistematizada, para maior compreensão de seu estruturação.

Tabela 7 – Conteúdo informativo principal.

ID_Inf Conteúdo Descrição

01 o que é violência? texto explicativo destinado a conceituar violência e suas
formas.

02 Bullying texto explicativo destinado a descrever o Bullying.

03 Exploração Sexual
texto explicativo destinado a descrever

Exploração Sexual de crianças e

adolescentes.

04 Trabalho Infantil texto explicativo destinado a descrever Trabalho Infantil.

05 Venda de bebidas Alcoólicas
texto explicativo destinado a descrever

a venda de bebidas alcoólicas para

crianças e adolescentes.

06 Automutilação texto explicativo destinado a descrever automutilação.

Inicialmente,  as  seguintes  categorias  de  dicas  de  profissionais  serão
utilizadas. Por se tratar de uma programa de dicas interdisciplicar, o estabelecimento
de  temas  deverá  ser  validado pela equipe da própria Seprev/AL em conjunto com o

CESMAC.

Tabela 8 – Conteúdo informativo principal.

ID_Dicas Categoria Descrição

01 Direito Dicas de profissionais da área do Direito.

02 Psicologia Dicas de profissionais da área da Psicologia. 03 Serviço

Social Dicas de profissionais da área de Serviço Social. 04 Pegagogia

Dicas de profissionais da área da Pedagogia. 05 Medicina Dicas de

profissionais da área da Medicina.

25

26

3 Fluxos de navegação

Esta subseção apresenta diagramas de sequência representando telas e suas
interações. O propósito destes diagramas de fluxo de navegação é a visualização de
como as telas interagem uma com as outras.

Figura 7 – Telas Principais.

Figura 8 – Telas Denúncia.

Figura 9 – Telas Informações Gerais.

Figura 10 – Telas Dicas de Especialistas.

4 WireFrames - proposta de telas

29

Esta  subseção  apresenta  um  protótico  em  baixa  fidelidade  do  sistema,
mostrando  como  os  componentes  gráficos  do  sistema  estarão  dispostos  para
melhor experiência de navegação entre telas para o usuário.

Figura 11 – Telas módulos informações e dicas.

Figura 12 – Telas módulo denúncia.

31

Figura 13 – Telas módulo contato.

