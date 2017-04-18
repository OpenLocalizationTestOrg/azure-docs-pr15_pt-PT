<properties
    pageTitle="O que é R no HDInsight? Introdução ao R Server no HDInsight (pré-visualização) | Microsoft Azure"
    description="O que é o servidor de R num HDInsight (pré-visualização) e como utilizar o servidor de R para criar aplicações para análise de dados grande."
    services="hdinsight"
    documentationCenter=""
    authors="jeffstokes72"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="08/17/2016"
   ms.author="jeffstok"/>


# <a name="overview-of-r-server-on-hdinsight-preview"></a>Descrição geral na barra de R HDInsight \(pré-visualização\)

Com o Microsoft Azure HDInsight Premium, Microsoft R Server está agora disponível como opção quando cria HDInsight clusters no Azure. Esta nova capacidade fornece cientistas de dados, estaticistas e os programadores de R com acesso a pedido dimensionáveis, distribuídos métodos de análise no HDInsight.

Clusters podem ser dimensionado de modo a projetos e tarefas à mão e incompleta para baixo quando já não está a necessárias. Já que são parte do Azure HDInsight, estes clusters fornecidos com o suporte de 24/7 de nível empresarial, um SLA de 99,9% tempo de utilização e a flexibilidade para integrar com outros componentes no Azure ecossistema.

>[AZURE.NOTE] Servidor de R só está disponível com HDInsight Premium. Atualmente, HDInsight Premium só está disponível para clusters Hadoop e motores. Por isso, atualmente pode utilizar R servidor apenas com Hadoop e motores clusters no HDInsight. Para obter mais informações, consulte o artigo [quais são os níveis de serviços diferente e componentes de Hadoop disponíveis com HDInsight?](hdinsight-component-versioning.md).

Servidor de R na HDInsight fornece as funcionalidades mais recentes para análise com base em R grandes conjuntos de dados que são carregados para o armazenamento de Blobs do Azure. Uma vez que o servidor de R é criada com base em Abrir origem R, as aplicações com base em R que constrói podem tirar partido dos pacotes de abrir origem R 8000 +, bem como as rotinas no medidor de escala, pacote de análise de dados grande da Microsoft que é incluído com o servidor de R.

O nó do limite de Premium clusters é útil para ligar ao cluster e para executar os scripts R. Com um nó de margem, tem a opção de executar funções de distribuído parallelized do medidor de escala entre os tarolos do servidor edge nó. Também tem a opção de executá-los em todos os nós do cluster através da utilização Hadoop mapa reduzir do medidor de escala ou motores calcular contextos.

