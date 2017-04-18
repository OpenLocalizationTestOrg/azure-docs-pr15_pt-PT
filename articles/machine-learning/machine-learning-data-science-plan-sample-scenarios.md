<properties
    pageTitle="Cenários de análise avançadas processo e tecnologia em aprender máquina Azure | Microsoft Azure"
    description="Selecione os cenários adequados para fazê-lo avançadas analytics aspeto do Office com o processo de ciências de dados de equipa."
    services="machine-learning"
    documentationCenter=""
    authors="bradsev"
    manager="jhubbard"
    editor="cgronlun" />

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na" 
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/19/2016" 
    ms.author="bradsev" />


# <a name="scenarios-for-advanced-analytics-in-azure-machine-learning"></a>Cenários de análise avançadas no Azure máquina aprendizagem

Este artigo descreve a variedade de origens de dados de exemplo e cenários de destino que poderá ser resolvidos pelo [Processo de ciências de dados de equipa (TDSP)](data-science-process-overview.md). O TDSP fornece uma abordagem sistemática para que as equipas a colaborar em criação de aplicações do inteligentes. Os cenários aqui apresentados ilustram opções disponíveis no fluxo de trabalho de processamento de dados que dependem as características de dados, localizações de origem e repositórios de destino no Azure.

A **árvore de decisões** para selecionar os cenários de exemplo adequado para os dados e objectivo é apresentado na última secção.

>[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]


Cada uma das seguintes secções apresenta um cenário de exemplo. Para cada cenário, uma ciência dados possíveis ou análise avançadas fluxo e Azure recursos de suporte são listados.

>[AZURE.NOTE]**Para todos os cenários seguintes, tem de:**
><br/>
>* [Criar uma conta de armazenamento](../storage/storage-create-storage-account.md)
><br/>
>* [Criar uma área de trabalho do Azure máquina aprendizagem](machine-learning-create-workspace.md)


## <a name="smalllocal"></a>Cenário \#1: pequeno para médio conjunto de dados de tabela em ficheiros de locais

![Pequenas e médias ficheiros locais][1]

#### <a name="additional-azure-resources-none"></a>Recursos adicionais de Azure: nenhuma

