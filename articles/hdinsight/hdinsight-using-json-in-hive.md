<properties
   pageTitle="Analisar e processo JSON documentos com ramo no HDInsight | Microsoft Azure"
   description="Saiba como utilizar documentos JSON e analisá-las utilizando ramo no HDInsight."
   services="hdinsight"
   documentationCenter=""
   authors="rashimg"
   manager="mwinkle"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="06/22/2015"
   ms.author="rashimg"/>


# <a name="process-and-analyze-json-documents-using-hive-in-hdinsight"></a>Processar e analisar os documentos JSON utilizando ramo no HDInsight

Saiba como processar e analisar os ficheiros JSON através do ramo no HDInsight. O documento JSON seguinte será utilizado no tutorial

    {
        "StudentId": "trgfg-5454-fdfdg-4346",
        "Grade": 7,
        "StudentDetails": [
            {
                "FirstName": "Peggy",
                "LastName": "Williams",
                "YearJoined": 2012
            }
        ],
        "StudentClassCollection": [
            {
                "ClassId": "89084343",
                "ClassParticipation": "Satisfied",
                "ClassParticipationRank": "High",
                "Score": 93,
                "PerformedActivity": false
            },
            {
                "ClassId": "78547522",
                "ClassParticipation": "NotSatisfied",
                "ClassParticipationRank": "None",
                "Score": 74,
                "PerformedActivity": false
            },
            {
                "ClassId": "78675563",
                "ClassParticipation": "Satisfied",
                "ClassParticipationRank": "Low",
                "Score": 83,
                "PerformedActivity": true
            }
        ]
    }

O ficheiro pode ser encontrado em wasbs://processjson@hditutorialdata.blob.core.windows.net/. Para mais informações sobre como utilizar o armazenamento de Blobs do Azure com HDInsight, consulte o artigo [utilizar compatível com o HDFS Azure Blob armazenamento com Hadoop no HDInsight](hdinsight-hadoop-use-blob-storage.md). Pode copiar o ficheiro para o contentor predefinido do seu cluster se pretender.

Neste tutorial, irá utilizar consola do ramo.  Para obter instruções de abertura de consola do ramo, consulte o artigo [Utilizar Hive com Hadoop no HDInsight com o ambiente de trabalho remoto](hdinsight-hadoop-use-hive-remote-desktop.md).

##<a name="flatten-json-documents"></a>Aplanar documentos JSON

Os métodos listados na secção seguinte requerem o documento JSON numa única linha. Por isso, tem aplanar documento JSON para uma cadeia. Se o seu documento JSON já é simplificado, pode ignorar este passo e ir diretamente para a secção seguinte dados JSON analisar.

    DROP TABLE IF EXISTS StudentsRaw;
    CREATE EXTERNAL TABLE StudentsRaw (textcol string) STORED AS TEXTFILE LOCATION "wasb://processjson@hditutorialdata.blob.core.windows.net/";

    DROP TABLE IF EXISTS StudentsOneLine;
    CREATE EXTERNAL TABLE StudentsOneLine
    (
      json_body string
    )
    STORED AS TEXTFILE LOCATION '/json/students';

    INSERT OVERWRITE TABLE StudentsOneLine
    SELECT CONCAT_WS(' ',COLLECT_LIST(textcol)) AS singlelineJSON
          FROM (SELECT INPUT__FILE__NAME,BLOCK__OFFSET__INSIDE__FILE, textcol FROM StudentsRaw DISTRIBUTE BY INPUT__FILE__NAME SORT BY BLOCK__OFFSET__INSIDE__FILE) x
          GROUP BY INPUT__FILE__NAME;

    SELECT * FROM StudentsOneLine

O ficheiro JSON observou está localizado em **wasbs://processjson@hditutorialdata.blob.core.windows.net/**. A tabela *StudentsRaw* Hive aponta para o documento JSON anulado plana observou.

