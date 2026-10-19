# Spec: Backend — Mapeamento da interface de programação (API)

> Fonte: `_md/01-das.md`, capítulo 9 (seções 9.1 a 9.17). Texto copiado do documento do grupo; as tabelas do PDF vieram quebradas e foram remontadas célula a célula, mantendo as palavras. Nenhuma operação foi acrescentada.

> Nota: "A notação técnica de endereçamento fica no artefato formal de contrato, mantido pela equipe; aqui interessa o mapeamento conceitual, que é o que orienta a divisão de trabalho e a avaliação de cobertura dos requisitos." (DAS cap. 9). Por isso este arquivo **não define rotas HTTP**.

Este capítulo cumpre a exigência de mapeamento completo da interface de programação do sistema. As operações estão descritas por nome de negócio, ação, recurso sobre o qual atuam, permissão exigida e rastreabilidade ao caso de uso ou requisito de origem.

Voltar para [`spec.md`](spec.md).

## 9.1 Convenções gerais
| Item | Definição |
|---|---|
| Formato de troca | Notação de objetos em texto, codificada em UTF-8 |
| Nomes de campo | Palavras compostas iniciando em minúscula, sem separador |
| Datas e horas | Formato internacional com fuso horário explícito, sempre |
| Durações | Número inteiro de minutos, com a unidade indicada no nome do campo |
| Identificadores | Valor único universal na variante ordenável por tempo |
| Idioma | Mensagens de erro em português, redigidas para exibição direta ao usuário |
| Paginação | Obrigatória em toda listagem, sem exceção, com tamanho padrão de vinte itens e teto de cem |

### Ações utilizadas no mapeamento
| Ação | Significado |
|---|---|
| Consultar | Leitura de um recurso ou de uma coleção, sem efeito colateral |
| Criar | Criação de um novo recurso, ou registro de um evento de negócio |
| Substituir | Atualização completa de um recurso existente |
| Alterar | Atualização parcial de um recurso existente |
| Remover | Exclusão de um recurso, sempre condicionada à ausência de vínculo ativo |

## 9.2 Autenticação e sessão
A autenticação devolve uma credencial de acesso de curta duração, entregue no corpo da resposta, e uma credencial de renovação de duração maior, entregue em um marcador de navegador inacessível a código de página. A credencial de acesso vale quinze minutos; a de renovação, oito horas — a duração de um turno da clínica. A escolha reflete o fato de que as estações da recepção são compartilhadas.

A credencial de acesso carrega o identificador do usuário, o nome, o perfil ativo, a unidade e a lista de permissões. A interface nunca decide permissão por nome de perfil, e sim pela lista de permissões, porque perfis são editáveis pela administração e qualquer nome fixado em código quebraria no primeiro perfil novo criado pela clínica.

Cinco tentativas de autenticação malsucedidas bloqueiam temporariamente o acesso. A resposta de credencial inválida é sempre a mesma, sem distinguir usuário inexistente de senha incorreta.

| Operação | Ação | Recurso | Permissão | Rastreabilidade |
|---|---|---|---|---|
| Autenticar usuário | Criar | Sessão | Pública | W-01, RNF-01 |
| Renovar credencial de acesso | Criar | Renovação de sessão | Sessão válida | RNF-01 |
| Encerrar sessão | Criar | Encerramento de sessão | Autenticado | RNF-01 |
| Consultar dados da sessão atual | Consultar | Sessão | Autenticado | W-02, RNF-01 |
| Consultar catálogo de permissões | Consultar | Catálogo de permissões | Consulta de perfis | UC-04, RF-02, W-05 |

O catálogo de permissões alimenta a matriz da tela de perfis e a filtragem do menu lateral. Módulos que envolvem dado de saúde ou registro de auditoria vêm marcados no próprio catálogo.

## 9.3 Perfis e usuários
| Operação | Ação | Recurso | Permissão | Rastreabilidade |
|---|---|---|---|---|
| Listar perfis | Consultar | Perfis | Consulta de perfis | UC-04, RF-02 |
| Criar perfil | Criar | Perfis | Criação de perfil | UC-04, RF-02 |
| Consultar perfil | Consultar | Perfil | Consulta de perfis | UC-04, RF-02 |
| Atualizar perfil e suas permissões | Substituir | Perfil | Edição de perfil | UC-04, RF-02, W-05 |
| Excluir perfil | Remover | Perfil | Exclusão de perfil | UC-04, RF-02 |
| Listar usuários | Consultar | Usuários | Consulta de perfis | RF-02 |
| Criar usuário e vincular perfil | Criar | Usuários | Criação de perfil | RF-02 |

