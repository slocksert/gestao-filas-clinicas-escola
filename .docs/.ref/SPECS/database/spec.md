# Spec: Banco de dados — Visão de dados

> Fontes: `_md/01-das.md` cap. 7 (§7.1 a §7.7) e `_md/pages-inv.txt` (Inventário de Dados, tabelas 1 a 24). Texto copiado do documento do grupo; as tabelas do PDF vieram quebradas e foram remontadas célula a célula, mantendo as palavras. Notas de divergência e marcas `[ilegível na extração]` são nossas e não alteram o conteúdo do grupo.

Plano de implementação do banco (proposta): [`plano-implementacao.md`](plano-implementacao.md). Quem fala com o banco é o serviço de negócio: [`../backend/spec.md`](../backend/spec.md). Mapeamento das operações: [`../backend/api.md`](../backend/api.md).

## 7.1 Convenções
Identificadores são valores únicos universais na variante ordenável por tempo, o que preserva a localidade de inserção nos índices e evita a fragmentação típica de identificadores aleatórios. Nomes de tabela seguem o padrão de palavras separadas por sublinhado; nomes de campo do domínio permanecem em português, na mesma terminologia da especificação de requisitos, para que a distância entre o texto do requisito e o código seja a menor possível.

## 7.2 Agrupamentos de entidades
| Agrupamento | Entidades | Observação |
|---|---|---|
| Acesso e configuração | Unidade, parâmetro de unidade, usuário, perfil, permissão de perfil e registro de auditoria | A unidade existe desde o início, ainda que só haja uma, para que o suporte a múltiplas unidades não exija migração futura |
| Cadastros base | Paciente, estudante, supervisor, sala e especialidade, com suas tabelas de vínculo para habilitação, procedimento, recurso e disponibilidade por turno | Habilitação, procedimento e recurso são tabelas de apoio, e não texto livre, porque a verificação de compatibilidade compara conjuntos |
| Fila e senha | Fila, triagem, senha, reclassificação de prioridade e chamada | A senha é o agregado central do sistema; quase todo fluxo converge nela |
| Alocação e atendimento | Alocação, atendimento, pausa de atendimento e encaminhamento | A pausa é uma linha por ocorrência, para que o tempo efetivo de atendimento desconte a soma das pausas nos indicadores |
| Ausência e gestão | Ausência, desistência, alerta e escalonamento | Ausência e desistência são tabelas separadas porque alimentam indicadores distintos |

## 7.3 Máquina de estados da senha
A senha percorre oito estados possíveis. A máquina de estados fica concentrada em um único módulo do servidor e tem teste de unidade cobrindo cada transição válida e cada transição proibida. Qualquer tentativa de transição não prevista é recusada com um código específico de conflito.

| Estado | Como se chega | Para onde pode ir |
|---|---|---|
| Aguardando | Emissão da senha, ou devolução após tolerância vencida | Chamada, Desistência |
| Chamada | Acionamento da chamada da próxima senha | Aguardando recurso, Em atendimento, Ausente, Desistência |
| Aguardando recurso | Alocação impossível por falta de estudante, supervisor ou sala | Em atendimento, Aguardando, Desistência |
| Em atendimento | Início do atendimento com recursos alocados | Pausado, Pendente de validação, Encerrado, Desistência |
| Pausado | Pausa registrada durante o atendimento | Em atendimento |
| Pendente de validação | Encerramento em serviço cujo protocolo exige validação do supervisor | Encerrado, ou permanece no mesmo estado em caso de recusa, com parecer registrado |
| Encerrado | Encerramento sem exigência de validação, ou validação concedida | Estado final |
| Ausente | Expiração do prazo de tolerância, ou marcação manual | Estado final |
| Desistência | Saída voluntária solicitada pelo paciente ou registrada pela equipe | Estado final |

> Nota de divergência (D-08, P-05): o texto do DAS (§3.3 e §7.3) e a premissa P-01 de `_md/04-especificacao-ui.md` falam em "oito estados", mas as tabelas listam **nove** (a tabela acima, a de P-01 e o campo de status da senha em `_md/pages-inv.txt`, tabela 11, concordam entre si). Este arquivo documenta os nove. Pendência: o autor da especificação de interface corrige a contagem.

> Nota de divergência (D-09): a chave de status de P-01 em `_md/04-especificacao-ui.md` é "aguardando alocação", com rótulo "Aguardando recurso"; o DAS e o inventário de dados usam "Aguardando recurso". As specs técnicas usam "Aguardando recurso".

> Nota de divergência (D-16): a pré-condição do UC-17 (`MODULOS/ausencias-e-desistencias/spec.md`) cita senha "aguardando" ou "em atendimento", mas a máquina de estados acima também permite Desistência a partir de "Chamada" e "Aguardando recurso". O texto do UC não foi alterado.

## 7.4 Invariantes garantidas no banco de dados
Regra que depende apenas da camada de serviço quebra no dia em que alguém esquece de chamar a verificação. As invariantes abaixo são garantidas por restrição do próprio banco, e não por disciplina da aplicação.

