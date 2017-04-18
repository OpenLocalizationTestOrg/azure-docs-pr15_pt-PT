<properties
    pageTitle="Importar dados para Studio de formação de máquina de origens de dados online | Microsoft Azure"
    description="Como importar os dados de formação Azure máquina aprendizagem Studio a partir de várias origens onlinehttps."
    keywords="Importar dados, formato de dados, tipos de dados, origens de dados, dados de formação"
    services="machine-learning"
    documentationCenter=""
    authors="bradsev"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/19/2016"
    ms.author="bradsev;garye" />


# <a name="import-data-into-azure-machine-learning-studio-from-various-online-data-sources-with-the-import-data-module"></a>Importar dados para o Azure máquina aprendizagem Studio a partir de várias origens de dados online com o módulo importar dados

Este artigo descreve o suporte para importar dados online a partir de várias origens e as informações necessárias para mover os dados a partir destes origens para uma experiência de aprendizagem do Azure máquina.

> [AZURE.NOTE] Este artigo fornece informações gerais sobre a [Importar dados] [ import-data] módulo. Para obter informações sobre os tipos de dados, pode aceder a mais detalhadas, formatos, parâmetros e respostas a perguntas comuns, consulte o tópico de referência do módulo para os [Importar dados] [ import-data] módulo.

<!-- -->

[AZURE.INCLUDE [import-data-into-aml-studio-selector](../../includes/machine-learning-import-data-into-aml-studio.md)]

## <a name="introduction"></a>Introdução

Pode aceder a dados a partir do Studio de aprendizagem do Azure máquina de uma das várias origens de dados online enquanto a experiência estiver em execução, utilizando a [Importar dados] [ import-data] módulo:

- Um URL de Web através de HTTP
- Hadoop utilizando HiveQL
- Armazenamento de Blobs do Azure
- Tabela do Azure
- Base de dados SQL Azure ou do SQL Server no Azure VM
- Base de dados do SQL Server no local
- Um fornecedor, OData atualmente de feed de dados
 
Fluxo de trabalho para condutores experiências no Azure máquina aprendizagem Studio consiste em componentes de arrastar e largar para a tela. Para aceder a origens de dados online, adicione os [Importar dados] [ import-data] módulo a sua experiência, selecione a **origem de dados**e, em seguida, forneça os parâmetros necessários para aceder aos dados. As origens de dados online que são suportadas são listadas na tabela abaixo. A tabela seguinte resume também os formatos de ficheiro que são suportados e parâmetros são utilizados para aceder aos dados.

Tenha em atenção que uma vez que estes dados de formação são acedidos enquanto a experiência estiver em execução, só está disponível dessa experiência. Por comparação, os dados que tem sido armazenados num módulo do conjunto de dados estão disponíveis para qualquer experiência na área de trabalho.

> [AZURE.IMPORTANT] Atualmente, a [Importar dados] [ import-data] e [Exportação de dados] [ export-data] módulos podem ler e escrever dados apenas a partir do Azure armazenamento criado utilizando o modelo de implementação clássica. Por outras palavras, o novo tipo de conta de armazenamento de Blobs do Azure oferece uma camada de acesso de armazenamento quente ou camada de acesso de armazenamento modernos ainda não é suportado. 

> Em geral, todas as contas de armazenamento Azure que pode ter criado antes de ficou disponível esta opção de serviço não deve afectado. Se necessitar de criar uma nova conta, selecione **clássico** para o modelo de implementação, ou utilize o Gestor de recursos e para **o tipo de conta**, selecione **Geral** em vez de **armazenamento de BLOBs**. 

> Para obter mais informações, consulte o artigo [armazenamento de Blobs do Azure: camadas de armazenamento quente e fria](../storage/storage-blob-storage-tiers.md).



## <a name="supported-online-data-sources"></a>Origens de dados online suportadas
Módulo Azure de aprendizagem máquina **Importar dados** suporta seguintes origens de dados:

