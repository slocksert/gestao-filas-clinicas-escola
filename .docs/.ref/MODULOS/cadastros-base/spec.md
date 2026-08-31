# Módulo: Cadastros Base

## Objetivo
Gerenciar o CRUD das entidades de cadastro base do sistema — Paciente, Estudante/Atendente, Supervisor, Sala e Especialidade/Serviço — garantindo que todo registro esteja disponível antes de ser referenciado pelos módulos de triagem, filas e alocação de recursos.

## Requisitos Funcionais
### RF-01 | Gerenciar cadastros base
O sistema deve permitir realizar o CRUD (criar, consultar, atualizar e excluir) de cada um dos cadastros base: **Paciente, Estudante/Atendente, Supervisor, Sala, Especialidade/Serviço**, garantindo que todo registro esteja devidamente cadastrado antes de ser referenciado pelos módulos de triagem, filas e alocação de recursos.

> Nota: a espec original resume RF-01 como "genérico" para as 5 entidades — o que varia entre elas (ator responsável, campos do formulário, regra de duplicidade) precisa ser levantado/detalhado por entidade nesta pasta antes da implementação.

## Casos de Uso
Os UC-01 a UC-03 são genéricos e se aplicam às cinco entidades acima; o que varia por entidade (ator, campos, duplicidade) deve ser detalhado numa tabela complementar.

### UC-01 | Cadastrar registro base
**Atores:** varia por entidade

**Pré-condição:** Usuário autenticado com permissão para cadastrar a entidade correspondente.

**Fluxo Principal:**
1. Usuário acessa o menu da entidade desejada.
2. Usuário clica em "Novo [Entidade]".
3. Sistema exibe o formulário de cadastro específico da entidade.
4. Usuário preenche os campos obrigatórios.
5. Usuário clica em "Salvar".
6. Sistema valida os campos obrigatórios.
7. Sistema salva o novo registro.
8. Sistema exibe mensagem de sucesso e retorna à lista.

**Fluxo Alternativo:** Usuário clica em "Cancelar" a qualquer momento → sistema descarta os dados e retorna à lista.

**Fluxo de Exceção:**
- Campo obrigatório vazio → sistema exibe mensagem de erro e mantém a tela.
- Registro já cadastrado (para entidades com verificação de duplicidade) → sistema exibe mensagem de duplicidade e não salva.

**Pós-condição:** Novo registro cadastrado e disponível para uso nos módulos relacionados (triagem, filas, alocação).

### UC-02 | Editar registro base
**Atores:** varia por entidade (mesmo ator responsável pelo cadastro)

**Pré-condição:** Registro já cadastrado; usuário autenticado com permissão.

**Fluxo Principal:**
1. Usuário acessa a lista da entidade.
2. Usuário seleciona o registro e clica em "Editar".
3. Sistema exibe o formulário preenchido com os dados atuais.
4. Usuário altera os campos desejados.
5. Usuário clica em "Salvar".
6. Sistema valida e atualiza o registro.
7. Sistema exibe mensagem de sucesso.

**Fluxo Alternativo:** Usuário clica em "Cancelar" → sistema descarta as alterações e retorna à lista.

**Fluxo de Exceção:** Campo obrigatório apagado → sistema exibe mensagem de erro e mantém a tela.

**Pós-condição:** Cadastro do registro atualizado.

### UC-03 | Consultar/listar registros base
**Atores:** varia por entidade (inclui também perfis de alocação, ex.: atendente responsável pela alocação)

**Pré-condição:** Usuário autenticado com permissão de consulta.

**Fluxo Principal:**
1. Usuário acessa o menu da entidade.
2. Sistema exibe a lista paginada de registros cadastrados.
3. Usuário aplica filtro relevante à entidade (nome, especialidade, disponibilidade), se desejar.
4. Sistema exibe o resultado filtrado.

**Fluxo Alternativo:** Usuário não aplica filtro → sistema mantém a lista completa.

**Fluxo de Exceção:** Nenhum resultado encontrado para o filtro → sistema exibe mensagem correspondente.

**Pós-condição:** Lista de registros exibida conforme critério de busca.

## Entidades relacionadas
- Paciente
- Estudante/Atendente
- Supervisor
- Sala
- Especialidade/Serviço

## Pendência
- **TODO:** preencher a "tabela de variações por entidade" (ator responsável, campos do formulário, regra de duplicidade) citada na espec original — ela existia no documento fonte mas não veio com o texto colado. Confirmar com o grupo/professor.