A alteração das permissões de um perfil informa, na resposta, quantos usuários serão afetados pela mudança, de modo que a tela possa apresentar essa consequência antes da confirmação. A exclusão de um perfil é recusada enquanto houver usuário vinculado, e perfis do sistema não são editáveis nem removíveis.

## 9.4 Cadastros base
As cinco entidades base compartilham o mesmo conjunto de operações. O que varia entre elas são os campos, a verificação de duplicidade e o perfil responsável, e essa variação está no formulário, não na estrutura das operações.

| Operação | Ação | Recurso | Permissão | Rastreabilidade |
|---|---|---|---|---|
| Listar pacientes | Consultar | Pacientes | Consulta de cadastros | UC-03, RF-01 |
| Cadastrar paciente | Criar | Pacientes | Criação em cadastros | UC-01, RF-01 |
| Consultar paciente | Consultar | Paciente | Consulta de cadastros | UC-03, RF-01 |
| Editar paciente | Substituir | Paciente | Edição em cadastros | UC-02, RF-01 |
| Excluir paciente | Remover | Paciente | Exclusão em cadastros | RF-01 |
| Listar estudantes ou atendentes | Consultar | Estudantes | Consulta de cadastros | UC-03, RF-01 |
| Cadastrar estudante ou atendente | Criar | Estudantes | Criação em cadastros | UC-01, RF-01, RN-06 |
| Consultar estudante | Consultar | Estudante | Consulta de cadastros | UC-03, RF-01 |
| Editar estudante | Substituir | Estudante | Edição em cadastros | UC-02, RF-01, RN-06 |
| Excluir estudante | Remover | Estudante | Exclusão em cadastros | RF-01 |
| Listar supervisores | Consultar | Supervisores | Consulta de cadastros | UC-03, RF-01, RN-08 |
| Cadastrar supervisor | Criar | Supervisores | Criação em cadastros | UC-01, RF-01, RN-08 |
| Consultar supervisor | Consultar | Supervisor | Consulta de cadastros | UC-03, RF-01 |
| Editar supervisor | Substituir | Supervisor | Edição em cadastros | UC-02, RF-01, RN-07, RN-08 |
| Excluir supervisor | Remover | Supervisor | Exclusão em cadastros | RF-01 |
| Listar salas | Consultar | Salas | Consulta de cadastros | UC-03, RF-01 |
| Cadastrar sala | Criar | Salas | Criação em cadastros | UC-01, RF-01, RN-09 |
| Consultar sala | Consultar | Sala | Consulta de cadastros | UC-03, RF-01 |
| Editar sala | Substituir | Sala | Edição em cadastros | UC-02, RF-01, RN-09 |
| Excluir sala | Remover | Sala | Exclusão em cadastros | RF-01 |
| Listar especialidades ou serviços | Consultar | Especialidades | Consulta de cadastros | UC-03, RF-01 |
| Cadastrar especialidade ou serviço | Criar | Especialidades | Criação em cadastros | UC-01, RF-01 |
| Consultar especialidade | Consultar | Especialidade | Consulta de cadastros | UC-03, RF-01 |
| Editar especialidade | Substituir | Especialidade | Edição em cadastros | UC-02, RF-01, RN-07, RN-11 |
| Excluir especialidade | Remover | Especialidade | Exclusão em cadastros | RF-01 |

Toda tentativa de exclusão de um registro com vínculo ativo em fila, alocação ou atendimento é recusada com um código específico, e a resposta orienta a alternativa de inativar o registro em vez de removê-lo. A verificação de duplicidade, quando existe para a entidade, devolve o identificador do registro já cadastrado, para que a tela ofereça o link de abertura direta.

> Nota de divergência (D-06): o DAS associa RN-08 ao cadastro de supervisor (§5.3 e esta tabela), mas a tabela "Entidades cobertas por RF-01" do documento de requisitos (`_md/03-especificacao-requisitos.md`, §2.1) só traz RN-06 e RN-09. Ver `MODULOS/cadastros-base/spec.md`.

