<properties
    pageTitle="Gerar recomendações utilizando Mahout e baseado em Linux HDInsight | Microsoft Azure"
    description="Saiba como utilizar a biblioteca de aprendizagem automática de Apache Mahout para gerar recomendações de filme com baseado em Linux HDInsight (Hadoop)."
    services="hdinsight"
    documentationCenter=""
    authors="Blackmist"
    manager="jhubbard"
    editor="cgronlun"
    tags="azure-portal"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/30/2016"
    ms.author="larryfr"/>

#<a name="generate-movie-recommendations-by-using-apache-mahout-with-linux-based-hadoop-in-hdinsight"></a>Gerar recomendações de filme utilizando Apache Mahout com baseado em Linux Hadoop no HDInsight

[AZURE.INCLUDE [mahout-selector](../../includes/hdinsight-selector-mahout.md)]

Saiba como utilizar a biblioteca com o Azure HDInsight de aprendizagem automática de [Apache Mahout](http://mahout.apache.org) para gerar recomendações de filme.

Mahout é uma [máquina de formação] [ ml] biblioteca para Apache Hadoop. Mahout contém algoritmos para processamento de dados, como filtrar, classificação e clusters. Neste artigo, irá utilizar um motor de recomendação para gerar recomendações de filme que sejam baseiam os seus amigos viu de filmes.

> [AZURE.NOTE] Os passos neste documento requerem uma Hadoop baseado em Linux num cluster de HDInsight. Para obter informações sobre como utilizar Mahout com um cluster de baseados no Windows, consulte o artigo [recomendações de filme gerar utilizando Apache Mahout com baseados no Windows Hadoop no HDInsight](hdinsight-mahout.md)

##<a name="prerequisites"></a>Pré-requisitos

* Um Hadoop baseado em Linux num cluster de HDInsight. Para obter informações sobre a criação de um, consulte o artigo [começar a utilizar com base em Linux Hadoop no HDInsight][getstarted]

##<a name="mahout-versioning"></a>Controlo de versões mahout

Para mais informações sobre a versão do Mahout incluído com o seu cluster HDInsight, consulte o artigo [HDInsight versões e Hadoop componentes](hdinsight-component-versioning.md).

> [AZURE.WARNING] Enquanto é possível carregar uma versão diferente do Mahout para HDInsight cluster, apenas os componentes fornecidos com o cluster HDInsight são totalmente suportados e Microsoft Support irá ajudar a identificar e resolver problemas relacionados com a estes componentes.
>
> Componentes personalizados recebem faremos suporte para o ajudar a resolver ainda mais o problema. Isto poderá resultar em resolver o problema ou pedir-lhe para participar canais disponíveis para as tecnologias de abrir origem onde se encontram conhecimentos abrangente para essa tecnologia. Por exemplo, existem muitos sites de Comunidade que podem ser utilizados, tal como: [Fórum MSDN para HDInsight](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=hdinsight), [http://stackoverflow.com](http://stackoverflow.com). Também projetos Apache tem sites de projeto no [http://apache.org](http://apache.org), por exemplo: [Hadoop](http://hadoop.apache.org/), [motores](http://spark.apache.org/).

##<a name="recommendations"></a>Recomendações Noções sobre

Uma das funções fornecido pelo Mahout é um motor de recomendação. Este motor aceita dados no formato de `userID`, `itemId`, e `prefValue` (a preferência de utilizadores do item). Mahout, em seguida, pode efetuar análise de cocriação ocorrência para determinar: _os utilizadores que tenham uma preferência de um item também têm uma preferência para estes outros itens_. Mahout determina, em seguida, os utilizadores com gosto item preferências, que podem ser utilizados para apresentar recomendações.

Segue-se um exemplo extremamente simple que utiliza filmes:

* __Cocriação ocorrência__: João, o Alice e o Rui gostou _Estrela os conflitos_, _O império greves novamente_e _devolver da Jedi_. Mahout determina que os utilizadores que gosta qualquer uma das seguintes filmes também como as outras duas.

* __Cocriação ocorrência__: Bruno e Alice gostou também _A ameaça de fantasma_, _ataque no que diz_e _Revenge do Sith_. Mahout determina que os utilizadores a quem gostou de três filmes anterior também como estes três.

* __Recomendação semelhança__: João porque gostou os três primeiros filmes, Mahout analisa filmes que outras pessoas com as preferências semelhantes gostou, mas João não viu (gosto/nominal). Neste caso, Mahout recomenda _A ameaça de fantasma_, _ataque no que diz_e _Revenge do Sith_.

###<a name="understanding-the-data"></a>Noções sobre os dados

Convenientemente, [GroupLens investigação] [ movielens] fornece dados de classificação para filmes num formato compatível com Mahout. Estes dados estão disponíveis no predefinição de armazenamento seu cluster na `/HdiSamples/HdiSamples/MahoutMovieData`.

Existem dois ficheiros, `moviedb.txt` (informações sobre os filmes) e `user-ratings.txt`. O utilizador ratings.txt ficheiro é utilizado durante a análise, enquanto moviedb.txt é utilizado para fornecer informações de texto simples ao apresentar os resultados da análise.

Os dados contidos no ratings.txt do utilizador tem uma estrutura de `userID`, `movieID`, `userRating`, e `timestamp`, que lhe indica-nos como altamente cada utilizador classificou um filme. Eis um exemplo de dados:


    196 242 3   881250949
    186 302 3   891717742
    22  377 1   878887116
    244 51  2   880606923
    166 346 1   886397596

##<a name="run-the-analysis"></a>Executar a análise

Utilize o seguinte comando para executar a tarefa de recomendação:

    mahout recommenditembased -s SIMILARITY_COOCCURRENCE -i /HdiSamples/HdiSamples/MahoutMovieData/user-ratings.txt -o /example/data/mahoutout --tempDir /temp/mahouttemp

> [AZURE.NOTE] A tarefa poderá demorar vários minutos para concluir e pode executar várias tarefas MapReduce.

##<a name="view-the-output"></a>Ver os resultados

1. Assim que a tarefa estiver concluída, utilize o seguinte comando para ver os resultados gerado:

        hdfs dfs -text /example/data/mahoutout/part-r-00000

    O resultado será apresentada da seguinte forma:

        1   [234:5.0,347:5.0,237:5.0,47:5.0,282:5.0,275:5.0,88:5.0,515:5.0,514:5.0,121:5.0]
        2   [282:5.0,210:5.0,237:5.0,234:5.0,347:5.0,121:5.0,258:5.0,515:5.0,462:5.0,79:5.0]
        3   [284:5.0,285:4.828125,508:4.7543354,845:4.75,319:4.705128,124:4.7045455,150:4.6938777,311:4.6769233,248:4.65625,272:4.649266]
        4   [690:5.0,12:5.0,234:5.0,275:5.0,121:5.0,255:5.0,237:5.0,895:5.0,282:5.0,117:5.0]

    A primeira coluna é o `userID`. Os valores contidos em ' [' e ']' são `movieId`:`recommendationScore`.

2. Pode utilizar o resultado, juntamente com moviedb.txt, para apresentar mais informações de utilizador amigável. Em primeiro lugar, é necessário copiar os ficheiros localmente utilizando os seguintes comandos:

        hdfs dfs -get /example/data/mahoutout/part-r-00000 recommendations.txt
        hdfs dfs -get /HdiSamples/HdiSamples/MahoutMovieData/* .

    Isto irá copiar os dados de saída para um ficheiro denominado **recommendations.txt** no diretório atual, juntamente com os ficheiros de dados de filme.

3. Utilize o seguinte comando para criar um novo script de Python irá procurar nomes de filme para os dados na saída recomendações:

        nano show_recommendations.py

    Quando o editor é aberto, utilize o seguinte como o conteúdo do ficheiro:

        #!/usr/bin/env python

        import sys

        if len(sys.argv) != 5:
                print "Arguments: userId userDataFilename movieFilename recommendationFilename"
                sys.exit(1)

        userId, userDataFilename, movieFilename, recommendationFilename = sys.argv[1:]

        print "Reading Movies Descriptions"
        movieFile = open(movieFilename)
        movieById = {}
        for line in movieFile:
                tokens = line.split("|")
                movieById[tokens[0]] = tokens[1:]
        movieFile.close()

        print "Reading Rated Movies"
        userDataFile = open(userDataFilename)
        ratedMovieIds = []
        for line in userDataFile:
                tokens = line.split("\t")
                if tokens[0] == userId:
                        ratedMovieIds.append((tokens[1],tokens[2]))
        userDataFile.close()

        print "Reading Recommendations"
        recommendationFile = open(recommendationFilename)
        recommendations = []
        for line in recommendationFile:
                tokens = line.split("\t")
                if tokens[0] == userId:
                        movieIdAndScores = tokens[1].strip("[]\n").split(",")
                        recommendations = [ movieIdAndScore.split(":") for movieIdAndScore in movieIdAndScores ]
                        break
        recommendationFile.close()

        print "Rated Movies"
        print "------------------------"
        for movieId, rating in ratedMovieIds:
                print "%s, rating=%s" % (movieById[movieId][0], rating)
        print "------------------------"

        print "Recommended Movies"
        print "------------------------"
        for movieId, score in recommendations:
                print "%s, score=%s" % (movieById[movieId][0], score)
        print "------------------------"

    Prima **CTRL + X**, **Y**e finalmente **Enter** para guardar os dados.

3. Utilize o seguinte comando para tornar o ficheiro executável:

        chmod +x show_recommendations.py

4. Execute o script Python. O seguinte procedimento pressupõe que está no diretório onde foram transferidos todos os ficheiros:

        ./show_recommendations.py 4 user-ratings.txt moviedb.txt recommendations.txt

    Isto irá observe as recomendações geradas para 4 de ID de utilizador.

    * O ficheiro de **utilizador ratings.txt** é utilizado para obter filmes que o utilizador tem classificados
    * O ficheiro **moviedb.txt** é utilizado para obter os nomes dos filmes
    * O **recommendations.txt** é utilizada para obter as recomendações de filme para a este utilizador

    O resultado deste comando será semelhante ao seguinte:

        Reading Movies Descriptions
        Reading Rated Movies
        Reading Recommendations
        Rated Movies
        ------------------------
        Mimic (1997), rating=3
        Ulee's Gold (1997), rating=5
        Incognito (1997), rating=5
        One Flew Over the Cuckoo's Nest (1975), rating=4
        Event Horizon (1997), rating=4
        Client, The (1994), rating=3
        Liar Liar (1997), rating=5
        Scream (1996), rating=4
        Star Wars (1977), rating=5
        Wedding Singer, The (1998), rating=5
        Starship Troopers (1997), rating=4
        Air Force One (1997), rating=5
        Conspiracy Theory (1997), rating=3
        Contact (1997), rating=5
        Indiana Jones and the Last Crusade (1989), rating=3
        Desperate Measures (1998), rating=5
        Seven (Se7en) (1995), rating=4
        Cop Land (1997), rating=5
        Lost Highway (1997), rating=5
        Assignment, The (1997), rating=5
        Blues Brothers 2000 (1998), rating=5
        Spawn (1997), rating=2
        Wonderland (1997), rating=5
        In & Out (1997), rating=5
        ------------------------
        Recommended Movies
        ------------------------
        Seven Years in Tibet (1997), score=5.0
        Indiana Jones and the Last Crusade (1989), score=5.0
        Jaws (1975), score=5.0
        Sense and Sensibility (1995), score=5.0
        Independence Day (ID4) (1996), score=5.0
        My Best Friend's Wedding (1997), score=5.0
        Jerry Maguire (1996), score=5.0
        Scream 2 (1997), score=5.0
        Time to Kill, A (1996), score=5.0
        Rock, The (1996), score=5.0
        ------------------------

##<a name="delete-temporary-data"></a>Eliminar dados temporários

Tarefas de mahout não remove dados temporário que são criados durante o processamento da tarefa. O `--tempDir` parâmetro for especificado na tarefa de exemplo para identificar os ficheiros temporários para um caminho específico para fácil eliminação. Para remover os ficheiros temporários, utilize o seguinte comando:

    hdfs dfs -rm -f -r /temp/mahouttemp

> [AZURE.WARNING] Se pretender executar o comando novamente, também tem de eliminar o directório de saída. Utilize o seguinte procedimento para eliminar este diretório:
>
> ```hdfs dfs -rm -f -r /example/data/mahoutout```

## <a name="next-steps"></a>Próximos passos

Agora que aprendeu como utilizar Mahout, descobri outras formas de trabalhar com dados no HDInsight:

* [Ramo de registo com HDInsight](hdinsight-use-hive.md)
* [Porco com HDInsight](hdinsight-use-pig.md)
* [MapReduce com HDInsight](hdinsight-use-mapreduce.md)

[build]: http://mahout.apache.org/developers/buildingmahout.html
[movielens]: http://grouplens.org/datasets/movielens/
[100k]: http://files.grouplens.org/datasets/movielens/ml-100k.zip
[getstarted]: hdinsight-hadoop-linux-tutorial-get-started.md
[upload]: hdinsight-upload-data.md
[ml]: http://en.wikipedia.org/wiki/Machine_learning
[forest]: http://en.wikipedia.org/wiki/Random_forest
[management]: https://manage.windowsazure.com/
[enableremote]: ./media/hdinsight-mahout/enableremote.png
[connect]: ./media/hdinsight-mahout/connect.png
[hadoopcli]: ./media/hdinsight-mahout/hadoopcli.png
[tools]: https://github.com/Blackmist/hdinsight-tools
 
