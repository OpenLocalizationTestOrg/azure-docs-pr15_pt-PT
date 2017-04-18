<properties
    pageTitle="Notas de componentes de Hadoop no Azure HDInsight lançamento | Microsoft Azure"
    description="Notas de lançamento mais recentes e versões dos componentes de Hadoop para Azure HDInsight. Obter sugestões de desenvolvimento e detalhes para Hadoop, Apache tempestade e HBase."
    services="hdinsight"
    documentationCenter=""
    editor="cgronlun"
    manager="jhubbard"
    authors="nitinme"
    tags="azure-portal"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/27/2016"
    ms.author="nitinme"/>


# <a name="release-notes-for-hadoop-components-on-azure-hdinsight"></a>Notas de lançamento do Hadoop componentes no Azure HDInsight

## <a name="notes-for-10262016-release-of-r-server-on-hdinsight"></a>Notas de lançamento de 26/10/2016 na barra de R HDInsight

- Servidor de R na HDInsight cluster aprovisionamento foi optimizada.
- Servidor de R na HDInsight está agora disponível como HDInsight normal "R servidor" cluster tipo e já não instalado como uma aplicação HDInsight separada. O nó do limite e binários do servidor R são agora aprovisionados como parte de implementação de cluster de servidor de R. Isto melhora a velocidade e fiabilidade de aprovisionamento. Comparar modelo para servidor de R é atualizado em conformidade.
- Preço de tipo de cluster de servidor de R agora baseia preço camada padrão plus preço de sobretaxa R servidor. Camadas de Premium passará a estar reservada para às funcionalidades Premium do disponíveis através de tipos de cluster diferente e não serão utilizadas para tipo de cluster de servidor de R. Esta alteração não afeta eficaz preços do servidor de R, apenas altera a forma como os encargos são apresentados na fatura. Todos os clusters de servidor de R existentes irão continuar a trabalhar e modelos de processador vai continuar a funcionar até aviso descontinuação. **É recomendável apesar da atualizar as implementações em script para utilizar o novo modelo de processador.**


## <a name="notes-for-08302016-release-of-r-server-on-hdinsight"></a>Notas de lançamento de 30/08/2016 na barra de R HDInsight

Os números de versão completa para clusters baseado em Linux HDInsight implementados nesta versão:

|HDI |Versão de cluster HDI   |HDP |Compilar HDP   |Compilar Ambari |
|----|----------------------|----|------------|-------------|
|3,2 |3.2.1000.0.8268980    |2.2 |2.2.9.1-19  |2.2.1.12-4   |
|3.3 |3.3.1000.0.8268980    |2.3 |2.3.3.1-25  |2.2.1.12-4   |
|3.4. |3.4.1000.0.8269383    |2.4 |2.4.2.4-5   |2.2.1.12-4   |

Os números de versão completa para clusters baseados no Windows HDInsight implementados nesta versão:

|HDI |Versão de cluster HDI   |HDP |Compilar HDP     |
|----|----------------------|----|--------------|
|2.1 |2.1.10.1033.2559206   |1.3 |1.3.12.0-01795|
|3.0 |3.0.6.1033.2559206    |2.0 |2.0.13.0-2117 |
|3.1 |3.1.4.1033.2559206    |2.1 |2.1.16.0-2374 |
|3,2 |3.2.7.1033.2559206    |2.2 |2.2.9.1-11    |
|3.3 |3.3.0.1033.2559206    |2.3 |2.3.3.1-25    |

## <a name="notes-for-08172016-release-of-r-server-on-hdinsight"></a>Notas de lançamento 17/08/2016 na barra de R HDInsight

