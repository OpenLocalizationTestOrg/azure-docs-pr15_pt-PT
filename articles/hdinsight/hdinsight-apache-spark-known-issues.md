<properties 
    pageTitle="Problemas de motores de Apache no HDInsight conhecidos | Microsoft Azure" 
    description="Problemas conhecidos do Apache motores no HDInsight." 
    services="hdinsight" 
    documentationCenter="" 
    authors="mumian" 
    manager="jhubbard" 
    editor="cgronlun"
    tags="azure-portal"/>

<tags 
    ms.service="hdinsight" 
    ms.workload="big-data" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/25/2016" 
    ms.author="nitinme"/>

# <a name="known-issues-for-apache-spark-cluster-on-hdinsight-linux"></a>Problemas conhecidos para cluster de motores de Apache HDInsight Linux

Este documento mantém um registo das todos os problemas conhecidos para a pré-visualização de público de motores de HDInsight.  

##<a name="livy-leaks-interactive-session"></a>Lívio fugas sessão interativo
 
Quando for reiniciado Lívio com uma sessão interactiva (a partir do Ambari ou devido a headnode 0 máquina virtual reinicie) ainda vivo, uma sessão de tarefa interativos irá fuga. Por esta razão, novas tarefas podem preso no estado de aceites e não podem ser iniciadas.

**Mitigação:**

Utilize o seguinte procedimento para contornar o problema:

1. SSH para headnode. 
2. Execute o seguinte comando para encontrar os IDs de aplicação das tarefas interativos iniciados através de Lívio. 

        yarn application –list

    A tarefa de predefinido nomes serão Lívio se as tarefas que foram iniciadas com uma sessão de interativa Lívio sem explícitas nomes especificados, sessão de para Lívio iniciado por Jupyter bloco de notas, o nome da tarefa será iniciado com remotesparkmagics_ *. 

3. Execute o seguinte comando para eliminar essas tarefas. 

        yarn application –kill <Application ID>

Novas tarefas comecem em execução. 

##<a name="spark-history-server-not-started"></a>Servidor de histórico de motores não iniciado 

Motores histórico Server não estiver iniciado automaticamente depois de criado um cluster.  

**Mitigação:** 

Inicie manualmente o servidor de histórico de Ambari.

## <a name="permission-issue-in-spark-log-directory"></a>Problema de permissão no directório de motores de registo 

Quando hdiuser submete uma tarefa com spark-submit, existe uma java.io.FileNotFoundException de erro: não é escrito /var/log/spark/sparkdriver_hdiuser.log (permissão negado) e o registo de controlador. 

**Mitigação:**
 
1. Adicione hdiuser ao grupo Hadoop. 
2. Forneça 777 permissões na /var/log/spark após a criação de cluster. 
3. Atualize a localização do registo motores utilizar Ambari para ser um diretório com 777 permissões.  
4. Motores de executar-submeter como sudo.  

## <a name="issues-related-to-jupyter-notebooks"></a>Problemas relacionados com a Jupyter blocos de notas

Seguem-se alguns problemas conhecidos relacionados com a Jupyter blocos de notas.


### <a name="notebooks-with-non-ascii-characters-in-filenames"></a>Blocos de notas com carateres não ASCII em nomes de ficheiros

Jupyter blocos de notas que podem ser utilizados em motores HDInsight clusters não devem ter não ASCII carateres em nomes de ficheiros. Se tentar carregar um ficheiro através da UI Jupyter que tem um nome de ficheiro não ASCII, irá falhar silenciosamente (ou seja, não permitem Jupyter carregue o ficheiro, mas não irá gerar um erro visível quer). 

### <a name="error-while-loading-notebooks-of-larger-sizes"></a>Ocorreu um erro ao carregar blocos de notas de grande tamanho

Poderá ver um erro **`Error loading notebook`** quando carregar blocos de notas que são maiores de tamanho.  

**Mitigação:**

Se receber este erro, não significa que os seus dados estão danificado ou perdido.  Os blocos de notas ainda estiverem presentes no disco na `/var/lib/jupyter`, e pode SSH num cluster para aceder aos mesmos. Pode copiar os blocos de notas a partir do seu cluster no computador local (utilizando SCP ou WinSCP) como uma cópia de segurança para evitar a perda de quaisquer dados importantes no bloco de notas. Pode, em seguida, SSH túnel para o seu headnode porto 8001 para aceder à Jupyter sem ter de aceder através do gateway.  A partir daqui, pode limpar a saída do seu bloco de notas e voltar a guardá-lo para minimizar o tamanho do bloco de notas.