Origem de dados | Descrição | Parâmetros |
---|---|---|
URL da Web através de HTTP |Lê dados em valores separados por vírgulas (CSV), valores delimitado por tabulações (TSV), o formato de ficheiro de atributo relação (ARFF) e formatos de suporte Vector máquinas (SVM light), a partir de qualquer URL da web que utiliza HTTP | <b>URL</b>: Especifica o nome completo do ficheiro, incluindo o URL do site e o nome do ficheiro, com uma extensão. <br/><br/><b>Formato de dados</b>: Especifica um dos dados suportados formatos: CSV, TSV, ARFF ou SVM light. Se os dados tiverem uma linha de cabeçalho, é utilizado para atribuir nomes das colunas.|
Hadoop/HDFS|Lê dados a partir do armazenamento distribuído no Hadoop. Especifique os dados que pretende utilizando HiveQL, um idioma de consulta SQL gosto. HiveQL pode também ser utilizado para agregar dados e executar antes de adicionar os dados a Studio de formação de máquina de filtragem de dados.|<b>Ramo de consulta de base de dados</b>: Especifica a consulta de ramo utilizada para gerar os dados.<br/><br/><b>URI do servidor de HCatalog</b> : especificado no nome do seu cluster utilizando o formato * &lt;o nome de cluster&gt;. azurehdinsight.net.*<br/><br/><b>Nome de conta de utilizador Hadoop</b>: Especifica o nome de conta de utilizador Hadoop utilizado para aprovisionar o cluster.<br/><br/><b>Palavra-passe de conta de utilizador Hadoop</b> : Especifica as credenciais utilizadas quando Aprovisiona o cluster. Para mais informações, consulte o artigo [Criar Hadoop clusters no HDInsight](../hdinsight/hdinsight-provision-clusters.md).<br/><br/><b>Localização dos dados de saída</b>: Especifica se os dados são armazenados num sistema de ficheiros distribuído de Hadoop (HDFS) ou no Azure. <br/><ul>Se armazenar dados de saída HDFS, especifique o servidor HDFS URI. (Não se esqueça de utilizar o nome do cluster HDInsight sem o prefixo HTTPS://). <br/><br/>Se armazenar os dados de saída no Azure, tem de especificar o nome da conta de armazenamento Azure, a tecla de acesso de armazenamento e o nome do contentor de armazenamento.</ul>|
Base de dados SQL |Lê dados armazenados numa base de dados Azure SQL ou numa base de dados do SQL Server em execução numa máquina virtual Azure. | <b>Nome do servidor de base de dados</b>: Especifica o nome do servidor no qual a base de dados está a ser executado.<br/><ul>Em caso de base de dados do SQL Azure, introduza o nome do servidor que é gerado. Normalmente, que tem o formulário * &lt;generated_identifier&gt;. database.windows.net.* <br/><br/>Em caso de SQL server alojado num computador Azure Virtual introduza *tcp:&lt;Máquina Virtual DNS nome&gt;, 1433*</ul><br/><b>Nome da base de dados </b>: Especifica o nome da base de dados no servidor. <br/><br/><b>Nome de conta de utilizador do servidor</b>: Especifica um nome de utilizador para uma conta que possua permissões de acesso para a base de dados. <br/><br/><b>Palavra-passe de conta de utilizador Server</b>: Especifica a palavra-passe da conta de utilizador.<br/><br/><b>Aceitar qualquer certificado do servidor</b>: Utilize esta opção (menos segura) se pretende ignorar a revisão do certificado de site antes de ler os seus dados.<br/><br/><b>Consulta de base de dados</b>: introduza uma instrução SQL que descreva os dados que pretende ler.|
Base de dados do SQL no local |Lê dados armazenados numa base de dados SQL no local. | <b>Gateway de dados</b>: Especifica o nome do Data Management Gateway instalado no computador onde pode aceder a sua base de dados do SQL Server. Para obter informações sobre como configurar o gateway, consulte o artigo [Executar avançadas analytics com formação de máquina Azure através de dados do SQL server no local](machine-learning-use-data-from-an-on-premises-sql-server.md).<br/><br/><b>Nome do servidor de base de dados</b>: Especifica o nome do servidor no qual a base de dados está a ser executado.<br/><br/><b>Nome da base de dados </b>: Especifica o nome da base de dados no servidor. <br/><br/><b>Nome de conta de utilizador do servidor</b>: Especifica um nome de utilizador para uma conta que possua permissões de acesso para a base de dados. <br/><br/><b>Nome de utilizador e palavra-passe</b>: clique em <b>valores de Enter</b> para introduzir as suas credenciais de base de dados. Pode utilizar autenticação integrada do Windows ou a autenticação do SQL Server, consoante a forma como o SQL Server no local está configurado.<br/><br/><b>Consulta de base de dados</b>: introduza uma instrução SQL que descreva os dados que pretende ler.|
Tabela do Azure|Lê dados a partir do serviço de tabela no armazenamento do Windows Azure.<br/><br/>Se ler grandes quantidades de dados com pouca frequência, utilize o serviço de tabela do Azure. Fornece um flexíveis que não sejam relacionais (NoSQL), a solução de armazenamento previstos excederem consideravelmente dimensionáveis, dispendioso e altamente disponível.| Alteram as opções na **Importar dados** dependendo se está a aceder ao públicas informações ou com uma conta de armazenamento privado credenciais de início de sessão. Isto é determinado pelo <b>Tipo de autenticação</b> que pode ter o valor de "PublicOrSAS" ou "Conta", cada um dos quais tem o seu próprio conjunto de parâmetros. <br/><br/><b>Público ou assinatura partilhados de acesso (SA) URI</b>: os parâmetros são:<br/><br/><ul><b>URI de tabela</b>: Especifica o público ou SA URL para a tabela.<br/><br/><b>Especifica as linhas para verificar a existência de nomes de propriedades</b>: os valores são <i>TopN</i> para analisar o número especificado de linhas ou <i>ScanAll</i> para obter todas as linhas na tabela. <br/><br/>Se os dados são previsíveis e homogénea, recomendamos que selecione *TopN* e introduza um número para N. Para tabelas grandes, isto pode resultar em vezes mais rápidos de leitura.<br/><br/>Se os dados estão estruturados com conjuntos de propriedades que variam com base no profundidade e a posição da tabela, selecione a opção de *ScanAll* para digitalizar todas as linhas. Este procedimento garante a integridade dos seus propriedade resultante e conversão de metadados.<br/><br/></ul><b>Conta de armazenamento privado</b>: os parâmetros são: <br/><br/><ul><b>Nome da conta</b>: Especifica o nome da conta que contém a tabela de ler.<br/><br/><b>Chave de conta</b>: Especifica a chave de armazenamento associada à conta.<br/><br/><b>Nome da tabela</b> : Especifica o nome da tabela que contém os dados de ler.<br/><br/><b>Linhas para verificar a existência de nomes de propriedades</b>: os valores são <i>TopN</i> para analisar o número especificado de linhas ou <i>ScanAll</i> para obter todas as linhas na tabela.<br/><br/>Se os dados são previsíveis e homogénea, recomendamos que selecione *TopN* e introduza um número para N. Para tabelas grandes, isto pode resultar em vezes mais rápidos de leitura.<br/><br/>Se os dados estão estruturados com conjuntos de propriedades que variam com base no profundidade e a posição da tabela, selecione a opção de *ScanAll* para digitalizar todas as linhas. Este procedimento garante a integridade dos seus propriedade resultante e conversão de metadados.<br/><br/>|
Armazenamento de Blobs do Azure | Lê dados armazenados no serviço Blob no armazenamento do Windows Azure, incluindo imagens, texto não estruturado ou dados binários.<br/><br/>Pode utilizar o serviço de BLOBs para expor publicamente dados ou para armazenar os dados da aplicação em privado. Pode aceder aos dados a partir de qualquer lugar, utilizando ligações de HTTP ou HTTPS. | Alteram as opções no módulo **Importar dados** dependendo se está a aceder ao públicas informações ou com uma conta de armazenamento privado credenciais de início de sessão. Isto é determinado por <b>Tipo de autenticação</b> que pode ter um valor de "PublicOrSAS" ou de "Conta".<br/><br/><b>Público ou assinatura partilhados de acesso (SA) URI</b>: os parâmetros são:<br/><br/><ul><b>URI</b>: Especifica público ou SA URL para o blob de armazenamento.<br/><br/><b>Formato de ficheiro</b>: Especifica o formato dos dados no serviço Blob. Os formatos suportados são CSV, TSV e ARFF.<br/><br/></ul><b>Conta de armazenamento privado</b>: os parâmetros são: <br/><br/><ul><b>Nome da conta</b>: Especifica o nome da conta que contém o blob que pretende ler.<br/><br/><b>Chave de conta</b>: Especifica a chave de armazenamento associada à conta.<br/><br/><b>Caminho para o contentor, diretório ou blob</b> : Especifica o nome do blob que contém os dados de ler.<br/><br/><b>Formato de ficheiro blob</b>: Especifica o formato dos dados no serviço blob. Os formatos de dados suportadas são CSV, TSV, ARFF, CSV com uma codificação especificada e o Excel. <br/><br/><ul>Se o formato CSV ou TSV, certifique-se de que indicam se o ficheiro contém uma linha de cabeçalho.<br/><br/>Pode utilizar a opção do Excel para ler dados a partir de livros do Excel. A opção de <i>formato de dados do Excel</i> , indicar se os dados são um intervalo de folha de cálculo do Excel ou numa tabela do Excel. Na opção <i>folha do Excel ou tabela incorporada </i>, especifique o nome da folha ou tabela que pretende ler a partir do.</ul><br/>|
Fornecedor de Feeds de dados | Lê dados a partir de um fornecedor de feed suportado. Atualmente apenas o formato Open Data Protocol (OData) é suportado. | <b>Tipo de conteúdo de dados</b>: Especifica o formato de OData.<br/><br/><b>URL de origem</b>: Especifica o URL completo no feed de dados. <br/>Por exemplo, o seguinte URL lê a partir da base de dados de exemplo Northwind: http://services.odata.org/northwind/northwind.svc/|


<!-- Module References -->
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
[export-data]: https://msdn.microsoft.com/library/azure/7A391181-B6A7-4AD4-B82D-E419C0D6522C/