| Invariante | Mecanismo | Regra |
|---|---|---|
| Um atendimento por senha | Restrição de unicidade sobre a referência à senha | RF-07 |
| Estudante sem duas alocações ativas ao mesmo tempo | Índice único parcial, considerando apenas alocações ainda não liberadas | RN-05 |
| Sala sem duas alocações ativas ao mesmo tempo | Índice único parcial, considerando apenas alocações ainda não liberadas | RN-09 |
| Justificativa presente em toda reclassificação de prioridade | Restrição de verificação sobre o tamanho mínimo do texto | RN-01 |
| Justificativa clínica presente em prioridade urgente | Restrição de verificação condicional à prioridade | RN-03 |
| Uma única fila ativa por especialidade | Índice único parcial sobre filas ativas | RF-04 |
| Trilha de auditoria imutável | Revogação das permissões de alteração e remoção para o usuário de banco da aplicação | RNF-05 |
| Limite de estudantes por supervisor | Verificado na camada de serviço, dentro da mesma transação que cria a alocação, com bloqueio na linha do supervisor | RN-08 |

O limite de supervisão é o único item que não vira restrição declarativa, porque depende de uma contagem sobre linhas relacionadas. A garantia vem de mantê-lo dentro da transação de alocação, com a linha do supervisor bloqueada durante a verificação.

## 7.5 Índice que implementa a ordenação da fila
A regra de ordenação da fila é traduzida diretamente em um índice composto sobre a tabela de senhas, combinando fila, status, prioridade em ordem decrescente, horário de chegada em ordem crescente e existência de agendamento prévio. A consulta do quadro de filas e a seleção da próxima senha usam esse mesmo índice, de modo que a ordenação exibida na tela e a ordenação aplicada na chamada não podem divergir.

## 7.6 Parâmetros que dependem da unidade
Os cinco valores que dependem de definição da clínica não são constantes no código. Ficam em tabela própria, editáveis pela administração, com a possibilidade de um valor específico por especialidade sobrepor o valor geral da unidade.

| Parâmetro | Onde afeta | Valor provisório |
|---|---|---|
| Prazo de tolerância por especialidade | Expiração automática de senha chamada | 5 minutos |
| Limite de tempo de espera por fila | Geração de alerta para a gestão | 25 minutos |
| Reincidências que disparam restrição | Aviso na triagem e no registro de ausência | 3 ausências em 90 dias |
| Limite de estudantes por supervisor | Verificação na alocação | 3 |
| Tamanho mínimo da justificativa de reclassificação | Validação da reclassificação de prioridade | 20 caracteres |

Os valores provisórios servem para que o sistema funcione em ambiente de desenvolvimento e homologação. A definição final é pendência registrada e cabe à coordenação da clínica-escola.

> Nota de divergência (D-13): o DAS (§3.2 e §7.6) fala em "cinco valores", e a tabela 8 do inventário de dados (`_md/pages-inv.txt`) traz **seis** campos, com `janelaReincidenciaDias` (valor provisório 90) separado de `reincidenciasRestricao` (valor provisório 3). No DAS os dois aparecem juntos em "3 ausências em 90 dias". Nenhum dos dois foi alterado; o grupo decide.

## 7.7 Retenção e proteção de dados pessoais
| Categoria de dado | Retenção | Observação |
|---|---|---|
| Triagem e atendimento | Conforme política da instituição | Base legal e prazo a confirmar com a coordenação |
| Trilha de auditoria | Mínimo de cinco anos | Nunca removida por rotina automática |
| Senha, chamada e alocação | Dois anos | Depois do prazo, o vínculo com o paciente é anonimizado, preservando o agregado para indicadores |
| Dados de indicador | Indefinida | Já agregados, sem identificação individual |

A anonimização substitui o vínculo com o paciente por um identificador irreversível. Isso preserva a capacidade de calcular indicadores históricos sem manter dado pessoal por mais tempo do que o necessário.

> Nota de divergência (D-14): a tabela 24 do inventário de dados (abaixo) inclui a categoria "Cadastros base — Enquanto houver vínculo", que não aparece no DAS §7.7. Foram transcritas as duas tabelas, sem fundir.

## Inventário de dados
Fonte: `_md/pages-inv.txt` (Especificação "Inventário de Dados"). O arquivo é uma extração de um documento do Apple Pages: **strings curtas se perderam** (nomes de campo curtos como `nome`, `status` e `ativo`, rótulos "Sim"/"Não" e algumas opções de lista). Onde a célula não pôde ser lida, está escrito `[ilegível na extração]`; nenhuma célula foi deduzida sem estar sinalizada em nota. Quando outro documento do grupo traz o valor, ele está completado com a fonte citada. Pendência: exportar o documento em PDF para recuperar essas células (ver `../../README.md`).

Texto introdutório do documento do grupo:

> Esta seção destina-se a listar e descrever os dados relevantes para a realização do objetivo da aplicação. Além disto, servirá de guia para a determinação das métricas que substanciarão a análise estatística para tomada de decisão baseada em evidência, prevista no módulo de indicadores. Os dados são coletados pela aplicação e enviados para armazenamento em banco de dados relacional em servidor da instituição.
>
> Os dados estão organizados por módulo, na mesma ordem do fluxo de atendimento: cadastros base, acesso, triagem, fila, chamada, alocação, atendimento, encaminhamento, ausências, gestão e auditoria. Cada tabela apresenta o identificador do dado dentro do módulo, o nome pelo qual ele é referenciado no sistema, a descrição do que se destina a registrar e as respostas possíveis.
>
> Alguns dados são condicionais, isto é, só são coletados quando outro dado assume determinado valor. Nesses casos a descrição indica a referência da qual o dado depende, no formato ID seguido do número, dentro da mesma tabela.
>
> Os dados que constituem informação de saúde estão assinalados na descrição e relacionados na última seção deste documento. Eles recebem tratamento distinto: não aparecem em listagens, painéis ou telas públicas, e toda consulta a eles gera registro na trilha de auditoria.

