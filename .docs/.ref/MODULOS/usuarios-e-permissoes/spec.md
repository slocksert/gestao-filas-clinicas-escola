# Módulo: Usuários e Permissões

## Objetivo
Gerenciar os perfis de usuário do sistema e as permissões de acesso associadas a cada perfil, controlando quem pode visualizar e executar cada operação.

## Requisitos Não Funcionais relacionados
- **RNF-01** — Segurança e controle de acesso: o sistema deve garantir controle de acesso baseado em perfis de usuário, permitindo que cada usuário visualize e execute somente as operações autorizadas para sua função, especialmente no acesso a dados pessoais e informações de saúde. (ver detalhamento completo em `SPECS/requisitos-nao-funcionais.md`)

## Requisitos Funcionais
### RF-02 | Gerenciar perfis de usuário e permissões de acesso
O sistema deve permitir realizar o CRUD de perfis de usuário, definindo as operações e informações que cada perfil pode acessar.
**Implementa:** RNF-01.

## Casos de Uso
### UC-04 | Cadastrar perfil de usuário e definir permissões
**Atores:** Administrador do sistema

**Pré-condição:** Usuário autenticado com permissão administrativa.

**Fluxo Principal:**
1. Usuário acessa o menu "Perfis de Usuário".
2. Usuário clica em "Novo Perfil".
3. Sistema exibe formulário com a lista de operações/módulos disponíveis.
4. Usuário nomeia o perfil e seleciona as permissões correspondentes.
5. Usuário clica em "Salvar".
6. Sistema valida os dados.
7. Sistema salva o perfil.

**Fluxo Alternativo:** Usuário clica em "Cancelar" → sistema descarta os dados.

**Fluxo de Exceção:** Nome de perfil já existente → sistema exibe mensagem de duplicidade.

**Pós-condição:** Perfil de usuário criado e disponível para vinculação a usuários do sistema.

## Entidades relacionadas
- Perfil de Usuário
- Usuário (vínculo com os demais cadastros: Estudante/Atendente, Supervisor, Administrador, Recepcionista/Atendente, Gestão)

## Dependências / Observações
- É pré-requisito lógico para todos os outros módulos, já que RNF-01 exige controle de acesso baseado em perfil em todo o sistema.
- Dados de saúde (ligados ao módulo `triagem-e-senha`) exigem restrição de acesso definida aqui (ver RNF-02 em `SPECS/requisitos-nao-funcionais.md`).