## 9.5 Triagem, senha e prioridade
| Operação | Ação | Recurso | Permissão | Rastreabilidade |
|---|---|---|---|---|
| Registrar triagem | Criar | Triagens | Criação em triagem | UC-05, RF-03, RN-01 a RN-03 |
| Listar triagens | Consultar | Triagens | Consulta em triagem | RF-03 |
| Consultar triagem completa | Consultar | Triagem | Consulta sensível em triagem | UC-05, RF-03, RNF-07 |
| Emitir senha | Criar | Senhas | Criação de senha | UC-06, RF-04, RN-04 |
| Consultar senha | Consultar | Senha | Consulta de senha; consulta sensível quando inclui a triagem | UC-08, RF-04, RNF-07 |
| Reclassificar prioridade | Alterar | Prioridade da senha | Reclassificação de senha | UC-07, RF-04, RN-01 |
| Consultar histórico de reclassificações | Consultar | Reclassificações da senha | Consulta de senha | UC-07, RNF-05 |

A consulta da triagem completa e a consulta da senha com o conteúdo clínico incluído são operações marcadas como sensíveis: registram um evento de consulta a informação sensível antes de responder, com o identificador do registro consultado. É esse registro que torna verdadeira a trilha exigida pela conformidade legal — um evento desse tipo significa que alguém de fato abriu o conteúdo clínico, e não apenas que o campo trafegou junto de outra informação.

A reclassificação de prioridade exige justificativa. Justificativa ausente ou abaixo do tamanho mínimo definido pela unidade resulta em recusa com código específico, e a mensagem devolvida já é o texto que a tela exibe abaixo do campo.

> Nota de divergência (D-07): o DAS associa UC-06 (§9.5 e §13.1) e UC-07 (§9.5) a RF-04, e o módulo "Triagem e senha" a RF-03 e RF-04 (§5.3), enquanto `_md/04-especificacao-ui.md` (T-08) associa UC-07 a RF-03. Ver `MODULOS/triagem-e-senha/spec.md`.

## 9.6 Fila, chamada e telão
| Operação | Ação | Recurso | Permissão | Rastreabilidade |
|---|---|---|---|---|
| Listar filas | Consultar | Filas | Consulta de fila | RF-04 |
| Criar fila | Criar | Filas | Criação de fila | RF-04 |
| Consultar fila, com o estado de bloqueio | Consultar | Fila | Consulta de fila | RF-04, RN-10, P-04 |
| Editar fila | Substituir | Fila | Edição de fila | RF-04 |
| Excluir fila | Remover | Fila | Exclusão de fila | RF-04 |
| Consultar quadro de senhas da fila, por status | Consultar | Senhas da fila | Consulta de fila | UC-08, W-08, RN-04 |
| Chamar próxima senha | Criar | Chamadas da fila | Chamada de senha | UC-09, RF-05, RN-04, RN-10 |
| Rechamar senha | Criar | Rechamada | Chamada de senha | UC-09, RF-05 |
| Cancelar chamada | Remover | Chamada | Chamada de senha | RF-05 |

A consulta da fila devolve o estado de bloqueio como parte do recurso, contendo se está ativo, o motivo e desde quando. É esse mesmo estado que faz a tela exibir o aviso permanente, que faz a operação de chamada ser recusada com código próprio e que faz o bloqueio aparecer na lista de alertas de gestão — as três manifestações exigidas pela premissa, todas derivadas de uma única fonte no servidor.

A operação de chamada aceita chave de idempotência e é a única do sistema que roda com bloqueio de linha e salto de linhas bloqueadas.

## 9.7 Alocação de recursos
| Operação | Ação | Recurso | Permissão | Rastreabilidade |
|---|---|---|---|---|
| Consultar sugestões e alternativas de alocação | Consultar | Sugestões de alocação da senha | Consulta de alocação | UC-10, RF-06, RN-05 a RN-09 |
| Confirmar alocação de recursos | Criar | Alocação da senha | Criação de alocação | UC-10, RF-06, RN-05 a RN-10 |
| Trocar recurso de uma alocação | Substituir | Alocação da senha | Edição de alocação | UC-10, RF-06 |
| Manter senha aguardando recurso | Criar | Pendência de recurso da senha | Edição de alocação | UC-10, RF-06 |

A consulta de sugestões devolve três blocos — estudante, supervisor e sala —, cada um com a sugestão do sistema e a lista de alternativas. Cada alternativa indica se está apta e, quando não está, o motivo em texto: já possui atendimento ativo, especialidade incompatível, fora do turno, limite de supervisão atingido ou recurso indisponível na sala. O motivo vem do servidor porque é resultado da mesma verificação que decidirá a confirmação; deixar a interface deduzi-lo abriria espaço para divergência entre o que a tela explica e o que o sistema recusa.

