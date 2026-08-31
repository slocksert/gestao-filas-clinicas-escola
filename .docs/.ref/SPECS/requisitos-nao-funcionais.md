# Requisitos Não Funcionais (RNF)

Requisitos transversais — não pertencem a um módulo específico, valem para o sistema como um todo. Cada módulo em `MODULOS/` referencia os RNFs que mais se aplicam a ele.

## RNF-01 | Segurança e controle de acesso
O sistema deve garantir controle de acesso baseado em perfis de usuário, permitindo que cada usuário visualize e execute somente as operações autorizadas para sua função, especialmente no acesso a dados pessoais e informações de saúde.
> Módulo relacionado: `usuarios-e-permissoes`.

## RNF-02 | Proteção de dados sensíveis
O sistema deve proteger os dados pessoais e informações de saúde armazenados e transmitidos, utilizando mecanismos adequados de segurança e restringindo o acesso a informações sensíveis somente a usuários autorizados.
> Módulo relacionado: `triagem-e-senha`.

## RNF-03 | Desempenho
O sistema deve responder às operações relacionadas à consulta, atualização e gerenciamento das filas em condições normais de utilização.
> Módulo relacionado: `fila`.

## RNF-04 | Disponibilidade
O sistema deve permanecer disponível durante o horário de funcionamento da clínica-escola, com mecanismos de recuperação em caso de falhas que possam interromper o gerenciamento das filas e dos atendimentos.

## RNF-05 | Rastreabilidade e auditoria
O sistema deve manter registros de auditoria das operações relevantes realizadas pelos usuários, incluindo identificação do usuário, operação realizada, data e horário.
> Módulo relacionado: `indicadores-alertas-auditoria`.

## RNF-06 | Usabilidade e acessibilidade
O sistema deve fornecer interfaces de exibição e chamada de senha acessíveis, considerando as necessidades de pacientes idosos e pessoas com deficiência.
> Módulo relacionado: `chamada-e-alocacao`.

## RNF-07 | Conformidade legal (LGPD)
O sistema deve tratar dados pessoais e de saúde em conformidade com a Lei Geral de Proteção de Dados (LGPD), incluindo trilha de consultas a informações de prontuário.
> Módulo relacionado: `triagem-e-senha`, `indicadores-alertas-auditoria`.

## RNF-08 | Escalabilidade
O sistema deve suportar o funcionamento simultâneo de múltiplas especialidades e, quando aplicável, múltiplas unidades, sem degradação perceptível de desempenho.

## RNF-09 | Backup e recuperação de desastres
O sistema deve realizar backup periódico dos dados e definir objetivos de tempo e ponto de recuperação (RTO/RPO).
