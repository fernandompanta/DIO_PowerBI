
📌 Visão Geral do Projeto
O objetivo principal foi transformar uma tabela única e plana (Financial Sample) em um Modelo Dimensional em Estrela (Star Schema) otimizado para análise de inteligência de negócios.

🏗️ Estrutura do Modelo Dimensional (Star Schema)
A arquitetura do modelo é composta por 1 Tabela Fato, 5 Tabelas Dimensão e 1 Tabela de Backup Oculta:

1. Tabela Fato
F_Vendas: Contém as métricas quantitativas e financeiras de vendas (Unidades Vendidas, Preço de Venda, Lucro, Vendas Totais) e as chaves de ligação (SK_ID, ID_Produto, Date).

2. Tabelas Dimensão
D_Produtos: Agrupamento por produto com métricas consolidadas (Média de Unidades Vendidas, Média do Valor de Vendas, Mediana do Valor de Vendas, Valor Máximo e Mínimo).

D_Produtos_Detalhes: Detalhes técnicos dos produtos (Discount Band, Sale Price, Units Sold, Manufacturing Price).

D_Descontos: Informações de faixas e valores de desconto agregados por produto.

D_Detalhes: Informações granulares e complementares das transações (Gross Sales, COGS, etc.).

D_Calendário: Tabela temporal gerada via linguagem DAX para suporte a inteligência temporal (Time Intelligence).

3. Tabela de Apoio
financials_origem: Cópia oculta da base original mantida como backup do processo ETL.

🛠️ Etapas do Processo de ETL (Power Query & DAX)
1.Criação da Chave Condicional (ID_Produto): Mapeamento numérico dos produtos (Carretera = 0, Montana = 1, Paseo = 2, Velo = 3, VTT = 4, Amarilla = 5).

2.Criação da Chave Substituta (SK_ID): Adição de índice numérico único para identificação transacional granular.

3.Agrupamentos e Agregações (Group By): Utilização do recurso Agrupar por no Power Query para construir a visão resumida em D_Produtos.

4.Construção da Tabela D_Calendário via DAX:

D_Calendário = 
VAR _DataMinima = MIN(F_Vendas[Date])
VAR _DataMaxima = MAX(F_Vendas[Date])
RETURN
ADDCOLUMNS(
    CALENDAR(_DataMinima, _DataMaxima),
    "Ano", YEAR([Date]),
    "Mês Número", MONTH([Date]),
    "Nome do Mês", FORMAT([Date], "mmmm"),
    "Ano-Mês", FORMAT([Date], "yyyy-mm"),
    "Trimestre", "T" & FORMAT([Date], "q")
)

5.Ajuste de Relacionamentos: Configuração de cardinalidades 1:N com filtro unidirecional conectando as dimensões à tabela fato F_Vendas.
