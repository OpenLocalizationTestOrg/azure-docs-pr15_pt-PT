<properties
   pageTitle="Desenvolver Python MapReduce tarefas com HDInsight | Microsoft Azure"
   description="Saiba como criar e executar tarefas Python MapReduce em clusters baseado em Linux HDInsight."
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="jhubbard"
   editor="cgronlun"
    tags="azure-portal"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="10/11/2016"
   ms.author="larryfr"/>

#<a name="develop-python-streaming-programs-for-hdinsight"></a>Desenvolver Python transmissão de programas para HDInsight

Hadoop fornece uma transmissão API para MapReduce permite-lhe escrever mapa e reduzir funções em idiomas que não seja Java. Este artigo vai aprender a utilizar Python para executar operações de MapReduce.

> [AZURE.NOTE] Enquanto o código de Python neste documento pode ser utilizado com um cluster de HDInsight baseados no Windows, os passos neste documento são específicos a clusters baseado em Linux.

Este artigo é baseado informações e exemplos publicados por Michael Noll ao [escrever um programa de MapReduce Hadoop no Python](http://www.michael-noll.com/tutorials/writing-an-hadoop-mapreduce-program-in-python/).

##<a name="prerequisites"></a>Pré-requisitos

Para concluir os passos neste artigo, terá o seguinte procedimento:

* Um Hadoop baseado em Linux num cluster de HDInsight

* Um editor de texto

    > [AZURE.IMPORTANT] O editor de texto tem de utilizar LF como o fim da linha. Se utiliza o CRLF, isto irá causar erros ao executar a tarefa de MapReduce em clusters baseado em Linux HDInsight. Se não tiver a certeza, utilize o passo opcional na secção [Executar MapReduce](#run-mapreduce) para converter qualquer CRLF LF.

* Para clientes do Windows, betumes e PSCP. Estes utilitários estão disponíveis a partir da <a href="http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html" target="_blank">Página de transferência do betumes</a>.

##<a name="word-count"></a>Contagem de palavras

Neste exemplo, que irá implementar uma contagem de básica utilizando um mapeamento de pontos e redutor. O mapeamento de pontos divide frases em palavras individuais e o redutor agrega as palavras e conta produzir os resultados.

O seguinte fluxograma ilustra o que acontece durante o mapa e reduzir fases.

![Ilustração de mapa de reduzir](./media/hdinsight-hadoop-streaming-python/HDI.WordCountDiagram.png)

##<a name="why-python"></a>Por que motivo Python?

Python é uma linguagem de programação uso geral de alto nível que permite-lhe os conceitos express menos linhas de código que muitos outros idiomas. Tem recentemente tornou populares com cientistas de dados como um idioma protótipos porque a sua interpretada natureza, escrever dinâmicos e elegante sintaxe torná-lo adequado para o desenvolvimento de aplicações rápida.

Python está instalado em todos os clusters HDInsight.

##<a name="streaming-mapreduce"></a>Transmissão MapReduce

Hadoop permite-lhe especificar um ficheiro que contém o mapa e reduzir lógica que é utilizada por uma tarefa. Os requisitos específicos para o mapa e reduzir lógica são:

* **Introdução**: O mapa e reduzir componentes tem lerem dados de entrada STDIN.

* **Resultado**: O mapa e reduzir componentes tem de escrever dados de saída STDOUT.

* **Formato de dados**: os dados consumidas e produzidos tem de ser um par de valor/chave, separado por um caráter de tabulação.

Python facilmente pode processar estes requisitos ao utilizar o módulo **sobre** lerem STDIN e utilizar o **Imprimir** para imprimir para STDOUT. A tarefa restante simplesmente está a formatar os dados com um separador (`\t`) carácter entre a chave e valor.

##<a name="create-the-mapper-and-reducer"></a>Criar o mapeamento de pontos e redutor

O mapeamento de pontos e redutor são ficheiros de texto, neste caso **mapper.py** e **reducer.py** (para torná-lo a limpar o que faz o quê). Pode criar estes utilizando o editor da sua escolha.

###<a name="mapperpy"></a>Mapper.PY

Criar um novo ficheiro chamado **mapper.py** e utilize o seguinte código como o conteúdo:

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

Observar lidos através do código para que possa compreender o que faz.

###<a name="reducerpy"></a>Reducer.PY

Criar um novo ficheiro chamado **reducer.py** e utilize o seguinte código como o conteúdo:

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

##<a name="upload-the-files"></a>Carregar os ficheiros

**Mapper.py** e **reducer.py** tem de ser no cabeça nó do cluster antes de podemos pode executá-los. A forma mais fácil para os carregar é utilizar **scp** (**pscp** se estiver a utilizar um cliente do Windows).

A partir do cliente, no mesmo directório como **mapper.py** e **reducer.py**, utilize o seguinte comando. Substitua o **nome de utilizador** com um utilizador SSH e **nome de cluster** com o nome do seu cluster.

    scp mapper.py reducer.py username@clustername-ssh.azurehdinsight.net:

Isto copia os ficheiros do sistema local para o nó cabeça.

> [AZURE.NOTE] Se utilizou uma palavra-passe para proteger a sua conta SSH, vai ser-lhe a palavra-passe. Caso tenha utilizado uma chave SSH, poderá ter de utilizar o `-i` parâmetro e o caminho para a chave privada, por exemplo, `scp -i /path/to/private/key mapper.py reducer.py username@clustername-ssh.azurehdinsight.net:`.

##<a name="run-mapreduce"></a>Executar MapReduce

1. Ligar ao cluster utilizando SSH:

        ssh username@clustername-ssh.azurehdinsight.net

    > [AZURE.NOTE] Se utilizou uma palavra-passe para proteger a sua conta SSH, vai ser-lhe a palavra-passe. Caso tenha utilizado uma chave SSH, poderá ter de utilizar o `-i` parâmetro e o caminho para a chave privada, por exemplo, `ssh -i /path/to/private/key username@clustername-ssh.azurehdinsight.net`.

2. (Opcional) Se tiver utilizado num editor de texto que utiliza CRLF como a linha final quando criar os ficheiros mapper.py e reducer.py, ou não sabe o que o editor de linha-terminação utiliza, utilize o seguinte comandos para converter ocorrências de CRLF no mapper.py e reducer.py LF.

        perl -pi -e 's/\r\n/\n/g' mappery.py
        perl -pi -e 's/\r\n/\n/g' reducer.py

2. Utilize o seguinte comando para iniciar a tarefa de MapReduce.

        yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-streaming.jar -files mapper.py,reducer.py -mapper mapper.py -reducer reducer.py -input wasbs:///example/data/gutenberg/davinci.txt -output wasbs:///example/wordcountout

    Este comando tem as seguintes partes:

    * **hadoop streaming.jar**: utilizado quando as operações de transmissão MapReduce. Hadoop-interfaces com o código MapReduce externo fornecidos.

    * **-ficheiros**: Hadoop indica que os ficheiros especificados são necessários para esta tarefa MapReduce e deve ser copiados para todos os nós de trabalho.

    * **-mapeamento de pontos**: indica qual o ficheiro para utilizar como o mapeamento de pontos de Hadoop.

    * **-redutor**: indica Hadoop qual o ficheiro para utilizar como a redutor.

    * **-entrada**: O ficheiro de entrada que recomendamos deve contar o número de palavras a partir de.

    * **-saída**: O directório que o resultado será escrito.

        > [AZURE.NOTE] Este diretório será criado pela tarefa.

Deverá ver um conjunto de declarações de **informações** como os inícios de tarefa e, por fim, consulte a operação de **mapa** e **reduzir** apresentada como percentagens.

    15/02/05 19:01:04 INFO mapreduce.Job:  map 0% reduce 0%
    15/02/05 19:01:16 INFO mapreduce.Job:  map 100% reduce 0%
    15/02/05 19:01:27 INFO mapreduce.Job:  map 100% reduce 100%

Receberá informações sobre a tarefa de estado quando que é concluído.

##<a name="view-the-output"></a>Ver os resultados

Quando a tarefa estiver concluída, utilize o seguinte comando para ver os resultados:

    hdfs dfs -text /example/wordcountout/part-00000

Isto deve apresentar uma lista de palavras e quantas vezes a palavra ocorreu. Um exemplo dos dados de saída é o seguinte:

    wrenching       1
    wretched        6
    wriggling       1
    wrinkled,       1
    wrinkles        2
    wrinkling       2

##<a name="next-steps"></a>Próximos passos

Agora que aprendeu como utilizar transmissão MapRedcue tarefas com HDInsight, utilize as ligações seguintes para explorar outras formas de trabalhar com Azure HDInsight.

* [Utilizar ramo com HDInsight](hdinsight-use-hive.md)
* [Utilizar porco com HDInsight](hdinsight-use-pig.md)
* [Utilizar tarefas de MapReduce com HDInsight](hdinsight-use-mapreduce.md)