A tabela *StudentsOneLine* Hive irá guardar os dados no sistema de ficheiros predefinido HDInsight em caminho */json/estudantes /* .

A instrução INSERT preencher a tabela de StudentOneLine com os dados JSON planos.

A instrução SELECT só deve devolver 1 linha.

Eis o resultado da instrução SELECT:

![Atenuamento do documento JSON.][image-hdi-hivejson-flatten]

##<a name="analyze-json-documents-in-hive"></a>Analisar documentos JSON no ramo

Ramo fornece mecanismos diferentes três para executar consultas de em documentos JSON:

- utilizar a\_JSON\_OBJETO UDF (função definida pelo utilizador)
- utilizar o UDF JSON_TUPLE
- utilizar SerDe personalizado
- é o proprietário UDF utilizando Python ou outros idiomas de escrita. Consulte [Este artigo] [ hdinsight-python] sobre como executar o seu próprio código Python com ramo.

### <a name="use-the-getjsonobject-udf"></a>Utilizar a\_JSON_OBJECT UDF
Ramo fornece que um UDF incorporado denominado [obter json objeto](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-get_json_object) que pode efetuar JSON consultar durante o tempo de execução. Este método necessita de dois argumentos – o nome da tabela e o nome do método que tem o documento JSON plana e o campo JSON que precisa de possam ser analisadas. Vamos ver um exemplo para ver como funciona esta UDF.

Obter o nome próprio e apelido para cada aluno

    SELECT
      GET_JSON_OBJECT(StudentsOneLine.json_body,'$.StudentDetails.FirstName'),
      GET_JSON_OBJECT(StudentsOneLine.json_body,'$.StudentDetails.LastName')
    FROM StudentsOneLine;

Aqui está o resultado quando esta consulta a ser executado na janela consola.

![get_json_object UDF][image-hdi-hivejson-getjsonobject]

Existem algumas limitações de UDF get-json_object.

- Uma vez que cada campo na consulta requer a análise novamente a consulta, o afeta o desempenho.
- OBTER\_JSON_OBJECT() devolve a representação de cadeia de uma matriz. Para converter isto uma matriz de ramo, terá de utilizar expressões regulares para substituir os parênteses Retos ' [' e ']' e, em seguida, também chamar divisão para obter a matriz.


Este é o motivo pelo qual wiki do ramo recomenda a utilização de json_tuple.  

### <a name="use-the-jsontuple-udf"></a>Utilizar o UDF JSON_TUPLE

Outro UDF fornecido pela ramo chama [json_tuple](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-json_tuple) executa melhor do que [Get _ json _object](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-get_json_object). Este método leva-o até um conjunto de teclas e uma cadeia JSON e devolve uma cadeia de identificação de valores utilizando uma função. A seguinte consulta devolve o id do aluno e o grau do documento JSON:

    SELECT q1.StudentId, q1.Grade
      FROM StudentsOneLine jt
      LATERAL VIEW JSON_TUPLE(jt.json_body, 'StudentId', 'Grade') q1
        AS StudentId, Grade;

O resultado deste script na consola do ramo:

![json_tuple UDF][image-hdi-hivejson-jsontuple]

JSON\_cadeia de identificação utiliza a sintaxe da [vista lateral](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+LateralView) no ramo que lhe permite json\_cadeia de identificação para criar uma tabela virtual aplicando a função UDT para cada linha da tabela original.  JSONs complexas tornam-se demasiado difícil devido a utilização repetida da vista LATERAL. Além disso, JSON_TUPLE não consegue processar JSONs aninhadas.


###<a name="use-custom-serde"></a>Utilizar SerDe personalizado

SerDe é a melhor escolha para análise de documentos do aninhadas JSON, permite-lhe definir o esquema JSON e utilizar o esquema para analisar os documentos. Neste tutorial, irá utilizar um da SerDe mais popular que foi desenvolvido pelo [rcongiu](https://github.com/rcongiu).

**Para utilizar o SerDe personalizado:**

1. Instale o [7u55 JDK 1.7.0_55 do Kit de desenvolvimento Sudeste Java](http://www.oracle.com/technetwork/java/javase/downloads/java-archive-downloads-javase7-521261.html#jdk-7u55-oth-JPR). Escolher a versão do Windows X64 da JDK se passar para a implementação do Windows do HDInsight de estar a utilizar

    >[AZURE.WARNING] JDK 1.8 não funciona com este SerDe.

    Depois da instalação estiver concluída, adicione uma nova variável de ambiente de utilizador:

    1. A partir do ecrã do Windows, abra a **vista de definições avançadas do sistema** .
    2. Clique em **ambiente variáveis**.  
    3. Adicione que uma nova variável de ambiente de **JAVA_HOME** está a apontar para **C:\Programas\Microsoft Files\Java\jdk1.7.0_55** ou onde quer que o seu JDK está instalado.

    ![Configurar o valores config correta para JDK][image-hdi-hivejson-jdk]

2. Instalar [Maven 3.3.1](http://mirror.olnevhost.net/pub/apache/maven/maven-3/3.3.1/binaries/apache-maven-3.3.1-bin.zip)

    Adicionar a pasta de posição para o seu caminho ao aceder ao controlo painel--> Editar as variáveis do sistema para o seu variáveis de Environment de conta. A captura de ecrã abaixo mostra-lhe como o fazer.

    ![Configurar o Maven][image-hdi-hivejson-maven]

3. Clonar projeto a partir do [Ramo-JSON-SerDe](https://github.com/sheetaldolas/Hive-JSON-Serde/tree/master) github site. Pode fazê-lo ao clicar no botão "Transferir Zip", conforme apresentado na captura de ecrã abaixo.

    ![Clonar o projeto][image-hdi-hivejson-serde]

4: Ir para a pasta onde transferiu este pacote e tipo "mvn pacote". Isto deve criar os ficheiros de necessárias para caixa, em seguida, pode copiar sobre para cluster.

5: Ir para a pasta de destino na pasta de raiz onde transferiu o pacote. Carregar o ficheiro json-serde-1.1.9.9-Hive13-jar-with-dependencies.jar para cabeça-nó do seu cluster. Posso normalmente, coloque-o na pasta binário ramo: C:\apps\dist\hive-0.13.0.2.1.11.0-2316\bin ou algo semelhante.

6: na linha de comandos ramo de registo, escreva "Adicionar para caixa /path/to/json-serde-1.1.9.9-Hive13-jar-with-dependencies.jar". Uma vez que no meu caso, para a caixa na pasta C:\apps\dist\hive-0.13.x\bin, posso pode adicionar diretamente para a caixa com o nome conforme apresentado abaixo:

    add jar json-serde-1.1.9.9-Hive13-jar-with-dependencies.jar;

    ![Adding JAR to your project][image-hdi-hivejson-addjar]

Agora, está pronto para utilizar o SerDe para executar consultas de contra o documento JSON.

A seguinte instrução de criar uma tabela com um esquema definido

    DROP TABLE json_table;
    CREATE EXTERNAL TABLE json_table (
      StudentId string,
      Grade int,
      StudentDetails array<struct<
          FirstName:string,
          LastName:string,
          YearJoined:int
          >
      >,
      StudentClassCollection array<struct<
          ClassId:string,
          ClassParticipation:string,
          ClassParticipationRank:string,
          Score:int,
          PerformedActivity:boolean
          >
      >
    ) ROW FORMAT SERDE 'org.openx.data.jsonserde.JsonSerDe'
    LOCATION '/json/students';

Para o nome próprio e apelido do aluno da lista

    SELECT StudentDetails.FirstName, StudentDetails.LastName FROM json_table;

Aqui está o resultado a partir da consola de ramo.

![Consulta SerDe 1][image-hdi-hivejson-serde_query1]

Para calcular a soma de resultados do documento JSON

    SELECT SUM(scores)
    FROM json_table jt
      lateral view explode(jt.StudentClassCollection.Score) collection as scores;

A consulta acima utiliza [vista lateral destacar](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+LateralView) UDF para expandir a matriz de resultados para que estes podem ser somados.

Eis a saída a partir da consola de ramo.

![Consulta SerDe 2][image-hdi-hivejson-serde_query2]

Para localizar os assuntos um determinado aluno obtiver numa pontuação total tem mais do que 80 pontos SELECIONE  
      JT. StudentClassCollection.ClassId a partir de json_table jt vista lateral destacar (jt. Colecção de StudentClassCollection.Score) como pontuação pontuação onde > 80;

A consulta acima devolve uma matriz de ramo ao contrário get\_json\_objecto que devolve uma cadeia.

![Consulta SerDe 3][image-hdi-hivejson-serde_query3]

Se pretender skil mal JSON, em seguida, tal como é explicado a [página wiki](https://github.com/sheetaldolas/Hive-JSON-Serde/tree/master) deste SerDe é possível alcançar que ao escrever o código abaixo:  

    ALTER TABLE json_table SET SERDEPROPERTIES ( "ignore.malformed.json" = "true");




##<a name="summary"></a>Resumo
Concluindo, o tipo de operador JSON no ramo que escolher depende do seu cenário. Se tiver um documento JSON simple e apenas tiver um campo para procurar no – pode optar por utilizar a Hive UDF\_json\_objeto. Se tiver mais do que um teclas para procurar, em seguida, pode utilizar json_tuple. Se tiver um documento aninhado, em seguida, deve utilizar o SerDe JSON.

Para outros artigos relacionados, consulte o artigo

- [Utilizar ramo e HiveQL com Hadoop no HDInsight para analisar um ficheiro do exemplo Apache log4j](hdinsight-use-hive.md)
- [Analisar os dados de atrasos em voos utilizando ramo no HDInsight](hdinsight-analyze-flight-delay-data.md)
- [Analisar dados de Twitter utilizando ramo no HDInsight](hdinsight-analyze-twitter-data.md)
- [Executar uma tarefa de Hadoop utilizando DocumentDB e HDInsight](../documentdb/documentdb-run-hadoop-with-hdinsight.md)

[hdinsight-python]: hdinsight-python.md

[image-hdi-hivejson-flatten]: ./media/hdinsight-using-json-in-hive/flatten.png
[image-hdi-hivejson-getjsonobject]: ./media/hdinsight-using-json-in-hive/getjsonobject.png
[image-hdi-hivejson-jsontuple]: ./media/hdinsight-using-json-in-hive/jsontuple.png
[image-hdi-hivejson-jdk]: ./media/hdinsight-using-json-in-hive/jdk.png
[image-hdi-hivejson-maven]: ./media/hdinsight-using-json-in-hive/maven.png
[image-hdi-hivejson-serde]: ./media/hdinsight-using-json-in-hive/serde.png
[image-hdi-hivejson-addjar]: ./media/hdinsight-using-json-in-hive/addjar.png
[image-hdi-hivejson-serde_query1]: ./media/hdinsight-using-json-in-hive/serde_query1.png
[image-hdi-hivejson-serde_query2]: ./media/hdinsight-using-json-in-hive/serde_query2.png
[image-hdi-hivejson-serde_query3]: ./media/hdinsight-using-json-in-hive/serde_query3.png
[image-hdi-hivejson-serde_result]: ./media/hdinsight-using-json-in-hive/serde_result.png
