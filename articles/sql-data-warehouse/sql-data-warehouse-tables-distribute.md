<properties
   pageTitle="Distribuindo tabelas no SQL Data Warehouse | Microsoft Azure"
   description="Introdução à distribuição de tabelas no Azure SQL Data Warehouse."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="jrowlandjones"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="07/11/2016"
   ms.author="jrj;barbkess;sonyama"/>

# Distribuindo tabelas no SQL Data Warehouse

> [AZURE.SELECTOR]
- [Visão geral][]
- [Tipos de dados][]
- [Distribuir][]
- [Índice][]
- [Partition][]
- [Estatísticas][]
- [Temporário][]

SQL Data Warehouse é um sistema de banco de dados distribuído de processamento extremamente paralelo (MPP). Ao dividir os dados e a funcionalidade de processamento em vários nós, o SQL Data Warehouse pode oferecer uma enorme escalabilidade, muito além de qualquer sistema individual. Decidir como distribuir seus dados dentro de seu SQL Data Warehouse é um dos fatores mais importantes para alcançar um desempenho ideal. A chave para o desempenho ideal é minimizar a movimentação dos dados, e a chave para minimizar a movimentação de dados é selecionar a estratégia de distribuição correta.

## Noções básicas sobre a movimentação de dados

Em um sistema MPP, os dados de cada tabela são divididos em vários bancos de dados subjacentes. As consultas mais otimizadas em um sistema MPP podem simplesmente ser passadas para executar em bancos de dados distribuídos individuais sem interação entre os outros bancos de dados. Por exemplo, digamos que você tenha um banco de dados com dados de vendas que contém duas tabelas, vendas e clientes. Se você tiver uma consulta que precise unir a tabela de vendas à tabela de clientes, e dividir as tabelas de vendas e clientes pelo número de clientes, colocando cada cliente em um banco de dados separado, todas as consultas que unem vendas e clientes poderão ser resolvidas em cada banco de dados sem conhecimento dos outros bancos de dados. Por outro lado, se você dividir seus dados de vendas pelo número de pedido e seus dados de clientes pelo número de cliente, qualquer banco de dados específico não terá os dados correspondentes para cada cliente e, assim, se você quiser unir os dados de vendas aos dados de clientes, precisará obter os dados de cada cliente dos outros bancos de dados. No segundo exemplo, a movimentação de dados precisa ocorrer para mover os dados de clientes para os dados de vendas, de modo que as duas tabelas possam ser unidas.

A movimentação de dados nem sempre é ruim; às vezes, ela é necessária para resolver uma consulta. Porém, quando essa etapa extra pode ser evitada, naturalmente a consulta é executada mais depressa. A movimentação de dados geralmente surge quando tabelas são unidas ou são executadas agregações. Muitas vezes, você precisa fazer ambos. Portanto, embora possa fazer a otimização de um cenário, como uma associação, você ainda precisará da movimentação de dados para ajudar a resolver o outro cenário, como uma agregação. O truque é descobrir o que dá menos trabalho. Na maioria dos casos, a distribuição de grandes tabelas de fatos em uma coluna unida normalmente é o método mais eficaz para reduzir a maior parte da movimentação de dados. A distribuição de dados em colunas de junção é um método muito mais comum para reduzir a movimentação de dados, em vez da distribuição de dados em colunas envolvidas em uma agregação.

## Selecionar método de distribuição

Nos bastidores, o SQL Data Warehouse divide seus dados em 60 bancos de dados. Cada banco de dados individual é conhecido como uma **distribuição**. Quando os dados são carregados em cada tabela, o SQL Data Warehouse precisa saber como dividir dados entre essas 60 distribuições.

O método de distribuição é definido no nível da tabela e, atualmente, há duas opções:

1. **Round robin**, que distribui dados uniformemente, mas aleatoriamente.
2. **Distribuídos por hash**, que distribui dados baseados em valores de hash de uma única coluna

Por padrão, quando você não define um método de distribuição de dados, a tabela será distribuída usando o método de distribuição **round robin**. No entanto, conforme a sua implementação vai se tornando mais sofisticada, convém considerar o uso de tabelas **distribuídas por hash** para minimizar a movimentação de dados, que por sua vez otimizará o desempenho da consulta.

### Tabelas Round Robin

O uso do método Round Robin de distribuição de dados é basicamente como parece. Conforme seus dados vão sendo carregados, cada linha é simplesmente enviada para a próxima distribuição. Esse método de distribuição de dados sempre distribui os dados muito uniforme e aleatoriamente em todas as distribuições. Ou seja, não há uma classificação durante o processo de round robin que coloca seus dados. Uma distribuição round robin é chamada às vezes de hash aleatório por esse motivo. Com uma tabela distribuída por round robin, não é necessário compreender os dados. Por esse motivo, as tabelas Round Robin normalmente são bons destinos de carregamento.