> Nota: a coluna **ID** das tabelas abaixo segue a ordem das linhas na extração. A ordem é confirmada pelas referências cruzadas do próprio texto (por exemplo, "referência ID07" na tabela 1 aponta para `restricaoAtiva`, a sétima linha).

> Nota: as habilitações, os procedimentos e os recursos citados nas tabelas 2 a 5 são cadastros de apoio, compostos apenas de identificador e nome. Existem como cadastro, e não como texto livre, porque a verificação de compatibilidade entre estudante, supervisor, sala e procedimento compara conjuntos, o que seria inviável sobre texto digitado livremente (`_md/pages-inv.txt`, seção 1).

> Nota: na tabela 22 a lista de opções do campo `operacao` começa em "Editar" na extração; opções anteriores, se existirem, podem ter se perdido.

### Tabela 1 – Dados de cadastro do paciente.

| ID | Nome | Descrição | Possíveis respostas |
|---|---|---|---|
| ID01 | nomeCompleto | destina-se a informar o nome completo do paciente. | texto livre. |
| ID02 | documento | destina-se a informar o documento de identificação do paciente, usado na verificação de duplicidade de cadastro. | texto formatado (numérico). |
| ID03 | dataNascimento | destina-se a informar a data de nascimento do paciente, usada na verificação de duplicidade e no enquadramento em prioridade legal por idade. | texto formatado: __/__/____. |
| ID04 | contatoTelefone | destina-se a informar o telefone de contato do paciente. | texto formatado: (__) _____-____. |
| ID05 | contatoEmail | destina-se a informar o endereço eletrônico de contato do paciente. | texto livre. |
| ID06 | vinculo | destina-se a classificar o vínculo do paciente com a instituição. | • Externo<br>• [opção ilegível na extração; "aluno" conforme `_md/03-especificacao-requisitos.md` §2.1 e `_md/04-especificacao-ui.md` T-04]<br>• Servidor |
| ID07 | restricaoAtiva | destina-se a apontar se o paciente possui restrição vigente decorrente de reincidência de ausências. É calculado pelo sistema, não informado pelo usuário. | [ilegível na extração] |
| ID08 | restricaoAte | destina-se a indicar até quando vigora a restrição. Este dado depende da indicação positiva da referência ID07. | texto formatado: __/__/____. |
| ID09 | [ilegível na extração] | destina-se a apontar se o cadastro está ativo. Cadastro com vínculo em atendimento não é excluído, apenas inativado. | [ilegível na extração] |

### Tabela 2 – Dados de cadastro do estudante ou atendente.

| ID | Nome | Descrição | Possíveis respostas |
|---|---|---|---|
| ID01 | [ilegível na extração] | destina-se a informar o nome do estudante ou atendente. | texto livre. |
| ID02 | especialidade | destina-se a indicar a especialidade ou serviço a que o estudante está vinculado, usada na verificação de compatibilidade com o atendimento. | seleção entre as especialidades cadastradas. |
| ID03 | nivelFormacao | destina-se a informar o estágio de formação do estudante, usado na verificação de compatibilidade com o procedimento. | texto livre ou seleção definida pela coordenação. |
| ID04 | habilitacoes | destina-se a relacionar as habilitações que o estudante possui, comparadas às exigidas pelo procedimento. | seleção múltipla entre as habilitações cadastradas. |
| ID05 | procedimentosAutorizados | destina-se a relacionar os procedimentos que o estudante está autorizado a executar. | seleção múltipla entre os procedimentos cadastrados. |
| ID06 | disponibilidade | destina-se a informar em que dias e turnos o estudante está disponível para atendimento. | combinação de dia da semana com: [continuação ilegível na extração] |
| ID07 | [ilegível na extração] | destina-se a apontar se o cadastro está ativo. | [ilegível na extração] |

### Tabela 3 – Dados de cadastro do supervisor.

| ID | Nome | Descrição | Possíveis respostas |
|---|---|---|---|
| ID01 | [ilegível na extração] | destina-se a informar o nome do supervisor. | texto livre. |
| ID02 | especialidadesHabilitadas | destina-se a relacionar as especialidades que o supervisor está habilitado a supervisionar. | seleção múltipla entre as especialidades cadastradas. |
| ID03 | procedimentosHabilitados | destina-se a relacionar os procedimentos que o supervisor está habilitado a supervisionar. | seleção múltipla entre os procedimentos cadastrados. |
| ID04 | disponibilidade | destina-se a informar em que dias e turnos o supervisor está disponível. | combinação de dia da semana com: [continuação ilegível na extração] |
| ID05 | limiteEstudantesSimultaneos | destina-se a informar quantos estudantes o supervisor pode acompanhar ao mesmo tempo. Impede alocação acima do limite. | número inteiro positivo. |
| ID06 | [ilegível na extração] | destina-se a apontar se o cadastro está ativo. | [ilegível na extração] |

### Tabela 4 – Dados de cadastro da sala.

| ID | Nome | Descrição | Possíveis respostas |
|---|---|---|---|
| ID01 | identificacao | destina-se a informar a identificação da sala, exibida na chamada e na tela pública. | texto livre. |
| ID02 | caracteristicas | destina-se a descrever as características físicas da sala relevantes para o atendimento. | texto livre. |
| ID03 | recursos | destina-se a relacionar os equipamentos e recursos disponíveis na sala, comparados aos exigidos pela especialidade. | seleção múltipla entre os recursos cadastrados. |
| ID04 | situacao | destina-se a informar a situação atual da sala. | • Disponível<br>• Em manutenção<br>• Indisponível |
| ID05 | [ilegível na extração] | destina-se a apontar se o cadastro está ativo. | [ilegível na extração] |