Quando a especialidade não exige supervisão docente, o bloco de supervisor simplesmente não vem na resposta, e a tela o omite. Quando não há nenhum supervisor habilitado disponível, a confirmação é recusada, a senha permanece aguardando recurso, a fila da especialidade é bloqueada e a gestão é notificada — tudo na mesma operação.

Recurso que deixa de estar disponível entre a sugestão e a confirmação produz recusa com código específico, sem confirmar a alocação, e a tela recarrega as alternativas.

## 9.8 Atendimento
| Operação | Ação | Recurso | Permissão | Rastreabilidade |
|---|---|---|---|---|
| Listar atendimentos | Consultar | Atendimentos | Consulta de atendimento | RF-07 |
| Consultar atendimento | Consultar | Atendimento | Consulta sensível de atendimento | RF-07, RNF-07 |
| Iniciar atendimento | Criar | Atendimento da senha | Criação de atendimento | UC-11, RF-07, RN-05 |
| Pausar atendimento | Criar | Pausa do atendimento | Edição de atendimento | UC-12, RF-07 |
| Retomar atendimento | Criar | Retomada do atendimento | Edição de atendimento | UC-12, RF-07 |
| Encerrar atendimento com desfecho | Criar | Encerramento do atendimento | Edição de atendimento | UC-13, RF-07, RN-11 |
| Validar ou recusar encerramento | Criar | Validação do atendimento | Validação de atendimento | UC-13, RF-07, RN-11 |

A exigência de validação do supervisor é copiada da especialidade para o atendimento no momento em que ele começa. Isso evita que uma alteração posterior no cadastro da especialidade mude a regra de um atendimento já em curso.

A recusa da validação não devolve o atendimento ao estado anterior: ele permanece pendente de validação, com o parecer do supervisor registrado. Encerramento sem desfecho é recusado com código próprio.

## 9.9 Encaminhamento
| Operação | Ação | Recurso | Permissão | Rastreabilidade |
|---|---|---|---|---|
| Registrar encaminhamento interno ou externo | Criar | Encaminhamentos do atendimento | Criação de encaminhamento | UC-14, UC-15, RF-08 |

A mesma operação atende às duas variantes, distinguidas pelo tipo informado. O encaminhamento interno gera uma nova senha na especialidade de destino, vinculada à senha de origem, e a resposta informa o número gerado para que a tela possa exibi-lo. O encaminhamento externo é apenas registrado, sem gerar entrada de fila. Especialidade de destino inativa produz recusa com código específico. Especialidade de destino com bloqueio ativo por ausência de supervisor não impede o encaminhamento — apenas informa que a chamada está suspensa naquela fila.

## 9.10 Ausência e desistência
| Operação | Ação | Recurso | Permissão | Rastreabilidade |
|---|---|---|---|---|
| Registrar ausência | Criar | Ausência da senha | Criação em ausências | UC-16, RF-09, RN-12, RN-13 |
| Registrar desistência voluntária | Criar | Desistência da senha | Criação em ausências | UC-17, RF-09 |

O registro manual de ausência antes do vencimento da tolerância exige confirmação explícita, e a resposta informa o tempo restante. A ausência automática por expiração não passa por esta operação: é produzida pela rotina agendada, sem intervenção de usuário, e gera notificação no painel e registro no histórico do paciente.

A reincidência de ausências não bloqueia a triagem. Ela aparece como aviso na resposta da busca de paciente, indicando a restrição aplicável, porque a política da unidade prevê restrição ou exigência de confirmação prévia para novos agendamentos, e não impedimento de atendimento.