1.  Inicie sessão no [Azure máquina de formação Studio](https://studio.azureml.net/).

2.  Carregar um conjunto de dados.

3.  Crie um fluxo de experiência de formação do Azure máquina começando publicados carregados.

## <a name="smalllocalprocess"></a>Cenário \#2: pequeno para o conjunto de dados médio de ficheiros locais que necessitam de processamento

![Pequenas e médias ficheiros locais com processamento][2]

#### <a name="additional-azure-resources-azure-virtual-machine-ipython-notebook-server"></a>Recursos adicionais de Azure: Azure Máquina Virtual (servidor de bloco de notas IPython)

1.  Crie uma máquina de Virtual Azure executar IPython bloco de notas.

2.  Carregar dados a um contentor de armazenamento Azure.

3.  Preparação do processo e limpar os dados no bloco de notas IPython, aceder a dados a partir do contentor de armazenamento Azure.

4.  Transforma dados Limpar formato tabular.

5.  Guarde os dados transformados com logaritmos blobs do Azure.

6.  Inicie sessão no [Azure máquina de formação Studio](https://studio.azureml.net/).

7.  Ler os dados a partir do Azure blobs utilizando os [Importar dados] [ import-data] módulo.

8. Crie um fluxo de experiência de aprendizagem do Azure máquina começando publicados aspirados.

## <a name="largelocal"></a>Cenário \#3: grande conjunto de dados de ficheiros locais, filtragem de Blobs do Azure

![Ficheiros de grandes dimensões locais][3]

#### <a name="additional-azure-resources-azure-virtual-machine-ipython-notebook-server"></a>Recursos adicionais de Azure: Azure Máquina Virtual (servidor de bloco de notas IPython)

1.  Crie uma máquina de Virtual Azure executar IPython bloco de notas.

2.  Carregar dados a um contentor de armazenamento Azure.

3.  Preparação do processo e limpar os dados no bloco de notas IPython, aceder a dados a partir do Azure blobs.

4.  Transforma dados Limpar formato tabular, se necessário.

5.  Explorar dados e criar funcionalidades conforme necessário.

6.  Extrai um exemplo de dados de pequenas para médio.

7.  Guarde os dados de amostra no Azure blobs.

8. Inicie sessão no [Azure máquina de formação Studio](https://studio.azureml.net/).

9. Ler os dados a partir do Azure blobs utilizando os [Importar dados] [ import-data] módulo.

10. Fluxo de experiência de formação do Azure máquina começando publicados aspirados a criar.


## <a name="smalllocaltodb"></a>Cenário \#4: pequeno para o conjunto de dados médio de ficheiros locais, filtragem de SQL Server em máquinas uma máquina de Virtal do Azure

![Pequenas e médias ficheiros locais para DB do SQL no Azure][4]

#### <a name="additional-azure-resources-azure-virtual-machine-sql-server--ipython-notebook-server"></a>Recursos adicionais de Azure: Azure Máquina Virtual (SQL Server / servidor IPython bloco de notas)

1.  Crie uma máquina de Virtual Azure a executar o SQL Server + IPython bloco de notas.

2.  Carregar dados a um contentor de armazenamento Azure.

3.  Preparação do processo e limpar os dados no contentor de armazenamento Azure utilizar IPython bloco de notas.

4.  Transforma dados Limpar formato tabular, se necessário.

5.  Guardar os dados para ficheiros de VM local (IPython bloco de notas está em execução no VM, unidades locais referem VM unidades).

6.  Carregar os dados a base de dados do SQL Server em execução numa VM Azure.

    Opção \#1: utilizar o SQL Server Management Studio.

    - Início de sessão para o SQL Server VM
    - Execute o SQL Server Management Studio.
    - Crie tabelas de base de dados e de destino.
    - Utilize um em volume importar métodos para carregar os dados a partir de ficheiros VM local.

    Opção \#2: utilizar IPython bloco de notas – não é aconselhável para conjuntos de dados médios e grandes<!-- -->    
    - Utilize a cadeia de ligação ODBC para aceder ao SQL Server no VM.
    - Crie tabelas de base de dados e de destino.
    - Utilize um em volume importar métodos para carregar os dados a partir de ficheiros VM local.

7.  Explorar dados, criar funcionalidades conforme necessário. Tenha em atenção que as funcionalidades não necessita de ser verificada nas tabelas da base de dados. Tenha em atenção apenas a consulta necessária criá-los.

8. Decida num tamanho dados de exemplo, se necessário e/ou pretendido.

9. Inicie sessão no [Azure máquina de formação Studio](https://studio.azureml.net/).

10. Ler os dados diretamente a partir do SQL Server utilizando os [Importar dados] [ import-data] módulo. Cole a consulta necessária que extrai campos, cria funcionalidades e amostras de dados se for necessário diretamente na [Importar dados] [ import-data] consulta.

11. Fluxo de experiência de formação do Azure máquina começando publicados aspirados a criar.

## <a name="largelocaltodb"></a>Cenário \#5: grande conjunto de dados em ficheiros de locais, SQL Server em máquinas Azure VM-alvo

![Ficheiros grandes locais à base de dados do SQL no Azure][5]

#### <a name="additional-azure-resources-azure-virtual-machine-sql-server--ipython-notebook-server"></a>Recursos adicionais de Azure: Azure Máquina Virtual (SQL Server / servidor IPython bloco de notas)

1.  Crie uma máquina de Virtual Azure a executar o SQL Server e server IPython bloco de notas.

2.  Carregar dados a um contentor de armazenamento Azure.

3.  (Opcional) Preparação do processo e limpar os dados.

    um.  Preparação do processo e limpar os dados no bloco de notas IPython, aceder a dados a partir do Azure blobs.

    b.  Transforma dados Limpar formato tabular, se necessário.

    c.  Guardar os dados para ficheiros de VM local (IPython bloco de notas está em execução no VM, unidades locais referem VM unidades).

4.  Carregar os dados a base de dados do SQL Server em execução numa VM Azure.

    um.  Início de sessão para o SQL Server VM.

    b.  Se não guardar os dados já, transferir ficheiros de dados a partir do contentor de armazenamento Azure para a pasta local VM.

    c.  Execute o SQL Server Management Studio.

    d.  Crie tabelas de base de dados e de destino.

    "e".  Utilize um em volume importar métodos para carregar os dados.

    f.  Se forem necessárias as associações de tabela, crie índices para acelerar associações.

     > [AZURE.NOTE] Para o carregamento mais rápido de tamanhos de dados grandes, é recomendado que criar tabelas com partições e em volume importar os dados em paralelo. Para mais informações, consulte o artigo [Paralelas importação de dados a tabelas divididos de SQL](machine-learning-data-science-parallel-load-sql-partitioned-tables.md).

5.  Explorar dados, criar funcionalidades conforme necessário. Tenha em atenção que as funcionalidades não necessita de ser verificada nas tabelas da base de dados. Tenha em atenção apenas a consulta necessária criá-los.

6.  Decida num tamanho dados de exemplo, se necessário e/ou pretendido.

7.  Inicie sessão no [Azure máquina de formação Studio](https://studio.azureml.net/).

8. Ler os dados diretamente a partir do SQL Server utilizando os [Importar dados] [ import-data] módulo. Cole a consulta necessária que extrai campos, cria funcionalidades e amostras de dados se for necessário diretamente na [Importar dados] [ import-data] consulta.

9. Fluxo de experiência Azure máquina formação Simple, começando carregado conjunto de dados

## <a name="largedbtodb"></a>Cenário \#6: conjunto de dados grande num do SQL Server da base de dados no local, filtragem de SQL Server numa máquina de Virtual do Azure

![SQL grande BD no local para DB do SQL no Azure][6]

#### <a name="additional-azure-resources-azure-virtual-machine-sql-server--ipython-notebook-server"></a>Recursos adicionais de Azure: Azure Máquina Virtual (SQL Server / servidor IPython bloco de notas)

1.  Crie uma máquina de Virtual Azure a executar o SQL Server e server IPython bloco de notas.

2.  Utilize um dos dados exportar métodos para exportar os dados do SQL Server para ficheiros de informação.

    > [AZURE.NOTE] Se decidir mover todos os dados da base de dados no local, um método (mais rápido) alternativo para mover a base de dados completo para a instância do SQL Server no Azure. Ignore os passos para exportar os dados, criar a base de dados e carregar/importar dados para a base de dados de destino e siga o método alternativo.

3.  Carregar ficheiros de informação para o contentor de armazenamento Azure.

4.  Carrega os dados numa base de dados do SQL Server em execução numa máquina de Virtual do Azure.

    um.  Início de sessão para o SQL Server VM.

    b.  Transferir ficheiros de dados a partir de um contentor de armazenamento Azure para a pasta local VM.

    c.  Execute o SQL Server Management Studio.

    d.  Crie tabelas de base de dados e de destino.

    "e".  Utilize um em volume importar métodos para carregar os dados.

    f.  Se forem necessárias as associações de tabela, crie índices para acelerar associações.

    > [AZURE.NOTE] Para carregamento mais rápido de tamanhos de dados grandes, crie tabelas com partições e para em volume importe os dados em paralelo. Para mais informações, consulte o artigo [Paralelas importação de dados a tabelas divididos de SQL](machine-learning-data-science-parallel-load-sql-partitioned-tables.md).

5.  Explorar dados, criar funcionalidades conforme necessário. Tenha em atenção que as funcionalidades não necessita de ser verificada nas tabelas da base de dados. Tenha em atenção apenas a consulta necessária criá-los.

6.  Decida num tamanho dados de exemplo, se necessário e/ou pretendido.

7.  Inicie sessão no [Azure máquina de formação Studio](https://studio.azureml.net/).

8. Ler os dados diretamente a partir do SQL Server utilizando os [Importar dados] [ import-data] módulo. Cole a consulta necessária que extrai campos, cria funcionalidades e amostras de dados se for necessário diretamente na [Importar dados] [ import-data] consulta.

9. Formação de máquina Azure simples experimentar fluxo começando com conjunto de dados carregado.

### <a name="alternate-method-to-copy-a-full-database-from-an-on-premises--sql-server-to-azure-sql-database"></a>Método alternativo para copiar uma base de dados completa de um SQL Server no local para a base de dados do Azure SQL

![Desanexar local DB e anexar a DB do SQL no Azure][7]

#### <a name="additional-azure-resources-azure-virtual-machine-sql-server--ipython-notebook-server"></a>Recursos adicionais de Azure: Azure Máquina Virtual (SQL Server / servidor IPython bloco de notas)

Para criar uma réplica da base de dados do SQL Server toda a VM do SQL Server, deverá de copiar uma base de dados a partir de uma localização/servidor para outra, a partindo do princípio de que a base de dados pode ser colocada temporariamente offline. Pode fazê-lo no SQL Server Management Studio Object Explorer ou utilizando os comandos Transact-SQL equivalentes.

1. Desanexe a base de dados na localização de origem. Para mais informações, consulte o artigo [desanexar uma base de dados](https://technet.microsoft.com/library/ms191491(v=sql.110).aspx).
2. Na janela do Explorador do Windows ou comandos do Windows, copie o ficheiro de base de dados destaquem ou ficheiros e ficheiro de registo ou ficheiros para a localização de destino na VM de servidor do SQL no Azure.
3. Anexe os ficheiros copiados a instância do SQL Server de destino. Para mais informações, consulte o artigo [anexar uma base de dados](https://technet.microsoft.com/library/ms190209(v=sql.110).aspx).

[Mover uma base de dados utilizando desanexar e anexar (Transact-SQL)](https://technet.microsoft.com/library/ms187858(v=sql.110).aspx)

## <a name="largedbtohive"></a>Cenário \#7: grandes dados em ficheiros locais, ramo de base de dados no Azure HDInsight Hadoop clusters de destino

![Grandes dados no local destino ramo][9]

#### <a name="additional-azure-resources-azure-hdinsight-hadoop-cluster-and-azure-virtual-machine-ipython-notebook-server"></a>Recursos adicionais de Azure: Azure HDInsight Hadoop Cluster e Azure Virtual Machine (servidor de bloco de notas IPython)

1.  Crie uma máquina de Virtual Azure com o servidor IPython bloco de notas.

2.  Crie um cluster de Azure HDInsight Hadoop.

3.  (Opcional) Preparação do processo e limpar os dados.

    um.  Preparação do processo e limpar os dados no bloco de notas IPython, aceder a dados a partir do Azure blobs.

    b.  Transforma dados Limpar formato tabular, se necessário.

    c.  Guardar os dados para ficheiros de VM local (IPython bloco de notas está em execução no VM, unidades locais referem VM unidades).

4.  Carregue os dados para o contentor predefinido do cluster Hadoop seleccionado no passo 2.

5.  Carregar dados ramo de base de dados no Azure HDInsight Hadoop cluster.

    um.  Iniciar sessão no nó do Hadoop cluster cabeça

    b.  Abra a linha de comandos Hadoop.

    c.  Introduza o diretório de raiz ramo ao comando `cd %hive_home%\bin` na linha de comandos do Hadoop.

    d.  Execute as consultas Hive para criar a base de dados e tabelas e carregar os dados a partir do armazenamento de Blobs do ramo de tabelas.

    > [AZURE.NOTE] Se os dados forem grandes, os utilizadores podem criar a tabela Hive com a partições. Em seguida, os utilizadores podem utilizar um `for` ciclo no Hadoop linha de comandos no nó cabeça para carregar os dados para a tabela Hive dividida por partição.

6.  Explorar dados e criar funcionalidades conforme necessário na linha de comandos do Hadoop. Tenha em atenção que as funcionalidades não necessita de ser verificada nas tabelas da base de dados. Tenha em atenção apenas a consulta necessária criá-los.

    um.  Iniciar sessão no nó do Hadoop cluster cabeça

    b.  Abra a linha de comandos Hadoop.

    c.  Introduza o diretório de raiz ramo ao comando `cd %hive_home%\bin` na linha de comandos do Hadoop.

    d.  Execute as consultas de ramo na linha de comandos do Hadoop no cabeça nó do cluster Hadoop para explorar os dados e criar funcionalidades conforme necessário.

7.  Se for necessário e/ou pretendido, os dados para se ajustar no Azure máquina aprendizagem Studio de exemplo.

8.  Inicie sessão no [Azure máquina de formação Studio](https://studio.azureml.net/).

9. Ler os dados diretamente a partir do `Hive Queries` utilizando os [Importar dados] [ import-data] módulo. Cole a consulta necessária que extrai campos, cria funcionalidades e amostras de dados se for necessário diretamente na [Importar dados] [ import-data] consulta.

10. Formação de máquina Azure simples experimentar fluxo começando com conjunto de dados carregado.

## <a name="decisiontree"></a>Árvore de decisões de selecção de cenário
------------------------

O seguinte diagrama resume os cenários descritos acima e o processo de análise avançadas e opções de tecnologia feitas que levá-lo para cada um dos cenários por item. Tenha em atenção que poderão demorar processamento de dados, informações detalhadas, engenharia funcionalidade e amostragem colocar um ou mais método/ambiente - na origem, intermédia, e/ou ambientes de destino – e pode prosseguir iterativamente conforme necessário. O diagrama só funciona como uma ilustração de alguns dos fluxos de possíveis e não fornece uma enumeração exaustiva.

![Exemplo DS processo guiadas cenários][8]

### <a name="advanced-analytics-in-action-examples"></a>Advanced Analytics em ação exemplos

Para terminar-para-end Azure máquina aprendizagem instruções passo a passo que utilizam a tecnologia utilizando conjuntos de dados públicos e o processo de análise avançadas, consulte:


* [Processo de ciências de dados de equipa em ação: utilizar o SQL Server](machine-learning-data-science-process-sql-walkthrough.md).
* [Processo de ciências de dados de equipa em ação: utilizar clusters de HDInsight Hadoop](machine-learning-data-science-process-hive-walkthrough.md).


[1]: ./media/machine-learning-data-science-plan-sample-scenarios/dsp-plan-small-in-aml.png
[2]: ./media/machine-learning-data-science-plan-sample-scenarios/dsp-plan-local-with-processing.png
[3]: ./media/machine-learning-data-science-plan-sample-scenarios/dsp-plan-local-large.png
[4]: ./media/machine-learning-data-science-plan-sample-scenarios/dsp-plan-local-to-db.png
[5]: ./media/machine-learning-data-science-plan-sample-scenarios/dsp-plan-large-to-db.png
[6]: ./media/machine-learning-data-science-plan-sample-scenarios/dsp-plan-db-to-db.png
[7]: ./media/machine-learning-data-science-plan-sample-scenarios/dsp-plan-attach-db.png
[8]: ./media/machine-learning-data-science-plan-sample-scenarios/dsp-plan-sample-scenarios.png
[9]: ./media/machine-learning-data-science-plan-sample-scenarios/dsp-plan-local-to-hive.png


<!-- Module References -->
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