### Tabela 5 – Dados de cadastro da especialidade ou serviço.

| ID | Nome | Descrição | Possíveis respostas |
|---|---|---|---|
| ID01 | [ilegível na extração] | destina-se a informar o nome da especialidade ou serviço, exibido inclusive na tela pública da sala de espera. | texto livre. |
| ID02 | descricao | destina-se a descrever a especialidade ou serviço. | texto livre. |
| ID03 | exigeSupervisaoDocente | destina-se a apontar se o atendimento nesta especialidade exige supervisor habilitado. Quando negativo, a alocação dispensa a etapa de supervisor. | [ilegível na extração] |
| ID04 | exigeValidacaoEncerramento | destina-se a apontar se o encerramento do atendimento precisa ser validado pelo supervisor. | [ilegível na extração] |
| ID05 | recursosExigidos | destina-se a relacionar os recursos que a sala precisa possuir para atender a especialidade. | seleção múltipla entre os recursos cadastrados. |
| ID06 | [ilegível na extração] | destina-se a apontar se a especialidade está ativa. Especialidade inativa não recebe encaminhamento interno nem emissão de senha. | [ilegível na extração] |

### Tabela 6 – Dados de cadastro do usuário do sistema.

| ID | Nome | Descrição | Possíveis respostas |
|---|---|---|---|
| ID01 | [ilegível na extração] | destina-se a informar o nome do usuário, exibido na barra superior e na trilha de auditoria. | texto livre. |
| ID02 | identificacao | destina-se a informar a matrícula ou o endereço eletrônico institucional usado na autenticação. É único no sistema. | texto livre. |
| ID03 | [ilegível na extração] | destina-se a autenticar o usuário. É armazenada apenas em forma irreversível e nunca é devolvida pelo sistema em nenhuma consulta. | texto livre, com política mínima definida pela instituição. |
| ID04 | [ilegível na extração] | destina-se a indicar o perfil que define as permissões do usuário. | seleção entre os perfis cadastrados. |
| ID05 | unidade | destina-se a vincular o usuário à unidade em que atua. | seleção entre as unidades cadastradas. |
| ID06 | tentativasFalhas | destina-se a contar as tentativas de autenticação malsucedidas consecutivas, para aplicar o bloqueio temporário após a quinta. | número inteiro positivo. |
| ID07 | bloqueadoAte | destina-se a indicar até quando vigora o bloqueio temporário por tentativas malsucedidas. | data e hora, com fuso horário. |
| ID08 | ultimoLoginEm | destina-se a registrar o momento do último acesso do usuário. | data e hora, com fuso horário. |
| ID09 | [ilegível na extração] | destina-se a apontar se o usuário pode acessar o sistema. | [ilegível na extração] |

### Tabela 7 – Dados do perfil de usuário e suas permissões.

| ID | Nome | Descrição | Possíveis respostas |
|---|---|---|---|
| ID01 | [ilegível na extração] | destina-se a informar o nome do perfil, único no sistema. Exemplos: Recepção, Triagem, Supervisão. | texto livre. |
| ID02 | descricao | destina-se a descrever a que função o perfil corresponde. | texto livre. |
| ID03 | permissoes | destina-se a relacionar as operações que o perfil pode executar, agrupadas por módulo. Determina os módulos exibidos no menu e as ações oferecidas em cada tela. | seleção múltipla na matriz de permissões, combinando cada módulo com: consultar, criar, editar, excluir e as operações próprias do módulo. |
| ID04 | sistema | destina-se a apontar se o perfil é do sistema. Perfil do sistema não pode ser editado nem excluído. | [ilegível na extração] |

### Tabela 8 – Parâmetros de funcionamento da unidade.

| ID | Nome | Descrição | Possíveis respostas |
|---|---|---|---|
| ID01 | toleranciaMinutos | destina-se a definir quanto tempo o paciente chamado tem para comparecer antes de ser marcado como ausente. | número inteiro positivo. Valor provisório: 5. |
| ID02 | slaMinutos | destina-se a definir o tempo de espera a partir do qual a fila gera alerta para a gestão. | número inteiro positivo. Valor provisório: 25. |
| ID03 | reincidenciasRestricao | destina-se a definir quantas ausências disparam restrição ao paciente. | número inteiro positivo. Valor provisório: 3. |
| ID04 | janelaReincidenciaDias | destina-se a definir em que intervalo de dias as ausências são contadas para efeito da referência ID03. | número inteiro positivo. Valor provisório: 90. |
| ID05 | limiteEstudantesSupervisor | destina-se a definir o limite geral de estudantes por supervisor, quando o cadastro do supervisor não trouxer valor próprio. | número inteiro positivo. Valor provisório: 3. |
| ID06 | justificativaMinCaracteres | destina-se a definir o tamanho mínimo exigido da justificativa de reclassificação de prioridade. | número inteiro positivo. Valor provisório: 20. |

### Tabela 9 – Dados da triagem do paciente.