Modelos ou previsões que pode ser transferido o resultado de análises para utilizam no local. Estes podem também ser operacionalizadas noutro local no Azure, tal como através de um [serviço web](../machine-learning/machine-learning-publish-a-machine-learning-web-service.md)de [Azure máquina aprendizagem Studio](http://studio.azureml.net) .

## <a name="get-started-with-r-on-hdinsight"></a>Introdução ao R no HDInsight

Para incluir R Server num HDInsight cluster, terá de criar uma Hadoop ou motores cluster com a opção Premium quando cria um cluster utilizando o portal do Azure. Ambos os tipos de cluster utilizam a mesma configuração, que inclui R servidor os nós de dados de um cluster e um nó limite como uma zona de destino para a análise de baseadas no servidor R. Consulte o artigo [Introdução ao servidor R HDInsight](hdinsight-hadoop-r-server-get-started.md) para uma guia passo a passo aprofundada sobre como criar um cluster.

## <a name="learn-about-data-storage-options"></a>Saiba mais sobre as opções de armazenamento de dados

Predefinição de armazenamento para HDInsight clusters é armazenamento de Blobs com o sistema de ficheiros HDFS mapeado para um contentor blob. Este procedimento garante que seja qual for dados são carregados para o armazenamento de cluster ou escritos ao armazenamento cluster durante o curso de análise, é tornado persistente. Pode utilizar o utilitário [AzCopy](../storage/storage-use-azcopy.md) para copiar dados de e para o blob.

Além de utilizar o armazenamento de BLOBs, tem a opção de utilização de [Armazenamento do Windows Azure dados Lake](https://azure.microsoft.com/services/data-lake-store/) com o seu cluster. Se utilizar dados Lake, em seguida, pode utilizar armazenamento de BLOBs e Lake de dados para o armazenamento do HDFS.

Também pode utilizar [Azure ficheiros](../storage/storage-how-to-use-files-linux.md) como uma opção de armazenamento para utilização no nó do limite. Ficheiros Azure permite-lhe montagem uma partilha de ficheiros que foi criada numa armazenamento do Windows Azure ao sistema de ficheiros Linux. Para mais informações sobre as opções de armazenamento de dados para o servidor de R num cluster de HDInsight, consulte o artigo [Opções de armazenamento para o servidor de R em HDInsight clusters](hdinsight-hadoop-r-server-storage.md).

## <a name="access-r-server-on-the-cluster"></a>Servidor de R de acesso no cluster

Após ter criado um cluster com o servidor de R, pode ligar para a consola de R o nó do limite do cluster através de SSH/betumes. Também pode ligar através de um browser se optar por instalar o opcional servidor IDE da RStudio no nó do limite. Para obter mais informações sobre como instalar RStudio Server, consulte o artigo [Instalar RStudio servidor em HDInsight clusters](hdinsight-hadoop-r-server-install-r-studio.md).   

## <a name="develop-and-run-r-scripts"></a>Desenvolver e executar scripts de R

Criar e executar os scripts de R podem utilizar qualquer um dos pacotes de abrir origem R 8000 + além as rotinas parallelized e distribuídas na biblioteca de medidor de escala. Em geral, script que é executado no servidor R o nó do limite é executado dentro de interpretação R nesse nó. A exceção é esses passos que utilizam um medidor de escala funcionam com um contexto cluster já conjunto para reduzir o mapa Hadoop (RxHadoopMR) ou motores (RxSpark).

Nestes casos, a função é executado de uma forma distribuída ao longo dos dados nós (tarefa) do cluster que estão associadas com os dados referenciados. Para mais informações sobre as opções de contexto de cluster diferente, consulte o artigo [Calcular opções de contexto para o servidor de R em HDInsight Premium](hdinsight-hadoop-r-server-compute-contexts.md).

## <a name="operationalize-a-model"></a>Operationalize um modelo

Quando a modelação de dados estiver concluída, pode operationalize o modelo para efetuar previsões dados novos, tanto no Azure e no local. Este processo é conhecido como pontuação. Aqui estão alguns exemplos.

### <a name="score-in-hdinsight"></a>Pontuação na HDInsight

À pontuação na HDInsight, escreva uma função de R que liga o seu modelo para criar previsões para um novo ficheiro de dados que tenha de ser carregado para a sua conta de armazenamento. Em seguida, guarde os previsões novamente para a conta de armazenamento. Pode executar a manutenção de rotina a pedido no nó do limite do seu cluster ou ao utilizar uma tarefa agendada.  

### <a name="score-in-azure-machine-learning"></a>Pontuação na formação de máquina Azure

Para pontuação utilizando um serviço web de aprendizagem do Azure máquina, utilize o pacote de Azure máquina aprendizagem R abrir origem conhecido como [AzureML](https://cran.r-project.org/web/packages/AzureML/vignettes/getting_started.html) para publicar o seu modelo como um serviço Azure web. Para sua comodidade, este pacote se encontra instalado no nó do limite. Em seguida, utilize as funcionalidades de aprendizagem automática para criar uma interface de utilizador para o serviço web e, em seguida, ligar o serviço web conforme necessário para pontuação.

Se selecionar esta opção, terá de converter os objetos do modelo qualquer medidor de escala objetos do modelo de abrir origem equivalente para utilização com o serviço web. Isto pode ser feito através da utilização de funções de coerção medidor de escala, tais como `as.randomForest()` para os modelos baseados em conjunto.


### <a name="score-on-premises"></a>Pontuação no local

Para pontuação no local depois de criar o seu modelo, pode serializar o modelo no R, transfira-o, anular a serialização e, em seguida, utilizá-lo para pontuação novos dados. Pode pontuação novos dados ao utilizar a abordagem já descrita [pontuação no HDInsight](#scoring-in-hdinsight) ou ao utilizar [DeployR](https://deployr.revolutionanalytics.com/).

## <a name="maintain-the-cluster"></a>Manter o cluster

### <a name="install-and-maintain-r-packages"></a>Instalar e manter pacotes R

A maior parte dos pacotes R que utilizar será exigido que no nó limite uma vez que a maioria dos seus scripts R executarão aí. Para instalar pacotes R adicionais sobre o nó do limite, pode utilizar o habitual `install.packages()` método no R.

Na maioria dos casos, não tem de instalar pacotes R adicionais em nós de dados se estiver apenas a utilizar rotinas a partir da biblioteca de medidor de escala ao longo do cluster. No entanto, poderá ter pacotes adicionais para suportar a utilização do **rxExec** ou **RxDataStep** execução em nós de dados.

Nestes casos, os pacotes adicionais tem de ser especificados através da utilização de uma ação de script depois de criar o cluster. Para mais informações, consulte o artigo [criar um cluster de HDInsight com o servidor de R](hdinsight-hadoop-r-server-get-started.md).   

### <a name="change-hadoop-map-reduce-memory-settings"></a>Alterar as definições de memória Hadoop mapa reduzir

Um cluster pode ser modificado para alterar a quantidade de memória que está disponível para servidor de R quando está a ser executada uma tarefa de reduzir o mapa. Para modificar um cluster, utilize a IU Ambari Apache que estão disponíveis através de Azure pá portal para o seu cluster. Para obter instruções sobre como aceder à IU Ambari para o seu cluster, consulte o artigo [Gerir HDInsight clusters utilizando a IU da Web de Ambari](hdinsight-hadoop-manage-ambari.md).

Também é possível alterar a quantidade de memória que está disponível para servidor de R ao utilizar Hadoop parâmetros na chamada para **RxHadoopMR** da seguinte forma:

    hadoopSwitches = "-libjars /etc/hadoop/conf -Dmapred.job.map.memory.mb=6656"  

### <a name="scale-your-cluster"></a>Dimensionar o seu cluster

Um cluster existente pode ser dimensionado para cima ou para baixo, através do portal. Ao dimensionamento, pode obter a capacidade de adicional que poderá ter de tarefas de processamento de maiores ou pode dimensionar novamente um cluster quando está inactivo. Para obter instruções sobre como dimensionar um cluster, consulte o artigo [Gerir HDInsight clusters](hdinsight-administer-use-portal-linux.md).

### <a name="maintain-the-system"></a>Manter o sistema de

Manutenção é executada na VMs Linux subjacente num HDInsight cluster horas de expediente para aplicar OS patches e outras atualizações. Normalmente, a manutenção é feita 3:30 AM (com base na data e hora local para a VM) cada segunda-feira e quinta-feira. As atualizações são executadas forma a que não a afetar o mais do que um trimestre do cluster de cada vez.  

Dado que são redundantes os nós de cabeça e não em todos os nós de dados são afetados, quaisquer tarefas que estiver a executar o durante este período de tempo podem diminuir a velocidade. Estes devem continuar a executar para conclusão, no entanto. Qualquer software personalizado ou dos dados locais que possui são preservados através destes eventos de manutenção, a menos que ocorre uma falha grave que requer um cluster reconstruir.

## <a name="learn-about-ide-options-for-r-server-on-an-hdinsight-cluster"></a>Saiba mais sobre opções IDE para servidor de R num HDInsight cluster

O nó do limite de Linux de um cluster de HDInsight Premium é a zona de destino para uma análise com base em R. Depois de ligar para o cluster, pode iniciar a interface de consola ao servidor de R ao escrever **R** na linha de comandos Linux. Utilização da interface consola é melhorada se executar um editor de texto para o desenvolvimento de script R na outra janela e cortar e colar secções do seu script consola do R conforme necessário.

Uma ferramenta mais sofisticada para o desenvolvimento do seu script R estiver IDE com base em R para utilização no ambiente de trabalho, tal como Microsoft recentemente anunciada [R ferramentas para o Visual Studio](https://www.visualstudio.com/en-us/features/rtvs-vs.aspx) (RTVS). Esta é uma família das ferramentas de ambiente de trabalho e de servidor de [RStudio](https://www.rstudio.com/products/rstudio-server/). Também pode utilizar com base em Eclipse do Walware [StatET](http://www.walware.de/goto/statet).

Outra opção é instalar um IDE no próprio nó de limite de Linux.  Uma escolha popular é [RStudio servidor](https://www.rstudio.com/products/rstudio-server/), que fornece um IDE baseada no browser para ser utilizado por clientes remotos. Instalar o servidor de RStudio no nó do limite de um cluster de HDInsight Premium fornece uma experiência de IDE completa para o desenvolvimento e a execução dos scripts R com R servidor no cluster. Pode ser consideravelmente mais produtivo que consola do R.  Se pretender utilizar RStudio servidor, consulte o artigo [Instalar o servidor de RStudio em HDInsight clusters](hdinsight-hadoop-r-server-install-r-studio.md).

## <a name="learn-about-pricing"></a>Saiba mais sobre preços

As taxas que estão associadas um cluster de HDInsight Premium com o servidor de R estão estruturadas de forma semelhante às taxas para clusters HDInsight padrão. São baseados no dimensionamento das VMs subjacentes no nome, dados e nós de margem, com a adição de uma elevação de hora core Premium. Para obter mais informações sobre HDInsight Premium preços, incluindo os preços durante a pré-visualização do público e a disponibilidade de uma avaliação gratuita de 30 dias, consulte o artigo [HDInsight preços](https://azure.microsoft.com/pricing/details/hdinsight/).

## <a name="next-steps"></a>Próximos passos

Siga as ligações abaixo para ler mais sobre como utilizar o servidor de R com HDInsight clusters.

- [Introdução ao servidor R HDInsight](hdinsight-hadoop-r-server-get-started.md)

- [Adicionar o servidor de RStudio HDInsight Premium](hdinsight-hadoop-r-server-install-r-studio.md)

- [Calcular opções de contexto para o servidor de R em HDInsight (pré-visualização)](hdinsight-hadoop-r-server-compute-contexts.md)

- [Azure opções de armazenamento para o servidor de R em HDInsight Premium](hdinsight-hadoop-r-server-storage.md)
