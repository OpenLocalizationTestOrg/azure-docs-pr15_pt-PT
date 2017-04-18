<properties
    pageTitle="Analisar dados de Twitter com Apache Hive no HDInsight | Microsoft Azure"
    description="Saiba como utilizar Python para armazenar Tweets que contêm palavras-chave específicas, em seguida, utilizam ramo e Hadoop no HDInsight para transformar os dados não processados do TWitter uma tabela Hive pesquisável."
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
    ms.date="09/27/2016"
    ms.author="larryfr"/>

# <a name="analyze-twitter-data-using-hive-in-hdinsight"></a>Analisar dados de Twitter utilizando ramo no HDInsight

Neste documento, irá obter tweets utilizando um Twitter transmissão API e, em seguida, utilizar Apache Hive num cluster baseado em Linux HDInsight processo a JSON formatado dados. O resultado será uma lista de utilizadores do Twitter que enviou as maioria dos tweets que continha uma determinada palavra.

> [AZURE.NOTE] Enquanto peças individuais deste documento podem ser utilizadas com clusters de HDInsight baseados no Windows (Python por exemplo), muitos passos são baseados no utilizando um cluster de baseado em Linux HDInsight. Para obter passos específicos a um cluster baseados no Windows, consulte o artigo [Analisar Twitter dados utilizando ramo no HDInsight](hdinsight-analyze-twitter-data.md).

###<a name="prerequisites"></a>Pré-requisitos

Antes de começar este tutorial, tem de ter o seguinte procedimento:

- Um __cluster de baseado em Linux Azure HDInsight__. Para obter informações sobre como criar um cluster, consulte o artigo [Introdução ao baseado em Linux HDInsight](hdinsight-hadoop-linux-tutorial-get-started.md) para obter passos sobre como criar um cluster.