| ID | Nome | Descrição | Possíveis respostas |
|---|---|---|---|
| ID01 | paciente | destina-se a vincular a triagem ao paciente atendido, localizado pela busca por nome ou documento. | seleção entre os pacientes cadastrados. |
| ID02 | profissional | destina-se a registrar quem realizou a triagem. | preenchido pelo sistema a partir do usuário autenticado. |
| ID03 | especialidadeDestino | destina-se a indicar a especialidade ou serviço para o qual o paciente será encaminhado. | seleção entre as especialidades ativas com fila configurada. |
| ID04 | tipoAtendimento | destina-se a classificar a natureza do atendimento solicitado. | • Primeira consulta<br>• Retorno<br>• Encaixe |
| ID05 | prioridade | destina-se a classificar a prioridade do atendimento, que determina a posição da senha na fila. | • Normal<br>• Preferencial<br>• Urgente |
| ID06 | [ilegível na extração] | destina-se a descrever o motivo do atendimento relatado pelo paciente. Dado sensível de saúde. | texto livre. |
| ID07 | enquadramentoLegal | destina-se a indicar o enquadramento em prioridade prevista em norma. Este dado depende de a referência ID05 resultar em Preferencial, e ao ser preenchido sinaliza automaticamente essa prioridade. Dado sensível de saúde. | • Gestante<br>• Lactante<br>• Pessoa com deficiência<br>• Outro previsto em norma |
| ID08 | justificativaClinica | destina-se a registrar o critério clínico que motivou a prioridade urgente. Este dado é obrigatório quando a referência ID05 resultar em Urgente. Dado sensível de saúde. | texto livre. |
| ID09 | observacoes | destina-se a complementar o encaminhamento clínico com informação adicional. Dado sensível de saúde. | texto livre. |
| ID10 | concluidaEm | destina-se a registrar o momento em que a triagem foi concluída. | data e hora, com fuso horário. |

### Tabela 10 – Dados da fila de atendimento.

| ID | Nome | Descrição | Possíveis respostas |
|---|---|---|---|
| ID01 | [ilegível na extração] | destina-se a identificar a fila. | texto livre. |
| ID02 | especialidade | destina-se a vincular a fila à especialidade ou serviço correspondente. | seleção entre as especialidades cadastradas. |
| ID03 | [ilegível na extração] | destina-se a apontar se a fila está em operação. Especialidade sem fila ativa não permite emissão de senha. | [ilegível na extração] |
| ID04 | bloqueada | destina-se a apontar se as chamadas da fila estão suspensas por ausência de supervisor habilitado. O bloqueio é por especialidade, nunca geral. | [ilegível na extração] |
| ID05 | bloqueadaMotivo | destina-se a informar a causa do bloqueio, exibida no aviso da tela e na lista de alertas. Este dado depende da indicação positiva da referência ID04. | texto livre. |
| ID06 | bloqueadaDesde | destina-se a registrar desde quando a fila está bloqueada. Este dado depende da indicação positiva da referência ID04. | data e hora, com fuso horário. |
| ID07 | slaMinutos | destina-se a definir um limite de tempo de espera próprio desta fila, sobrepondo o parâmetro geral da unidade. | número inteiro positivo. |

### Tabela 11 – Dados da senha.

| ID | Nome | Descrição | Possíveis respostas |
|---|---|---|---|
| ID01 | [ilegível na extração] | destina-se a identificar a senha de forma visível ao paciente, inclusive na tela pública. É único por dia e especialidade. | texto formatado: letra da especialidade seguida de três dígitos. Exemplo: F014. |
| ID02 | [ilegível na extração] | destina-se a vincular a senha à fila em que aguarda atendimento. | seleção automática a partir da especialidade de destino da triagem. |
| ID03 | triagem | destina-se a vincular a senha à triagem que a originou. | preenchido pelo sistema. |
| ID04 | paciente | destina-se a vincular a senha ao paciente. Nunca é exposto na tela pública; em listagens, apenas as iniciais do nome são exibidas. | preenchido pelo sistema. |
| ID05 | prioridade | destina-se a indicar a prioridade vigente da senha, primeiro critério de ordenação da fila. | • Normal<br>• Preferencial<br>• Urgente |
| ID06 | tipoAtendimento | destina-se a indicar a natureza do atendimento, usada como filtro no painel de fila. | • Primeira consulta<br>• Retorno<br>• Encaixe |
| ID07 | [ilegível na extração] | destina-se a indicar a situação atual da senha no ciclo de atendimento. | • Aguardando<br>• Chamada<br>• Aguardando recurso<br>• Em atendimento<br>• Pausado<br>• Pendente de validação<br>• Encerrado<br>• Ausente<br>• Desistência |
| ID08 | temAgendamentoPrevio | destina-se a apontar se o paciente possuía agendamento, terceiro critério de desempate na ordenação da fila. | [ilegível na extração] |
| ID09 | chegadaEm | destina-se a registrar o horário de chegada, segundo critério de ordenação da fila. | data e hora, com fuso horário. |
| ID10 | chamadaEm | destina-se a registrar o horário da primeira chamada da senha. | data e hora, com fuso horário. |
| ID11 | toleranciaAte | destina-se a indicar até quando o paciente chamado pode comparecer antes de ser marcado como ausente. | data e hora, com fuso horário. |
| ID12 | iniciadaEm | destina-se a registrar o horário de início do atendimento. | data e hora, com fuso horário. |
| ID13 | encerradaEm | destina-se a registrar o horário de encerramento do atendimento. | data e hora, com fuso horário. |
| ID14 | senhaOrigem | destina-se a vincular a senha à senha anterior, quando ela foi gerada por encaminhamento interno. | preenchido pelo sistema. |

### Tabela 12 – Dados da reclassificação de prioridade da senha.

