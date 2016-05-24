<properties
	pageTitle="Visão geral da Ciência de dados usando Spark no Azure HDInsight | Microsoft Azure"
	description="O kit de ferramentas Spark MLlib traz recursos consideráveis de modelagem de aprendizado de máquina para o ambiente distribuído do HDInsight."
	services="machine-learning"
	documentationCenter=""
	authors="bradsev,deguhath,gokuma"
	manager="paulettm"
	editor="cgronlun"  />

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/26/2016"
	ms.author="bradsev" />

# Visão geral da Ciência de dados usando Spark no Azure HDInsight

[AZURE.INCLUDE [machine-learning-spark-modeling](../../includes/machine-learning-spark-modeling.md)]

## Introdução

O [Spark](http://spark.apache.org/) é uma estrutura de processamento paralelo de software livre que dá suporte ao processamento na memória para melhorar o desempenho de aplicativos analíticos de Big Data. O mecanismo de processamento do Spark foi desenvolvido para velocidade, facilidade de uso e análise sofisticada. As funcionalidades de computação distribuídas na memória do Spark fazem dele uma boa escolha para algoritmos iterativos em cálculos de gráfico e aprendizado de máquina. [MLlib](http://spark.apache.org/mllib/) é a biblioteca de aprendizado de máquina escalonável do Spark que oferece recursos de modelagem para esse ambiente distribuído.

O HDInsight Spark é a oferta do Spark de software livre hospedada no Azure. Ele também inclui suporte para blocos de anotações Jupyter no cluster Spark, que podem executar consultas interativas de Spark SQL para transformar, filtrar e visualizar dados armazenados em Blobs do Azure (WASB).

A coleção de tópicos vinculados no menu demonstra isso percorrendo tarefas de classificação binária e de regressão em uma amostra do conjunto de dados de corridas e tarifas de táxi de Nova York de 2013 e armazenando o modelo no WASB. Os modelos criados incluem a regressão logística e linear, florestas aleatórias e árvores aumentadas gradientes. Eles também mostram como consumir esses modelos para pontuar e avaliar o desempenho preditivo dos modelos. Tópicos mais avançados abordarão como os modelos podem ser treinados usando validação cruzada e limpeza de hiperparâmetro.

As etapas de modelagem nesses tópicos contêm código que mostra como treinar, avaliar, salvar e consumir cada tipo de modelo. Foi usado Python (PySpark) em funcionamento em blocos de anotações Jupyter instalados nos clusters Spark para codificar a solução e mostrar as plotagens relevantes para visualizar os dados.

As etapas de configuração e o código fornecidos neste passo a passo são referentes ao HDInsight Spark. No entanto, o código é genérico e deve funcionar em qualquer cluster Spark. As etapas de configuração e gerenciamento do cluster poderão ser ligeiramente diferentes do que é mostrado aqui se você não estiver usando o HDInsight Spark.

## Pré-requisitos

1\. Antes de começar os tópicos, você precisa ter uma assinatura do Azure. Se ainda não tiver uma, consulte [Obter avaliação gratuita do Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

2\. Para criar o cluster do HDInsight Spark, versão Spark 1.5.2 (HDI 3.3), consulte [Get started: create Apache Spark on Azure HDInsight](../hdinsight/hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql.md) (Introdução: criar Apache Spark no Azure HDInsight)

>[AZURE.NOTE] O kernel python2 usado atualmente pelos blocos de anotações e pelo código nesse passo-a-passo deve usar o Spark (Preview) -> versão Spark 1.5.2 (HDI 3.3).

![](./media/machine-learning-data-science-spark-overview/spark-cluster-on-portal.png)


[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]


## Os dados de Táxi em NYC em 2013

Os dados de Viagens de Táxi em NYC são cerca de 20 GB de arquivos compactados em valores separados por vírgulas (CSV) (cerca de 48 GB descompactados), que incluem mais de 173 milhões de viagens individuais e a tarifa paga por cada viagem. Cada registro de corrida inclui o local e horário de saída e chegada, número da carteira de motorista de taxista anônima e o número do medalhão (identificador exclusivo do táxi). Os dados abrangem todas as corridas no ano de 2013 e são fornecidos nos dois conjuntos de dados a seguir para cada mês:

1. Os arquivos CSV “trip\_data” contêm detalhes da corrida, como o número de passageiros, pontos de saída e chegada, duração e extensão da corrida. Aqui estão alguns exemplos de registros:

		medallion,hack_license,vendor_id,rate_code,store_and_fwd_flag,pickup_datetime,dropoff_datetime,passenger_count,trip_time_in_secs,trip_distance,pickup_longitude,pickup_latitude,dropoff_longitude,dropoff_latitude
		89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,1,N,2013-01-01 15:11:48,2013-01-01 15:18:10,4,382,1.00,-73.978165,40.757977,-73.989838,40.751171
		0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-06 00:18:35,2013-01-06 00:22:54,1,259,1.50,-74.006683,40.731781,-73.994499,40.75066
		0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-05 18:49:41,2013-01-05 18:54:23,1,282,1.10,-74.004707,40.73777,-74.009834,40.726002
		DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:54:15,2013-01-07 23:58:20,2,244,.70,-73.974602,40.759945,-73.984734,40.759388
		DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:25:03,2013-01-07 23:34:24,1,560,2.10,-73.97625,40.748528,-74.002586,40.747868

2. Os arquivos CSV “trip\_fare” contêm detalhes sobre as passagens pagas por cada corrida, como tipo de pagamento, valor da tarifa, custos adicionais e impostos, gorjetas e pedágios, e o valor total pago. Aqui estão alguns exemplos de registros:

		medallion, hack_license, vendor_id, pickup_datetime, payment_type, fare_amount, surcharge, mta_tax, tip_amount, tolls_amount, total_amount
		89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,2013-01-01 15:11:48,CSH,6.5,0,0.5,0,0,7
		0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-06 00:18:35,CSH,6,0.5,0.5,0,0,7
		0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-05 18:49:41,CSH,5.5,1,0.5,0,0,7
		DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:54:15,CSH,5,0.5,0.5,0,0,6
		DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:25:03,CSH,9.5,0.5,0.5,0,0,10.5


A chave exclusiva para unir trip\_data e trip\_fare é composta pelos campos: medallion, hack\_license e pickup\_datetime.


Pegamos uma amostra de 0,1% desses arquivos e os unimos em um único conjunto de dados que será usado como o conjunto de dados de entrada para este passo a passo. A chave exclusiva para unir trip\_data e trip\_fare é composta pelos campos: medallion, hack\_license e pickup\_datetime. Cada registro do conjunto de dados contém os seguintes atributos que representam uma corrida de táxi em NYC:


|Campo| Breve descrição
|------|---------------------------------
| medallion |Licença do táxi em forma anônima (ID exclusiva do táxi)
| hack\_license |	Número de licença do carro em forma anônima
| vendor\_id |	ID do fornecedor do serviço de táxi
| rate\_code | Tarifa praticada pelos táxis em NYC
| store\_and\_fwd\_flag | Sinalizador de que os dados foram armazenados no veículo e encaminhados posteriormente
| pickup\_datetime |	Data e hora do início da corrida
| dropoff\_datetime | Data e hora do final da corrida
| pickup\_hour |	Hora do início da corrida
| pickup\_week |	Semana do início da corrida
| weekday |	Dia da semana (intervalo de 1 a 7)
| passenger\_count |	Número de passageiros em uma corrida de táxi
| trip\_time\_in\_secs | Tempo da corrida em segundos
| trip\_distance | Distância percorrida na corrida em milhas
| pickup\_longitude | Longitude do início da corrida
| pickup\_latitude |	Latitude do início da corrida
| dropoff\_longitude | Longitude do final da corrida
| dropoff\_latitude | Latitude do final da corrida
| direct\_distance |	Distância direta entre os locais inicial e final da corrida
| payment\_type | Tipo de pagamento (dinheiro, cartão de crédito etc.)
| fare\_amount | Valor da tarifa
| surcharge | Sobretaxa
| mta\_tax |	Imposto da MTA
| tip\_amount | Valor da gorjeta
| tolls\_amount | Valor dos pedágios
| total\_amount | Valor total
| tipped | Gorjeta fornecida (0/1 para não ou sim)
| tip\_class | Classe da gorjeta (0: US$ 0, 1: US$ 0 a 5, 2: US$ 6 a 10, 3: US$ 11 a 20, 4: mais de US$ 20)


## Executar código de um bloco de anotações Jupyter no cluster Spark 

Você pode iniciar o bloco de anotações Jupyter no portal do Azure: encontre seu cluster Spark e clique nele para inserir a página detalhada de gerenciamento do cluster. Lá, você pode clicar nos **Painéis do Cluster**, nos quais está o ícone do bloco de anotações Jupyter associado ao cluster Spark.

![](./media/machine-learning-data-science-spark-overview/spark-jupyter-on-portal.png)

Você também pode navegar até ***https://CLUSTERNAME.azurehdinsight.net/jupyter*** para acessar os blocos de anotações Jupyter. Você precisará da senha de sua conta de administrador para acessar os blocos de anotações.

![](./media/machine-learning-data-science-spark-overview/spark-jupyter-notebook.png)

Navegue até o Python para ver os blocos de anotações existentes que executam scripts de python. Você verá um diretório que contém alguns exemplos de blocos de anotações pré-empacotados listados. O bloco de anotações que contém os exemplos de código neste tópico está disponível no [Github](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/Spark/Python).

Você pode carregar os blocos de anotações diretamente do Github para o servidor do bloco de anotações Jupyter no seu cluster Spark. Na home page do seu Jupyter, clique no botão **Carregar** na parte direita da tela. Um explorador de arquivos abrirá. Aqui, você pode colar a URL do Github (conteúdo bruto) do Notebook e clicar em **Abrir**. Você verá o nome do arquivo na sua lista de arquivos do Jupyter, com um botão **Carregar** novamente. Clique nesse botão **Carregar**. Agora, você importou o bloco de anotações. Repita estas etapas para carregar os seguintes blocos de anotações deste passo a passo. (Observação: você pode clicar com o botão direito do mouse nos links abaixo no seu navegador e selecione **Copiar Link** para obter a URL do conteúdo bruto do github que pode ser colada na caixa de diálogo do explorador de arquivos Carregar do Jupyter)

1.	[machine-learning-data-science-spark-data-exploration-modeling.ipynb](https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/Spark/Python/machine-learning-data-science-spark-data-exploration-modeling.ipynb)
2.	[machine-learning-data-science-spark-model-consumption.ipynb](https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/Spark/Python/machine-learning-data-science-spark-model-consumption.ipynb)
3.	[machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb](https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/Spark/Python/machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb)

Agora você pode:

- clicar no bloco de anotações e ver o código
- executar cada célula pressionando **SHIFT-ENTER**
- executar o notebook inteiro clicando em **Célula** -> **Executar**


## O que vem a seguir?

Agora que configurou um cluster HDInsight Spark e carregou os blocos de anotações Jupyter, você está pronto para percorrer os tópicos que correspondem a esses três blocos de anotações, que mostram como explorar seus dados, criar e consumir modelos. O bloco de anotações de exploração e modelagem de dados avançadas se aprofunda na inclusão da validação cruzada, limpeza de hiperparâmetro e avaliação de modelo.

**Modelagem e exploração de dados com Spark:** explore o conjunto de dados, crie os modelos de aprendizado de máquina para serem classificados e avaliados aqui percorrendo o tópico [Criar modelos de regressão e classificação binária para dados com o kit de ferramentas Spark MLlib](machine-learning-data-science-spark-data-exploration-modeling.md).

**Consumo de modelos:** para aprender a classificar os modelos de classificação e regressão criados neste tópico, confira [Pontuar e avaliar modelos de aprendizado de máquina criados com Spark](machine-learning-data-science-spark-model-consumption.md).

**Validação cruzada e limpeza de hiperparâmetro**: consulte [Advanced data exploration and modeling with Spark](machine-learning-data-science-spark-advanced-data-exploration-modeling.md) (Modelagem e exploração de dados avançadas com Spark) sobre como os modelos podem ser treinados usando a validação cruzada e a limpeza de hiperparâmetro

<!---HONumber=AcomDC_0427_2016-->