## 9.11 Indicadores, alertas e auditoria
| Operação | Ação | Recurso | Permissão | Rastreabilidade |
|---|---|---|---|---|
| Consultar indicadores consolidados do período | Consultar | Resumo de indicadores | Consulta de indicadores | UC-18, RF-10 |
| Consultar volume de atendimentos por especialidade | Consultar | Volume por especialidade | Consulta de indicadores | UC-18, RF-10 |
| Exportar indicadores | Consultar | Exportação de indicadores | Exportação de indicadores | UC-18, RF-10, RNF-05 |
| Listar alertas ativos e histórico | Consultar | Alertas | Consulta de alertas | UC-19, RF-11 |
| Reconhecer alerta | Criar | Reconhecimento do alerta | Reconhecimento de alerta | UC-19, RF-11 |
| Acionar medida de escalonamento | Criar | Escalonamento do alerta | Escalonamento de alerta | UC-19, RF-11 |
| Consultar trilha de auditoria | Consultar | Eventos de auditoria | Consulta de auditoria | UC-20, RF-12, RNF-05 |
| Consultar detalhe de evento de auditoria | Consultar | Evento de auditoria | Consulta de auditoria | UC-20, RF-12 |
| Exportar trilha filtrada | Consultar | Exportação de auditoria | Exportação de auditoria | UC-20, RF-12, RNF-05 |

Os indicadores têm granularidade mínima de especialidade e período; nenhuma resposta expõe dado individual identificável de paciente. Toda exportação, tanto de indicador quanto de auditoria, gera ela própria um evento na trilha — inclusive a exportação da trilha.

O reconhecimento de um alerta muda sua apresentação sem removê-lo da lista. A normalização é automática, quando o tempo de espera volta ao limite, e registra o horário. O bloqueio de fila por ausência de supervisor aparece nessa mesma lista, como alerta de origem distinta.

## 9.12 Operações públicas
| Operação | Ação | Recurso | Permissão | Rastreabilidade |
|---|---|---|---|---|
| Consultar estado do telão da sala de espera | Consultar | Painel da unidade | Pública | UC-09, W-09, RF-05 |
| Consultar posição de uma senha | Consultar | Senha pública | Pública | UC-08, W-10, RF-04 |

As operações públicas não exigem autenticação e servem apenas ao telão e à consulta do paciente. Estão sujeitas a limite de trinta requisições por minuto por origem. Nunca devolvem nome de paciente, prioridade clínica ou dado de saúde. Senha inexistente ou de outro dia produz mensagem neutra, sem revelar se aquele número já existiu.

## 9.13 Verificações de saúde
| Operação | Ação | Recurso | Permissão | Uso |
|---|---|---|---|---|
| Verificar se o processo está vivo | Consultar | Estado do serviço | Pública | Responde sem tocar o banco de dados |
| Verificar se o serviço está apto a receber tráfego | Consultar | Prontidão do serviço | Pública | Verifica o banco e a aplicação das migrações |

## 9.14 Autorização
Cada operação exige uma permissão formada por módulo e operação. O catálogo completo é fornecido pelo próprio sistema e alimenta a matriz de permissões da tela de perfis, de modo que a criação de um perfil novo pela administração nunca dependa de alteração em código.

| Módulo | Operações disponíveis |
|---|---|
| Cadastros | consultar, criar, editar, excluir |
| Perfis | consultar, criar, editar, excluir |
| Triagem | consultar, criar, editar, consultar informação sensível |
| Senha | consultar, criar, reclassificar |
| Fila | consultar, criar, editar, excluir, chamar |
| Alocação | consultar, criar, editar |
| Atendimento | consultar, criar, editar, validar, consultar informação sensível |
| Encaminhamento | consultar, criar |
| Ausência | consultar, criar |
| Indicadores | consultar, exportar |
| Alertas | consultar, reconhecer, escalonar |
| Auditoria | consultar, exportar |

A falta de permissão produz resposta de acesso negado com corpo neutro, sem revelar existência ou volume de dados. Recurso inexistente sob permissão negada também responde acesso negado, e nunca recurso não encontrado, porque a segunda resposta revelaria que o recurso existe.

> Nota de divergência (D-12): `_md/04-especificacao-ui.md` (T-05) descreve a matriz de permissões com "quatro colunas por módulo: consultar, criar, editar, excluir", e `_md/pages-inv.txt` (tabela 7) fala em "consultar, criar, editar, excluir e as operações próprias do módulo". Este arquivo segue o catálogo do DAS acima, por ser o documento mais recente e consolidador (ver precedência em `../../README.md`).

## 9.15 Projeções de senha e proteção de dado sensível
A senha tem três projeções distintas e mutuamente exclusivas. A escolha da projeção é feita pelo tipo de consumidor, e não por filtragem aplicada sobre um objeto único. A diferença é decisiva: um objeto filtrado ainda carrega os campos que foram omitidos em algum ponto do caminho, e basta um componente novo esquecido para expô-los.

