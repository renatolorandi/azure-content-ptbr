<properties 
	pageTitle="Criar/agendar pipelines, cadeia de atividades no Data Factory | Microsoft Azure" 
	description="Aprenda a criar um pipeline de dados no Azure Data Factory para mover e transformar dados. Crie um fluxo de trabalho orientado por dados para produção pronto para usar informações." 
    keywords="pipeline de dados, fluxo de trabalho controlados por dados"
	services="data-factory" 
	documentationCenter="" 
	authors="spelluru" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="data-factory" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article"
	ms.date="06/27/2016" 
	ms.author="spelluru"/>

# Pipelines e Atividades no Azure Data Factory: criar/agendar pipelines e atividades de cadeia
Este artigo o ajudará a entender pipelines de dados e atividades no Azure Data Factory e usá-los para construir fluxos de trabalho de ponta a ponta orientados a dados para seu cenário ou empresa, desde recomendações de produtos personalizadas até análise de uma campanha de marketing.

> [AZURE.NOTE] Esse artigo pressupõe que você tenha lido os artigos [Introdução ao Azure Data Factory](data-factory-introduction.md) e [Criando Conjuntos de Dados](data-factory-create-datasets.md) antes disso. Se você não tiver experiência prática com a criação de data factories, o tutorial [Compilar sua primeira data factory](data-factory-build-your-first-pipeline.md) o ajudará a entender melhor este artigo.

## O que é um pipeline de dados?
**Pipelines são um agrupamento lógico de Atividades**. Eles são usados para agrupar atividades em uma unidade que executa uma tarefa. Para entender melhor os pipelines, você precisa entender uma atividade primeiro.

## O que é uma atividade?
As Atividades definem as ações a serem realizadas em seus dados. Cada atividade leva zero ou mais [conjuntos de dados](data-factory-create-datasets.md) como entrada e produz um ou mais conjuntos de dados como saída. **Uma atividade é uma unidade de orquestração no Azure Data Factory.**

Por exemplo, você pode usar uma atividade de Cópia para orquestrar a cópia de dados de um conjunto de dados para outro. Da mesma forma, você pode usar uma atividade do HDInsight Hive para executar uma consulta de Hive em um cluster do Azure HDInsight para transformar ou analisar seus dados. O Azure Data Factory fornece uma ampla gama de atividades de [movimentação, análise](data-factory-data-transformation-activities.md) e [transformação de dados](data-factory-data-movement-activities.md). Você também pode optar por criar uma atividade personalizada do .NET para executar seu próprio código.

Considere os dois conjuntos de dados a seguir:

**Conjunto de dados do SQL Azure**

A tabela 'MyTable' contém uma coluna 'timestampcolumn', que ajuda a especificar a data e hora de quando os dados foram inseridos no banco de dados.

	{
	  "name": "AzureSqlInput",
	  "properties": {
	    "type": "AzureSqlTable",
	    "linkedServiceName": "AzureSqlLinkedService",
	    "typeProperties": {
	      "tableName": "MyTable"
	    },
	    "external": true,
	    "availability": {
	      "frequency": "Hour",
	      "interval": 1
	    },
	    "policy": {
	      "externalData": {
	        "retryInterval": "00:01:00",
	        "retryTimeout": "00:10:00",
	        "maximumRetry": 3
	      }
	    }
	  }
	}

**Conjunto de dados de Blob do Azure**

Dados são copiados para um novo blob a cada hora com o caminho para o blob que reflete a data e hora específicas com granularidade de hora.

	{
	  "name": "AzureBlobOutput",
	  "properties": {
	    "type": "AzureBlob",
	    "linkedServiceName": "StorageLinkedService",
	    "typeProperties": {
	      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
	      "partitionedBy": [
	        {
	          "name": "Year",
	          "value": {
	            "type": "DateTime",
	            "date": "SliceStart",
	            "format": "yyyy"
	          }
	        },
	        {
	          "name": "Month",
	          "value": {
	            "type": "DateTime",
	            "date": "SliceStart",
	            "format": "%M"
	          }
	        },
	        {
	          "name": "Day",
	          "value": {
	            "type": "DateTime",
	            "date": "SliceStart",
	            "format": "%d"
	          }
	        },
	        {
	          "name": "Hour",
	          "value": {
	            "type": "DateTime",
	            "date": "SliceStart",
	            "format": "%H"
	          }
	        }
	      ],
	      "format": {
	        "type": "TextFormat",
	        "columnDelimiter": "\t",
	        "rowDelimiter": "\n"
	      }
	    },
	    "availability": {
	      "frequency": "Hour",
	      "interval": 1
	    }
	  }
	}