Por padrão, se nenhum método de distribuição for escolhido, o método de distribuição round robin será usado. No entanto, embora as tabelas round robin sejam fáceis de usar, como os dados são distribuídos aleatoriamente em todo o sistema, isso significa que o sistema não pode garantir em que distribuição está cada linha. Como resultado, o sistema às vezes precisa chamar uma operação de movimentação de dados para organizar melhor seus dados antes de poder resolver uma consulta. Essa etapa extra pode causar lentidão em suas consultas.

Considere usar a distribuição round robin para a sua tabela nos seguintes cenários:

- Ao começar, como um simples ponto de partida
- Se não houver uma chave de junção óbvia
- Se não houver colunas candidatas boas para distribuir a tabela de hash
- Se a tabela não compartilhar uma chave de junção comum com outras tabelas
- Se a junção for menos significativa do que outras junções na consulta
- Quando a tabela é uma tabela temporária de preparo

Ambos os exemplos criarão uma tabela Round Robin:

```SQL
-- Round Robin created by default
CREATE TABLE [dbo].[FactInternetSales]
(   [ProductKey]            int          NOT NULL
,   [OrderDateKey]          int          NOT NULL
,   [CustomerKey]           int          NOT NULL
,   [PromotionKey]          int          NOT NULL
,   [SalesOrderNumber]      nvarchar(20) NOT NULL
,   [OrderQuantity]         smallint     NOT NULL
,   [UnitPrice]             money        NOT NULL
,   [SalesAmount]           money        NOT NULL
)
;

-- Explicitly Created Round Robin Table
CREATE TABLE [dbo].[FactInternetSales]
(   [ProductKey]            int          NOT NULL
,   [OrderDateKey]          int          NOT NULL
,   [CustomerKey]           int          NOT NULL
,   [PromotionKey]          int          NOT NULL
,   [SalesOrderNumber]      nvarchar(20) NOT NULL
,   [OrderQuantity]         smallint     NOT NULL
,   [UnitPrice]             money        NOT NULL
,   [SalesAmount]           money        NOT NULL
)
WITH
(   CLUSTERED COLUMNSTORE INDEX
,   DISTRIBUTION = ROUND_ROBIN
)
;
```

> [AZURE.NOTE] Embora o round robin seja o tipo de tabela padrão, ser claro em seu DDL é considerado uma prática recomendada para que as intenções do seu layout de tabela fiquem claras para outras pessoas.

### Tabelas distribuídas por hash

Usar um algoritmo **Distribuído por hash** para distribuir suas tabelas pode melhorar o desempenho em muitos cenários, reduzindo a movimentação de dados no momento da consulta. As tabelas distribuídas por hash são tabelas divididas entre os bancos de dados distribuídos usando um algoritmo de hash em uma única coluna que você seleciona. A coluna de distribuição é o que determina como os dados são divididos em seus bancos de dados distribuídos. A função de hash usa a coluna de distribuição para atribuir linhas a distribuições. O algoritmo de hash e a distribuição resultante são determinísticos. Isso é, o mesmo valor com o mesmo tipo de dados sempre terá a mesma distribuição.

Este exemplo criará uma tabela distribuída na ID:

```SQL
CREATE TABLE [dbo].[FactInternetSales]
(   [ProductKey]            int          NOT NULL
,   [OrderDateKey]          int          NOT NULL
,   [CustomerKey]           int          NOT NULL
,   [PromotionKey]          int          NOT NULL
,   [SalesOrderNumber]      nvarchar(20) NOT NULL
,   [OrderQuantity]         smallint     NOT NULL
,   [UnitPrice]             money        NOT NULL
,   [SalesAmount]           money        NOT NULL
)
WITH
(   CLUSTERED COLUMNSTORE INDEX
,  DISTRIBUTION = HASH([ProductKey])
)
;
```

## Selecionar coluna de distribuição

Quando você opta por **distribuir uma tabela por hash**, precisa selecionar uma coluna de distribuição individual. Ao selecionar uma coluna de distribuição, três principais fatores deverão ser considerados.

Selecione uma única coluna que:

1. Não será atualizada
2. Distribuirá os dados uniformemente, evitando a distorção de dados
3. Minimizar a movimentação de dados

### Selecionar a coluna de distribuição que não será atualizada

