<properties
   pageTitle="Desenvolver trabalhos de Python MapReduce com HDInsight | Microsoft Azure"
   description="Saiba como criar e executar trabalhos Python MapReduce em clusters HDInsight baseados em Linux."
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="paulettm"
   editor="cgronlun"
	tags="azure-portal"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="05/13/2016"
   ms.author="larryfr"/>

#Desenvolver programas de transmissão do Python para HDInsight

O Hadoop fornece uma API de streaming para o MapReduce que permite que você escreva funções de mapeamento e redução em outras linguagens além do Java. Neste artigo, você aprenderá como usar o Python para executar operações de MapReduce.

> [AZURE.NOTE] Embora o código Python neste documento possa ser usado com um cluster HDInsight baseado no Windows, as etapas neste documento são específicas de clusters baseados em Linux.

Esse artigo se baseia em informações e exemplos publicados por Michael Noll em [Escrevendo um programa de MapReduce do Hadoop em Python](http://www.michael-noll.com/tutorials/writing-an-hadoop-mapreduce-program-in-python/).

##Pré-requisitos

Para concluir as etapas neste artigo, você precisará do seguinte:

* Um Hadoop baseado em Linux no cluster HDInsight

* Um editor de texto

    > [AZURE.IMPORTANT] O editor de texto deve usar LF com fim de linha. Se usar CRLF, isso causará erros ao executar o trabalho MapReduce em clusters HDInsight baseados em Linux. Se você não tiver certeza, use a etapa opcional na seção [Executar MapReduce](#run-mapreduce) para converter qualquer CRLF em LF.

* Para clientes Windows, PuTTY e PSCP. Esses utilitários estão disponíveis na <a href="http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html" target="_blank">Página de Download do PuTTY</a>.

##Contagem de palavras

Para esse exemplo, você implementará uma contagem de palavras básicas usando um mapeador e um redutor. O mapeador divide frases em palavras individuais e o redutor agrega as palavras e contagens para produzir a saída.

O fluxograma a seguir ilustra o que acontece durante o mapa e reduz as fases.

![ilustração da redução do mapa](./media/hdinsight-hadoop-streaming-python/HDI.WordCountDiagram.png)

##Por que Python?

Python é uma linguagem de programação de alto nível para fins gerais que permite expressar conceitos em menos linhas de código que muitas outras linguagens. Ele recentemente se tornou popular com os cientistas de dados como uma linguagem de criação de protótipos, graças à sua natureza interpretada, digitação dinâmica e sintaxe elegante o torna adequado para o método RAD.

Python é instalado em todos os clusters HDInsight.

##Transmissão do MapReduce

O Hadoop permite que você especifique um arquivo que contém o mapa e a lógica de redução usada por um trabalho. Os requisitos específicos para o mapa e lógica de redução são:

* **Entrada**- os componentes mapa e reduzir devem ler dados de entrada de STDIN.

* **Saída** - os componentes mapa e reduzir devem gravar os dados de saída para STDOUT.

* **Formato de dados** - os dados consumidos e produzidos devem ser um par chave/valor, separado por um caractere de tabulação.

O Python pode facilmente atender a esses requisitos usando o módulo **sys** para ler do STDIN e usando **print** para imprimir para o STDOUT. O restante é simplesmente formatar os dados com um caractere de tabulação (`\t`) entre a chave e o valor.

##Criar o mapeador e redutor

O mapeador e redutor são arquivos de texto, nesse caso, **mapper.py** e **reducer.py** (para deixar claro o que cada um faz). Você pode criá-los usando o editor de sua escolha.

###Mapper.py

Crie um novo arquivo chamado **mapper.py** e use o seguinte código como o conteúdo:

    #!/usr/bin/env python

    # Use the sys module
    import sys

    # 'file' in this case is STDIN
    def read_input(file):
        # Split each line into words
        for line in file:
            yield line.split()

    def main(separator='\t'):
        # Read the data using read_input
        data = read_input(sys.stdin)
        # Process each words returned from read_input
        for words in data:
            # Process each word
            for word in words:
                # Write to STDOUT
                print '%s%s%d' % (word, separator, 1)

    if __name__ == "__main__":
        main()

Dedique uns momentos para ler o código e entender o que ele faz.

###Reducer.py

Crie um novo arquivo chamado **reducer.py** e use o seguinte código como o conteúdo:

    #!/usr/bin/env python

    # import modules
    from itertools import groupby
    from operator import itemgetter
    import sys

    # 'file' in this case is STDIN
    def read_mapper_output(file, separator='\t'):
        # Go through each line
        for line in file:
            # Strip out the separator character
            yield line.rstrip().split(separator, 1)

    def main(separator='\t'):
        # Read the data using read_mapper_output
        data = read_mapper_output(sys.stdin, separator=separator)
        # Group words and counts into 'group'
        #   Since MapReduce is a distributed process, each word
        #   may have multiple counts. 'group' will have all counts
        #   which can be retrieved using the word as the key.
        for current_word, group in groupby(data, itemgetter(0)):
            try:
                # For each word, pull the count(s) for the word
                #   from 'group' and create a total count
                total_count = sum(int(count) for current_word, count in group)
                # Write to stdout
                print "%s%s%d" % (current_word, separator, total_count)
            except ValueError:
                # Count was not a number, so do nothing
                pass

    if __name__ == "__main__":
        main()

##Carregar os arquivos

Ambos **mapper.py** e **reducer.py** devem estar no nó principal do cluster antes que o cluster possa executá-los. A maneira mais fácil de carregá-los é usando **scp** (**pscp** se você estiver usando um cliente Windows).

No cliente, no mesmo diretório do **mapper.py** e **reducer.py**, use o comando a seguir. Substitua **username** com um usuário SSH e **clustername** com o nome do cluster.

	scp mapper.py reducer.py username@clustername-ssh.azurehdinsight.net:

Isso copiará os arquivos do sistema local para o nó principal.

> [AZURE.NOTE] Se você usou uma senha para proteger sua conta SSH, você será solicitado pela senha. Se você usou uma chave SSH, talvez precise usar o parâmetro `-i` e o caminho para a chave privada, por exemplo `scp -i /path/to/private/key mapper.py reducer.py username@clustername-ssh.azurehdinsight.net:`.

##Executar MapReduce

1. Conecte-se ao cluster usando o SSH:

		ssh username@clustername-ssh.azurehdinsight.net

	> [AZURE.NOTE] Se você usou uma senha para proteger sua conta SSH, você será solicitado pela senha. Se você usou uma chave SSH, talvez precise usar o parâmetro `-i` e o caminho para a chave privada, por exemplo `ssh -i /path/to/private/key username@clustername-ssh.azurehdinsight.net`.

2. (Opcional) Se você usou um editor de texto que usa CRLF como fim de linha ao criar os arquivos mapper.py e reducer.py, ou não souber qual fim de linha seu editor usa, use os seguintes comandos para converter as ocorrências de CRLF em mapper.py e reducer.py para LF.

        perl -pi -e 's/\r\n/\n/g' mappery.py
        perl -pi -e 's/\r\n/\n/g' reducer.py

2. Use o seguinte comando para iniciar o trabalho MapReduce.

		yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-streaming.jar -files mapper.py,reducer.py -mapper mapper.py -reducer reducer.py -input wasb:///example/data/gutenberg/davinci.txt -output wasb:///example/wordcountout

	Esse comando tem as seguintes partes:

	* **hadoop-streaming.jar**: usado ao executar operações de transmissão do MapReduce. Faz interfaces do Hadoop com o código externo do MapReduce fornecido por você.

	* **-files**: informa o Hadoop que os arquivos especificados são necessários para este trabalho do MapReduce e devem ser copiados para todos os nós do trabalho.

	* **-mapper**: informa o Hadoop qual arquivo usar como mapeador.

	* **-reducer**: informa o Hadoop qual arquivo usar como redutor.

	* **-input**: o arquivo de entrada por meio do qual devemos contar palavras.

	* **-output**: o diretório no qual a saída será gravada.

		> [AZURE.NOTE] Esse diretório será criado pelo trabalho.

Você deve ver um monte de instruções **INFO** quando o trabalho é iniciado e finalmente vê as operações **map** e **reduce** exibidas como porcentagens.

	15/02/05 19:01:04 INFO mapreduce.Job:  map 0% reduce 0%
	15/02/05 19:01:16 INFO mapreduce.Job:  map 100% reduce 0%
	15/02/05 19:01:27 INFO mapreduce.Job:  map 100% reduce 100%

Por fim, você receberá informações de status sobre o trabalho quando ele for concluído.

##Exibir a saída

Quando o trabalho for concluído, use o seguinte comando para exibir a saída:

	hdfs dfs -text /example/wordcountout/part-00000

Isso deve exibir uma lista de palavras e quantas vezes a palavra ocorreu. A seguir está um exemplo dos dados de saída:

	wrenching       1
	wretched        6
	wriggling       1
	wrinkled,       1
	wrinkles        2
	wrinkling       2

##Próximas etapas

Agora que você aprendeu a usar a transmissão de trabalhos MapReduce com o HDInsight, use os links abaixo para explorar outras maneiras de trabalhar com o Azure HDInsight.

* [Usar o Hive com o HDInsight](hdinsight-use-hive.md)
* [Usar o Pig com o HDInsight](hdinsight-use-pig.md)
* [Usar trabalhos do MapReduce com o HDInsight](hdinsight-use-mapreduce.md)

<!---HONumber=AcomDC_0518_2016-->