- R Server 8.0.5 – principalmente um lançamento de corrigir erros. Veja as [Notas de lançamento do servidor de R](https://msdn.microsoft.com/microsoft-r/notes/r-server-notes) para obter mais informações. 
- Invólucro AzureML o nó do limite – [Este pacote R](https://cran.r-project.org/web/packages/AzureML/vignettes/getting_started.html) permite que os modelos de R ser publicado e consumidas como um serviço web do Azure ML.  Consulte a secção ["Operationalize um modelo de"](hdinsight-hadoop-r-server-overview.md#operationalize-a-model) nosso artigo ["Descrição geral de R Server no HDInsight"](hdinsight-hadoop-r-server-overview.md) para obter mais informações.
- Dependências de Linux dos [superiores pacotes R 100 mais populares](https://github.com/metacran/cranlogs) – estas Linux dependências de pacote agora previamente instaladas.  
- Opção para utilizar o repo CRAN quando adicionar R pacotes para os nós de dados. Consulte a secção ["Pacotes de instalação R"](hdinsight-hadoop-r-server-get-started.md#install-r-packages) nosso artigo ["Começar a utilizar o servidor de R na HDInsight"](hdinsight-hadoop-r-server-get-started.md) para obter mais informações.
- Melhorado a fiabilidade dos R servidor de aprovisionamento quando são criados clusters.


## <a name="notes-for-08012016-release-of-hdinsight"></a>Notas de versão de 08/01/2016 do HDInsight

Os números de versão completa para clusters baseado em Linux HDInsight implementados nesta versão:

|HDI |Versão de cluster HDI   |HDP |Compilar HDP   |Compilar Ambari |
|----|----------------------|----|------------|-------------|
|3,2 |3.2.1000.0.8028416    |2.2 |2.2.9.1-19  |2.2.1.12-4   |
|3.3 |3.3.1000.0.8028416    |2.3 |2.3.3.1-25  |2.2.1.12-4   |
|3.4. |3.4.1000.0.8053402    |2.4 |2.4.2.4-5   |2.2.1.12-4   |

Os números de versão completa para clusters baseados no Windows HDInsight implementados nesta versão:

|HDI |Versão de cluster HDI   |HDP |Compilar HDP     |
|----|----------------------|----|--------------|
|2.1 |2.1.10.1005.2488842   |1.3 |1.3.12.0-01795|
|3.0 |3.0.6.1005.2488842    |2.0 |2.0.13.0-2117 |
|3.1 |3.1.4.1005.2488842    |2.1 |2.1.16.0-2374 |
|3,2 |3.2.7.1005.2488842    |2.2 |2.2.9.1-11    |
|3.3 |3.3.0.1005.2488842    |2.3 |2.3.3.1-25    |

Nesta versão contém as seguintes atualizações.

| Título                                           | Descrição                                          | Área afetada (por exemplo, serviço, componente ou SDK) | Tipo de cluster (por exemplo motores, Hadoop, HBase ou tempestade) | JIRA (se aplicável) |
|-------------------------------------------------|------------------------------------------------------|---------------------------------------------------------|-----------------------------------------------------|----------------------|
| Alterações a HDInsight 3.4 clusters | O valor predefinido para configurações ramo seguintes são alteradas para um melhor desempenho <ul><li>`hive.vectorized.execution.reduce.enabled=true`</li><li>`hive.tez.min.partition.factor=1f`</li><li>`hive.tez.max.partition.factor=3f`</li><li>`tez.shuffle-vertex-manager.min-src-fraction=0.9`</li><li>`tez.shuffle-vertex-manager.max-src-fraction=0.95`</li><li>`tez.runtime.shuffle.connect.timeout= 30000`</li></ul>| Serviço    | Todos os| N/D|
| Correções seguintes estão incluídas nesta versão | RAMO 13632, HIVE-12897,HIVE-12907,HIVE-12908,HIVE-12988,HIVE-13510,HIVE-13572,HIVE-13716,HIVE-13726,HIVE-12505,HIVE-13632,HIVE-13661,HIVE-13705,HIVE-13743,HIVE-13810,HIVE-13857,HIVE-13902,HIVE-13911,HIVE-13933| Serviço    | Todos os| N/D

## <a name="notes-for-07142016-release-of-hdinsight"></a>Notas de versão de 14/07/2016 do HDInsight

Os números de versão completa para clusters baseado em Linux HDInsight implementados nesta versão:

|HDI |Versão de cluster HDI   |HDP |Compilar HDP   |Compilar Ambari |
|----|----------------------|----|------------|-------------|
|3,2 |3.2.1000.0.7932505    |2.2 |2.2.9.1-11  |2.2.1.12-2   |
|3.3 |3.3.1000.0.7932505    |2.3 |2.3.3.1-18  |2.2.1.12-2   |
|3.4. |3.4.1000.0.7933003    |2.4 |2.4.2.0     |2.2.1.12-2   |

Os números de versão completa para clusters baseados no Windows HDInsight implementados nesta versão:

|HDI |Versão de cluster HDI   |HDP |Compilar HDP     |
|----|----------------------|----|--------------|
|2.1 |2.1.10.989.2441725    |1.3 |1.3.12.0-01795|
|3.0 |3.0.6.989.2441725     |2.0 |2.0.13.0-2117 |
|3.1 |3.1.4.989.2441725     |2.1 |2.1.16.0-2374 |
|3,2 |3.2.7.989.2441725     |2.2 |2.2.9.1-11    |
|3.3 |3.3.0.989.2441725     |2.3 |2.3.3.1-21    |

## <a name="notes-for-07072016-release-of-hdinsight"></a>Notas de versão de 07/07/2016 do HDInsight

Os números de versão completa para clusters baseado em Linux HDInsight implementados nesta versão:

|HDI |Versão de cluster HDI   |HDP |Compilar HDP   |
|----|----------------------|----|------------|
|3,2 |3.2.1000.0.7864996    |2.2 |2.2.9.1-11  |
|3.3 |3.3.1000.0.7864996    |2.3 |2.3.3.1-18  |
|3.4. |3.4.1000.0.7861906    |2.4 |2.4.2.0     |

Os números de versão completa para clusters baseados no Windows HDInsight implementados nesta versão:

|HDI |Versão de cluster HDI   |HDP |Compilar HDP     |
|----|----------------------|----|--------------|
|2.1 |2.1.10.977.2413853    |1.3 |1.3.12.0-01795|
|3.0 |3.0.6.977.2413853     |2.0 |2.0.13.0-2117 |
|3.1 |3.1.4.977.2413853     |2.1 |2.1.16.0-2374 |
|3,2 |3.2.7.977.2413853     |2.2 |2.2.9.1-11    |
|3.3 |3.3.0.977.2413853     |2.3 |2.3.3.1-21    |

Nesta versão contém as seguintes atualizações.

| Título                                           | Descrição                                          | Área afetada (por exemplo, serviço, componente ou SDK) | Tipo de cluster (por exemplo motores, Hadoop, HBase ou tempestade) | JIRA (se aplicável) |
|-------------------------------------------------|------------------------------------------------------|---------------------------------------------------------|-----------------------------------------------------|----------------------|
| [Ferramentas de HDInsight para IntelliJ](hdinsight-apache-spark-intellij-tool-plugin.md) | Plug-in do IntelliJ IDEIA para HDInsight motores clusters agora está integrado com o Azure Toolkit para IntelliJ. Suporta Azure SDK v2.9.1, SDK Java mais recente e inclui todas as funcionalidades do plug-in do HDInsight para IntelliJ autónomo.| Ferramentas    | Motores| N/D|
| [Ferramentas de HDInsight para Eclipse](hdinsight-apache-spark-eclipse-tool-plugin.md) | Toolkit Azure para Eclipse suporta agora clusters de motores de HDInsight. Permite-as seguintes funcionalidades. <ul><li>Criar e escrever uma aplicação de motores facilmente em Scala e Java com primeira classe criação de suporte para o IntelliSense, formatação automática, erros, etc.</li><li>Teste a aplicação de motores localmente.</li><li>Submeter a cluster HDInsight motores de tarefas e obter os resultados.</li><li>Inicie sessão no Azure e aceder a todos os clusters de motores associados com as suas subscrições Azure.</li><li>Navegue até todos os recursos associadas de armazenamento do seu cluster de motores de HDInsight.</li></ul>| Ferramentas    | Motores| N/D

Começando nesta versão, podemos alterou a política de patch do SO convidado para clusters baseado em Linux HDInsight. O objetivo da nova política é significativamente reduzir o número de ser reiniciado devido a aplicação de patches. A nova política irão continuar a máquinas virtuais do patches (VMs) em Linux clusters cada segunda-feira ou Quinta-feira, começando na 12: 00 UTC de uma forma escalonada em nós de qualquer cluster determinado. No entanto, qualquer VM determinado só será reiniciado pelo mais uma vez a cada 30 dias devido a aplicação de SO patches convidado. Além disso, o primeiro reinício para um cluster recentemente criado não irá acontecer mais cedo de 30 dias a partir da data de criação de cluster.

>[AZURE.NOTE] Estas alterações só serão aplicadas a clusters recentemente criados iguais ou maiores que esta versão de lançamento.

## <a name="notes-for-06062016-release-of-hdinsight"></a>Notas de versão de 06/06/2016 do HDInsight

Os números de versão completa para clusters HDInsight implementados nesta versão:

|HDP    |Versão HDI    |Motores de versão  |Número de compilação Ambari    |Número de compilação HDP|
|-------|---------------|---------------|-----------------------|----------------|
|2.3    |3.3.1000.0.7702215|    1.5.2|  2.2.1.8-2|  2.3.3.1-18|
|2.4    |3.4.1000.0.7702224|    1.6.1|  2.2.1.8-2|  2.4.2.0|


Nesta versão contém as seguintes atualizações.

| Título                                           | Descrição                                          | Área afetada (por exemplo, serviço, componente ou SDK) | Tipo de cluster (por exemplo motores, Hadoop, HBase ou tempestade) | JIRA (se aplicável) |
|-------------------------------------------------|------------------------------------------------------|---------------------------------------------------------|-----------------------------------------------------|----------------------|
| Motores no HDInsight são geralmente disponíveis | Nesta versão mostra melhorias na disponibilidade, escalabilidade e produtividade para abrir origem Apache motores no HDInsight. <ul><li>Indústria levando disponibilidade SLA de 99,9%, que torna adequado para exigem das cargas de trabalho do enterprise.</li><li>Camada de armazenamento dimensionáveis utilizar Azure dados Lake arquivo.</li><li>Ferramentas de produtividade para cada fase de exploração de dados e desenvolvimento. Jupyter blocos de notas com personalizada kernel de motores activar informações detalhadas de dados interativos, integração com dashboards BI como o Power BI, quadro e Qlik é boa ideia para partilha rápida de dados e relatórios contínuo, plug-in do IntelliJ é a opção fiável para desenvolvimento a longo prazo código artefacto e depuração.</li></ul>| Serviço    | Motores| N/D|
| Ferramentas de HDInsight para IntelliJ | Este é um plug-in do IntelliJ IDEIA para clusters de motores de HDInsight. Permite-as seguintes funcionalidades.<ul><li>Criar e escrever uma aplicação de motores facilmente em Scala e Java com primeira classe criação de suporte para o IntelliSense, formatação automática, erros, etc.</li><li>Teste a aplicação de motores localmente.</li><li>Submeter a cluster HDInsight motores de tarefas e obter os resultados.</li><li>Inicie sessão no Azure e aceder a todos os clusters de motores associados com as suas subscrições Azure.</li><li>Navegue até todos os recursos associadas de armazenamento do seu cluster de motores de HDInsight.</li><li>Navegue até todas as histórico de tarefas e informações sobre a tarefa para o seu cluster de motores de HDInsight.</li><li>Depurar tarefas motores remotamente a partir do computador de secretária.</li></ul>| Ferramentas    | Motores| N/D

## <a name="notes-for-05132016-release-of-hdinsight"></a>Notas de versão de 13/05/2016 do HDInsight

Os números de versão completa para clusters HDInsight implementados nesta versão:

* HDInsight (Windows) 2.1.10.875.2159884 (HDP 1.3.12.0-01795 - inalterada)
* HDInsight (Windows) 3.0.6.875.2159884 (HDP 2.0.13.0-2117 - inalterada)
* HDInsight (Windows) 3.1.4.922.2266903 (HDP 2.1.15.0-2374 - inalterada)
* HDInsight (Windows) 3.2.7.922.2266903 (HDP 2.2.9.1-11)
* HDInsight (Windows) 3.3.0.922.2266903 (HDP 2.3.3.1-18)
* HDInsight (Linux) 3.2.1000.0.7565644 (HDP 2.2.9.1-11)
* HDInsight (Linux) 3.3.1000.0.7565644 (HDP 2.3.3.1-18)
* HDInsight (Linux) 3.4.1000.0.7548380 (HDP 2.4.2.0)

Nesta versão contém as seguintes atualizações.

| Título                                           | Descrição                                          | Área afetada (por exemplo, serviço, componente ou SDK) | Tipo de cluster (por exemplo motores, Hadoop, HBase ou tempestade) | JIRA (se aplicável) |
|-------------------------------------------------|------------------------------------------------------|---------------------------------------------------------|-----------------------------------------------------|----------------------|
| Gerar actualização de versão e outras correções de erros | Nesta versão actualiza a versão de motores num cluster de HDInsight para 1.6.1 e correções outros erros| Serviço    | Motores| N/D

## <a name="notes-for-04112016-release-of-hdinsight"></a>Notas de versão de 04/11/2016 do HDInsight

Os números de versão completa para clusters HDInsight implementados nesta versão:

* HDInsight (Windows) 2.1.10.889.2191206 (HDP 1.3.12.0-01795 - inalterada)
* HDInsight (Windows) 3.0.6.889.2191206 (HDP 2.0.13.0-2117 - inalterada)
* HDInsight (Windows) 3.1.4.889.2191206 (HDP 2.1.15.0-2374 - inalterada)
* HDInsight (Windows) 3.2.7.889.2191206 (HDP 2.2.9.1-10)
* HDInsight (Windows) 3.3.0.889.2191206 (HDP 2.3.3.1-16-inalterado)
* HDInsight (Linux) 3.2.1000.0.7339916 (HDP 2.2.9.1-10)
* HDInsight (Linux) 3.3.1000.0.7339916 (HDP 2.3.3.1-16)
* HDInsight (Linux) 3.4.1000.0.7338911 (HDP 2.4.1.1-3)
* SDK 1.5.8

Nesta versão contém as seguintes atualizações.

| Título                                           | Descrição                                          | Área afetada (por exemplo, serviço, componente ou SDK) | Cluster tipo (por exemplo, Hadoop, HBase ou tempestade) | JIRA (se aplicável) |
|-------------------------------------------------|------------------------------------------------------|---------------------------------------------------------|-----------------------------------------------------|----------------------|
| Problemas de atualização de metastore personalizado para HDI 3.4 | Se utilizou um metastore personalizado, que foi utilizada anteriormente numa versão anterior de outro cluster de HDInsight a criação de cluster seria falhar. Trata devido a um erro de script atualização que agora foram corrigido| Criação de cluster    | Todos os | N/D
| Recuperação de falhas Lívio | Fornece RDP de estado da tarefa para qualquer tarefa submetida através de Lívio | Fiabilidade | Motores em Linux| N/D
| Conteúdo Jupyter HA | Fornece a capacidade de guardar e carregar o conteúdo do bloco de notas de Jupyter de e para a conta de armazenamento associado ao cluster. Para mais informações, consulte o artigo [Kernels disponíveis para Jupyter blocos de notas](hdinsight-apache-spark-jupyter-notebook-kernels.md).| Blocos de notas | Motores em Linux| N/D
| Remoção da hiveContext nos Jupter blocos de notas | Utilizar `%%sql` mágico em vez de `%%hive` mágico. SqlContext é equivalente ao hiveContext. Para obter mais informações, consulte o artigo [Kernels disponíveis para Jupyter blocos de notas](hdinsight-apache-spark-jupyter-notebook-kernels.md)| Blocos de notas    | Clusters de motores Linux| N/D
| Descontinuação de versões mais antigas do motores | Versão mais antiga motores 1.3.1 será removido do serviço no 5/31 | Serviço | Motores clusters no Windows | N/D

## <a name="notes-for-03292016-release-of-hdinsight"></a>Notas de versão de 29/03/2016 do HDInsight

Os números de versão completa para clusters HDInsight implementados nesta versão:

* HDInsight (Windows) 2.1.10.875.2159884 (HDP 1.3.12.0-01795 - inalterada)
* HDInsight (Windows) 3.0.6.875.2159884 (HDP 2.0.13.0-2117 - inalterada)
* HDInsight (Windows) 3.1.4.875.2159884 (HDP 2.1.15.0-2374 - inalterada)
* HDInsight (Windows) 3.2.7.875.2159884 (HDP 2.2.9.1-7 - inalterada)
* HDInsight (Windows) 3.3.0.875.2159884 (HDP 2.3.3.1-16)
* HDInsight (Linux) 3.2.1000.0.7193255 (HDP 2.2.9.1-8 - inalterada)
* HDInsight (Linux) 3.3.1000.0.7193255 (HDP 2.3.3.1-7 - inalterada)
* HDInsight (Linux) 3.4.1000.0.7195842 (HDP 2.4.1.0-327)
* SDK 1.5.8

Nesta versão contém as seguintes atualizações.

| Título                                           | Descrição                                          | Área afetada (por exemplo, serviço, componente ou SDK) | Cluster tipo (por exemplo, Hadoop, HBase ou tempestade) | JIRA (se aplicável) |
|-------------------------------------------------|------------------------------------------------------|---------------------------------------------------------|-----------------------------------------------------|----------------------|
| Adicionado HDInsight 3.4 versão atualizadas HDP versões e para todos os clusters HDInsight | Com esta versão, ter adicionado v3.4 HDInsight (com base na HDP 2.4) e também os tiverem sido atualizados de outras versões HDP. Notas de lançamento HDP 2.4 estão disponíveis [aqui](http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.4.0/bk_HDP_RelNotes/content/ch_relnotes_v240.html) e obter mais informações sobre HDInsight versões podem ser encontrado [aqui](hdinsight-component-versioning.md).| Serviço    | Todos os Linux clusters| N/D
| HDInsight Premium | HDInsight está agora disponível em duas categorias - padrão e Premium. HDInsight Premium está atualmente na pré-visualização e disponível apenas para Hadoop e motores clusters no Linux. Para mais informações consulte [aqui](hdinsight-component-versioning.md#hdinsight-standard-and-hdinsight-premium).| Serviço    | Hadoop e motores em Linux| N/D
| Servidor Microsoft R | HDInsight Premium fornece Microsoft R Server que podem ser incluídas com Hadoop e motores clusters no Linux. Para obter mais informações consulte o artigo [Descrição geral de R Server no HDInsight](hdinsight-hadoop-r-server-overview.md).| Serviço    | Hadoop e motores em Linux| N/D
| Motores 1.6.0 | HDInsight 3.4 clusters incluem agora motores 1.6.0| Serviço    | Clusters de motores Linux| N/D
| Melhoramentos de bloco de notas Jupyter | Jupyter blocos de notas disponíveis com clusters de motores fornecem motores adicionais kernels. Também incluem melhoramentos como a utilização da % mágico, visualização automática e integração com bibliotecas de visualização Python (como matplotlib). Para mais informações, consulte o artigo [Kernels disponíveis para Jupyter blocos de notas](hdinsight-apache-spark-jupyter-notebook-kernels.md). | Serviço | Clusters de motores Linux | N/D

## <a name="notes-for-03222016-release-of-hdinsight"></a>Notas de versão de 22/03/2016 do HDInsight

Os números de versão completa para clusters HDInsight implementados nesta versão:

* HDInsight (Windows) 2.1.10.875.2159884 (HDP 1.3.12.0-01795 - inalterada)
* HDInsight (Windows) 3.0.6.875.2159884 (HDP 2.0.13.0-2117 - inalterada)
* HDInsight (Windows) 3.1.4.875.2159884 (HDP 2.1.15.0-2374 - inalterada)
* HDInsight (Windows) 3.2.7.875.2159884 (HDP 2.2.9.1-7 - inalterada)
* HDInsight (Windows) 3.3.0.875.2159884 (HDP 2.3.3.1-16)
* HDInsight (Linux) 3.2.1000.0.7193255 (HDP 2.2.9.1-8 - inalterada)
* HDInsight (Linux) 3.3.1000.0.7193255 (HDP 2.3.3.1-7 - inalterada)
* SDK 1.5.8

Nesta versão contém as seguintes atualizações.

| Título                                           | Descrição                                          | Área afetada (por exemplo, serviço, componente ou SDK) | Cluster tipo (por exemplo, Hadoop, HBase ou tempestade) | JIRA (se aplicável) |
|-------------------------------------------------|------------------------------------------------------|---------------------------------------------------------|-----------------------------------------------------|----------------------|
| Versões de HDInsight atualizados para todos os clusters HDInsight | Com esta versão, podemos os tiverem sido atualizados HDInsight versões para todos os clusters HDInsight| Serviço    | Todos os| N/D


## <a name="notes-for-03102016-release-of-hdinsight"></a>Notas de versão de 10/03/2016 do HDInsight

Os números de versão completa para clusters HDInsight implementados nesta versão:

* HDInsight (Windows) 2.1.10.859.2123216 (HDP 1.3.12.0-01795 - inalterada)
* HDInsight (Windows) 3.0.6.859.2123216 (HDP 2.0.13.0-2117 - inalterada)
* HDInsight (Windows) 3.1.4.859.2123216 (HDP 2.1.15.0-2374 - inalterada)
* HDInsight (Windows) 3.2.7.859.2123216 (HDP 2.2.9.1-7)
* HDInsight (Windows) 3.3.0.859.2123216 (HDP 2.3.3.1-5 - inalterada)
* HDInsight (Linux) 3.2.1000.7076817 (HDP 2.2.9.1-8)
* HDInsight (Linux) 3.3.1000.7076817 (HDP 2.3.3.1-7)
* SDK 1.5.8

Nesta versão contém as seguintes atualizações.

| Título                                           | Descrição                                          | Área afetada (por exemplo, serviço, componente ou SDK) | Cluster tipo (por exemplo, Hadoop, HBase ou tempestade) | JIRA (se aplicável) |
|-------------------------------------------------|------------------------------------------------------|---------------------------------------------------------|-----------------------------------------------------|----------------------|
| Versões de HDInsight atualizados para todos os clusters HDInsight | Com esta versão, podemos os tiverem sido atualizados HDInsight versões para todos os clusters HDInsight| Serviço    | Todos os| N/D

## <a name="notes-for-01272016-release-of-hdinsight"></a>Notas de versão de 27/01/2016 do HDInsight

Os números de versão completa para clusters HDInsight implementados nesta versão:

* HDInsight (Windows) 2.1.10.817.2028315 (HDP 1.3.12.0-01795 - inalterada)
* HDInsight (Windows) 3.0.6.817.2028315 (HDP 2.0.13.0-2117 - inalterada)
* HDInsight (Windows) 3.1.4.817.2028315 (HDP 2.1.15.0-2374 - inalterada)
* HDInsight (Windows) 3.2.7.817.2028315 (HDP 2.2.9.1-1)
* HDInsight (Windows) 3.3.0.817.2028315 (HDP 2.3.3.1-5 - inalterada)
* HDInsight (Linux) 3.2.1000.4072335 (HDP 2.2.9.1-1)
* HDInsight (Linux) 3.3.1000.4072335 (HDP 2.3.3.1-1)
* SDK 1.5.8

Nesta versão contém as seguintes atualizações.

| Título                                           | Descrição                                          | Área afetada (por exemplo, serviço, componente ou SDK) | Cluster tipo (por exemplo, Hadoop, HBase ou tempestade) | JIRA (se aplicável) |
|-------------------------------------------------|------------------------------------------------------|---------------------------------------------------------|-----------------------------------------------------|----------------------|
| Versões de HDInsight atualizados para todos os clusters HDInsight | Com esta versão, podemos os tiverem sido atualizados HDInsight versões para todos os clusters HDInsight| Serviço    | Todos os| N/D

## <a name="notes-for-12022015-release-of-hdinsight"></a>Notas de versão de 02/12/2015 do HDInsight

Os números de versão completa para clusters HDInsight implementados nesta versão:

* HDInsight (Windows) 2.1.10.763.1931434 (HDP 1.3.12.0-01795 - inalterada)
* HDInsight (Windows) 3.0.6.763.1931434 (HDP 2.0.13.0-2117 - inalterada)
* HDInsight (Windows) 3.1.4.763.1931434 (HDP 2.1.15.0-2374 - inalterada)
* HDInsight (Windows) 3.2.7.763.1931434 (HDP 2.2.7.1-34 - inalterada)
* HDInsight (Windows) 3.3.1000.0 (HDP 2.3.3.1-5)
* HDInsight (Linux) 3.2.1000.0.6392801 (HDP 2.2.7.1-34 - inalterada)
* HDInsight (Linux) 3.3.1000.0 (HDP 2.3.3.0-3039)
* SDK 1.5.8

Nesta versão contém as seguintes atualizações.

| Título                                           | Descrição                                          | Área afetada (por exemplo, serviço, componente ou SDK) | Cluster tipo (por exemplo, Hadoop, HBase ou tempestade) | JIRA (se aplicável) |
|-------------------------------------------------|------------------------------------------------------|---------------------------------------------------------|-----------------------------------------------------|----------------------|
| Adicionado HDInsight 3.3 versão atualizadas HDP versões e para todos os clusters HDInsight | Com esta versão, ter adicionado v3.3 HDInsight (com base na HDP 2.3) e também os tiverem sido atualizados de outras versões HDP. Notas sobre a versão HDP 2.3 está disponíveis [aqui](http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.3.0/bk_HDP_RelNotes/content/ch_relnotes_v230.html) e obter mais informações sobre HDInsight versões podem ser encontrado [aqui](hdinsight-component-versioning.md).| Serviço    | Todos os| N/D

## <a name="notes-for-11302015-release-of-hdinsight"></a>Notas de versão de 30/11/2015 do HDInsight

Os números de versão completa para clusters HDInsight implementados nesta versão:

* HDInsight (Windows) 2.1.10.757.1923908 (HDP 1.3.12.0-01795 - inalterada)
* HDInsight (Windows) 3.0.6.757.1923908 (HDP 2.0.13.0-2117 - inalterada)
* HDInsight (Windows) 3.1.4.757.1923908 (HDP 2.1.15.0-2374 - inalterada)
* HDInsight (Windows) 3.2.7.757.1923908 (HDP 2.2.7.1-34)
* HDInsight (Linux) 3.2.1000.0.6392801 (HDP 2.2.7.1-34)
* SDK 1.5.8

Nesta versão contém as seguintes atualizações.

| Título                                           | Descrição                                          | Área afetada (por exemplo, serviço, componente ou SDK) | Cluster tipo (por exemplo, Hadoop, HBase ou tempestade) | JIRA (se aplicável) |
|-------------------------------------------------|------------------------------------------------------|---------------------------------------------------------|-----------------------------------------------------|----------------------|
| Versões de HDInsight atualizados para todas as versões HDP para HDInsight 3,2 clusters (Windows e Linux) e HDInsight clusters | Com esta versão, versões HDInsight e HDP tiverem sido atualizadas | Serviço    | Todos os| N/D


## <a name="notes-for-10272015-release-of-hdinsight"></a>Notas de versão de 27/10/2015 do HDInsight

Os números de versão completa para clusters HDInsight implementados nesta versão:

* HDInsight (Windows) 2.1.10.726.1866228 (HDP 1.3.12.0-01795 - inalterada)
* HDInsight (Windows) 3.0.6.726.1866228 (HDP 2.0.13.0-2117 - inalterada)
* HDInsight (Windows) 3.1.4.726.1866228 (HDP 2.1.15.0-2374 - inalterada)
* HDInsight (Windows) 3.2.7.726.1866228 (HDP 2.2.7.1-33)
* HDInsight (Linux) 3.2.1000.0.6035701 (HDP 2.2.7.1-33)
* SDK 1.5.8

Nesta versão contém as seguintes atualizações.

| Título                                           | Descrição                                          | Área afetada (por exemplo, serviço, componente ou SDK) | Cluster tipo (por exemplo, Hadoop, HBase ou tempestade) | JIRA (se aplicável) |
|-------------------------------------------------|------------------------------------------------------|---------------------------------------------------------|-----------------------------------------------------|----------------------|
| Versões de HDInsight atualizados para todos os HDInsight clusters (Windows e Linux) | Com esta versão, versões HDInsight e HDP tiverem sido atualizadas | Serviço    | Todos os| N/D
| Fixos clusters de motores de Jupyter para Windows com clusters de letras maiúsculas | Clusters que tinha nomes DNS especificados em maiúsculas tinham problemas com blocos de notas Jupyter devido a uma verificação de pedido de origem. A fix foi alterar o nome DNS para a configuração do Jupyter para minúsculas. | Serviço    | Motores de HDInsight (Windows)| N/D


## <a name="notes-for-10202015-release-of-hdinsight"></a>Notas para 20/10/2015 do lançamento do HDInsight

Os números de versão completa para clusters HDInsight implementados nesta versão:

* HDInsight 2.1.10.716.1846990 (Windows) (HDP 1.3.12.0-01795 - inalterada)
* HDInsight 3.0.6.716.1846990 (Windows) (HDP 2.0.13.0-2117 - inalterada)
* HDInsight 3.1.4.716.1846990 (Windows) (HDP 2.1.16.0-2374)
* HDInsight 3.2.7.716.1846990 (Windows) (HDP 2.2.7.1-0004)
* HDInsight 3.2.1000.0.5930166 (Linux) (HDP 2.2.7.1-0004)
* SDK 1.5.8

Nesta versão contém as seguintes atualizações.

| Título                                           | Descrição                                          | Área afetada (por exemplo, serviço, componente ou SDK) | Cluster tipo (por exemplo, Hadoop, HBase ou tempestade) | JIRA (se aplicável) |
|-------------------------------------------------|------------------------------------------------------|---------------------------------------------------------|-----------------------------------------------------|----------------------|
| Versão HDP predefinida alterada para HDP 2.2 | A versão predefinida para HDInsight Windows clusters é alterada para HDP 2.2. HDInsight versão 3,2 (HDP 2.2) foi ficará disponível no desde de Fevereiro de 2015. Esta alteração apenas Inverte a versão de cluster predefinida, quando não tiver sido feita uma seleção explícita enquanto o cluster utilizando o portal do Azure, os cmdlets do PowerShell ou o SDK de aprovisionamento. | Serviço    | Todos os| N/D                  |
|Alterações no formato de nome VM para implementar o HDInsight vários em Linux clusters numa única rede Virtual | Suporte para implementar vários HDInsight Linux clusters numa única rede virtual está a ser adicionado nesta versão. Como parte disto, o formato de nomes de máquina virtual no cluster foi alterado do headnode\*, workernode\* e zookeepernode\* para hn\*, wn\*e zk\* respetivamente. Não é uma prática recomendada para manter uma dependência direta o formato de nomes de máquina virtual, uma vez que esta está sujeito a alterações. Utilize "nome do anfitrião -f" no computador local ou Ambari APIs para determinar a lista de anfitriões e o mapeamento de componentes para anfitriões. Pode encontrar mais informações no [https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/hosts.md](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/hosts.md) e [https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/host-components.md](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/host-components.md). | Serviço | HDInsight clusters em Linux | N/D |
| Alterações à configuração | Para clusters HDInsight 3.1, as seguintes configurações agora estão ativadas: <ul><li>tez.yarn.ATS.Enabled e yarn.log.server.url. Isto permite o servidor de linha cronológica da aplicação e o registo de servidor possam servir saída registos.</li></ul>Para clusters de 3,2 HDInsight, foram modificadas configurações que se seguem: <ul><li>mapreduce.fileoutputcommitter.Algorithm.version foi definida como 2. Isto permite a utilização da versão V2 da FileOutputCommitter.</li></ul> | Serviço | Todos os | N/D |


## <a name="notes-for-09092015-release-of-hdinsight"></a>Notas de versão de 09/09/2015 do HDInsight

Os números de versão completa para clusters HDInsight implementados nesta versão:

* HDInsight 2.1.10.675.1768697 (HDP 1.3.12.0-01795 - inalterada)
* HDInsight 3.0.6.675.1768697 (HDP 2.0.13.0-2117 - inalterada)
* HDInsight 3.1.4.675.1768697 (HDP 2.1.15.0-2334 - inalterada)
* HDInsight 3.2.6.675.1768697 (HDP 2.2.6.1-0012 - inalterada)
* SDK 1.5.8

Nesta versão contém as seguintes atualizações.

| Título                                           | Descrição                                          | Área afetada (por exemplo, serviço, componente ou SDK) | Cluster tipo (por exemplo, Hadoop, HBase ou tempestade) | JIRA (se aplicável) |
|-------------------------------------------------|------------------------------------------------------|---------------------------------------------------------|-----------------------------------------------------|----------------------|
| Versões de HDInsight atualizados para todos os clusters HDInsight | Com esta versão, HDInsight versões tiverem sido atualizados | Serviço    | Todos os| N/D                  |

## <a name="notes-for-07312015-release-of-hdinsight"></a>Notas de versão de 31/07/2015 do HDInsight

Os números de versão completa para clusters HDInsight implementados nesta versão:

* HDInsight 2.1.10.640.1695824 (HDP 1.3.12.0-01795 - inalterada)
* HDInsight 3.0.6.640.1695824 (HDP 2.0.13.0-2117 - inalterada)
* HDInsight 3.1.4.640.1695824 (HDP 2.1.15.0-2334 - inalterada)
* HDInsight 3.2.6.640.1695824 (HDP 2.2.6.1-0012 - inalterada)
* SDK 1.5.8

Nesta versão contém as seguintes atualizações.

| Título                                           | Descrição                                          | Área afetada (por exemplo, serviço, componente ou SDK) | Cluster tipo (por exemplo, Hadoop, HBase ou tempestade) | JIRA (se aplicável) |
|-------------------------------------------------|------------------------------------------------------|---------------------------------------------------------|-----------------------------------------------------|----------------------|
| Corrigir motores fluxo de trabalho novamente imagens cluster nó | Fixa um erro que estava a causar motores nós do cluster para não recuperar após novamente imagem | Serviço    | Motores| N/D                  |


## <a name="notes-for-07312015-release-of-hdinsight"></a>Notas de versão de 31/07/2015 do HDInsight

Os números de versão completa para clusters HDInsight implementados nesta versão:

* HDInsight 2.1.10.635.1684502 (HDP 1.3.12.0-01795 - inalterada)
* HDInsight 3.0.6.635.1684502 (HDP 2.0.13.0-2117 - inalterada)
* HDInsight 3.1.4.635.1684502 (HDP 2.1.15.0-2334 - inalterada)
* HDInsight 3.2.6.635.1684502 (HDP 2.2.6.1-0012 - inalterada)
* SDK 1.5.8

Nesta versão contém as seguintes atualizações.

| Título                                           | Descrição                                          | Área afetada (por exemplo, serviço, componente ou SDK) | Cluster tipo (por exemplo, Hadoop, HBase ou tempestade) | JIRA (se aplicável) |
|-------------------------------------------------|------------------------------------------------------|---------------------------------------------------------|-----------------------------------------------------|----------------------|
| Versões de HDInsight atualizados para todos os clusters HDInsight | Com esta versão, HDInsight versões tiverem sido atualizados | Serviço    | Todos os| N/D                  |


## <a name="notes-for-07072015-release-of-hdinsight"></a>Notas de versão de 07/07/2015 do HDInsight

Os números de versão completa para clusters HDInsight implementados nesta versão:

* HDInsight 2.1.10.610.1630216 (HDP 1.3.12.0-01795 - inalterada)
* HDInsight 3.0.6.610.1630216 (HDP 2.0.13.0-2117 - inalterada)
* HDInsight 3.1.4.610.1630216 (HDP 2.1.15.0-2334 - inalterada)
* HDInsight 3.2.4.610.1630216 (HDP 2.2.6.1-0012)
* SDK 1.5.8


Nesta versão contém as seguintes atualizações.

| Título                                           | Descrição                                          | Área afetada (por exemplo, serviço, componente ou SDK) | Cluster tipo (por exemplo, Hadoop, HBase ou tempestade) | JIRA (se aplicável) |
|-------------------------------------------------|------------------------------------------------------|---------------------------------------------------------|-----------------------------------------------------|----------------------|
| Versões HDP atualizados para clusters de 3,2 HDInsight | Com esta versão, HDInsight 3,2 implementa HDP 2.2.6.1-0012 | Serviço    | Todos os                                                 | N/D                  |


## <a name="notes-for-06262015-release-of-hdinsight"></a>Notas de versão de 26/06/2015 do HDInsight

Os números de versão completa para clusters HDInsight implementados nesta versão:

* HDInsight 2.1.10.601.1610731 (HDP 1.3.12.0-01795 - inalterada)
* HDInsight 3.0.6.601.1610731 (HDP 2.0.13.0-2117 - inalterada)
* HDInsight 3.1.4.601.1610731 (HDP 2.1.15.0-2334 - inalterada)
* HDInsight 3.2.4.601.1610731 (HDP 2.2.6.1-0011)
* SDK 1.5.8


Nesta versão contém as seguintes atualizações.

<table border="1">
<tr>
<th>Título</th>
<th>Descrição</th>
<th>Área afetada (por exemplo, serviço, componente ou SDK)</p></th>
<th>Cluster tipo (por exemplo, Hadoop, HBase ou tempestade)</th>
<th>JIRA (se aplicável)</th>
</tr>


<tr>
<td>Versões HDP atualizados para clusters de 3,2 HDInsight</td>
<td>Com esta versão, HDInsight 3,2 implementa HDP pontos 2.2.6.1</td>
<td>Serviço</td>
<td>Todos os</td>
<td>N/D</td>
</tr>

</table>

## <a name="notes-for-06182015-release-of-hdinsight"></a>Notas de versão de 18/06/2015 do HDInsight

Os números de versão completa para clusters HDInsight implementados nesta versão:

* HDInsight 2.1.10.596.1601657 (HDP 1.3.12.0-01795 - inalterada)
* HDInsight 3.0.6.596.1601657 (HDP 2.0.13.0-2117 - inalterada)
* HDInsight 3.1.4.596.1601657 (HDP 2.1.15.0-2334)
* HDInsight 3.2.4.596.1601657 (HDP 2.2.6.1-0002)
* SDK 1.5.8


Nesta versão contém as seguintes atualizações.

<table border="1">
<tr>
<th>Título</th>
<th>Descrição</th>
<th>Área afetada (por exemplo, serviço, componente ou SDK)</p></th>
<th>Cluster tipo (por exemplo, Hadoop, HBase ou tempestade)</th>
<th>JIRA (se aplicável)</th>
</tr>


<tr>
<td>Portas HTTPS adicionais abertas</td>
<td>O serviço de nuvem agora abre 5 portas 8001 para 8005 no cluster E.g. em https://<clustername>.azurehdinsight.net:8001/. Pedidos para estes URLs são autenticados utilizando o mesmo mecanismo de palavra-passe de autenticação básica como porta 443. Vincular estas portas para a mesma porta na headnode ativo. Acções de script podem ser utilizadas para tornam os serviços de cliente escutar as seguintes portas na headnode e rota para fora do cluster.</td>
<td>Serviço na nuvem</td>
<td>Todos os</td>
<td>N/D</td>
</tr>

<tr>
<td>Intermitente MapReduce aleatório este problema 3,2 HDInsight</td>
<td>Correção para uma condição de corrida raros, intermitentes no mapa reduzir aleatório em grandes clusters que resulta em ocasionais falhas de tarefa. Para mais informações, consulte <a href="https://issues.apache.org/jira/browse/MAPREDUCE-6361" target="_blank">MAPREDUCE 6361</a> .</td>
<td>Hadoop Core</td>
<td>Todos os</td>
<td><a href="https://issues.apache.org/jira/browse/MAPREDUCE-6361" target="_blank">MAPREDUCE 6361</a></td>
</tr>

<tr>
<td>Deslocar-se para a mais recente Java Azure SDK 2.2 para HDInsight 3,2</td>
<td>Movido para a versão mais recente do Azure SDK para Java utilizado pelo controlador WASB. O mais recente SDK tem correções alguns e as notas de lançamento para a mesma estão disponíveis no https://github.com/Azure/azure-storage-java/blob/master/ChangeLog.txt.</td>
<td>Hadoop Core</td>
<td>Todos os</td>
<td><a href="https://issues.apache.org/jira/browse/HADOOP-11959" target="_blank">HADOOP 11959</a></td>
</tr>

<tr>
<td>Deslocar-se para HDP 2.1.15 para HDInsight 3.1 clusters</td>
<td>Notas de lançamento Hortonworks para a versão estão disponíveis <a href="http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.15-Win/bk_releasenotes_HDP-Win/content/ch_relnotes-HDP-2.1.15.html" target="_blank">aqui</a>.</td>
<td>HDP</td>
<td>Todos os</td>
<td>N/D</td>
</tr>

</table>

## <a name="notes-for-06042015-release-of-hdinsight"></a>Notas de versão de 04/06/2015 do HDInsight

Os números de versão completa para clusters HDInsight implementados nesta versão:

* HDInsight 2.1.10.583.1575584 (HDP 1.3.12.0-01795 - inalterada)
* HDInsight 3.0.6.583.1575584 (HDP 2.0.13.0-2117 - inalterada)
* HDInsight 3.1.3.583.1575584 (HDP 2.1.12.1-0003 - inalterada)
* HDInsight 3.2.4.583.1575584 (HDP 2.2.6.1-1)
* SDK 1.5.8


Nesta versão contém as seguintes atualizações.

<table border="1">
<tr>
<th>Título</th>
<th>Descrição</th>
<th>Área afetada (por exemplo, serviço, componente ou SDK)</p></th>
<th>Cluster tipo (por exemplo, Hadoop, HBase ou tempestade)</th>
<th>JIRA (se aplicável)</th>
</tr>


<tr>
<td>Corrigir o erro de gateway inválido 502 para clusters tempestade</td>
<td>Nesta versão corrige um erro que afetam a apresentação de tarefa API que causaram o Web site ser para baixo após reiniciar o computador.</td>
<td>Serviço</td>
<td>Tempestade</td>
<td>N/D</td>
</tr>

</table>

## <a name="notes-for-06012015-release-of-hdinsight"></a>Notas de versão de 06/01/2015 do HDInsight

Os números de versão completa para clusters HDInsight implementados nesta versão:

* HDInsight 2.1.10.577.1563827 (HDP 1.3.12.0-01795 - inalterada)
* HDInsight 3.0.6.577.1563827 (HDP 2.0.13.0-2117 - inalterada)
* HDInsight 3.1.3.577.1563827 (HDP 2.1.12.1-0003 - inalterada))
* HDInsight 3.2.4.577.1563827 (HDP 2.2.6.0-2800 - inalterada)
* SDK 1.5.8


Nesta versão contém as seguintes atualizações.

<table border="1">
<tr>
<th>Título</th>
<th>Descrição</th>
<th>Área afetada (por exemplo, serviço, componente ou SDK)</p></th>
<th>Cluster tipo (por exemplo, Hadoop, HBase ou tempestade)</th>
<th>JIRA (se aplicável)</th>
</tr>


<tr>
<td>Várias correções de erros</td>
<td>Nesta versão corrige erros relacionados com cluster de aprovisionamento.</td>
<td>Serviço</td>
<td>Todos os tipos de cluster</td>
<td>N/D</td>
</tr>

</table>

## <a name="notes-for-05272015-release-of-hdinsight"></a>Notas de versão de 27/05/2015 do HDInsight

Os números de versão completa para clusters HDInsight implementados nesta versão:

* HDInsight 3.2.4.570.1554102 (HDP 2.2.6.0-2800)
* As outras versões de cluster e SDK não são implementadas como parte nesta versão.


Nesta versão contém as seguintes atualizações.

<table border="1">
<tr>
<th>Título</th>
<th>Descrição</th>
<th>Área afetada (por exemplo, serviço, componente ou SDK)</p></th>
<th>Cluster tipo (por exemplo, Hadoop, HBase ou tempestade)</th>
<th>JIRA (se aplicável)</th>
</tr>


<tr>
<td>HDP 2.2 actualização</td>
<td>Nesta versão do HDInsight 3,2 contém HDP 2.2.6 e dá várias correções de erros importantes ao HDInsight. As notas de lançamento completo está disponível em <a href="http://dev.hortonworks.com.s3.amazonaws.com/HDPDocuments/HDP2/HDP-2.2.6/HDP_RelNotes_v226/index.html">HDP 2.2.6 notas de versão</a>.</td>
<td>HDP</td>
<td>Todos os tipos de cluster</td>
<td>N/D</td>
</tr>

<tr>
<td>Alterar a configuração de memória predefinida fio contentor</td>
<td>Nesta actualização, a predefinição memória disponível para contentores FIO (yarn.nodemanager.resource.memory mb e yarn.scheduler.maximum-alocação-mb), iniciada pelo nó Gestor, é aumentado para 5632MB. Anteriormente este foi reduzido para 4608MB, mas com base em vários tarefa é executada, o novo valor deve oferecer melhor fiabilidade e desempenho até à maioria das tarefas, é, por conseguinte, uma melhor predefinida. Como se habitualmente, um terem dependência crítica esta configuração de memória, defina-explicitamente ao criar o cluster.</td>
<td>HDP</td>
<td>Todos os tipos de cluster</td>
<td>N/D</td>
</tr>

<tr>
<td>Valor predefinido Config paridade para HBase e tempestade clusters</td>
<td>Esta atualização restaura Hbase e tempestade clusters para utilizar os mesmos valores de FIO configs como Hadoop clusters. Isto é feito para paridade em todos os tipos de cluster.</td>
<td>HDP</td>
<td>HBase, tempestade</td>
<td>N/D</td>
</tr>

</table>

## <a name="notes-for-05202015-release-of-hdinsight"></a>Notas para 20/05/2015 do lançamento do HDInsight

Os números de versão completa para clusters HDInsight implementados nesta versão:

* HDInsight 2.1.10.564.1542093 (HDP 1.3.12.0-01795 - inalterada)
* HDInsight 3.0.6.564.1542093 (HDP 2.0.13.0-2117 - inalterada)
* HDInsight 3.1.3.564.1542093 (HDP 2.1.12.1-0003)
* HDInsight 3.2.4.564.1542093 (HDP 2.2.4.6-2)
* SDK 1.5.8

Nesta versão contém as seguintes atualizações.

<table border="1">
<tr>
<th>Título</th>
<th>Descrição</th>
<th>Área afetada (por exemplo, serviço, componente ou SDK)</p></th>
<th>Cluster tipo (por exemplo, Hadoop, HBase ou tempestade)</th>
<th>JIRA (se aplicável)</th>
</tr>


<tr>
<td>Suporte de EventHub SCP.NET</td>
<td>Os pacotes de cluster atualizada para o HDInsight tempestade trazem novas funcionalidades para SCP.NET. Tem agora acesso a novos APIs no construtor de topologia de que a tornam mais fácil de utilizar EventHubSpout ou Java Spouts. Tem de atualizar o seu cliente de SCP.NET SDK para trabalhar com novos clusters como contratos tiverem sido atualizados. Para obter detalhes sobre as novas notas APIs, a utilização e lançamento (incluindo correções de erros) consulte o ficheiro Leia-me incluído no pacote nuget SCP.NET.</td>
<td>Ferramenta de VS</td>
<td>Tempestade HDInsight 3,2 clusters</td>
<td>N/D</td>
</tr>

<tr>
<td>Actualização do controlador JDBC</td>
<td>O controlador actualizado para a versão do SQL Server suportada no sqljdbc_4.1.5605.100.</td>
<td>Metastore</td>
<td>Todos os</td>
<td>N/D</td>
</tr>
</table>

## <a name="notes-for-04272015-release-of-hdinsight"></a>Notas de versão de 27/04/2015 do HDInsight

Os números de versão completa para clusters HDInsight implementados nesta versão:

* HDInsight 2.1.10.537.1486660 (HDP 1.3.12.0-01795 - inalterada)
* HDInsight 3.0.6.537.1486660 (HDP 2.0.13.0-2117 - inalterada)
* HDInsight 3.1.3.537.1486660 (HDP 2.1.12.0-2329 - inalterada)
* HDInsight 3.2.3.537.1486660 (HDP 2.2.2.2-4)
* SDK 1.5.8

Nesta versão contém as seguintes atualizações.

<table border="1">
<tr>
<th>Título</th>
<th>Descrição</th>
<th>Área afetada (por exemplo, serviço, componente ou SDK)</p></th>
<th>Cluster tipo (por exemplo, Hadoop, HBase ou tempestade)</th>
<th>JIRA (se aplicável)</th>
</tr>


<tr>
<td>Corrigir dependência de DLL</td>
<td>Remove HDInsight dependência relativa a unidade de teste quadro.</td>
<td>SDK</td>
<td>Hadoop</td>
<td>N/D</td>
</tr>

<tr>
<td>Corrigir erros de condição de corrida</td>
<td>Um cluster de criar pedido agora aguarda no pedido de colocar para ser aceite antes de inquéritos sobre o Estado</td>
<td>SDK</td>
<td>Hadoop</td>
<td>N/D</td>
</tr>
</table>

## <a name="notes-for-04142015-release-of-hdinsight"></a>Notas de versão de 14/04/2015 do HDInsight

Os números de versão completa para clusters HDInsight implementados nesta versão:

* HDInsight 2.1.10.521.1453250 (HDP 1.3.12.0-01795 - inalterada)
* HDInsight 3.0.6.521.1453250 (HDP 2.0.13.0-2117 - inalterada)
* HDInsight 3.1.3.521.1453250 (HDP 2.1.12.0-2329 - inalterada)
* HDInsight 3.2.3.525.1459730 (HDP 2.2.2.2-2)
* SDK 1.5.6

Nesta versão contém as seguintes atualizações.

<table border="1">
<tr>
<th>Título</th>
<th>Descrição</th>
<th>Área afetada (por exemplo, serviço, componente ou SDK)</p></th>
<th>Cluster tipo (por exemplo, Hadoop, HBase ou tempestade)</th>
<th>JIRA (se aplicável)</th>
</tr>


<tr>
<td>Correções de erros de Tez</td>
<td>Correções para Apache TEZ 2214 e TEZ 1923 estão incluídas nesta versão do HDI 3,2. Estas são especificamente necessários para determinados consultas de ramo no Tez que requerem para ordenar aleatoriamente uma significativa quantidade de dados.
</td>
<td>HDP</td>
<td>Hadoop</td>
<td><a href="https://issues.apache.org/jira/browse/TEZ-2214">TEZ 2214</a></br><a href="https://issues.apache.org/jira/browse/TEZ-1923">TEZ 1923</a></td>
</tr>
</table>

## <a name="notes-for-04062015-release-of-hdinsight"></a>Notas de versão de 04/06/2015 do HDInsight

Os números de versão completa para clusters HDInsight implementados nesta versão:

* HDInsight 2.1.10.521.1453250 (HDP 1.3.12.0-01795 - inalterada)
* HDInsight 3.0.6.521.1453250 (HDP 2.0.13.0-2117 - inalterada)
* HDInsight 3.1.3.521.1453250 (HDP 2.1.12.0-2329 - inalterada)
* HDInsight 3.2.3.521.1453250 (HDP 2.2.2.2-1)
* SDK 1.5.6

Nesta versão contém as seguintes atualizações.

<table border="1">
<tr>
<th>Título</th>
<th>Descrição</th>
<th>Área afetada (por exemplo, serviço, componente ou SDK)</p></th>
<th>Cluster tipo (por exemplo, Hadoop, HBase ou tempestade)</th>
<th>JIRA (se aplicável)</th>
</tr>


<tr>
<td>HDInsight .NET SDK 1.5.6</td>
<td>Atualizações para remover algumas classes internas para HDInsight no Linux.</td>
<td>SDK</td>
<td>Hadoop</td>
<td>N/D</td>
</tr>

<tr>
<td>Biblioteca de Avro 1.5.6</td>
<td>Adicionado <b>KnownTypeAttribute</b> para o método <b>GetAllKnownTypes</b>. Fixo NullReferenceException quando um tipo de é nulo para o método de GetAllKnownTypes.</td>
<td>SDK</td>
<td>Hadoop</td>
<td>N/D</td>
</tr>

<tr>
<td>Correções de erros</td>
<td>Várias correções de erros para o serviço</td>
<td>Serviço</td>
<td>Todos os</td>
<td>N/D</td>
</tr>

</table>
<br>

## <a name="notes-for-04012015-release-of-hdinsight"></a>Notas de versão de 04/01/2015 do HDInsight

Os números de versão completa para clusters HDInsight implementados nesta versão:

* HDInsight 2.1.10.513.1431705 (HDP 1.3.12.0-01795)
* HDInsight 3.0.6.513.1431705 (HDP 2.0.13.0-2117)
* HDInsight 3.1.3.513.1431705 (HDP 2.1.12.0-2329)
* HDInsight 3.2.3.513.1431705 (HDP 2.2.2.1-2600)
* SDK 1.5.5

Nesta versão contém as seguintes atualizações.

<table border="1">
<tr>
<th>Título</th>
<th>Descrição</th>
<th>Área afetada (por exemplo, serviço, componente ou SDK)</p></th>
<th>Cluster tipo (por exemplo, Hadoop, HBase ou tempestade)</th>
<th>JIRA (se aplicável)</th>
</tr>


<tr>
<td>Capacidade de ativar/desativar as credenciais de ambiente de trabalho remotas no Windows clusters através do .NET SDK</td>
<td>Suporte de programação para ativar ou desativar as credenciais RDP em clusters do Windows.</td>
<td>SDK</td>
<td>Todos os</td>
<td>N/D</td>
</tr>

<tr>
<td>Capacidade de ativar as credenciais de ambiente de trabalho remotas no clusters enquanto estes estão a ser aprovisionados</td>
<td>Suporte de programação para ativar as credenciais de ambiente de trabalho remotas, tal como o cluster está a ser criado. Esta ação remove o processo de dois passos para cluster de aprovisionamento pela primeira vez e, em seguida, ativar o ambiente de trabalho remoto.</td>
<td>SDK</td>
<td>Todos os</td>
<td>N/D</td>
</tr>

<tr>
<td>Python atualizado para 2.7.8</td>
<td>Correções de Python atualizado em HDInsight Clusters para Python 2.7.8, que contém algumas segurança importante para versões HDInsight 2.1, 3.0, 3.1 e 3,2</td>
<td>Serviço</td>
<td>Todos os</td>
<td>N/D</td>
</tr>

<tr>
<td>Alteração da configuração FIO</td>
<td>Alteradas FIO configuração yarn.resourcemanager.max concluída-aplicações e 1000 para todos os tipos de cluster para versões HDInsight 3.1 e 3,2. Este valor controla apenas na lista de aplicações concluídas na IU do FIO. Para obter informações sobre as aplicações que foram submetidas antes da lista de aplicações apresentadas na IU, pode aceder diretamente para o servidor do histórico.</td>
<td>FIO</td>
<td>Todos os</td>
<td>N/D</td>
</tr>

<tr>
<td>Redimensionar de nós num HBase cluster</td>
<td>HBase clusters permitirem agora o redimensionamento de nós (para cima e para baixo) para as versões de HDInsight 3.1 e 3,2</td>
<td>Serviço</td>
<td>HBase</td>
<td>N/D</td>
</tr>

<tr>
<td>Atualização JDBC</td>
<td>Controlador de SQL JDBC é atualizado para a versão sqljdbc_4.0.2206.100 para HDInsight versão 3,2. Esta versão contém melhoramentos de segurança importantes.</td>
<td>HDP</td>
<td>Todos os</td>
<td>N/D</td>
</tr>

<tr>
<td>Actualização da configuração JVM</td>
<td>Atualizado JVM configuração networkaddress.cache.ttl para 300 segundos a partir do valor predefinido de -1 para versões HDInsight 3.1 e 3,2. Este valor configuração controla a política de colocação em cache para pesquisas com êxito nome a partir do serviço de nomes. Isto corrige um erro relacionado com aumentar e diminuir HBase clusters.</td>
<td>Serviço</td>
<td>HBase</td>
<td>N/D</td>
</tr>

<tr>
<td>Atualizar para o armazenamento Azure SDK para Java 2.0</td>
<td>HDInsight versão 3,2 é atualizado para utilizar a versão mais recente do Azure armazenamento SDK para Java. Este contém várias correções de erros importantes sobre o 0.6.0 atual versão.</td>
<td>HDP</td>
<td>Todos os</td>
<td>N/D</td>
</tr>

<tr>
<td>Atualizado para código fonte de Apache WASB</td>
<td>Versão 3,2 agora utiliza o código do mais recente para o controlador de sistema de ficheiros WASB a partir do Apache Hadoop HDInsight. Com esta alteração, o controlador WASB agora é fornecido como uma para caixa separada. Este é puramente uma alteração da embalagem e não contém as alterações ao comportamento do controlador WASB. O nome deste ficheiro para caixa é hadoop-azure-2.6.0.jar.</td>
<td>HDP</td>
<td>Todos os</td>
<td>N/D</td>
</tr>

<tr>
<td>JAR atualizações de nome de ficheiro no 3,2 HDInsight</td>
<td>Esta atualização ao HDInsight versão 3,2 contém várias correções de erros e alguns jarros internos empacotados como parte do HDP tenham sido atualizados. Note que estes ficheiros para caixa são internos para o pacote HDP e não para utilização direta por aplicações de cliente. Aplicações devem compactar a sua própria versão dos jarros para que uma atualização para os jarros internos HDP não interromper aplicações de cliente.</td>
<td>HDP</td>
<td>Todos os</td>
<td>N/D</td>
</tr>

</table>
<br>

## <a name="notes-for-03032015-release-of-hdinsight"></a>Notas de versão de 03/03/2015 do HDInsight

Os números de versão completa para clusters HDInsight implementados nesta versão:

* HDInsight 2.1.10.488.1375841 (HDP 1.3.9.0-01351 - inalterada)
* HDInsight 3.0.6.488.1375841 (HDP 2.0.9.0-2097 - inalterada)
* HDInsight 3.1.3.488.1375841 (HDP 2.1.10.0-2290 - inalterada)
* HDInsight 3.2.3.488.1375841 (HDP-2.2.10.0-2340 - inalterada)
* SDK 1.5.0 (inalterado)

Nesta versão contém a seguinte actualização.

<table border="1">
<tr>
<th>Título</th>
<th>Descrição</th>
<th>Área afetada (por exemplo, serviço, componente ou SDK)</p></th>
<th>Cluster tipo (por exemplo, Hadoop, HBase ou tempestade)</th>
<th>JIRA</th>
</tr>


<tr>
<td>Melhorias de fiabilidade</td>
<td>Foram efetuadas correções permitir que o serviço Dimensionar melhor com a carga maior relativamente às criação do cluster.</td>
<td>Serviço</td>
<td>Todos os</td>
<td>N/D</td>
</tr>



</table>
<br>

## <a name="notes-for-02182015-release-of-hdinsight"></a>Notas de versão de 18/02/2015 do HDInsight

Os números de versão completa para clusters HDInsight implementados nesta versão:

* HDInsight 2.1.10.471.1342507 (HDP 1.3.9.0-01351 - inalterada)
* HDInsight 3.0.6.471.1342507 (HDP 2.0.9.0-2097 - inalterada)
* HDInsight 3.1.3.471.1342507 (HDP 2.1.10.0-2290 - inalterada)
* HDInsight 3.2.3.471.1342507 (HDP-2.2.10.0 2340)
* SDK 1.5.0

Nesta versão contém as seguintes atualizações.

<table border="1">
<tr>
<th>Título</th>
<th>Descrição</th>
<th>Área afetada (por exemplo, serviço, componente ou SDK)</p></th>
<th>Cluster tipo (por exemplo, Hadoop, HBase ou tempestade)</th>
<th>JIRA (se aplicável)</th>
</tr>


<tr>
<td>HDInsight 3,2 clusters</td>
<td>Hadoop 2.6/HDP2.2 está disponível com clusters de 3,2 HDInsight. Contém principais atualizações para todos os componentes de abrir origem. Para obter mais detalhes, consulte o artigo Novidades no HDInsight e <a href ="http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.2.0/HDP_2.2.0_Release_Notes_20141202_version/index.html" target="_blank">notas de lançamento do HDP 2.2.0.0</a>.</td>
<td>Abrir origem software</td>
<td>Todos os</td>
<td>N/D</td>
</tr>

<tr>
<td>HDinsight no Linux (pré-visualização)</td>
<td>Podem ser implementados clusters em execução no Ubuntu Linux. Para obter mais detalhes, consulte o artigo Introdução ao HDInsight Linux.</td>
<td>Serviço</td>
<td>Hadoop</td>
<td>N/D</td>
</tr>

<tr>
<td>Disponibilidade geral tempestade</td>
<td>Apache tempestade clusters geral, estão disponíveis. Para obter mais detalhes, consulte o artigo começar a utilizar tempestade no HDInsight.</td>
<td>Serviço</td>
<td>Tempestade</td>
<td>N/D</td>
</tr>

<tr>
<td>Tamanhos de máquina virtual</td>
<td>Azure HDInsight está disponível no mais tipos de máquina virtual e tamanhos. HDInsight pode utilizar A2 a A7 tamanhos criados para fins gerais; D série nós que funcionalidade unidades Estado sólidas (SSDs) e processadores mais rápidos de percentagem de 60; e tamanhos de A8 e A9 que tenham InfiniBand suportam para redes fast.</td>
<td>Serviço</td>
<td>Todos os</td>
<td>N/D</td>
</tr>

<tr>
<td>Cluster de dimensionamento</td>
<td>Pode alterar o número de nós de dados para um cluster de HDInsight a execução sem ter de eliminar ou criar novamente. Atualmente, só as tipos de cluster consulta Hadoop e Apache tempestade têm esta capacidade, mas o suporte para HBase Apache tipo cluster mais cedo está a seguir. Para obter mais informações, consulte Gerir HDInsight clusters.</td>
<td>Serviço</td>
<td>Hadoop, tempestade</td>
<td>N/D</td>
</tr>

<tr>
<td>Ferramentas do Visual Studio</td>
<td>Para além de ferramentas completa para Apache tempestade, as ferramentas para Apache Hive no Visual Studio foi atualizada para incluir a conclusão da declaração, validação local e suporte depuração melhorado. Para mais informações, consulte o artigo introdução às ferramentas de Hadoop HDInsight para Visual Studio.</td>
<td>Ferramentas</td>
<td>Hadoop</td>
<td>N/D</td>
</tr>

<tr>
<td>Conector do Hadoop para DocumentDB</td>
<td>Com Hadoop Connector para DocumentDB, pode executar agregações complexas, análise e manipulações ao longo do esquema menos JSON documentos armazenados em coleções de DocumentDB ou em várias contas de base de dados. Para obter mais informações e um tutorial, consulte o artigo tarefas de executar Hadoop utilizando DocumentDB e HDInsight.</td>
<td>Serviço</td>
<td>Hadoop</td>
<td>N/D</td>
</tr>

<tr>
<td>Correções de erros</td>
<td>Várias correções de erros mínima para serviços de HDInsight foram feitas. Sem alterações de comportamento de cliente destinado são esperadas.</td>
<td>Serviço</td>
<td>Todos os</td>
<td>N/D</td>
</tr>

</table>
<br>

## <a name="notes-for-02062015-release-of-hdinsight"></a>Notas de versão de 02/06/2015 do HDInsight

Os números de versão completa para clusters HDInsight implementados nesta versão:

* HDInsight 2.1.10.463.1325367 (HDP 1.3.9.0-01351 - inalterada)
* HDInsight 3.0.6.463.1325367 (HDP 2.0.9.0-2097 - inalterada)
* HDInsight 3.1.2.463.1325367 (HDP 2.1.10.0-2290)
* N/D DE SDK

Nesta versão contém as seguintes atualizações.

<table border="1">
<tr>
<th>Título</th>
<th>Descrição</th>
<th>Área afetada (por exemplo, serviço, componente ou SDK)</p></th>
<th>Cluster tipo (por exemplo, Hadoop, HBase ou tempestade)</th>
<th>JIRA (se aplicável)</th>
</tr>


<tr>
<td>Correções de erros</td>
<td>Várias correções de erros mínima para serviços de HDInsight foram feitas. Sem alterações de comportamento de cliente destinado são esperadas.</td>
<td>Serviço</td>
<td>Todos os</td>
<td>N/D</td>
</tr>

<tr>
<td>Atualização de manutenção HDP 2.1</td>
<td>HDInsight 3.1 é atualizado para implementar HDP 2.1.10.0. Para mais informações, consulte o artigo <a href ="http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.10/bk_releasenotes_hdp_2.1/content/ch_relnotes-HDP-2.1.10.html" target="_blank">notas de lançamento HDP-2.1.10</a>. </td>
<td>Abrir origem software</td>
<td>Todos os</td>
<td>N/D</td>
</tr>

<tr>
<td>Atualizações de binárias HDP</td>
<td>Existem alguns ficheiros de para caixa HBase para o qual o ficheiro que os nomes tiverem sido atualizados. Estes ficheiros para caixa são utilizados pelo HBase, para que não é esperado que os clientes tenham uma dependência nos nomes destes ficheiros para caixa. Estes incluem:
<ul>
<li>./lib/jetty-6.1.26.hwx.JAR</li>
<li>./lib/jetty-sslengine-6.1.26.hwx.JAR</li>
<li>./lib/jetty-Util-6.1.26.hwx.JAR</li>
</ul>
</td>
<td>Abrir origem software</td>
<td>HBase</td>
<td>N/D</td>
</tr>

</table>
<br>

## <a name="notes-for-1292015-release-of-hdinsight"></a>Notas de versão de 29/1/2015 do HDInsight

Os números de versão completa para clusters HDInsight implementados nesta versão:

* HDInsight 2.1.10.455.1309616 (HDP 1.3.9.0-01351 - inalterada)
* HDInsight 3.0.6.455.1309616 (HDP 2.0.9.0-2097 - inalterada)
* HDInsight 3.1.2.455.1309616 (HDP 2.1.9.0-2196 - inalterada)
* N/D DE SDK

Nesta versão contém a seguinte actualização.

<table border="1">
<tr>
<th>Título</th>
<th>Descrição</th>
<th>Área afetada (por exemplo, serviço, componente ou SDK)</p></th>
<th>Cluster tipo (por exemplo, Hadoop, HBase ou tempestade)</th>
<th>JIRA (se aplicável)</th>
</tr>


<tr>

<td>Correções de erros</td>
<td>Foram feitas alguns correções de erros importantes que melhorar a fiabilidade dos conjuntos de sectores HDInsight durante as actualizações Azure.</td>
<td>Serviço</td>
<td>Todos os</td>
<td>N/D</td>
</tr>



</table>
<br>

## <a name="notes-for-152015-release-of-hdinsight"></a>Notas de versão de 5/1/2015 do HDInsight

Os números de versão completa para clusters HDInsight implementados nesta versão:

* HDInsight 2.1.10.420.1246118 (HDP 1.3.9.0-01351 - inalterada)
* HDInsight 3.0.6.420.1246118 (HDP 2.0.9.0-2097 - inalterada)
* HDInsight 3.1.2.420.1246118 (HDP 2.1.9.0-2196 - inalterada)


Nesta versão contém as seguintes atualizações.

<table border="1">
<tr>
<th>Título</th>
<th>Descrição</th>
<th>Componente</th>
<th>Tipo de cluster</th>
<th>JIRA (se aplicável)</th>
</tr>


<tr>
<td>Amostras para análise de tendências Twitter e recomendações de filme com base em Mahout</td>
<td><p>Neste lançamento, consola de consulta HDInsight tem duas amostras adicionais:</p>

<p><b>Análise de tendências twitter</b><br>
APIs públicos fornecidas pela sites semelhantes Twitter são uma origem de dados para analisar e compreender tendências populares útil. Neste tutorial, saiba como utilizar Hive para obter uma lista de utilizadores do Twitter que enviou as maioria dos tweets que contém uma determinada palavra. </p>

<p><b>Recomendação mahout filme</b><br>
Apache Mahout é uma máquina de Apache Hadoop biblioteca de formação. Mahout contém algoritmos para processamento de dados (como a filtragem, classificação e clusters). Neste tutorial, utilize um motor de recomendação para gerar recomendações de filme com base nas filmes viu os seus amigos.</p></td>
<td>Consola de consulta</td>
<td>Hadoop</td>
<td>N/D</td>
</tr>

<tr>
<td>Alterar para o valor predefinido para a configuração do ramo: hive.auto.convert.join.noconditionaltask.size</td>
<td><p>Esta configuração de tamanho se aplica ao mapa convertido automaticamente associações. O valor representa a soma dos tamanhos das tabelas que podem ser convertidos para o mapas hash que cabem na memória. Uma versão anterior, este valor aumentado do valor predefinido de 10 MB para 128 MB. No entanto, o novo valor de 128 MB estava a causar a falhar devido à falta de memória tarefas. Nesta versão reverte o valor predefinido para 10 MB. Os clientes ainda podem optar por substituir este valor durante a criação de cluster, tendo em conta as respetivas consultas e tamanhos de tabela. Para mais informações sobre esta definição e como substituí-lo, consulte o artigo <a href="http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.0.0.2/ds_Hive/optimize-joins.html#JoinOptimization-OptimizeAutoJoinConversion" target="_blank">Otimizar conversão automática participar</a> na documentação Hortonworks. </p></td>
<td>Ramo</td>
<td>Hadoop, Hbase</td>
<td>N/D</td>
</tr>

</table>
<br>

## <a name="notes-for-12232014-release-of-hdinsight"></a>Notas de versão de 23/12/2014 do HDInsight

Os números de versão completa para clusters HDInsight implementados com esta versão são:

* HDInsight 2.1.10.420.1246783 (versão HDP inalterada)
* HDInsight 3.0.6.420.1246783 (versão HDP inalterada)
* HDInsight 3.1.1.420.1246783 (versão HDP inalterada)

Nesta versão contém a seguinte actualização.

<table border="1">
<tr>
<th>Título</th>
<th>Descrição</th>
<th>Componente</th>
<th>Tipo de cluster</th>
<th>JIRA (se aplicável)</th>
</tr>


<tr>
<td>Falhas de criação de cluster intermitentes devido a carga excessiva</td>
<td><p>Algoritmo de melhoradas para transferir pacotes HDP durante a criação de cluster permite mais robusto processamento de falhas devido a carga excessiva.</p></td>
<td>Serviço</td>
<td>Hadoop, Hbase, tempestade</td>
<td>N/D</td>
</tr>



</table>
<br>

## <a name="notes-for-12182014-release-of-hdinsight"></a>Notas de versão de 18/12/2014 do HDInsight

Nesta versão contém a seguinte actualização de componente.

<table border="1">
<tr>
<th>Título</th>
<th>Descrição</th>
<th>Componente</th>
<th>Tipo de cluster</th>
<th>JIRA (se aplicável)</th>
</tr>

<tr>
<td><a href = "hdinsight-hadoop-customize-cluster.md" target="_blank">Personalização de cluster Avalability geral</a></td>
<td><p>Personalização fornece a capacidade para personalizar o seu clusters Azure HDInsight com projetos que estão disponíveis a partir do ecossistema Apache Hadoop. Com a nova função, pode experimentar e implementar Hadoop projetos ao Azure HDInsight. Este é activado através da funcionalidade **Script ação** , para que possa modificar Hadoop clusters de formas arbitrários utilizando scripts personalizados. Esta personalização está disponível em todos os tipos de clusters HDInsight incluindo Hadoop, HBase e tempestade. Para demonstrar potência desta capacidade, podemos ter-se documentados o processo para instalar o populares <a href = "hdinsight-hadoop-spark-install.md" target="_blank">motores</a>, <a href = "hdinsight-hadoop-r-scripts.md" target="_blank">R</a>, <a href = "hdinsight-hadoop-solr-install.md" target="_blank">Solr</a>e <a href = "hdinsight-hadoop-giraph-install.md" target="_blank">Giraph</a> módulos. Nesta versão também adiciona a capacidade de clientes especificar a sua ação dos scripts personalizados através do portal do Azure, fornece diretrizes e as melhores práticas sobre como construir ações de scripts personalizados através de métodos de helper e fornece diretrizes sobre como testar a ação de script. </p></td>
<td>Disponibilidade da funcionalidade geral</td>
<td>Todos os</td>
<td>N/D</td>
</tr>


</table>
<br>

## <a name="notes-for-12052014-release-of-hdinsight"></a>Notas de versão de 05/12/2014 do HDInsight

Os números de versão completa para clusters HDInsight implementados com esta versão são:

* HDInsight 2.1.9.406.1221105 (HDP 1.3.9.0-01351)
* HDInsight 3.0.5.406.1221105 (HDP 2.0.9.0-2097)
* HDInsight 3.1.1.406.1221105 (HDP 2.1.9.0-2196)
* HDInsight SDK n/d

Nesta versão contém as seguintes atualizações de componente.

<table border="1">
<tr>
<th>Título</th>
<th>Descrição</th>
<th>Componente</th>
<th>Tipo de cluster</th>
<th>JIRA (se aplicável)</th>
</tr>

<tr>
<td>Corrigir erros: intermitente Ocorreu um erro ao adicionar grandes quantidades de partições a uma tabela numa DDL Hive </td>
<td><p>Se existir um erro de ligação intermitentes com a base de dados do ramo metastore ao adicionar a partições muitas para uma tabela Hive, a DDL Hive pode falhar. A seguinte instrução será vista no registo de erros de ramo se esta falha ocorre: </p><p>"Erro [principais]: ql. Controlador (SessionState.java:printError(547)) - Ocorreu uma falha: execução erro, o código de retorno 1 de org.apache.hadoop.hive.ql.exec.DDLTask. MetaException (message:java.lang.RuntimeException: commitTransaction era denominado mas openTransactionCalls = 0. Isto provavelmente indica que não existem colunas equilibradas chamadas para openTransaction/commitTransaction)"</p></td>
<td>Ramo</td>
<td>Hadoop, Hbase</td>
<td>RAMO-482 (isto é um JIRA interno, para que não pode ser proposto externamente. De referir aqui para referência.)</td>
</tr>

<tr>
<td>Corrigir erros: ocasional desligar o telefone na consola de consulta do HDInsight</td>
<td>Quando isto acontece, a seguinte instrução pode ser vista no registo de WebHCat para a tarefa de iniciador WebHCat: <p>"org.apache.hive.hcatalog.templeton.CatchallExceptionMapper | org.apache.hadoop.ipc.RemoteException(org.apache.hadoop.yarn.exceptions.YarnRuntimeException): não é possível carregar o ficheiro de histórico {wasb o url para o ficheiro de histórico} "</p></td>
<td>WebHCat</td>
<td>Hadoop</td>
<td>RAMO-482 (isto é um JIRA interno, para que não pode ser proposto externamente. De referir aqui para referência.)</td>
</tr>

<tr>
<td>Corrigir erros: coletor ocasional no latência de Hbase consultas</td>
<td>Se tal acontecer, os utilizadores irão reparar uma coletor ocasional de 3 segundos na latência da Hbase consultas. </td>
<td>HDInsight Cluster Gateway</td>
<td>HBase</td>
<td>N/D</td>
</tr>

<tr>
<td>Alterações de nome de ficheiro HDP JAR</td>
<td>Para HDI cluster versão 3.0, daí algumas alterações para os ficheiros para caixa internos instalados pelo HDP. foi substituído cais 6.1.26.jar com cais 6.1.26.hwx.jar. foi substituído cais-/Util-6.1.26.jar com cais-/Util-6.1.26.hwx.jar. Aplicam estas alterações a Hadoop, Mahout, WebHCat e Oozie projetos.</td>
<td>Hadoop, Mahout, WebHCat, Oozie</td>
<td>Hadoop, HBase</td>
<td>N/D</td>
</tr>

</table>
<br>


## <a name="notes-for-11212014-release-of-hdinsight"></a>Notas de versão de 21/11/2014 do HDInsight

Os números de versão completa para clusters HDInsight implementados com esta versão são:

* HDInsight 2.1.9.382.1169709 (sem alterações 14/11/2014)
* HDInsight 3.0.5.382.1169709 (sem alterações 14/11/2014)
* HDInsight 3.1.1.382.1169709 (sem alterações 14/11/2014)
* HDINsight SDK 1.4.0

Nesta versão contém as seguintes atualizações de componente.

<table border="1">
<tr>
<th>Título</th>
<th>Descrição</th>
<th>Componente</th>
<th>Tipo de cluster</th>
<th>JIRA (se aplicável)</th>
</tr>

<tr>
<td>Aceder a registos da aplicação</td>
<td>Capacidade para enumerar através de programação de aplicações que tiverem sido executadas no seu clusters e transferir relevantes registos específicos da aplicação ou específicas do contentor para ajudar a depurar problemáticas aplicações.</td>
<td>SDK</td>
<td>Hadoop</td>
<td>N/D</td>
</tr>

<tr>
<td>Capacidade de especificar o nome de região no IHdInsightClient.DeleteCluster </td>
<td>O SDK do Azure HDInsight fornece a capacidade de especificar um nome de região quando utilizar **DeleteCluster**. Isto ajuda a desbloquear clientes que tinham dois recursos com o mesmo nome no regiões diferentes e tinham sido não é possível eliminar qualquer uma delas.</td>
<td>SDK</td>
<td>Todos os</td>
<td>N/D</td>
</tr>

<tr>
<td>ClusterDetails.DeploymentId</td>
<td>O objeto **ClusterDetails** devolve um campo de **DeploymentID** que representa um identificador exclusivo para o cluster. Se garante a ser exclusivos tentativas de criação de cluster com os mesmos nomes.</td>
<td>SDK</td>
<td>Todos os</td>
<td>N/D</td>
</tr>
</table>
<br>

## <a name="notes-for-11142014-release-of-hdinsight"></a>Notas de versão de 14/11/2014 do HDInsight

Os números de versão completa para clusters HDInsight implementados com esta versão são:

* HDInsight 2.1.9.382.1169709
* HDInsight 3.0.5.382.1169709
* HDInsight 3.1.1.382.1169709

Nesta versão contém as seguintes funcionalidades novas, componente atualizações e correções de erros.

<table border="1">
<tr>
<th>Título</th>
<th>Descrição</th>
<th>Componente</th>
<th>Tipo de cluster</th>
<th>JIRA (se aplicável)</th>
</tr>

<tr>
<td>Acção de script (pré-visualização)</td>
<td>Pré-visualização da funcionalidade de personalização cluster que permite a modificação de Hadoop clusters de formas arbitrários utilizando scripts personalizados. Com esta funcionalidade, os utilizadores podem experimentar e implementar projetos que estão disponíveis a partir do ecossistema Apache Hadoop clusters Azure HDInsight. Esta funcionalidade de personalização está disponível em todos os tipos de clusters HDInsight, incluindo Hadoop, HBase e tempestade.</td>
<td>Nova funcionalidade</td>
<td>Todos os</td>
<td>N/D</td>
</tr>

<tr>
<td>Tarefas pré-concebidas para análise de registo de Web sites e armazenamento Azure</td>
<td>Consola de consulta do HDInsight tem uma galeria de introdução que suporte soluções que trabalharem nos seus dados ou dados de exemplo.
<p>**Soluções que trabalhar nos seus dados**:<br>
Já criámos trabalhos para alguns cenários de análise de dados mais comuns para fornecer um ponto de partida para criar o seus próprio soluções. Pode utilizar os seus dados com a tarefa para ver como funciona. Em seguida, quando estiver pronto, utilize o que aprendeu criar uma solução que é baseada na tarefa pré-concebidas.</p>
<p>**Soluções que trabalhar em dados de exemplo**:<br>
Saiba como trabalhar com HDInsight ao observar alguns cenários de básicos (tais como analisar dados sensor e registos de web). Irá obter informações sobre como utilizar HDInsight para analisar esses dados e como pode ligar a outras aplicações e serviços a estes dados. Visualizar dados ligando-se para o Microsoft Excel fornece um exemplo de potência desta abordagem.</p></td>
<td>Consola de consulta</td>
<td>Hadoop</td>
<td>N/D</td>
</tr>

<tr>
<td>Corrigir de perda de memória no Templeton</td>
<td>Uma perda de memória Templeton afetadas clientes que tinham clusters de execução longa ou foram submeter 100s da tarefa de pedidos de que uma segunda foi resolvida. O problema conforme manifestado como Templeton 5xx erros e a solução foi reiniciar o serviço. A solução já não é necessária.</td>
<td>Templeton</td>
<td>Todos os</td>
<td>https://issues.apache.org/jira/Browse/HADOOP-11248</td>
</tr>
</table>
<br>


**Nota**: para demonstrar as novas capacidades disponibilizadas pelos personalização cluster, os procedimentos utilizar a ação de Script para instalar motores e R módulos num cluster de tem sido documentados. Para obter mais informações, consulte:

* [Instalar e utilizar motores 1.0 no HDInsight clusters](hdinsight-hadoop-spark-install.md)
* [Instalar e utilizar R no HDInsight Hadoop clusters](hdinsight-hadoop-r-scripts.md)




## <a name="notes-for-11072014-release-of-hdinsight"></a>Notas de versão de 11/07/2014 do HDInsight

Os números de versão completa para clusters de HDInsight implementado com esta versão são:

* HDInsight 2.1 2.1.9.374.1153876
* HDInsight 3.0 3.0.5.374.1153876
* HDInsight 3.1 3.1.1.374.1153876

Nesta versão contém as seguintes atualizações de componente.

<table border="1">
<tr>
<th>Título</th>
<th>Descrição</th>
<th>Componente</th>
<th>Tipo de cluster</th>
<th>JIRA (se aplicável)</th>
</tr>

<tr>
<td>HDP 2.1.7</td>
<td>Nesta versão é baseada no Hortonworks dados plataforma (HDP) 2.1.7. Para mais informações, consulte o artigo <a href="http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.7-Win/bk_releasenotes_HDP-Win/content/ch_relnotes-HDP-2.1.7.html" target="_blank">Notas de lançamento do HDP 2.1.7</a>.</td>
<td>HDP</td>
<td>Todos os</td>
<td>N/D</td>
</tr>

<tr>
<td>Servidor de linha cronológica FIO</td>
<td>O servidor de linha cronológica FIO (também conhecido como o servidor de histórico de aplicação genérico) tiver sido ativado por predefinição. O servidor de linha cronológica fornece informações genéricas sobre aplicações concluídas (como o ID da aplicação, nome da aplicação, estado da aplicação, tempo de submissão da aplicação e tempo de conclusão da aplicação).

Estas informações de aplicação podem obtidas a partir do nó cabeça acedendo o URI http://headnodehost:8188 ou ao executar o comando FIO: fio aplicação – – appStates da lista todos os.

Estas informações podem também ser recuperadas remotamente apesar de um REST API na https://{ClusterDnsName}. azurehdinsight.NET/ws/V1/applicationhistory/.

Para informações mais detalhadas, consulte o artigo <a href="http://hadoop.apache.org/docs/r2.4.0/hadoop-yarn/hadoop-yarn-site/TimelineServer.html" target="_blank">FIO de servidor de linha cronológica</a>.</td>
<td>Serviço, FIO</td>
<td>Hadoop, HBase</td>
<td>N/D</td>
</tr>

<tr>
<td>ID da cluster de implementação</td>
<td>Começando SDK versão 1.3.3.1.5426.29232, os utilizadores podem aceder um ID exclusivo para cada cluster, que é emitido HDInsight. Isto permite que clientes descobrir instâncias exclusivas de clusters quando um nome de DNS está a ser reutilizado lado ao outro criar ou largar cenários.</td>
<td>SDK</td>
<td>Todos os</td>
<td>N/D</td>
</tr>
</table>
<br>

**Nota**: foi corrigido o erro que impedido o número de versão completa da aparecer no portal do ou do que está a ser devolvido pelo SDK ou ao Windows PowerShell nesta versão.

## <a name="notes-for-10152014-release"></a>Notas de lançamento de 15/10/2014

Nesta versão correcção fixo uma perda de memória na Templeton afetadas os utilizadores frequente de Templeton. Em alguns casos, os utilizadores que exercício Templeton fortemente iria visualizar erros conforme manifestados como 500 códigos de erro porque os pedidos de não teria memória suficiente para executar. Solução para este problema foi reiniciar o serviço de Templeton. Este problema foi resolvido.


## <a name="notes-for-1072014-release"></a>Notas de lançamento de 7/10/2014

* Quando utilizar o ponto final de Ambari, "https://{clusterDns}.azurehdinsight.net/ambari/api/v1/clusters/{clusterDns}.azurehdinsight.net/services/{servicename}/components/{componentname}", o campo *host_name* devolve o nome de domínio completamente qualificado (FQDN) do nó em vez de apenas o nome do anfitrião. Por exemplo, em vez de voltar "**headnode0**", obterá o FQDN "**headnode0. { ClusterDNS} .azurehdinsight .net**". Esta alteração foi necessário para facilitar a cenários onde vários tipos de cluster (como HBase e Hadoop) podem ser implementados numa rede virtual. Isto acontece, por exemplo, quando utilizar HBase como uma plataforma back-end para Hadoop.

* Fornecemos novas definições de memória para a implementação de predefinido do HDInsight cluster. Anterior predefinições de memória não adequadamente conta as orientações para o número de núcleos CPU a ser implementada. Estas novas definições de memória devem fornecer uma melhor predefinições (de acordo com as recomendações de Hortonworks). Para alterar estas, consulte a documentação de referência SDK sobre como alterar a configuração do cluster. As novas definições de memória são utilizadas pelo cluster predefinido 4 CPU core (8 container) HDInsight são listadas na tabela seguinte. (Os valores utilizados antes deste lançamento também são fornecidos entre.)

<table border="1">
<tr><th>Componente</th><th>Alocação de memória</th></tr>
<tr><td> atribuição de yarn.Scheduler.Minimum</td><td>768 MB (anteriormente 512 MB)</td></tr>
<tr><td> atribuição de yarn.Scheduler.Maximum</td><td>6144 MB (inalterado)</td></tr>
<tr><td>yarn.nodemanager.Resource.Memory</td><td>6144 MB (inalterado)</td></tr>
<tr><td>mapreduce.Map.Memory</td><td>768 MB (anteriormente 512 MB)</td></tr>
<tr><td>mapreduce.Map.Java.opts</td><td>Opte = Xmx512m (anteriormente - Xmx410m)</td></tr>
<tr><td>mapreduce.reduce.Memory</td><td>1536 MB (anteriormente 1024 MB)</td></tr>
<tr><td>mapreduce.reduce.Java.opts</td><td>Opte = Xmx1024m (anteriormente - Xmx819m)</td></tr>
<tr><td>yarn.App.mapreduce.AM.Resource</td><td>768 MB (anteriormente 1024 MB)</td></tr>
<tr><td>yarn.App.mapreduce.AM.Command</td><td>Opte = Xmx512m (anteriormente - Xmx819m)</td></tr>
<tr><td>mapreduce.Task.IO.sort</td><td>256 MB (anteriormente 200 MB)</td></tr>
<tr><td>tez.AM.Resource.Memory</td><td>1536 MB (inalterado)</td></tr>

</table><br>

Para mais informações sobre as definições de configuração de memória utilizado pelo FIO e MapReduce a plataforma de dados de Hortonworks que é utilizado pelo HDInsight, consulte o artigo [Determinar definições de configuração de memória HDP](http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1-latest/bk_installing_manually_book/content/rpm-chap1-11.html). Hortonworks também fornecida uma ferramenta para calcular as definições de memória inicial maiúscula.

Sobre o PowerShell do Azure e a mensagem de erro HDInsight SDK: "*Cluster não estiver configurado para o acesso de serviços HTTP*":

* Este erro é um [problema de compatibilidade](https://social.msdn.microsoft.com/Forums/azure/a7de016d-8de1-4385-b89e-d2e7a1a9d927/hdinsight-powershellsdk-error-cluster-is-not-configured-for-http-services-access?forum=hdinsight) conhecido que pode ocorrer devido a uma diferença entre a versão do SDK do HDInsight ou PowerShell do Azure e a versão do cluster. Clusters criados no 8/15 ou posterior existe suporte para a nova capacidade de aprovisionamento em redes virtuais. Mas esta funcionalidade não está corretamente interpretada por versões mais antigas do HDInsight SDK ou Azure PowerShell. O resultado é uma falha em algumas operações de submissão da tarefa. Se utilizar o HDInsight SDK APIs ou os cmdlets do PowerShell do Azure (**Utilizar AzureRmHDInsightCluster** ou **Invocar AzureRmHDInsightHiveJob**) para submeter tarefas, essas operações podem falhar com a mensagem de erro "*Cluster <clustername> não está configurado para o acesso de serviços HTTP*." Ou (consoante a operação), poderá obter outras mensagens de erro, tal como "*não é possível ligar ao cluster*".

* As versões mais recentes do HDInsight SDK e Azure PowerShell resolvidos destes problemas de compatibilidade. Recomendamos que atualizar o SDK HDInsight a versão 1.3.1.6 ou posteriores e Azure ferramentas de PowerShell para a versão 0.8.8 ou posterior. Pode obter um acesso o SDK do HDInsight mais recente do [](http://nuget.codeplex.com/wikipage?title=Getting%20Started) e as ferramentas de PowerShell Azure como [instalar e configurar o Azure PowerShell](../powershell-install-configure.md).



## <a name="notes-for-9122014-release-of-hdinsight-31"></a>Notas de lançamento de 12/9/2014 do HDInsight 3.1

* Nesta versão é baseada no Hortonworks dados plataforma (HDP) 2.1.5. Para obter uma lista de erros fixa nesta versão, consulte a página de [fixo neste lançamento](http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.5/bk_releasenotes_hdp_2.1/content/ch_relnotes-hdp-2.1.5-fixed.html) no Hortonworks site.
* Na pasta de bibliotecas porco, o ficheiro "avro-mapred 1.7.4.jar" ter sido alterado para "avro-mapred-1.7.4-hadoop2.jar." O conteúdo deste ficheiro contém uma correção de erros secundária que faz sem quebra. Recomenda-se que os clientes não faça uma dependência direta no nome do ficheiro para caixa para evitar quebras quando são mudados ficheiros.


## <a name="notes-for-8212014-release"></a>Notas de lançamento 21/8/2014

* Vamos está a adicionar a seguinte configuração de WebHCat (7155 ramo), que define o limite de memória predefinido para uma tarefa de controlador Templeton a 1 GB. (O valor predefinido anterior era 512 MB).

     templeton.mapper.Memory.MB (= 1024)

    * Esta alteração resolve o erro seguinte que determinadas consultas ramo tinham executar para devido inferiores limites de memória: "Contentor está em execução para além dos limites de memória física."
    * Para reverter para as predefinições antigas, pode definir a 512 através do Azure PowerShell este valor configuração na hora de criação de cluster utilizando o seguinte comando:

        AzureRmHDInsightConfigValues adicionar-Core@{"templeton.mapper.memory.mb"="512";}


* O nome do anfitrião da função zookeeper foi alterado para *zookeeper*. Isto afeta a resolução do nome dentro do cluster, mas não afeta API REST externas. Se tiver componentes que utilizam o nome do anfitrião *zookeepernode* , terá de atualizá-las para utilizar o novo nome. Os novos nomes para os nós zookeeper três são:
    * zookeeper0
    * zookeeper1
    * zookeeper2
* Matriz de suporte de versão HBase é atualizado. HDInsight apenas a versão 3.1 (HBase versão 0.98) é suportada para produção para HBase cargas de trabalho. Versão 3.0 (que foi disponível para a pré-visualização) não é suportada mover reencaminhar.

## <a name="notes-about-clusters-created-prior-to-8152014"></a>Notas sobre clusters criados antes de 8/15/2014

Uma mensagem de erro Azure PowerShell ou SDK do HDInsight, "Cluster <clustername> não está configurado para o acesso de serviços HTTP" (ou, consoante a operação, outras mensagens de erro tais como: "Não é possível ligar ao cluster") poderá ser encontrada devido a uma diferença entre o Azure PowerShell ou o SDK HDInsight e um cluster de entre as versões. Clusters criados no 8/15 ou posterior existe suporte para a nova capacidade de aprovisionamento em redes virtuais. Esta funcionalidade não está corretamente interpretada por versões mais antigas do Azure PowerShell ou o SDK do HDInsight, que resulta numa falhas de operações de submissão de tarefa. Se utilizar cmdlets HDInsight SDK APIs ou Azure PowerShell (como utilizar AzureRmHDInsightCluster ou invocar AzureRmHDInsightHiveJob) para submeter tarefas, essas operações podem falhar com uma das mensagens de erro descritas.

As versões mais recentes do HDInsight SDK e Azure PowerShell resolvidos destes problemas de compatibilidade. Recomendamos que atualizar o SDK HDInsight a versão 1.3.1.6 ou posteriores e Azure ferramentas de PowerShell para a versão 0.8.8 ou posterior. Pode obter um acesso o SDK do HDInsight mais recente do [NuGet][nuget-link]. Que pode aceder às ferramentas de PowerShell do Azure utilizando o [Microsoft Web plataforma Installer][webpi-link].


## <a name="notes-for-7282014-release"></a>Notas de lançamento 28/7/2014

* **HDInsight disponível no novas regiões**: Recomendamos expandido HDInsight geográfica presença para três regiões. HDInsight clientes podem criar clusters nas seguintes regiões:
    * Este asiático
    * América do Norte Central (EUA)
    * Sul Central (EUA)
* HDInsight versão 1.6 (HDP 1.1 e Hadoop 1.0.3) e HDInsight versão 2.1 (HDP1.3 e Hadoop 1.2) estão a ser removida de portal do Azure. Pode continuar a criar clusters de Hadoop para estas versões utilizando o cmdlet do PowerShell do Azure, [AzureRmHDInsightCluster novo](http://msdn.microsoft.com/library/dn593744.aspx) ou utilizando o [HDInsight SDK](http://msdn.microsoft.com/library/azure/dn469975.aspx). Consulte a página de [controlo de versões do HDInsight componente](hdinsight-component-versioning.md) para obter mais informações.
* Alterações de plataforma de dados de Hortonworks (HDP) nesta versão:

<table border="1">
<tr><th>HDP</th><th>Alterações</th></tr>
<tr><td>HDP 1.3 / HDI 2.1</td><td>Sem alterações</td></tr>
<tr><td>HDP 2.0 / HDI 3.0</td><td>Sem alterações</td></tr>
<tr><td>HDP 2.1 / HDI 3.1</td><td>zookeeper: ['3.4.5.2.1.3.0-1948'] -> ['3.4.5.2.1.3.2-0002']</td></tr>


</table><br>

## <a name="notes-for-6242014-release"></a>Notas de lançamento de 24/6/2014

Nesta versão contém melhoramentos para o serviço de HDInsight:

* **Disponibilidade de HDP 2.1**: 3.1 HDInsight (que contém HDP 2.1) é disponibilizado e é a versão predefinida para novos clusters.
* **HBase – Azure melhorias portais**: estamos a tornar HBase clusters disponível na pré-visualização. Pode criar HBase clusters a partir do portal com apenas alguns cliques. 

Com HBase, pode criar uma variedade de cargas de trabalho em tempo real no HDInsight, de Web sites interativos que funcionam com grandes conjuntos de dados para serviços de armazenar dados sensor e de telemetria de milhões de pontos de fim. O passo seguinte seria analisar os dados estas cargas de trabalho com tarefas de Hadoop e isto é possível no HDInsight através do PowerShell do Azure e o dashboard de cluster ramo.

### <a name="apache-mahout-preinstalled-on-hdinsight-31"></a>Mahout Apache pré-instalado em HDInsight 3.1

 [Mahout](http://hortonworks.com/hadoop/mahout/) se encontra instalado no clusters HDInsight 3.1 Hadoop, para que possa executar tarefas Mahout sem necessidade de configuração do cluster adicionais. Por exemplo, pode remoto para um cluster de Hadoop com protocolo de ambiente de trabalho remoto (RDP) e, sem passos adicionais, pode executar o seguinte comando Olá mundo Mahout:

        mahout org.apache.mahout.classifier.df.tools.Describe -p /user/hdp/glass.data -f /user/hdp/glass.info -d I 9 N L  

        mahout org.apache.mahout.classifier.df.BreimanExample -d /user/hdp/glass.data -ds /user/hdp/glass.info -i 10 -t 100

Para obter uma explicação mais completa deste procedimento, consulte a documentação para o [Exemplo Breiman](https://mahout.apache.org/users/classification/breiman-example.html) no Web site da Apache Mahout.


### <a name="hive-queries-can-use-tez-in-hdinsight-31"></a>Ramo de consultas podem utilizar Tez HDInsight 3.1

Ramo 0.13 está disponível no HDInsight 3.1 e é capaz de executar consultas através do Tez, que pode ser utilizada para obter melhorias de desempenho.
Tez não está a ativar por predefinição para ramo de consultas. Para utilizá-la, tem de optar por no. Pode ativar Tez executando o seguinte fragmento de código:

        set hive.execution.engine=tez;
        select sc_status, count(*), histogram_numeric(sc_bytes,5) from website_logs_orc_local group by sc_status;

Hortonworks publicou uma desagregação detalhada dos melhoramentos de desempenho do ramo de consulta com Tez tal como foi fornecido no padrão de referência. Para obter detalhes, consulte o artigo [direccionamento de caminhos 13 de Hive Apache para o Enterprise Hadoop](http://hortonworks.com/blog/benchmarking-apache-hive-13-enterprise-hadoop/).

Para obter mais detalhes sobre como utilizar ramo com Tez, consulte o artigo [Hive no Tez](https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez).

###<a name="global-availability"></a>Disponibilidade global
Com a versão do HDInsight no Hadoop 2.2, Microsoft disponibilizou HDInsight em todas as principais geografias onde o Azure está disponível. Especificamente, os centros de dados do Europa e Sudeste asiático oeste tem sido colocados online. Permite que clientes localizar clusters num centro de dados que está a fechar e potencialmente numa zona de requisitos de conformidade semelhantes.


###<a name="dos--donts-between-cluster-versions"></a>Coisas a fazer e entre versões de cluster

**Metastores Oozie utilizadas com um cluster de HDInsight 3.1 são não compatível com o HDInsight 2.1 clusters, e não podem ser utilizados com esta versão anterior**.

Não pode ser reutilizado Oozie metastore base de dados personalizada implementado com um cluster de HDInsight 3.1 com um cluster de HDInsight 2.1. Este é o caso, mesmo se o metastore teve origem com um cluster de HDInsight 2.1. Este cenário não é suportado porque o esquema de metastore obtém actualizado quando utilizado com um cluster de HDInsight 3.1, pelo que já não é compatível com metastore uma ferramenta necessária ao clusters HDInsight 2.1. Qualquer tentativa de reutilizar um metastore Oozie que tenha sido utilizada com um cluster de HDInsight 3.1 serão compostas cluster HDInsight 2.1 inútil.

**Oozie metastores não podem ser partilhadas por clusters.**

Oozie metastores estão anexados a clusters específicos e não pode ser partilhados através de clusters.

###<a name="breaking-changes"></a>Força de alterações

**Sintaxe do prefixo**: apenas o "wasbs: / /" sintaxe é suportado no HDInsight 3.1 e 3.0 clusters. O mais antigo "VSA: / /" sintaxe é suportada no HDInsight 2.1 e 1.6 clusters, mas não é suportada no HDInsight 3.1 ou 3.0 clusters. Isto significa que quaisquer tarefas submetidas a uma 3.1 HDInsight ou 3.0 cluster que utilizam explicitamente o "VSA: / /" sintaxe irá falhar. O "wasbs: / /" sintaxe deverá ser utilizado em vez disso. Além disso, as tarefas submetidas para qualquer 3.1 HDInsight ou 3.0 clusters que são criados com um metastore existente que contém referências explícitas a recursos utilizando o "VSA: / /" sintaxe irá falhar. Estes metastores precisa de ser novamente criado utilizando o "wasbs: / /" sintaxe para recursos de endereço.


**Portas**: as portas utilizadas pelo serviço HDInsight foram alteradas. Os números de porta que foram a ser utilizados foram dentro do intervalo de portas efémeras do sistema operativo Windows. Portas são automaticamente atribuídas a partir de um intervalo efémero predefinido para comunicações curto de baseadas em protocolo de Internet. O novo conjunto de números de porta de serviço da plataforma de dados de Hortonworks (HDP) permitidos estão fora deste intervalo para evitar a ocorrência de conflitos que podem surgir com as portas utilizadas pelo services em execução no nó cabeça. Os novos números de porta devem não fazer com que as alterações recentes. Os números de utilizados são os seguintes:

 **HDInsight 1.6 (HDP 1.1)**
<table border="1">
<tr><th>Nome</th><th>Valor</th></tr>
<tr><td>DFS.HTTP.address</td><td>namenodehost:30070</td></tr>
<tr><td>DFS.datanode.address</td><td>0.0.0.0:30010</td></tr>
<tr><td>DFS.datanode.HTTP.address</td><td>0.0.0.0:30075</td></tr>
<tr><td>DFS.datanode.IPC.address</td><td>0.0.0.0:30020</td></tr>
<tr><td>DFS.Secondary.HTTP.address</td><td>0.0.0.0:30090</td></tr>
<tr><td>mapred.job.Tracker.HTTP.address</td><td>jobtrackerhost:30030</td></tr>
<tr><td>mapred.Task.Tracker.HTTP.address</td><td>0.0.0.0:30060</td></tr>
<tr><td>mapreduce.history.Server.HTTP.address</td><td>0.0.0.0:31111</td></tr>
<tr><td>templeton.Port</td><td>30111</td></tr>
</table><br>

 **HDInsight 3.1 e 3.0 (HDP 2.1 e 2.0)**
<table border="1">
<tr><th>Nome</th><th>Valor</th></tr>
<tr><td>endereço de DFS.namenode.http</td><td>namenodehost:30070</td></tr>
<tr><td>endereço de DFS.namenode.HTTPS</td><td>headnodehost:30470</td></tr>
<tr><td>DFS.datanode.address</td><td>0.0.0.0:30010</td></tr>
<tr><td>DFS.datanode.HTTP.address</td><td>0.0.0.0:30075</td></tr>
<tr><td>DFS.datanode.IPC.address</td><td>0.0.0.0:30020</td></tr>
<tr><td>endereço de DFS.namenode.Secondary.http</td><td>0.0.0.0:30090</td></tr>
<tr><td>yarn.nodemanager.webapp.address</td><td>0.0.0.0:30060</td></tr>
<tr><td>templeton.Port</td><td>30111</td></tr>
</table><br>

###<a name="dependencies"></a>Dependências

As seguintes dependências foram adicionadas no HDInsight 3. x (HDP2.x):

* guice servlet
* optiq core
* javax.inject
* ativação
* jsr305
* geronimo-jaspic_1.0_spec
* Jul para slf4j
* Java xmlbuilder
* conselhos
* Commons compilador
* jdo api
* Commons math3
* paranamer
* jaxb impl
* stringtemplate
* eigenbase xom
* Jersey servlet
* Commons execução
* jaxb api
* servidor de todas as cais
* janino
* xercesImpl
* optiq avatica
* jta
* Propriedades de eigenbase
* groovy tudo
* hamcrest core
* correio
* linq4j
* jpam
* cliente Jersey
* aopalliance
* geronimo-annotation_1.0_spec
* Iniciador de conselhos
* Jersey guice
* XML apis
* stax api
* Asm commons
* Asm árvore
* wadl
* geronimo-jta_1.1_spec
* guice
* leveldbjni tudo
* velocidade
* proceder
* chamativas java
* cais tudo
* Commons dbcp

As seguintes dependências já não existem no HDInsight 3. x (HDP2.x):

* jdeb
* kfs
* sqlline
* hera
* aspectjrt
* JSON
* principais
* jdo2 api
* avro mapred
* datanucleus enhancer
* JSP
* api do registo Commons
* matemática Commons
* JavaEWAH
* aspectjtools
* javolution
* hdfsproxy
* hbase
* chamativas

###<a name="version-changes"></a>Alterações de versão

As seguintes alterações de versão foram efetuadas entre HDInsight 2. x (HDP1.x) e HDInsight 3. x (HDP2.x):

* métricas core: ['2.1.2'] -> ['3.0.0']
* derbynet: ['10.4.2.0'] -> ['10.10.1.1']
* datanucleus: ['rdbms-3.0.8'] -> ['rdbms-3.2.9']
* jasper compilador: ['5.5.12'] -> ['5.5.23']
* log4j: ['1.2.15', '1.2.16'] -> ['1.2.16', '1.2.17']
* derbyclient: ['10.4.2.0'] -> ['10.10.1.1']
* httpcore: ['4.2.4'] -> ['ponto 4.2.5']
* hsqldb: ['1.8.0.10'] -> ['2.0.0']
* jets3t: ['regulamentares 0.6.1'] -> ['0.9.0']
* protobuf java: ['2.4.1'] -> ['2.5.0']
* derby: ['10.4.2.0'] -> ['10.10.1.1']
* jasper: ['runtime-5.5.12'] -> ['runtime-5.5.23']
* Commons daemon: ['1.0.1'] -> ['1.0.13']
* datanucleus core: ['3.0.9'] -> ['3.2.10']
* api-datanucleus-jdo: ['3.0.7'] -> ['3.2.6']
* zookeeper: ['3.4.5.1.3.9.0-01320'] -> ['3.4.5.2.1.3.0-1948']
* bonecp: ['0.7.1.RELEASE'] -> ['
* 0.8.0.RELEASE']


### <a name="drivers"></a>Controladores
O controlador da base de dados de Java Connnectivity (JDBC) para o SQL Server é utilizado pelo HDInsight e não é utilizada para operações de externo. Se pretender ligar ao HDInsight utilizando Open Database Connectivity (ODBC), utilize o controlador de ODBC Hive da Microsoft. Para mais informações, consulte o artigo [Ligar o Excel ao HDInsight com o controlador de ODBC Hive da Microsoft](hdinsight-connect-excel-hive-odbc-driver.md).


### <a name="bug-fixes"></a>Correções de erros

Com esta versão, podemos ter atualizados as seguintes versões do HDInsight com várias correções de erros:

* HDInsight 2.1 (HDP 1.3)
* HDInsight 3.0 (HDP 2.0)
* HDInsight 3.1 (HDP 2.1)


## <a name="hortonworks-release-notes"></a>Notas de lançamento Hortonworks

Notas de lançamento das plataformas de dados de Hortonworks (HDPs) são utilizados pelo clusters de versão HDInsight estão disponíveis nas seguintes localizações:

* HDInsight versão 3.1 utiliza uma distribuição de Hadoop baseia-se a [Plataforma de dados Hortonworks 2.1.7][hdp-2-1-7]. Este é cluster Hadoop predefinido criado quando utilizar o portal do Azure após 7/11/2014. HDInsight 3.1 clusters criados antes de 7/11/2014 foram com base na [Hortonworks dados plataforma 2.1.1][hdp-2-1-1]

* HDInsight versão 3.0 utiliza uma distribuição Hadoop que é baseada no [Hortonworks dados plataforma 2.0][hdp-2-0-8].

* HDInsight versão 2.1 utiliza uma distribuição Hadoop que é baseada na [Hortonworks dados plataforma 1.3][hdp-1-3-0].

* HDInsight versão 1.6 utiliza uma distribuição Hadoop que é baseada no [Hortonworks dados plataforma 1.1][hdp-1-1-0].

[hdp-2-1-7]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.7-Win/bk_releasenotes_HDP-Win/content/ch_relnotes-HDP-2.1.7.html

[hdp-2-1-1]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.1/bk_releasenotes_hdp_2.1/content/ch_relnotes-hdp-2.1.1.html

[hdp-2-0-8]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.0.8.0/bk_releasenotes_hdp_2.0/content/ch_relnotes-hdp2.0.8.0.html

[hdp-1-3-0]: http://docs.hortonworks.com/HDPDocuments/HDP1/HDP-1.3.0/bk_releasenotes_hdp_1.x/content/ch_relnotes-hdp1.3.0_1.html

[hdp-1-1-0]: http://docs.hortonworks.com/HDPDocuments/HDP1/HDP-Win-1.1/bk_releasenotes_HDP-Win/content/ch_relnotes-hdp-win-1.1.0_1.html

[nuget-link]: https://www.nuget.org/packages/Microsoft.WindowsAzure.Management.HDInsight/

[webpi-link]: http://go.microsoft.com/?linkid=9811175&clcid=0x409

[hdinsight-install-spark]: ../hdinsight-hadoop-spark-install/
[hdinsight-r-scripts]: ../hdinsight-hadoop-r-scripts/
 