As colunas de distribuição não são atualizáveis. Portanto, selecione uma coluna com valores estáticos. Se uma coluna precisar ser atualizada, ela geralmente não será uma candidata a distribuição válida. Se você precisar atualizar uma coluna de distribuição, isso pode ser feito excluindo a linha e inserindo uma nova linha.

### Selecionar a coluna de distribuição que distribuirá os dados uniformemente

Como a velocidade máxima de um sistema distribuído é a sua distribuição mais lenta, é importante dividir o trabalho igualmente entre as distribuições para conseguir uma execução equilibrada em todo o sistema. A forma como o trabalho é dividido em um sistema distribuído baseia-se em onde residem os dados de cada distribuição. Isso faz com que a seleção da coluna de distribuição correta para distribuir os dados seja muito importante, para que cada distribuição possua a mesma quantidade de trabalho e leve o mesmo tempo para concluir sua parte do trabalho. Quando o trabalho é bem dividido em todo o sistema, é uma execução equilibrada. Quando os dados não são divididos uniformemente em um sistema e não estão bem equilibrados, nós chamamos de **distorção de dados**.

Para dividir os dados uniformemente e evitar a distorção de dados, considere o seguinte ao selecionar a coluna de distribuição:

1. Selecione uma coluna quem contenha um número significativo de valores distintos.
2. Evite a distribuição de dados em colunas com alta frequência de poucos valores ou alta frequência de valores nulos.
3. Evite a distribuição de dados em colunas de data.
4. Evite a distribuição em colunas com menos de 60

Já que cada valor é distribuído por hash para uma das 60 distribuições, para conseguir uma distribuição uniforme, você deverá selecionar uma coluna que seja altamente exclusiva e fornecer mais de 60 valores exclusivos. Para ilustrar, considere o caso extremo em que a coluna tenha apenas 40 valores exclusivos. Se esta coluna fosse selecionada como a chave de distribuição, os dados da tabela seriam espalhados apenas por uma parte do sistema, deixando 20 distribuições sem nenhum processamento ou dados. Por outro lado, as outras 40 distribuições teriam mais trabalho a fazer do que se os dados fossem espalhados uniformemente entre as 60 distribuições.

Se você distribuir uma tabela em uma coluna altamente anulável, todos os valores nulos serão levados para a mesma distribuição e esta terá mais trabalho do que outras distribuições, o que irá retardar o sistema inteiro. A distribuição em uma coluna de data também pode causar distorção no processamento quando as consultas são altamente seletivas em data e apenas algumas datas estão envolvidas em uma consulta.

Quando não há colunas adequadas, considere o uso de round robin como método de distribuição.

### Selecione a coluna de distribuição que irá minimizar a movimentação de dados

Minimizar a movimentação de dados selecionando a coluna de distribuição correta é uma das estratégias mais importantes para otimizar o desempenho do SQL Data Warehouse. A movimentação de dados geralmente surge quando tabelas são unidas ou são executadas agregações. As colunas usadas nas cláusulas `JOIN`, `GROUP BY`, `DISTINCT`, `OVER` e `HAVING` todas são candidatas à **boa** distribuição de hash. Por outro lado, colunas na cláusula `WHERE` **não** são boas para coluna de hash porque elas limitam quais distribuições participam da consulta.

Em termos gerais, se tiver duas tabelas de fatos grande frequentemente envolvidas em uma associação, você obterá mais desempenho distribuindo ambas as tabelas em uma das colunas de junção. Se você tiver uma tabela que nunca foi unida a outra tabela de fatos grande, procure as colunas que estão frequentemente na cláusula `GROUP BY`.

Há alguns critérios principais que devem ser atendidos para evitar a movimentação de dados durante uma junção:

1. As tabelas envolvidas na junção devem ser distribuídas por hash em **uma** das colunas que participam da junção.
2. Os tipos de dados das colunas de junção devem ser correspondentes entre as duas tabelas.
3. As colunas devem ser associadas com um operador equals.
4. O tipo de associação pode não ser `CROSS JOIN`.


## Solucionando problemas de distorção de dados

Quando os dados da tabela são distribuídos usando o método de distribuição de hash, é possível que algumas distribuições sejam distorcidas para ter desproporcionalmente mais dados que outras. A distorção de dados em excesso pode afetar o desempenho de consulta porque o resultado final de uma consulta distribuída precisa aguardar até que a distribuição de execução mais longa termine. Dependendo do grau da distorção de dados, pode ser necessário tratá-lo.

### Identificando distorções