| Projeção | Onde é usada | Campos | Auditoria |
|---|---|---|---|
| Pública | Telão e consulta do paciente | Número, especialidade e local | Nenhuma |
| Resumo | Listagens, painel de fila e indicadores | Número, iniciais do paciente, especialidade, tipo, prioridade, status e horários | Nenhuma |
| Detalhe | Ficha individual e tela de atendimento | Tudo do resumo, mais triagem completa, motivo, justificativa e observações | Registra evento de consulta a informação sensível |

A abreviação do nome do paciente acontece no servidor. O nome completo não sai do sistema em nenhuma operação de listagem — dado que não sai do servidor não vaza nas ferramentas de inspeção do navegador. A projeção pública é servida por um caminho que não passa pelo interceptador de sessão e que usa um serializador que conhece somente os três campos previstos; não é filtro por omissão, é um tipo que não tem os demais campos.

> Nota de divergência (D-31): `_md/04-especificacao-ui.md` T-12 (consulta do paciente, W-10) mostra "número, especialidade, posição atual, tempo médio estimado e status", mas o DAS §9.15 define a projeção pública como "Número, especialidade e local", "um tipo que não tem os demais campos"; a operação "Consultar posição de uma senha" (DAS §9.12) também não cabe nessa projeção. Nenhum dos dois foi alterado. Pendência: o grupo define se a consulta pública usa uma quarta variante de projeção ou se a projeção pública ganha posição e status.

A integração contínua reprova a construção se qualquer resposta de listagem contiver os campos de motivo, justificativa clínica, observações ou nome completo. É a verificação automática que transforma o requisito de privacidade em algo checável, em vez de depender de revisão humana a cada mudança.

## 9.16 Tratamento de erros
Todos os erros seguem o mesmo formato padronizado de detalhes de problema, com dois campos adicionais: um código estável e legível por máquina, e a lista de campos com problema, presente apenas em erro de validação. A mensagem de cada campo é escrita para ser exibida diretamente abaixo do campo no formulário, e descreve a causa e o próximo passo, nunca apenas que houve erro.

| Código | Situação | Regra de origem |
|---|---|---|
| Validação | Campo obrigatório ausente ou inválido | — |
| Duplicidade | Registro já cadastrado; a resposta traz o identificador do registro existente | UC-01 |
| Vínculo ativo | Exclusão de cadastro com vínculo ativo; a resposta sugere inativar | RF-01 |
| Transição inválida | Mudança de estado não prevista na máquina de estados da senha | P-01 |
| Versão desatualizada | Conflito de edição concorrente; a resposta traz o estado atual | — |
| Fila bloqueada | Chamada em fila sem supervisor disponível | RN-10 |
| Fila vazia | Chamada da próxima senha sem nenhuma senha aguardando | UC-09 |
| Sem fila configurada | Emissão de senha para especialidade sem fila ativa | UC-06 |
| Justificativa obrigatória | Reclassificação de prioridade sem justificativa | RN-01 |
| Recurso indisponível | Recurso deixou de estar disponível entre a sugestão e a confirmação | RN-05, RN-09 |
| Limite de supervisão atingido | Supervisor no limite de estudantes simultâneos | RN-08 |
| Supervisor indisponível | Nenhum supervisor habilitado; dispara o bloqueio da fila | RN-07, RN-10 |
| Desfecho obrigatório | Encerramento de atendimento sem desfecho informado | UC-13 |
| Especialidade inativa | Encaminhamento interno para especialidade inativa | UC-14 |
| Paciente com restrição | Reincidência de ausências; vem como aviso, não como erro, porque não bloqueia a triagem | RN-13 |

## 9.17 Versionamento do contrato
A versão é indicada no endereçamento do serviço. Mudança compatível — campo novo opcional, operação nova — não altera a versão. Mudança incompatível — remoção de campo, mudança de tipo, mudança de significado — exige nova versão e um período de convivência entre as duas.

O contrato é alterado em revisão própria, separada da implementação, aprovada pela liderança técnica e pela pessoa responsável pelo módulo no servidor, e integrado antes de a implementação começar dos dois lados. Com o contrato pronto, a equipe de interface constrói a tela sobre uma simulação gerada a partir das próprias definições de validação, sem esperar o servidor ficar pronto. Quando o servidor entrega, a troca é de destino apenas. Se a tela quebra nesse momento, quem se afastou do contrato está identificado, e a verificação automática de contrato aponta o lado.