A atividade de cópia no pipeline abaixo copiará os dados do SQL Azure para o armazenamento de Blob do Azure. Ele usa a tabela do SQL Azure como o conjunto de dados de entrada com frequência por hora e grava os dados para o armazenamento de Blob do Azure representado pelo conjunto de dados 'AzureBlobOutput'. O conjunto de dados de saída também tem uma frequência por hora. Confira a seção [Agendamento e execução](#scheduling-and-execution) para entender como os dados são copiados na unidade de tempo. Esse pipeline tem um período ativo de 3 horas "2015-01-01T08:00:00" para "2015-01-01T11:00:00".

**Pipeline:**
	
	{  
	    "name":"SamplePipeline",
	    "properties":{  
	    "start":"2015-01-01T08:00:00",
	    "end":"2015-01-01T11:00:00",
	    "description":"pipeline for copy activity",
	    "activities":[  
	      {
	        "name": "AzureSQLtoBlob",
	        "description": "copy activity",
	        "type": "Copy",
	        "inputs": [
	          {
	            "name": "AzureSQLInput"
	          }
	        ],
	        "outputs": [
	          {
	            "name": "AzureBlobOutput"
	          }
	        ],
	        "typeProperties": {
	          "source": {
	            "type": "SqlSource",
	            "SqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
	          },
	          "sink": {
	            "type": "BlobSink"
	          }
	        },
	       "scheduler": {
	          "frequency": "Hour",
	          "interval": 1
	        },
	        "policy": {
	          "concurrency": 1,
	          "executionPriorityOrder": "OldestFirst",
	          "retry": 0,
	          "timeout": "01:00:00"
	        }
	      }
	     ]
	   }
	}

Agora que temos uma breve compreensão sobre o que uma atividade é, vamos ver novamente o pipeline.
 
Pipelines são um agrupamento lógico de Atividades. Eles são usados para agrupar atividades em uma unidade que executa uma tarefa. **Um pipeline também é a unidade de implantação e gerenciamento de atividades.** Por exemplo, você poderá reunir atividades relacionadas logicamente como um pipeline, que podem estar em estado ativo ou pausado.

Um conjunto de dados de saída de uma atividade em um pipeline pode ser o conjunto de dados de entrada para outra atividade no mesmo pipeline/pipeline diferente, definindo as dependências entre atividades. Confira [agendamento e execução](#chaining-activities) para obter detalhes.

As etapas comuns ao criar um pipeline no Azure Data Factory são:

1.	Criar uma fábrica de dados (caso não tenha criado).
2.	Criar um serviço vinculado para cada armazenamento de dados ou serviço de computação.
3.	Criar conjuntos de dados de entrada e saída.
4.	Criar um pipeline com atividades que operam em conjuntos de dados definidos acima.

![Entidades de Data Factory](./media/data-factory-create-pipelines/entities.png)

Vamos dar uma olhada mais próxima em como um pipeline é definido.

## Anatomia de um Pipeline  

A estrutura genérica para um pipeline será semelhante ao seguinte:

	{
	    "name": "PipelineName",
	    "properties": 
	    {
	        "description" : "pipeline description",
	        "activities":
	        [
	
	        ],
			"start": "<start date-time>",
			"end": "<end date-time>"
	    }
	}

A seção **Atividades** pode ter uma ou mais atividades definidas dentro dela. Cada atividade tem a seguinte estrutura de nível superior:

	{
	    "name": "ActivityName",
	    "description": "description", 
	    "type": "<ActivityType>",
	    "inputs":  "[]",
	    "outputs":  "[]",
	    "linkedServiceName": "MyLinkedService",
	    "typeProperties":
	    {
	
	    },
	    "policy":
	    {
	    }
	    "scheduler":
	    {
	    }
	}

A tabela a seguir descreve as propriedades nas definições de JSON de atividade e pipeline:

Marca | Descrição | Obrigatório
--- | ----------- | --------
name | Nome da atividade ou pipeline. Especifique um nome que representa a ação que a atividade ou o pipeline é configurado para executar<br/><ul><li>Número máximo de caracteres: 260</li><li>Deve começar com uma letra ou número ou um sublinhado (\_)</li><li>Os seguintes caracteres não são permitidos: “.”, “+”, “?”, “/”, “<”,”>”,”*”,”%”,”&”,”:”,”\\”</li></ul> | Sim
description | Texto que descreve para que a atividade ou o pipeline é usado | Sim
type | Especifica o tipo da atividade. Confira os artigos [Atividades de movimentação de dados](data-factory-data-movement-activities.md) e [Atividades de transformação de dados](data-factory-data-transformation-activities.md) para diferentes tipos de atividades. | Sim
inputs | Tabelas de entrada utilizadas pela atividade<br/><br/>//uma tabela de entrada<br/>"inputs": [ { "name": "inputtable1" } ],<br/><br/>//duas tabelas de entrada <br/>"inputs": [ { "name": "inputtable1" }, { "name": "inputtable2" } ], | Sim
outputs | Tabelas de saída usadas pela atividade.// Uma tabela de saída.<br/>"outputs": [ { "name": "outputtable1" } ],<br/><br/>//duas tabelas de saída<br/>"outputs": [ { "name": "outputtable1" }, { "name": "outputtable2" } ], | Sim
linkedServiceName | Nome do serviço vinculado usado pela atividade. <br/><br/>Uma atividade pode exigir que você especifique o serviço vinculado que é vinculado ao ambiente de computação necessário. | Sim para Atividade do HDInsight e Atividade de Pontuação de Lote do Aprendizado de Máquina do Azure <br/><br/>Não para todas as outros
typeProperties | As propriedades na seção typeProperties dependem do tipo de atividade. Confira o artigo sobre cada atividade individual para saber mais | Não
policy | Diretivas que afetam o comportamento de tempo de execução da atividade. Se não for especificado, as políticas padrão serão utilizadas. Role para baixo para obter detalhes | Não
iniciar | Data e hora de início para o pipeline. Deve estar no [formato ISO](http://en.wikipedia.org/wiki/ISO_8601). Por exemplo: 2014-10-14T16:32:41Z. <br/><br/>É possível especificar uma hora local, por exemplo, EST. Aqui está um exemplo: "2016-02-27T06:00:00**-05:00**", que é 6h EST.<br/><br/>As propriedades de início e término especificam o período ativo para o pipeline. Fatias de saída são produzidas somente nesse período ativo. | Não<br/><br/>Se especificar um valor para a propriedade final, você deverá especificar o valor da propriedade inicial.<br/><br/>Os horários de início e fim podem estar vazios para criar um pipeline, mas ambos devem ter valores para definir um período ativo de execução do pipeline. Se não especificar os horários de início e fim ao criar um pipeline, você poderá defini-los depois usando o cmdlet Set-AzureRmDataFactoryPipelineActivePeriod.
end | Data e hora de término para o pipeline. Se especificado, deve estar no formato ISO. Por exemplo: 2014-10-14T17:32:41Z <br/><br/>É possível especificar uma hora local, por exemplo, uma hora EST. Aqui está um exemplo: "2016-02-27T06:00:00**-05:00**", que é 6h EST.<br/><br/>Para executar o pipeline indefinidamente, especifique 9999-09-09 como o valor da propriedade end. | Não <br/><br/>Se especificar um valor para a propriedade start, você deverá especificar o valor para a propriedade end.<br/><br/>Confira as observações para a propriedade **start**.
isPaused | Se definido como verdadeiro, o pipeline não será executado. Valor padrão = falso. Você pode usar essa propriedade para habilitar ou desabilitar. | Não 
agendador | A propriedade "scheduler" é usada para definir o agendamento desejado para a atividade. Suas sub-propriedades são as mesmas que aquelas na [propriedade de disponibilidade em um conjunto de dados](data-factory-create-datasets.md#Availability). | Não |   
| pipelineMode | O método de agendamento é executado para o pipeline. Os valores permitidos são: scheduled (padrão), onetime.<br/><br/>'Scheduled' indica que o pipeline será executado em um intervalo de tempo especificado de acordo com seu período ativo (hora de início e término). “Onetime” indica que o pipeline será executado apenas uma vez. Pipelines Onetime não podem ser modificados e atualizados depois de criados atualmente. Confira [Pipeline avulso](data-factory-scheduling-and-execution.md#onetime-pipeline) para obter detalhes sobre a configuração única. | Não | 
| expirationTime | Duração de tempo após a criação pela qual o pipeline é válido e deve permanecer provisionado. O pipeline será excluído automaticamente depois de atingir o tempo de expiração se ele não tiver nenhum ativo, falha ou execuções pendentes. | Não | 
| datasets | Lista de conjuntos de dados a serem usados por atividades definidas no pipeline. Isso pode ser usado para definir conjuntos de dados que são específicos para este pipeline e não definido dentro da data factory. Conjuntos de dados definidos dentro este pipeline só podem ser usados por este pipeline e não podem ser compartilhados. Confira [Conjuntos de dados com escopo](data-factory-create-datasets.md#scoped-datasets) para obter detalhes.| Não |  
 

## Tipos de atividades para movimentação de dados e transformação de dados
O Azure Data Factory fornece uma ampla gama de atividades de [Movimentação de dados](data-factory-data-movement-activities.md) e [Transformação de dados](data-factory-data-transformation-activities.md).

### Políticas
As políticas afetam o comportamento de tempo de execução de uma atividade, especialmente quando a divisão de uma tabela é processada. A tabela a seguir fornece os detalhes.

Propriedade | Valores permitidos | Valor Padrão | Descrição
-------- | ----------- | -------------- | ---------------
simultaneidade | Inteiro <br/><br/>Valor máximo: 10 | 1 | Número de execuções simultâneas da atividade.<br/><br/>Determina o número de execuções de atividade paralela que podem ocorrer em divisões diferentes. Por exemplo, se uma atividade precisa passar por um grande conjunto de dados disponíveis, ter uma maior simultaneidade acelera o processamento de dados. 
executionPriorityOrder | NewestFirst<br/><br/>OldestFirst | OldestFirst | Determina a ordem das divisões de dados que estão sendo processadas.<br/><br/>Por exemplo, se houver duas fatias (uma ocorre às 16h e a outra às 17h),e ambas estiverem com a execução pendente. Se você definir executionPriorityOrder como NewestFirst, a divisão às 17h será processada primeiro. De modo semelhante, se você definir executionPriorityORder como OldestFIrst, a fatia às 16h será processada. 
tentar novamente | Inteiro<br/><br/>Valor máx. pode ser 10 | 3 | Número de novas tentativas antes do processamento de dados da divisão ser marcado como Com falha. A execução da atividade para uma divisão de dados é repetida até a contagem de repetição especificada. A nova tentativa é feita logo após a falha.
Tempo limite | TimeSpan | 00:00:00 | Tempo limite para a atividade. Exemplo: 00:10:00 (implica o tempo limite de 10 minutos)<br/><br/>Se um valor não for especificado ou for 0, o tempo limite será infinito.<br/><br/>Se o tempo de processamento de dados em uma divisão exceder o valor de tempo limite, ele será cancelado e o sistema tentará repetir o processamento. O número de repetições depende da propriedade de repetição. Quando atingir o tempo limite, o status será TimedOut.
atrasar | TimeSpan | 00:00:00 | Especifique o atraso antes do processamento de dados da divisão começar.<br/><br/>A execução da atividade para uma divisão de dados é iniciada após o Atraso passar do tempo de execução esperado.<br/><br/>Exemplo: 00:10:00 (implica um atraso de 10 minutos)
longRetry | Inteiro<br/><br/>Valor máximo: 10 | 1 | O número de tentativas repetidas longas antes que a execução da divisão falhe.<br/><br/>Tentativas de longRetry são espaçadas por longRetryInterval. Portanto, se você precisar especificar um tempo entre tentativas de repetição, use longRetry. Se Retry e longRetry forem especificados, cada tentativa de longRetry incluirá tentativas de Retry, e o número máximo de tentativas será Retry * longRetry.<br/><br/>Por exemplo, se houver o seguinte na política de atividade:<br/>Retry: 3<br/>longRetry: 2<br/>longRetryInterval: 01:00:00<br/><br/>Presumindo que haja apenas uma fatia para execução (o status é Aguardando) e a execução da atividade sempre falhe. Inicialmente haveria três tentativas consecutivas de execução. Após cada tentativa, o status de divisão seria Retry. Depois das três primeiras tentativas, o status da divisão seria LongRetry.<br/><br/>Depois de uma hora (por exemplo, valor de longRetryInteval), deve haver outro conjunto de três tentativas consecutivas de execução. Depois disso, o status da divisão seria Com falha e não haveria nova tentativa. Portanto, em geral, foram feitas seis tentativas.<br/><br/>Observação: se uma execução for bem-sucedida, o status da divisão seria Pronto e não haveria novas tentativas.<br/><br/>longRetry pode ser usado em situações em que dados dependentes chegam em horários não determinísticos ou o ambiente geral está bastante instável onde o processamento de dados ocorre. Nesses casos, fazer novas tentativas uma após a outra pode não ajudar e fazer isso após um intervalo de tempo resulta na saída desejada.<br/><br/>Advertência: não defina valores altos para longRetry ou longRetryInterval. Geralmente, valores mais altos implicam em outros problemas sistêmicos que estão sendo tratados em 
longRetryInterval | TimeSpan | 00:00:00 | O intervalo entre tentativas de repetição longa 

## Encadeando atividades
Se você tiver várias atividades em um pipeline e elas não forem interdependentes (a saída de uma atividade não é uma entrada de outra), as atividades poderão ser executadas em paralelo se as fatias de dados de entrada das atividades estiverem prontas.

É possível encadear duas atividades fazendo com que o conjunto de dados de saída de uma atividade seja o conjunto de dados de entrada da outra atividade. As atividades podem estar no mesmo pipeline ou em pipelines diferentes. A segunda atividade é executada apenas quando a primeira é concluída com êxito.

Por exemplo, considere o seguinte caso:
 
1.	O pipeline P1 contém a Atividade A1, que exige o conjunto de dados de entrada externa D1, e produz o conjunto de dados de **saída** **D2**.
2.	O pipeline P2 contém a Atividade A2, que exige a **entrada** do conjunto de dados **D2**, e produz o conjunto de dados de saída D3.
 
Nesse cenário, a atividade A1 será executada quando os dados externos estiverem disponíveis e a frequência de disponibilidade agendada for alcançada. A atividade A2 será executada quando as fatias agendadas de D2 ficarem disponíveis e a frequência de disponibilidade agendada for alcançada. Se houver um erro em uma das fatias no conjunto de dados D2, A2 não será executada para essa fatia até que fique disponível.

A Exibição de Diagrama teria a aparência mostrada abaixo:

![Encadeando atividades em dois pipelines](./media/data-factory-create-pipelines/chaining-two-pipelines.png)

A Exibição de Diagrama com ambas as atividades no mesmo pipeline teria a aparência mostrada abaixo:

![Encadeando atividades no mesmo pipeline](./media/data-factory-create-pipelines/chaining-one-pipeline.png)

## Planejamento e execução
Até agora você entendeu o que são pipelines e atividades. Você também viu como eles são definidos e uma exibição de alto nível das atividades no Azure Data Factory. Agora, vamos dar uma olhada em como eles são executados.

Um pipeline está ativo somente entre sua hora de início e de término. Ele não é executado antes da hora de início ou após a hora de término. Se o pipeline for pausado, ele não será executado independentemente da sua hora de início e término. Para um pipeline ser executado, ele não deve estar pausado. Na verdade, não é o pipeline que é executado. São as atividades no pipeline que são executadas. Entretanto, elas fazem isso no contexto geral do pipeline.

Confira [Agendamento e Execução](data-factory-scheduling-and-execution.md) para saber como funciona o agendamento e a execução no Azure Data Factory.

### Processamento paralelo de fatias
Defina o valor de **simultaneidade** na definição JSON da atividade como um valor maior que 1, para que várias fatias sejam processadas em paralelo por várias instâncias da atividade em tempo de execução. Isso é muito útil durante o processamento de fatias com preenchimento de fundo do passado.


## Criação e gerenciamento de um pipeline
O Azure Data Factory fornece vários mecanismos para criar e implantar pipelines (que, por sua vez, contêm uma ou mais atividades neles).

### Usando o Portal do Azure

1. Faça logon no [Portal do Azure](https://portal.azure.com/).
2. Navegue até sua instância do Azure Data Factory na qual você deseja criar um pipeline
3. Clique no bloco **Criar e Implantar** na lente **Resumo**. 
 
	![Bloco Criar e implantar](./media/data-factory-create-pipelines/author-deploy-tile.png)

4. Clique em **Novo pipeline** na barra de comandos.

	![Botão Novo pipeline](./media/data-factory-create-pipelines/new-pipeline-button.png)

5. Você deve ver a janela do editor com um modelo JSON de pipeline.

	![Editor de pipeline](./media/data-factory-create-pipelines/pipeline-in-editor.png)

6. Depois de concluir a criação do pipeline, clique em **Implantar** na barra de comando para implantar o pipeline.

	**Observação:** durante a implantação, o serviço Azure Data Factory executa algumas verificações de validação para ajudar a corrigir alguns problemas comuns. Caso haja um erro, as informações correspondentes serão exibidas. Execute ações corretivas e, em seguida, reimplante o pipeline criado. Você pode usar o editor para atualizar e excluir um pipeline.

Confira a [Introdução ao Azure Data Factory (Editor do Data Factory)](data-factory-build-your-first-pipeline-using-editor.md) para obter um passo a passo de ponta a ponta para criar uma data factory com um pipeline.

### Usando o plug-in do Visual Studio
Você pode usar o Visual Studio para criar e implantar pipelines no Azure Data Factory. Para saber mais, confira [Veja [Introdução ao Azure Data Factory (Visual Studio)](data-factory-build-your-first-pipeline-using-vs.md) para obter um passo a passo de ponta a ponta para criar uma data factory com um pipeline].


### Usando o PowerShell do Azure
Você pode usar o Azure PowerShell para criar pipelines no Azure Data Factory. Digamos que você definiu o pipeline JSON em um arquivo em c:\\DPWikisample.json. Você pode carregá-lo na sua instância do Azure Data Factory, conforme mostrado no exemplo a seguir.

	New-AzureRmDataFactoryPipeline -ResourceGroupName ADF -Name DPWikisample -DataFactoryName wikiADF -File c:\DPWikisample.json

Confira a [Introdução ao Azure Data Factory (Azure PowerShell)](data-factory-build-your-first-pipeline-using-powershell.md) para obter um passo a passo de ponta a ponta para criar uma data factory com um pipeline.

### Usando o SDK .NET
Você também pode criar e implantar o pipeline usando SDK .NET. Esse mecanismo pode ser utilizado para criar pipelines programaticamente. Para saber mais, confira [Criar, gerenciar e monitorar fábricas de dados de forma programática](data-factory-create-data-factories-programmatically.md).


### Usando o modelo do ARM (Azure Resource Manager)
É possível criar e implantar o pipeline usando um modelo do ARM (Azure Resource Manager). Para saber mais sobre isso, confira a [Introdução ao Azure Data Factory (Azure Resource Manager)](data-factory-build-your-first-pipeline-using-arm.md).

### Usando a API REST
Você também pode criar e implantar o pipeline usando APIs REST. Esse mecanismo pode ser utilizado para criar pipelines programaticamente. Para saber mais, confira [Criar ou atualizar um pipeline](https://msdn.microsoft.com/library/azure/dn906741.aspx).


## Gerenciar e monitorar  
Quando um pipeline é implantado, você pode gerenciar e monitorar seu pipeline, divisões e execuções. Leia mais sobre isso aqui: [Monitorar e gerenciar pipelines](data-factory-monitor-manage-pipelines.md).

## Próximas etapas

- Conheça o [agendamento e execução no Azure Data Factory](data-factory-scheduling-and-execution.md).  
- Leia sobre o [movimento de dados](data-factory-data-movement-activities.md) e [recursos de transformação de dados](data-factory-data-transformation-activities.md) no Azure Data Factory
- Conheça o [gerenciamento e monitoramento no Azure Data Factory](data-factory-monitor-manage-pipelines.md).
- [Criar e implantar seu primeiro pipeline](data-factory-build-your-first-pipeline.md). 

<!---HONumber=AcomDC_0629_2016-->