Uma maneira simples de identificar uma tabela como distorcida é usar `DBCC PDW_SHOWSPACEUSED`. Esta é uma maneira simples e rápida de ver o número de linhas da tabela que estão armazenadas em cada uma das 60 distribuições do seu banco de dados. Lembre-se que, para um desempenho mais equilibrado, as linhas na tabela distribuída devem ser divididas uniformemente entre todas as distribuições.

```sql
-- Find data skew for a distributed table
DBCC PDW_SHOWSPACEUSED('dbo.FactInternetSales');
```

No entanto, se você consultar as exibições de gerenciamento dinâmico do Azure SQL Data Warehouse (DMV), poderá executar uma análise mais detalhada. Para começar, crie o modo de exibição [dbo.vTableSizes][] usando o SQL do artigo [Visão geral da tabela][Overview]. Depois que a exibição é criada, execute esta consulta para identificar quais tabelas têm mais de 10% de distorção de dados.

```sql
select *
from dbo.vTableSizes 
where two_part_name in 
    (
    select two_part_name
    from dbo.vDistributionSkew 
    where row_count > 0
    group by two_part_name
    having min(row_count * 1.000)/max(row_count * 1.000) > .10
	)
order by two_part_name, row_count
;
```

### Resolvendo distorções de dados

Nem toda distorção é suficiente para exigir uma correção. Em alguns casos, o desempenho de uma tabela em algumas consultas pode superar os danos de distorção de dados. Para decidir se deve resolver a distorção de dados em uma tabela, você deve compreender o máximo possível sobre os volumes de dados e consultas na carga de trabalho. Uma maneira de observar o impacto da distorção é usar as etapas do artigo [Consultar monitoramento][] para monitorar o impacto da distorção no desempenho da consulta e, especificamente, o impacto em quanto tempo as consultas demoram para concluir as distribuições individuais.

A distribuição de dados é uma questão de encontrar o equilíbrio certo entre minimizar a distorção de dados e minimizar a movimentação de dados. Esses podem ser objetivos opostos e, às vezes, você desejará manter a distorção de dados para reduzir a movimentação de dados. Por exemplo, quando a coluna de distribuição com frequência é a coluna compartilhada em junções e agregações, você vai minimizar a movimentação de dados. O benefício de ter o mínimo de movimentação de dados pode superar o impacto de ter a distorção de dados.

A maneira comum de resolver a distorção de dados é recriar a tabela com uma coluna de distribuição diferente. Como não há nenhuma maneira de alterar a coluna de distribuição em uma tabela existente, a maneira de alterar a distribuição de uma tabela é recriá-la com [CTAS][]. Aqui estão dois exemplos de como resolver a distorção de dados:

### Exemplo 1: criar novamente a tabela com uma nova coluna de distribuição

Este exemplo usa [CTAS][] para recriar uma tabela com uma coluna de distribuição de hash diferente.

```sql
CREATE TABLE [dbo].[FactInternetSales_CustomerKey] 
WITH (  CLUSTERED COLUMNSTORE INDEX
     ,  DISTRIBUTION =  HASH([CustomerKey])
     ,  PARTITION       ( [OrderDateKey] RANGE RIGHT FOR VALUES (   20000101, 20010101, 20020101, 20030101
                                                                ,   20040101, 20050101, 20060101, 20070101
                                                                ,   20080101, 20090101, 20100101, 20110101
                                                                ,   20120101, 20130101, 20140101, 20150101
                                                                ,   20160101, 20170101, 20180101, 20190101
                                                                ,   20200101, 20210101, 20220101, 20230101
                                                                ,   20240101, 20250101, 20260101, 20270101
                                                                ,   20280101, 20290101
                                                                )
                        )
    )
AS
SELECT  *
FROM    [dbo].[FactInternetSales]
OPTION  (LABEL  = 'CTAS : FactInternetSales_CustomerKey')
;

--Create statistics on new table
CREATE STATISTICS [ProductKey] ON [FactInternetSales_CustomerKey] ([ProductKey]);
CREATE STATISTICS [OrderDateKey] ON [FactInternetSales_CustomerKey] ([OrderDateKey]);
CREATE STATISTICS [CustomerKey] ON [FactInternetSales_CustomerKey] ([CustomerKey]);
CREATE STATISTICS [PromotionKey] ON [FactInternetSales_CustomerKey] ([PromotionKey]);
CREATE STATISTICS [SalesOrderNumber] ON [FactInternetSales_CustomerKey] ([SalesOrderNumber]);
CREATE STATISTICS [OrderQuantity] ON [FactInternetSales_CustomerKey] ([OrderQuantity]);
CREATE STATISTICS [UnitPrice] ON [FactInternetSales_CustomerKey] ([UnitPrice]);
CREATE STATISTICS [SalesAmount] ON [FactInternetSales_CustomerKey] ([SalesAmount]);

--Rename the tables
RENAME OBJECT [dbo].[FactInternetSales] TO [FactInternetSales_ProductKey];
RENAME OBJECT [dbo].[FactInternetSales_CustomerKey] TO [FactInternetSales];
```

