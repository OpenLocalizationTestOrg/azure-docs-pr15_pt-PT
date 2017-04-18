<properties
   pageTitle="Calcular opções de contexto para o servidor de R em HDInsight (pré-visualização) | Microsoft Azure"
   description="Saiba mais sobre as diferentes cluster contexto opções disponíveis para os utilizadores com o servidor de R HDInsight (pré-visualização)"
   services="HDInsight"
   documentationCenter=""
   authors="jeffstokes72"
   manager="jhubbard"
   editor="cgronlun"
/>

<tags
   ms.service="HDInsight"
   ms.devlang="R"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-services"
   ms.date="10/18/2016"
   ms.author="jeffstok"
/>

# <a name="compute-context-options-for-r-server-on-hdinsight-preview"></a>Calcular opções de contexto para o servidor de R em HDInsight (pré-visualização)

Microsoft R Server no Azure HDInsight (pré-visualização) fornece as funcionalidades mais recentes para análise com base em R. Utiliza dados armazenados no HDFS num contentor apresentado na sua conta de armazenamento de(../storage/storage-introduction.md "armazenamento de Blobs do Azure") de [BLOBs do Azure]ou o sistema de ficheiros Linux local. Uma vez que o servidor de R é criada com base em Abrir origem R, as aplicações com base em R que constrói podem tirar partido dos pacotes de abrir origem R 8000 +. Estes também podem tirar partido rotinas no [Medidor de escala](http://www.revolutionanalytics.com/revolution-r-enterprise-scaler "Medidor de escala de análise de rotação"), pacote de análise de dados grande da Microsoft que é incluído com o servidor de R.  

O nó do limite de um cluster de Premium fornece um local conveniente para ligar ao cluster e executar os scripts R. Com um nó de margem, tem a opção de executar funções de distribuído parallelized do medidor de escala entre os tarolos do servidor edge nó. Também tem a opção de executá-los em todos os nós do cluster através da utilização Hadoop mapa reduzir do medidor de escala ou motores calcular contextos.

## <a name="compute-contexts-for-an-edge-node"></a>Calcular contextos para um nó de margem

Em geral, um script de R é executado no servidor R o nó do limite é executado dentro de interpretação R nesse nó. A exceção é esses passos que utilizam uma função medidor de escala. Execute as chamadas medidor de escala num ambiente cluster que é determinado pela forma como configurou o contexto de cluster medidor de escala.  Quando executa o script R a partir de um nó de margem, os valores possíveis do contexto cluster são local sequenciais ('local'), paralelas local ('localpar'), reduzir de mapa e motores.

As opções de 'locais' e 'localpar' diferem apenas em como chamadas rxExec são executadas. Ambas as executam outras chamadas de função de recepção de uma forma paralela ao longo núcleos todos disponíveis salvo através da utilização da opção numCoresToUse medidor de escala, por exemplo, rxOptions(numCoresToUse=6). A lista seguinte resume as várias opções de contexto de cluster

| Calcular contexto  | Como configurar                      | Contexto de execução                                                                     |
|------------------|---------------------------------|---------------------------------------------------------------------------------------|
| Local sequencial | rxSetComputeContext('local')    | Execução parallelized entre os tarolos da margem nó do servidor, exceto rxExec chamadas que são executados em série |
| Paralelamente local   | rxSetComputeContext('localpar') | Execução parallelized entre os tarolos dos servidor edge nó                                 |
| Motores            | RxSpark()                       | Execução distribuída parallelized através de motores em todos os nós do HDI cluster      |
| Reduzir o mapa       | RxHadoopMR()                    | Execução distribuída parallelized através do mapa reduzir em todos os nós do HDI cluster |


Partindo do princípio que gostaria execução parallelized para fins de desempenho, existem três opções. Opção que escolher depende da natureza da análise número do trabalho e o tamanho e localização dos seus dados.

## <a name="guidelines-for-deciding-on-a-compute-context"></a>Diretrizes para escolher um cluster de contexto

Atualmente, não existe nenhuma fórmula que mostra-lhe qual calcular contexto para utilizar. No entanto, existem algumas orientações que podem ajudar a tornar a opção acertada ou, pelo menos, ajudá-lo a limitar as opções antes de executar uma referência. Estes orientar princípios incluem:

1.  O sistema de ficheiros Linux local é mais rápido que HDFS.
2.  Análises repetidas são mais rápidos se os dados forem locais e, se estiver na XDF.
3.  É preferível para transmitir em fluxo pequenas quantidades de dados a partir de uma origem de dados de texto; Se a quantidade de dados for maior, convertê-la XDF antes de análise.
4.  O overhead da cópia ou transmissão os dados para o nó do limite para uma análise torna-se impraticável para muito grandes quantidades de dados.
5.  Motores são mais rápido que mapa reduzir para análise no Hadoop.

Algumas regras gerais para selecionar um contexto cluster dada estes princípios, são:

### <a name="local"></a>Local

- Se a quantidade de dados para analisar é pequena e não requer análise repetida, em seguida, transmitir em fluxo-lo diretamente para a rotina de análise e utilize 'local' ou 'localpar'.
- Se a quantidade de dados para analisar é pequenas e médias empresas e precisa de análise repetida, em seguida, copiá-lo para o sistema de ficheiros local, importe-o para XDF e analisá-los através de 'local' ou 'localpar'.

### <a name="hadoop-spark"></a>Motores de Hadoop

- Se a quantidade de dados para analisar for grande, em seguida, importá-lo para XDF no HDFS (a não ser armazenamento é um problema) e analisá-los através do 'Motores'.

### <a name="hadoop-map-reduce"></a>Mapa de Hadoop reduzir

- Utilize apenas se se deparar com um problema com a utilização do contexto cluster motores de intransponível desde geralmente serão mais lento.  

## <a name="inline-help-on-rxsetcomputecontext"></a>Obter ajuda sobre o rxSetComputeContext inline

Para obter mais informações e exemplos de contextos de cluster medidor de escala, consulte o artigo inline ajuda na R método rxSetComputeContext, por exemplo:

    > ?rxSetComputeContext

Também pode consultar o "medidor de escala distribuído computação guia" que está disponível a partir da biblioteca de(https://msdn.microsoft.com/library/mt674634.aspx "Servidor de R no MSDN") [MSDN do servidor de R].


## <a name="next-steps"></a>Próximos passos

Neste artigo, o que aprendeu como criar um novo cluster de HDInsight inclui R servidor. Aprendeu também os princípios básicos para utilizar a consola do R a partir de uma sessão SSH. Agora pode ler os artigos seguintes para descobrir outras formas de trabalhar com o servidor de R na HDInsight:

- [Descrição geral do servidor de R para Hadoop](hdinsight-hadoop-r-server-overview.md)
- [Introdução ao servidor de R para Hadoop](hdinsight-hadoop-r-server-get-started.md)
- [Adicionar o servidor de RStudio HDInsight Premium](hdinsight-hadoop-r-server-install-r-studio.md)
- [Azure opções de armazenamento para o servidor de R em HDInsight Premium](hdinsight-hadoop-r-server-storage.md)