| ID | Nome | Descrição | Possíveis respostas |
|---|---|---|---|
| ID01 | [ilegível na extração] | destina-se a vincular a reclassificação à senha alterada. | preenchido pelo sistema. |
| ID02 | dePrioridade | destina-se a registrar a prioridade anterior à alteração. | • Normal<br>• Preferencial<br>• Urgente |
| ID03 | paraPrioridade | destina-se a registrar a nova prioridade, que deve ser diferente da referência ID02. | • Normal<br>• Preferencial<br>• Urgente |
| ID04 | justificativa | destina-se a registrar o motivo da alteração, obrigatório e copiado para a trilha de auditoria. Costuma descrever agravamento clínico, sendo tratado como dado sensível. | texto livre, com tamanho mínimo definido pela unidade. |
| ID05 | usuario | destina-se a registrar quem realizou a reclassificação. | preenchido pelo sistema a partir do usuário autenticado. |
| ID06 | ocorridoEm | destina-se a registrar o momento da reclassificação. | data e hora, com fuso horário. |

### Tabela 13 – Dados da chamada de senha.

| ID | Nome | Descrição | Possíveis respostas |
|---|---|---|---|
| ID01 | [ilegível na extração] | destina-se a vincular a chamada à senha chamada. | preenchido pelo sistema. |
| ID02 | usuario | destina-se a registrar quem acionou a chamada. | preenchido pelo sistema a partir do usuário autenticado. |
| ID03 | localExibicao | destina-se a informar onde o paciente deve comparecer, exibido na tela pública. | seleção entre as salas cadastradas. |
| ID04 | sequencia | destina-se a distinguir a primeira chamada das rechamadas da mesma senha. | número inteiro, sendo 1 a primeira chamada. |
| ID05 | chamadoEm | destina-se a registrar o momento da chamada. | data e hora, com fuso horário. |

### Tabela 14 – Dados da alocação de recursos ao atendimento.

| ID | Nome | Descrição | Possíveis respostas |
|---|---|---|---|
| ID01 | [ilegível na extração] | destina-se a vincular a alocação à senha atendida. | preenchido pelo sistema. |
| ID02 | estudante | destina-se a indicar o estudante ou atendente que conduzirá o atendimento, verificado quanto a disponibilidade e compatibilidade. | seleção entre os estudantes aptos apresentados pelo sistema. |
| ID03 | supervisor | destina-se a indicar o supervisor responsável. Este dado só é preenchido quando a especialidade exigir supervisão docente. | seleção entre os supervisores aptos apresentados pelo sistema. |
| ID04 | [ilegível na extração] | destina-se a indicar a sala onde o atendimento ocorrerá, verificada quanto a disponibilidade e recursos exigidos. | seleção entre as salas aptas apresentadas pelo sistema. |
| ID05 | confirmadaPor | destina-se a registrar quem confirmou a alocação. | preenchido pelo sistema a partir do usuário autenticado. |
| ID06 | confirmadaEm | destina-se a registrar o momento da confirmação. | data e hora, com fuso horário. |
| ID07 | liberadaEm | destina-se a registrar quando os recursos foram liberados, seja por encerramento, ausência ou desistência. | data e hora, com fuso horário. |

### Tabela 15 – Dados do atendimento.

| ID | Nome | Descrição | Possíveis respostas |
|---|---|---|---|
| ID01 | [ilegível na extração] | destina-se a vincular o atendimento à senha. Cada senha origina no máximo um atendimento. | preenchido pelo sistema. |
| ID02 | alocacao | destina-se a vincular o atendimento aos recursos alocados. | preenchido pelo sistema. |
| ID03 | iniciadoEm | destina-se a registrar o horário de início, base para o cálculo da duração. | data e hora, com fuso horário. |
| ID04 | encerradoEm | destina-se a registrar o horário de encerramento. | data e hora, com fuso horário. |
| ID05 | desfecho | destina-se a registrar a conclusão do atendimento, obrigatória no encerramento. Dado sensível de saúde. | • Retorno agendado<br>• Encaminhamento interno<br>• Encaminhamento externo |
| ID06 | observacoes | destina-se a registrar o que foi realizado no atendimento. Dado sensível de saúde. | texto livre. |
| ID07 | validacaoExigida | destina-se a apontar se o encerramento precisa de validação do supervisor. É copiado da especialidade no início do atendimento, para que alteração posterior no cadastro não afete atendimento em curso. | [ilegível na extração] |
| ID08 | parecerSupervisor | destina-se a registrar a manifestação do supervisor na validação ou na recusa do encerramento. Este dado depende da indicação positiva da referência ID07. Dado sensível de saúde. | texto livre. |
| ID09 | validadoPor | destina-se a registrar qual supervisor validou o encerramento. | preenchido pelo sistema a partir do usuário autenticado. |
| ID10 | validadoEm | destina-se a registrar o momento da validação. | data e hora, com fuso horário. |

### Tabela 16 – Dados da pausa do atendimento.

| ID | Nome | Descrição | Possíveis respostas |
|---|---|---|---|
| ID01 | atendimento | destina-se a vincular a pausa ao atendimento interrompido. | preenchido pelo sistema. |
| ID02 | [ilegível na extração] | destina-se a informar a razão da interrupção. É obrigatório quando o protocolo do serviço assim determinar. | texto livre. |
| ID03 | pausadoPor | destina-se a registrar quem interrompeu o atendimento. | preenchido pelo sistema a partir do usuário autenticado. |
| ID04 | pausadoEm | destina-se a registrar o momento da interrupção. | data e hora, com fuso horário. |
| ID05 | retomadoEm | destina-se a registrar o momento da retomada. | data e hora, com fuso horário. |

