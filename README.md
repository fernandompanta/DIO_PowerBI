# Processamento e Transformação de Dados com Power BI e MySQL na Azure

Este repositório contém a resolução do Desafio de Projeto do Bootcamp DIO/Universia: **"Integrando Dados com MySQL Azure e Transformando com Power BI"**.

## 📌 Descrição do Projeto
Este projeto consiste na criação de uma instância de banco de dados MySQL na nuvem Microsoft Azure, importação de uma base de dados de colaboradores/departamentos, integração com o Power BI e execução de um processo de ETL (Extração, Transformação e Carga) seguindo as boas práticas de modelagem de dados.

---

🛠️ Etapas do Projeto

1. Criação e Configuração do Banco de Dados no Azure
•	Foi criada uma instância do Azure Database for MySQL flexible server com nome desafio-projeto-fernandompanta.
•	Configurada a regra de Firewall para liberar o IP local e permitir a conexão externa.
•	O banco de dados foi populado com o script de exemplo fornecido no GitHub (company database), utilizando o MySQL Workbench.

2. Integração com o Power BI
•	O Power BI Desktop foi conectado ao MySQL hospedado na Azure informando o servidor, porta e credenciais de acesso.
•	Os dados foram importados para o Power Query para a etapa de ETL (Extração, Transformação e Carga).

🧹 Diretrizes e Transformações de Dados Realizadas

1.	Cabeçalhos e Tipos de Dados:
o	Verificados todos os cabeçalhos de colunas e ajustados os tipos de dados de cada tabela (Textos, Números Inteiros e Datas).

2.	Ajuste de Valores Monetários:
o	Os campos monetários (como Salary) foram convertidos para tipo Número Decimal Fixo / Double Preciso para evitar perda de precisão em cálculos financeiros.

3.	Tratamento de Nulos:
o	Super_ssn: Analisados os valores nulos na coluna Super_ssn da tabela employee. Identificou-se que o registro nulo corresponde ao Gerente Geral/CEO (James Borg), que não possui um gerente acima dele. O registro foi mantido por ter justificativa de negócio.
o	Departamentos sem Gerente: Verificado se havia departamentos sem gerente associado e preenchido conforme as regras estipuladas.

4.	Análise dos Projetos:
o	Verificado o número de horas trabalhadas por projeto na tabela works_on para garantir integridade nos totais de horas acumuladas por colaborador.

5.	Separação de Colunas Complexas:
o	A coluna Address (Endereço) continha dados compostos (Rua, Número, Cidade, Estado). Foi utilizada a funcionalidade Dividir Coluna por Delimitador (vírgula/hífen) para organizar os campos individualmente.

6.	Mescla das Tabelas employee e department:
o	Foi realizada a Mescla de Consultas (Left Outer Join) entre employee e departament, usando como base a tabela employee, adicionando o nome do departamento correspondente a cada colaborador.
o	As colunas redundantes e desnecessárias resultantes da junção foram removidas.

7.	Relacionamento Colaborador x Gerente:
o	Realizada a junção para relacionar o nome do gerente a cada colaborador via auto-junção (Self-Join no Power Query relacionando Super_ssn com Ssn).
o	Nomes e Sobrenomes: As colunas Fname, Minit e Lname foram mescladas para formar a coluna única Nome Completo.

8.	Mescla de Departamento e Localização:
o	Foi criada uma combinação única entre o Nome do Departamento e sua Localização (ex: Research - Houston), garantindo a unicidade necessária para o futuro modelo relacional em estrela (Star Schema).

9.	Agrupamento de Colaboradores por Gerente:
o	Foi criada uma consulta resumida utilizando o recurso Agrupar Por (Group By) para contagem de colaboradores supervisionados por cada gerente.

10.	Limpeza Final de Colunas:
o	Eliminadas todas as colunas desnecessárias, redundantes ou chaves temporárias de cruzamento de todas as tabelas para otimizar o desempenho do modelo de dados.
❓ Pergunta Teórica do Desafio

Por que utilizar a opção MESCLAR e não ATRIBUIR (Acrescentar) no item 13 do desafio?
•	Mesclar (Merge): Funciona como um JOIN no SQL. Ela combina colunas de duas tabelas na horizontal com base em uma chave relacional em comum. Como o objetivo era associar a localização da tabela dept_locations à respectiva linha de departamento, a mescla é a operação correta.
•	Atribuir/Acrescentar (Append): Funciona como um UNION no SQL. Ela empilha linhas de uma tabela embaixo de outra na vertical. Se usássemos o Atribuir, as tabelas seriam apenas empilhadas sem correlacionar quem pertence a qual departamento e localização, corrompendo a estrutura relacional dos dados.

11.	Agrupe os dados a fim de saber quantos colaboradores existem por gerente:
•	Existem 03 colaboradores para Franklin, 02 colaboradores para o James e 02 colaboradores para Jennifer