### Exemplo 2: recriar a tabela usando a distribuição round robin

Este exemplo usa [CTAS][] para recriar uma tabela com round robin em vez de uma distribuição por hash. Essa alteração produzirá a distribuição uniforme de dados às custas de uma maior movimentação de dados.

```sql
CREATE TABLE [dbo].[FactInternetSales_ROUND_ROBIN] 
WITH (  CLUSTERED COLUMNSTORE INDEX
     ,  DISTRIBUTION =  ROUND_ROBIN
     ,  PARTITION       ( [OrderDateKey] RANGE RIGHT FOR VALUES (   20000101, 20010101, 20020101, 20030101
                                                                ,   20040101, 20050101, 20060101, 20070101
                                                                ,   20080101, 20090101, 20100101, 20110101
                                                                ,   20120101, 20130101, 20140101, 20150101
                                                                ,   20160101, 20170101, 20180101, 20190101
                                                                ,   20200101, 20210101, 20220101, 20230101
                                                                ,   20240101, 20250101, 20260101, 20270101
                                                                ,   20280101, 20290101
                                                                )
                        )
    )
AS
SELECT  *
FROM    [dbo].[FactInternetSales]
OPTION  (LABEL  = 'CTAS : FactInternetSales_ROUND_ROBIN')
;

--Create statistics on new table
CREATE STATISTICS [ProductKey] ON [FactInternetSales_ROUND_ROBIN] ([ProductKey]);
CREATE STATISTICS [OrderDateKey] ON [FactInternetSales_ROUND_ROBIN] ([OrderDateKey]);
CREATE STATISTICS [CustomerKey] ON [FactInternetSales_ROUND_ROBIN] ([CustomerKey]);
CREATE STATISTICS [PromotionKey] ON [FactInternetSales_ROUND_ROBIN] ([PromotionKey]);
CREATE STATISTICS [SalesOrderNumber] ON [FactInternetSales_ROUND_ROBIN] ([SalesOrderNumber]);
CREATE STATISTICS [OrderQuantity] ON [FactInternetSales_ROUND_ROBIN] ([OrderQuantity]);
CREATE STATISTICS [UnitPrice] ON [FactInternetSales_ROUND_ROBIN] ([UnitPrice]);
CREATE STATISTICS [SalesAmount] ON [FactInternetSales_ROUND_ROBIN] ([SalesAmount]);

--Rename the tables
RENAME OBJECT [dbo].[FactInternetSales] TO [FactInternetSales_HASH];
RENAME OBJECT [dbo].[FactInternetSales_ROUND_ROBIN] TO [FactInternetSales];
```

## Próximas etapas

Para saber mais sobre o design da tabela, confira os artigos [Distribuir][], [Índice][], [Partição][], [Tipos de dados][], [Estatísticas][] e [Tabelas temporárias][Temporary]. Para obter uma visão geral das práticas recomendadas, confira [Práticas recomendadas para o Azure SQL Data Warehouse][].


<!--Image references-->

<!--Article references-->
[Overview]: ./sql-data-warehouse-tables-overview.md
[Visão geral]: ./sql-data-warehouse-tables-overview.md
[Tipos de dados]: ./sql-data-warehouse-tables-data-types.md
[Distribuir]: ./sql-data-warehouse-tables-distribute.md
[Índice]: ./sql-data-warehouse-tables-index.md
[Partition]: ./sql-data-warehouse-tables-partition.md
[Partição]: ./sql-data-warehouse-tables-partition.md
[Estatísticas]: ./sql-data-warehouse-tables-statistics.md
[Temporary]: ./sql-data-warehouse-tables-temporary.md
[Temporário]: ./sql-data-warehouse-tables-temporary.md
[Práticas recomendadas para o Azure SQL Data Warehouse]: ./sql-data-warehouse-best-practices.md
[Consultar monitoramento]: ./sql-data-warehouse-manage-monitor.md
[dbo.vTableSizes]: ./sql-data-warehouse-tables-overview.md#querying-table-sizes

<!--MSDN references-->
[DBCC PDW_SHOWSPACEUSED()]: https://msdn.microsoft.com/library/mt204028.aspx

<!--Other Web references-->

<!---HONumber=AcomDC_0713_2016-->