### Tabela 17 – Dados do encaminhamento.

| ID | Nome | Descrição | Possíveis respostas |
|---|---|---|---|
| ID01 | atendimento | destina-se a vincular o encaminhamento ao atendimento de origem. | preenchido pelo sistema. |
| ID02 | [ilegível na extração] | destina-se a classificar o destino do encaminhamento. | • Interno<br>• Externo |
| ID03 | especialidadeDestino | destina-se a indicar para qual especialidade da clínica o paciente é encaminhado. Este dado depende de a referência ID02 resultar em Interno. Dado sensível de saúde. | seleção entre as especialidades ativas. |
| ID04 | instituicaoDestino | destina-se a informar a instituição ou serviço externo de destino. Este dado depende de a referência ID02 resultar em Externo. Dado sensível de saúde. | texto livre. |
| ID05 | [ilegível na extração] | destina-se a registrar a razão do encaminhamento. Dado sensível de saúde. | texto livre. |
| ID06 | prioridadeSugerida | destina-se a sugerir a prioridade da nova senha gerada. Este dado depende de a referência ID02 resultar em Interno. | • Normal<br>• Preferencial<br>• Urgente |
| ID07 | senhaGerada | destina-se a vincular o encaminhamento interno à nova senha criada na fila de destino. | preenchido pelo sistema. |
| ID08 | observacoes | destina-se a complementar o registro do encaminhamento. Dado sensível de saúde. | texto livre. |
| ID09 | criadoPor | destina-se a registrar quem realizou o encaminhamento. | preenchido pelo sistema a partir do usuário autenticado. |
| ID10 | criadoEm | destina-se a registrar o momento do encaminhamento. | data e hora, com fuso horário. |

### Tabela 18 – Dados do registro de ausência.

| ID | Nome | Descrição | Possíveis respostas |
|---|---|---|---|
| ID01 | [ilegível na extração] | destina-se a vincular a ausência à senha não atendida. | preenchido pelo sistema. |
| ID02 | automatica | destina-se a distinguir a ausência gerada pelo sistema, por expiração do prazo de tolerância, daquela marcada manualmente pela equipe. | [ilegível na extração] |
| ID03 | observacao | destina-se a complementar o registro da ausência. | texto livre. |
| ID04 | registradoPor | destina-se a registrar quem marcou a ausência. Fica vazio quando a referência ID02 for positiva. | preenchido pelo sistema a partir do usuário autenticado. |
| ID05 | registradoEm | destina-se a registrar o momento da ausência. | data e hora, com fuso horário. |

### Tabela 19 – Dados do registro de desistência voluntária.

| ID | Nome | Descrição | Possíveis respostas |
|---|---|---|---|
| ID01 | [ilegível na extração] | destina-se a vincular a desistência à senha retirada da fila. | preenchido pelo sistema. |
| ID02 | [ilegível na extração] | destina-se a apontar quem solicitou a desistência. | • Paciente<br>• Equipe |
| ID03 | [ilegível na extração] | destina-se a informar a razão da desistência, cujo registro é opcional. Motivos frequentes alimentam a análise de tempo de espera. | texto livre. |
| ID04 | registradoPor | destina-se a registrar quem lançou a desistência no sistema. | preenchido pelo sistema a partir do usuário autenticado. |
| ID05 | registradoEm | destina-se a registrar o momento da desistência. | data e hora, com fuso horário. |

### Tabela 20 – Dados do alerta de gestão.

| ID | Nome | Descrição | Possíveis respostas |
|---|---|---|---|
| ID01 | [ilegível na extração] | destina-se a classificar a origem do alerta. | • Tempo de espera excedido<br>• Fila bloqueada por ausência de supervisor |
| ID02 | [ilegível na extração] | destina-se a indicar a fila a que o alerta se refere. | preenchido pelo sistema. |
| ID03 | [ilegível na extração] | destina-se a indicar a situação atual do alerta. | • Reconhecido<br>• Normalizado |
| ID04 | tempoEsperaMinutos | destina-se a registrar o tempo de espera verificado no momento do disparo. | número inteiro positivo. |
| ID05 | limiteMinutos | destina-se a registrar o limite vigente contra o qual o tempo foi comparado. | número inteiro positivo. |
| ID06 | senhasAfetadas | destina-se a informar quantas senhas estavam aguardando acima do limite. | número inteiro positivo. |
| ID07 | disparadoEm | destina-se a registrar o momento do disparo do alerta. | data e hora, com fuso horário. |
| ID08 | reconhecidoPor | destina-se a registrar qual gestor reconheceu o alerta. | preenchido pelo sistema a partir do usuário autenticado. |
| ID09 | reconhecidoEm | destina-se a registrar o momento do reconhecimento. | data e hora, com fuso horário. |
| ID10 | normalizadoEm | destina-se a registrar quando o tempo de espera voltou ao limite, encerrando o alerta automaticamente. | data e hora, com fuso horário. |

### Tabela 21 – Dados da medida de escalonamento.

| ID | Nome | Descrição | Possíveis respostas |
|---|---|---|---|
| ID01 | [ilegível na extração] | destina-se a vincular a medida ao alerta que a motivou. | preenchido pelo sistema. |
| ID02 | [ilegível na extração] | destina-se a identificar a medida acionada pela gestão. | • Reforço de atendentes<br>• Abertura de vaga extra |
| ID03 | observacao | destina-se a complementar o registro da medida. | texto livre. |
| ID04 | acionadoPor | destina-se a registrar qual gestor acionou a medida. | preenchido pelo sistema a partir do usuário autenticado. |
| ID05 | acionadoEm | destina-se a registrar o momento do acionamento. | data e hora, com fuso horário. |