- Um __cliente SSH__. Para obter mais informações sobre como utilizar SSH com baseado em Linux HDInsight, consulte os artigos seguintes:

    * [Utilizar SSH com baseado em Linux Hadoop no HDInsight Linux, Unix ou OS X](hdinsight-hadoop-linux-use-ssh-unix.md)

    * [Utilizar SSH com baseado em Linux Hadoop no HDInsight a partir do Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

- __Python__ e [pip](https://pypi.python.org/pypi/pip)

##<a name="get-a-twitter-feed"></a>Obter um Twitter feed

Twitter permite-lhe obter os [dados para cada tweet](https://dev.twitter.com/docs/platform-objects/tweets) como um documento com notação de objeto JavaScript (JSON) através de um REST API. [OAuth](http://oauth.net) é necessário para autenticação à API. Também terá de criar uma _Aplicação Twitter_ que contém as definições utilizadas para aceder à API.

###<a name="create-a-twitter-application"></a>Criar uma aplicação do Twitter

1. A partir de um browser, inicie sessão no [https://apps.twitter.com/](https://apps.twitter.com/). Clique na ligação **Inscreva-se agora** se não tiver uma conta do Twitter.
2. Clique em **Criar nova aplicação**.
3. Introduza o **nome**, **Descrição**, **Web site**. Pode fazer cópia de um URL para o campo **Web site** . A tabela seguinte mostra alguns valores de exemplo para utilizar:

  	| Campo | Valor |
  	|:----- |:----- |
  	| Nome  | MyHDInsightApp |
  	| Descrição | MyHDInsightApp |
  	| Web site | http://www.myhdinsightapp.com |
    
4. Selecione **Sim, Concordo**e, em seguida, clique em **criar a aplicação Twitter**.
5. Clique no separador **permissões** . A permissão predefinido é **só de leitura**. Isto é suficiente para este tutorial.
6. Clique no separador **teclas e Tokens de acesso** .
7. Clique em **Criar meu token de acesso**.
8. Clique em **Testar OAuth** no canto superior direito da página.
9. Anote **chave do consumidor**, **secreta consumidor**, **token de acesso**e **secreta token de acesso**. Terá dos valores mais tarde.

>[AZURE.NOTE] Quando utiliza o comando de Laço no Windows, utilize aspas em vez de aspas simples para os valores de opção.

###<a name="download-tweets"></a>Transferir tweets

O seguinte código de Python irá transferir 10.000 tweets do Twitter e guardá-los num ficheiro denominado __tweets.txt__.

> [AZURE.NOTE] Os passos seguintes são executados no HDInsight cluster, uma vez que Python já está instalado.

1. Ligar ao cluster HDInsight utilizando SSH:

        ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
        
    Se utilizou uma palavra-passe para proteger a sua conta de utilizador SSH, vai ser-lhe para introduzi-lo. Se utilizou uma chave pública, poderá ter de utilizar o `-i` parâmetro para especificar a chave privada correspondente. Por exemplo, `ssh -i ~/.ssh/id_rsa USERNAME@CLUSTERNAME-ssh.azurehdinsight.net`.
        
    Para obter mais informações sobre como utilizar SSH com baseado em Linux HDInsight, consulte os artigos seguintes:
    
    * [Utilizar SSH com baseado em Linux Hadoop no HDInsight Linux, Unix ou OS X](hdinsight-hadoop-linux-use-ssh-unix.md)

    * [Utilizar SSH com baseado em Linux Hadoop no HDInsight a partir do Windows](hdinsight-hadoop-linux-use-ssh-windows)
    
2. Por predefinição, o utilitário __pip__ não está instalado no nó cabeça HDInsight. Utilize o seguinte procedimento para instalar e, em seguida, atualize este utilitário:

        sudo apt-get install python-pip
        sudo pip install --upgrade pip

3. O código para transferir tweets depende [Tweepy](http://www.tweepy.org/) e [Progressbar](https://pypi.python.org/pypi/progressbar/2.2). Para instalar estes, utilize o seguinte comando:

        sudo apt-get install python-dev libffi-dev libssl-dev
        sudo apt-get remove python-openssl
        sudo pip install tweepy progressbar pyOpenSSL requests[security]
        
    > [AZURE.NOTE] Os bits sobre como remover openssl python, instalar python-Dev Center, libffi-Dev Center, libssl-Dev Center, pyOpenSSL e pedidos [segurança] é evitar um aviso de InsecurePlatform quando se liga ao Twitter através de SSL a partir do Python.
    >
    > Tweepy v3.2.0 é utilizada para evitar [um erro](https://github.com/tweepy/tweepy/issues/576) que podem ocorrer quando está a processar tweets.

4. Utilize o seguinte comando para criar um novo ficheiro chamado __gettweets.py__:

        nano gettweets.py

5. Utilize o seguinte como o conteúdo do ficheiro __gettweets.py__ . Substituir as informações de marcador de posição para __consumidor\_secreta__, __consumidor\_chave__, __acesso /\_token__, e __acesso\_token\_secreta__ com as informações a partir da sua aplicação Twitter.

        #!/usr/bin/python

        from tweepy import Stream, OAuthHandler
        from tweepy.streaming import StreamListener
        from progressbar import ProgressBar, Percentage, Bar
        import json
        import sys

        #Twitter app information
        consumer_secret='Your consumer secret'
        consumer_key='Your consumer key'
        access_token='Your access token'
        access_token_secret='Your access token secret'

        #The number of tweets we want to get
        max_tweets=10000

        #Create the listener class that will receive and save tweets
        class listener(StreamListener):
            #On init, set the counter to zero and create a progress bar
            def __init__(self, api=None):
                self.num_tweets = 0
                self.pbar = ProgressBar(widgets=[Percentage(), Bar()], maxval=max_tweets).start()

            #When data is received, do this
            def on_data(self, data):
                #Append the tweet to the 'tweets.txt' file
                with open('tweets.txt', 'a') as tweet_file:
                    tweet_file.write(data)
                    #Increment the number of tweets
                    self.num_tweets += 1
                    #Check to see if we have hit max_tweets and exit if so
                    if self.num_tweets >= max_tweets:
                        self.pbar.finish()
                        sys.exit(0)
                    else:
                        #increment the progress bar
                        self.pbar.update(self.num_tweets)
                return True

            #Handle any errors that may occur
            def on_error(self, status):
                print status

        #Get the OAuth token
        auth = OAuthHandler(consumer_key, consumer_secret)
        auth.set_access_token(access_token, access_token_secret)
        #Use the listener class for stream processing
        twitterStream = Stream(auth, listener())
        #Filter for these topics
        twitterStream.filter(track=["azure","cloud","hdinsight"])

6. Utilize __Ctrl + X__e __Y__ para guardar o ficheiro.

7. Utilize o seguinte comando para executar o ficheiro e transferir tweets:

        python gettweets.py

    Um indicador de progresso deve aparecer e contar até 100%, tal como os tweets são transferidas e guardados no ficheiro.

    > [AZURE.NOTE] Se estiver a demorar muito tempo para a barra de progresso avançar, deverá alterar o filtro para controlar a tendência tópicos; Quando existem muitas tweets sobre o tópico que estiver a filtrar, pode aceder rapidamente as 10000 tweets conforme necessário.

###<a name="upload-the-data"></a>Carregar os dados

Para carregar os dados para WASB (o sistema de ficheiros distribuído utilizado pelo HDInsight), utilize os comandos seguintes:

    hdfs dfs -mkdir -p /tutorials/twitter/data
    hdfs dfs -put tweets.txt /tutorials/twitter/data/tweets.txt

Isto armazena os dados numa localização que podem aceder a todos os nós no cluster.

##<a name="run-the-hiveql-job"></a>Executar a tarefa de HiveQL


1. Utilize o seguinte comando para criar um ficheiro que contenha declarações de HiveQL:

        nano twitter.hql
    
    Utilize o seguinte como o conteúdo do ficheiro:

        set hive.exec.dynamic.partition = true;
        set hive.exec.dynamic.partition.mode = nonstrict;
        -- Drop table, if it exists
        DROP TABLE tweets_raw;
        -- Create it, pointing toward the tweets logged from Twitter
        CREATE EXTERNAL TABLE tweets_raw (
            json_response STRING
        )
        STORED AS TEXTFILE LOCATION '/tutorials/twitter/data';
        -- Drop and recreate the destination table
        DROP TABLE tweets;
        CREATE TABLE tweets
        (
            id BIGINT,
            created_at STRING,
            created_at_date STRING,
            created_at_year STRING,
            created_at_month STRING,
            created_at_day STRING,
            created_at_time STRING,
            in_reply_to_user_id_str STRING,
            text STRING,
            contributors STRING,
            retweeted STRING,
            truncated STRING,
            coordinates STRING,
            source STRING,
            retweet_count INT,
            url STRING,
            hashtags array<STRING>,
            user_mentions array<STRING>,
            first_hashtag STRING,
            first_user_mention STRING,
            screen_name STRING,
            name STRING,
            followers_count INT,
            listed_count INT,
            friends_count INT,
            lang STRING,
            user_location STRING,
            time_zone STRING,
            profile_image_url STRING,
            json_response STRING
        );
        -- Select tweets from the imported data, parse the JSON,
        -- and insert into the tweets table
        FROM tweets_raw
        INSERT OVERWRITE TABLE tweets
        SELECT
            cast(get_json_object(json_response, '$.id_str') as BIGINT),
            get_json_object(json_response, '$.created_at'),
            concat(substr (get_json_object(json_response, '$.created_at'),1,10),' ',
            substr (get_json_object(json_response, '$.created_at'),27,4)),
            substr (get_json_object(json_response, '$.created_at'),27,4),
            case substr (get_json_object(json_response, '$.created_at'),5,3)
                when "Jan" then "01"
                when "Feb" then "02"
                when "Mar" then "03"
                when "Apr" then "04"
                when "May" then "05"
                when "Jun" then "06"
                when "Jul" then "07"
                when "Aug" then "08"
                when "Sep" then "09"
                when "Oct" then "10"
                when "Nov" then "11"
                when "Dec" then "12" end,
            substr (get_json_object(json_response, '$.created_at'),9,2),
            substr (get_json_object(json_response, '$.created_at'),12,8),
            get_json_object(json_response, '$.in_reply_to_user_id_str'),
            get_json_object(json_response, '$.text'),
            get_json_object(json_response, '$.contributors'),
            get_json_object(json_response, '$.retweeted'),
            get_json_object(json_response, '$.truncated'),
            get_json_object(json_response, '$.coordinates'),
            get_json_object(json_response, '$.source'),
            cast (get_json_object(json_response, '$.retweet_count') as INT),
            get_json_object(json_response, '$.entities.display_url'),
            array(
                trim(lower(get_json_object(json_response, '$.entities.hashtags[0].text'))),
                trim(lower(get_json_object(json_response, '$.entities.hashtags[1].text'))),
                trim(lower(get_json_object(json_response, '$.entities.hashtags[2].text'))),
                trim(lower(get_json_object(json_response, '$.entities.hashtags[3].text'))),
                trim(lower(get_json_object(json_response, '$.entities.hashtags[4].text')))),
            array(
                trim(lower(get_json_object(json_response, '$.entities.user_mentions[0].screen_name'))),
                trim(lower(get_json_object(json_response, '$.entities.user_mentions[1].screen_name'))),
                trim(lower(get_json_object(json_response, '$.entities.user_mentions[2].screen_name'))),
                trim(lower(get_json_object(json_response, '$.entities.user_mentions[3].screen_name'))),
                trim(lower(get_json_object(json_response, '$.entities.user_mentions[4].screen_name')))),
            trim(lower(get_json_object(json_response, '$.entities.hashtags[0].text'))),
            trim(lower(get_json_object(json_response, '$.entities.user_mentions[0].screen_name'))),
            get_json_object(json_response, '$.user.screen_name'),
            get_json_object(json_response, '$.user.name'),
            cast (get_json_object(json_response, '$.user.followers_count') as INT),
            cast (get_json_object(json_response, '$.user.listed_count') as INT),
            cast (get_json_object(json_response, '$.user.friends_count') as INT),
            get_json_object(json_response, '$.user.lang'),
            get_json_object(json_response, '$.user.location'),
            get_json_object(json_response, '$.user.time_zone'),
            get_json_object(json_response, '$.user.profile_image_url'),
            json_response
        WHERE (length(json_response) > 500);
        
        
3. Prima __Ctrl + X__e, em seguida, prima __Y__ para guardar o ficheiro.

4. Utilize o seguinte comando para executar HiveQL contido no ficheiro:

        beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -n admin -i twitter.hql
        
    Isto irá carregar a shell de ramo, execute o HiveQL no ficheiro __twitter.hql__ e finalmente, devolver uma `jdbc:hive2//localhost:10001/>` linha de comandos.
    
5. A partir da linha de comandos beeline, utilize o seguinte para confirmar que pode selecionar dados a partir da tabela __tweets__ criada por HiveQL no ficheiro __twitter.hql__ :
        
        SELECT name, screen_name, count(1) as cc
            FROM tweets
            WHERE text like "%Azure%"
            GROUP BY name,screen_name
            ORDER BY cc DESC LIMIT 10;

    Isto irá devolver um máximo de 10 tweets que contenham a palavra __Azure__ no texto da mensagem.

##<a name="next-steps"></a>Próximos passos

Neste tutorial podemos ter visto como transformar um conjunto de dados JSON não estruturado uma tabela Hive estruturada para consultar, explorar e analisar os dados a partir do Twitter utilizando o HDInsight no Azure. Para obter mais informações, consulte o artigo:

- [Introdução ao HDInsight](hdinsight-hadoop-linux-tutorial-get-started.md)
- [Analisar os dados de atrasos em voos utilizando HDInsight](hdinsight-analyze-flight-delay-data-linux.md)

[curl]: http://curl.haxx.se
[curl-download]: http://curl.haxx.se/download.html

[apache-hive-tutorial]: https://cwiki.apache.org/confluence/display/Hive/Tutorial

[twitter-streaming-api]: https://dev.twitter.com/docs/streaming-apis
[twitter-statuses-filter]: https://dev.twitter.com/docs/api/1.1/post/statuses/filter