Para impedir que este erro a ocorrer no futuro, tem de seguir alguns dos procedimentos recomendados:

* É importante manter o bloco de notas tamanho pequeno. Qualquer saída das tarefas de motores que é enviada para o Jupyter é mantida no bloco de notas.  É aconselhável com Jupyter geral para evitar a trabalhar rapidamente `.collect()` em grandes RDD ou dataframes; em vez disso, se pretender observar de conteúdo de uma RDD, considere a trabalhar rapidamente `.take()` ou `.sample()` para que a saída não aceder demasiado grande.
* Além disso, quando guarda um bloco de notas, desmarque todas as saída células para reduzir o tamanho.

### <a name="notebook-initial-startup-takes-longer-than-expected"></a>Arranque inicial do bloco de notas demora mais tempo do que o esperado 

A primeira instrução de código no bloco de notas Jupyter utilizando mágico motores poderá demorar mais de um minuto.  

**Explicação:**
 
Isto acontece porque quando é executada a primeira célula de código. Em segundo plano Isto inicia a configuração de sessões e motores, SQL e ramo contextos são definidos. Depois destes contextos estão definidos, a primeira instrução é executada e isto dá a impressão de que a declaração de demorou muito tempo a concluir.

### <a name="jupyter-notebook-timeout-in-creating-the-session"></a>Tempo limite do bloco de notas Jupyter na criação da sessão

Quando o cluster de motores estiver fora do recursos, os kernels motores e Pyspark no bloco de notas Jupyter irão tempo excedido ao tentar criar a sessão. 

**Atenuações:** 

1. Liberte alguns recursos no seu cluster de motores por:

    - Parar a outros blocos de notas motores aceder ao menu fechar e parar ou clicando em encerramento no Explorador de bloco de notas.
    - Parar a outras aplicações de motores de FIO.

2. Reinicie o bloco de notas que estava a tentar iniciar o. Suficiente recursos deverão estar disponíveis criar uma sessão de agora.

##<a name="see-also"></a>Consulte também

* [Descrição geral: Apache motores no Azure HDInsight](hdinsight-apache-spark-overview.md)

### <a name="scenarios"></a>Cenários

* [Motores com BI: efetuar uma análise de dados interativos utilizando motores no HDInsight com ferramentas de BI](hdinsight-apache-spark-use-bi-tools.md)

* [Motores com máquina formação: utilizar motores no HDInsight para analisar temperatura edifício utilizando dados de AVAC](hdinsight-apache-spark-ipython-notebook-machine-learning.md)

* [Motores com máquina formação: utilizar motores no HDInsight para prever resultados da inspeção de alimentação](hdinsight-apache-spark-machine-learning-mllib-ipython.md)

* [Transmissão de motores: Motores de utilização no HDInsight para criar aplicações de transmissão em tempo real](hdinsight-apache-spark-eventhub-streaming.md)

* [Análise de registo de Web site utilizando motores no HDInsight](hdinsight-apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Criar e executar as aplicações

* [Criar uma aplicação autónoma Scala](hdinsight-apache-spark-create-standalone-application.md)

* [Executar tarefas remotamente num cluster de motores utilizando Lívio](hdinsight-apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Ferramentas e extensões

* [Utilize o plug-in do HDInsight ferramentas para obter IntelliJ IDEIA para criar e submeter motores Scala aplicações](hdinsight-apache-spark-intellij-tool-plugin.md)

* [Utilizar o plug-in do HDInsight ferramentas para obter IntelliJ IDEIA para depurar as aplicações de motores remotamente](hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)

* [Utilizar blocos de notas Zeppelin com um cluster de motores num HDInsight](hdinsight-apache-spark-use-zeppelin-notebook.md)

* [Kernels disponíveis para o bloco de notas Jupyter num cluster de motores para HDInsight](hdinsight-apache-spark-jupyter-notebook-kernels.md)

* [Utilizar os pacotes externos com Jupyter blocos de notas](hdinsight-apache-spark-jupyter-notebook-use-external-packages.md)

* [Instalar Jupyter no seu computador e ligar a um cluster de motores de HDInsight](hdinsight-apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Adicionar e utilizar recursos

* [Gerir os recursos para o cluster de motores de Apache no Azure HDInsight](hdinsight-apache-spark-resource-manager.md)

* [Controlar e depuração tarefas em execução num cluster de motores de Apache HDInsight](hdinsight-apache-spark-job-debugging.md)