### Tabela 22 – Dados do registro de auditoria.

| ID | Nome | Descrição | Possíveis respostas |
|---|---|---|---|
| ID01 | usuario | destina-se a identificar quem realizou a operação. | preenchido pelo sistema a partir do usuário autenticado. |
| ID02 | perfilNoMomento | destina-se a registrar o nome do perfil vigente no instante da ação, preservando a informação mesmo que o perfil do usuário mude depois. | preenchido pelo sistema. |
| ID03 | operacao | destina-se a classificar o que foi feito. | • Editar<br>• Excluir<br>• Consultar informação sensível<br>• Exportar<br>• Entrar no sistema<br>• Sair do sistema |
| ID04 | [ilegível na extração] | destina-se a indicar em que módulo a operação ocorreu. | seleção entre os módulos do sistema. |
| ID05 | entidade | destina-se a indicar sobre que tipo de registro a operação incidiu. | texto preenchido pelo sistema. |
| ID06 | entidadeId | destina-se a identificar o registro específico afetado, permitindo reconstruir o histórico de um paciente ou de uma senha. | preenchido pelo sistema. |
| ID07 | sensivel | destina-se a marcar as consultas a dado de saúde, atendendo à exigência legal de trilha de consultas a prontuário. Essas linhas recebem marcação distinta na tela de auditoria. | [ilegível na extração] |
| ID08 | justificativa | destina-se a preservar o motivo declarado pelo usuário, quando a operação exige justificativa. É o caso da reclassificação de prioridade. | texto livre. |
| ID09 | origemIp | destina-se a registrar de qual origem partiu o acesso, para apuração em caso de incidente. | preenchido pelo sistema. |
| ID10 | userAgent | destina-se a registrar qual navegador e dispositivo realizaram a operação. | preenchido pelo sistema. |
| ID11 | ocorridoEm | destina-se a registrar o momento exato da operação. | data e hora, com fuso horário. |

### Tabela 23 – Dados sensíveis de saúde e onde podem ser consultados.

Os dados relacionados na tabela 23 constituem informação de saúde de pessoa identificável e recebem tratamento distinto dos demais: não são incluídos em nenhuma resposta de listagem, painel de fila ou tela pública; exigem permissão específica de consulta; e toda abertura gera registro na trilha de auditoria com a marcação correspondente. O nome completo do paciente, embora não seja dado de saúde, também não sai do sistema em consulta de listagem: a abreviação para iniciais é feita no servidor, e não na tela. Na tela pública da sala de espera nenhum dado de paciente é exibido, apenas número da senha, especialidade e local de atendimento.

> Nota: na extração só o cabeçalho "Onde pode ser consultado" foi lido. Os cabeçalhos "Dado" e "Módulo" abaixo foram deduzidos do conteúdo das colunas.

| Dado | Módulo | Onde pode ser consultado |
|---|---|---|
| [ilegível na extração] | Triagem | Ficha individual da triagem e tela de atendimento |
| enquadramentoLegal | Triagem | Ficha individual da triagem; na fila aparece apenas como etiqueta de prioridade, sem detalhamento |
| justificativaClinica | Triagem | Ficha individual da triagem e tela de atendimento |
| observacoes | Triagem | Ficha individual da triagem e tela de atendimento |
| justificativa | Reclassificação de prioridade | Histórico de reclassificações da senha e detalhe da trilha de auditoria |
| desfecho | Atendimento | Tela de atendimento e ficha individual |
| observacoes | Atendimento | Tela de atendimento e ficha individual |
| parecerSupervisor | Atendimento | Tela de validação do supervisor e ficha individual |
| especialidadeDestino | Encaminhamento | Tela de encaminhamento e ficha individual do atendimento de origem |
| instituicaoDestino | Encaminhamento | Tela de encaminhamento e ficha individual do atendimento de origem |
| [ilegível na extração] | Encaminhamento | Tela de encaminhamento e ficha individual do atendimento de origem |
| observacoes | Encaminhamento | Tela de encaminhamento e ficha individual do atendimento de origem |

### Tabela 24 – Prazos de retenção por categoria de dado.

Três definições ainda dependem da coordenação da clínica-escola e estão registradas como pendência do projeto: a base legal do tratamento dos dados de triagem e atendimento, considerando que a clínica-escola combina prestação de cuidado e formação acadêmica; o prazo de retenção desses registros; e a política de resposta a solicitação do titular dos dados, incluindo quem responde e em que prazo. Enquanto isso, nenhum dado real de paciente é utilizado em ambiente de desenvolvimento ou de homologação.

> Nota: na extração só os cabeçalhos "Categoria" e "Ao fim do prazo" foram lidos; o cabeçalho da coluna do meio está `[ilegível na extração]`.

| Categoria | [cabeçalho ilegível na extração] | Ao fim do prazo |
|---|---|---|
| Triagem e atendimento | A definir com a coordenação | Depende da política da instituição para registro clínico em contexto acadêmico |
| Senha, chamada e alocação | Dois anos | O vínculo com o paciente é substituído por identificador irreversível, preservando os totais usados nos indicadores |
| Trilha de auditoria | Mínimo de cinco anos | Nunca removida por rotina automática |
| Indicadores agregados | Indefinido | Permanecem, por não conterem identificação individual |
| Cadastros base | Enquanto houver vínculo | Registro com vínculo ativo não é excluído; a alternativa oferecida é a inativação |
