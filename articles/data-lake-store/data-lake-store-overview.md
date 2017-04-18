<properties
   pageTitle="Descrição geral do Azure Lake de arquivo de dados | Microsoft Azure"
   description="Compreender o que é o arquivo de Lake de dados do Azure e o valor-fornece através de outros arquivos de dados"
   services="data-lake-store"
   documentationCenter=""
   authors="nitinme"
   manager="jhubbard"
   editor="cgronlun"/>

<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="10/28/2016"
   ms.author="nitinme"/>

# <a name="overview-of-azure-data-lake-store"></a>Descrição geral do Azure Lake de arquivo de dados

Azure dados Lake arquivo é um repositório de escala hyper ao nível da empresa das cargas de trabalho analítico dados grande. Azure dados Lake permite-lhe capturar dados de qualquer tamanho, tipo e ingestão velocidade num único local único para análise operacional e exploratória.

> [AZURE.TIP] Utilize o [arquivo de dados de Lake caminho de formação](https://azure.microsoft.com/documentation/learning-paths/data-lake-store-self-guided-training/) para começar a explorar o serviço de arquivo do Azure dados Lake.

Azure dados Lake loja podem ser acedida a partir de Hadoop (disponível com HDInsight cluster) utilizando as APIs de resto compatível com o WebHDFS. -Concebido especificamente para ativar a análise nos dados armazenados e está optimizada para desempenho de cenários de análise de dados. Terminar a caixa de, inclui todas as capacidades de empresarial — segurança, capacidade de gestão, escalabilidade, fiabilidade e disponibilidade — essenciais para os casos de utilização reais enterprise.


![Azure dados Lake](./media/data-lake-store-overview/data-lake-store-concept.png)

Algumas das capacidades de chaves do Lake de dados Azure incluem o seguinte.

### <a name="built-for-hadoop"></a>Criadas para Hadoop

O arquivo de Azure dados Lake é um sistema de ficheiro Apache Hadoop compatível com distribuído ficheiro Hadoop sistema (HDFS) e funciona com o ecossistema Hadoop.  Aplicações de HDInsight existente ou serviços que utilizam a API WebHDFS facilmente podem integrar com dados Lake arquivo. Arquivo de dados de Lake também expõe uma interface compatível com o WebHDFS REST para aplicações

Dados armazenados no arquivo de dados de Lake podem ser analisados de facilmente utilizando Hadoop quadros analíticos como MapReduce ou ramo. Podem ser aprovisionados e configurados para aceder diretamente a dados armazenados no arquivo de dados de Lake clusters do Microsoft Azure HDInsight.

### <a name="unlimited-storage-petabyte-files"></a>Armazenamento ilimitado, petabyte ficheiros

Azure dados Lake arquivo fornece ilimitado de armazenamento e adequado para armazenar uma variedade de dados para análise. Não pode impor qualquer limites de tamanhos de conta, tamanhos de ficheiro ou a quantidade de dados que podem ser armazenados num lake de dados. Ficheiros individuais podem variar entre quilobytes e petabytes de tamanho se tornou uma excelente escolha para armazenar qualquer tipo de dados. Os dados são armazenados durável ao efetuar várias cópias e não existe limite sobre a duração de tempo para os quais os dados podem ser armazenados numa lake dados.

### <a name="performance-tuned-for-big-data-analytics"></a>Desempenho optimizada para análise de dados grande

Azure dados Lake loja baseia-se para a execução de grande escala sistemas analíticos que requerem grandes débito para consultar e analisar grandes quantidades de dados. O lake dados propaga partes de um ficheiro de um número de servidores de armazenamento individuais. Este procedimento melhora o débito leitura ao ler o ficheiro em paralelo para efetuar análises de dados.


### <a name="enterprise-ready-highly-available-and-secure"></a>Pronto para Enterprise: Altamente-disponíveis e segura

Azure dados Lake arquivo fornece fiabilidade e disponibilidade de norma da indústria. Os elementos de dados são armazenados durável ao efetuar cópias redundantes para protecção contra quaisquer falhas inesperadas. Empresas podem utilizar Azure dados Lake as respetivas soluções como uma parte importante da sua plataforma de dados existente.

Arquivo de dados de Lake também fornece segurança empresarial para os dados armazenados. Para mais informações, consulte [proteger dados Azure dados Lake loja](#DataLakeStoreSecurity).


### <a name="all-data"></a>Todos os dados

Azure dados Lake loja podem armazenar quaisquer dados no respetivo formato nativo, tal como está, sem necessidade de quaisquer transformações prévias. Arquivo de dados de Lake não requer um esquema para ser definidas antes dos dados são carregados, deixando-o até a arquitetura analítica individual para interpretar os dados e definir um esquema no momento da análise. Ser capaz de armazenar ficheiros de tamanhos arbitrários e formatos torna possíveis para o arquivo de dados de Lake processar dados estruturados, semiestruturados e não estruturados.

Contentores de arquivo de Lake Azure dados para os dados são, essencialmente, pastas e ficheiros. Trabalhar com os dados armazenados através do SDK, Azure Portal e Azure Powershell. Desde que introduz dados para o arquivo de utilizar estas interfaces e os contentores adequados a utilizar, pode armazenar qualquer tipo de dados. Arquivo de dados de Lake não efetua qualquer tratamento especial de dados com base no tipo de dados armazena.


## <a name="DataLakeStoreSecurity"></a>Proteger os dados na loja do Azure dados Lake

Azure dados Lake loja utiliza o Azure Active Directory para autenticação e listas de controlo de acesso (ACL) para gerir o acesso aos seus dados.

| Funcionalidade                                 | Descrição                              |
|-----------------------------------------|------------------------------------------|
| Autenticação | Azure dados Lake loja integra-se com o Azure Active Directory (AAD) para a gestão de identidade e de acesso para todos os dados armazenados no Azure dados Lake arquivo. Estatístico como resultado da integração, benefícios Azure Lake de dados a partir de todas as funcionalidades AAD incluindo autenticação multifator, acesso condicional, controlo de acesso baseado em funções, monitorização da utilização de aplicações, segurança monitorização e alertar, etc. Azure dados Lake loja suporta o protocolo de OAuth 2.0 para autenticação com na interface de descanso. |
| Controlo de acesso                          | Azure dados Lake arquivo fornece o controlo de acesso por permissões de estilo POSIX expostas pelo protocolo WebHDFS de suporte. Na versão atual, ACL podem ser ativadas na pasta de raiz, subpastas, bem como ficheiros individuais. As ACL que aplicam-se para a pasta raiz irão também aplicável a todos os subordinados pastas/ficheiros também.|

Pretende saber mais sobre como proteger dados no arquivo de dados de Lake. Siga as ligações em baixo.

* Para obter instruções sobre como proteger dados no arquivo de dados de Lake, consulte [proteger dados Azure dados Lake loja](data-lake-store-secure-data.md).
* Prefere vídeos? [Veja este vídeo](https://mix.office.com/watch/1q2mgzh9nn5lx) sobre como proteger dados armazenados no arquivo de dados de Lake.

## <a name="applications-compatible-with-azure-data-lake-store"></a>Compatíveis com o Azure dados Lake loja de aplicações

Azure dados Lake loja é compatível com aberta mais componentes de origem no ecossistema Hadoop. Também integra corretamente com outros serviços do Azure. Isto torna arquivo de dados de Lake uma opção perfeita para as suas necessidades de armazenamento de dados. Siga as ligações abaixo para saber mais sobre como arquivo de dados de Lake pode ser utilizado tanto com componentes de abrir origem, bem como de outros serviços do Azure.

* Consulte o artigo [aplicações e serviços compatíveis com o arquivo de Lake Azure dados](data-lake-store-compatible-oss-other-applications.md) para uma lista de aplicações abrir origem interoperabilidade com dados Lake arquivo.
* Consulte o artigo [integração com outros serviços do Azure](data-lake-store-integrate-with-other-services.md) para compreender como arquivo de dados de Lake podem ser utilizado com outros serviços do Azure para ativar uma vasta gama de cenários.
* Consulte o artigo [cenários para utilizar o arquivo de dados de Lake](data-lake-store-data-scenarios.md) para saber como utilizar o arquivo de dados de Lake em cenários como ingesting dados, processamento de dados, transferir dados de e visualizar dados.

## <a name="what-is-azure-data-lake-store-file-system-adl"></a>O que é o sistema de ficheiros de arquivo do Azure dados Lake (adl: / /)?

Arquivo de dados de Lake pode ser acedido através do novo sistema de ficheiros, o AzureDataLakeFilesystem (adl: / /), nos ambientes Hadoop (disponíveis com HDInsight cluster). Aplicações e serviços que utilizam adl: / / podem tirar partido dos ainda mais optimização do desempenho que não estão atualmente disponíveis no WebHDFS. Como resultado, arquivo de dados de Lake fornece-lhe a flexibilidade de recorrer quer o melhor desempenho com a opção de utilização de adl recomendada: / / ou manter código existente por continuar a utilizar a API WebHDFS diretamente. Azure HDInsight totalmente tira partido de AzureDataLakeFilesystem para fornecer o melhor desempenho no arquivo de Lake de dados.

Pode aceder a dados a utilizar o arquivo de Lake dados `adl://<data_lake_store_name>.azuredatalakestore.net`. Para obter mais informações sobre como aceder aos dados do arquivo de dados Lake, consulte o artigo [Ver propriedades dos dados armazenados](data-lake-store-get-started-portal.md#properties)

## <a name="how-do-i-start-using-azure-data-lake-store"></a>Como posso começar a utilizar o arquivo de Lake de dados do Azure

Consulte o [artigo Introdução ao arquivo de dados de Lake utilizando o Portal de Azure](data-lake-store-get-started-portal.md), sobre como pode aprovisionar um arquivo de Lake dados utilizando o Portal do Azure. Assim que tiver aprovisionado Azure Lake de dados, pode aprender a utilizar ofertas de dados grande como Azure dados Lake Analytics ou Azure HDInsight com dados Lake arquivo. Também pode criar uma aplicação do .NET para criar uma conta do arquivo de Lake de dados do Azure e efetuar operações, como os dados de carregamento, transferir dados, etc.

- [Introdução ao Azure dados Lake Analytics](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
- [Utilizar o Azure HDInsight com o arquivo de dados de Lake](data-lake-store-hdinsight-hadoop-use-portal.md)
- [Introdução ao Azure dados Lake loja utilizando .NET SDK](data-lake-store-get-started-net-sdk.md)


## <a name="data-lake-store-videos"></a>Vídeos de arquivo de Lake de dados

Se preferir ver vídeos para saber mais, o arquivo de dados de Lake fornece vídeos num intervalo de funcionalidades.

* [Criar uma conta do arquivo de Lake de dados do Azure](https://mix.office.com/watch/1k1cycy4l4gen)
* [Utilize o Explorador de dados para gerir os dados na loja do Azure dados Lake](https://mix.office.com/watch/icletrxrh6pc)
* [Ligar a análise de dados Azure Lake ao arquivo de Lake de dados do Azure](https://mix.office.com/watch/qwji0dc9rx9k)
* [Arquivo do Lake de dados do Access Azure através do Lake a análise de dados](https://mix.office.com/watch/1n0s45up381a8)
* [Ligar o Azure HDInsight ao arquivo de Lake de dados do Azure](https://mix.office.com/watch/l93xri2yhtp2)
* [Arquivo do Lake de dados do Access Azure através do ramo e porco](https://mix.office.com/watch/1n9g5w0fiqv1q)
* [Utilizar DistCp (cópia distribuído Hadoop) para copiar dados de e para a loja do Azure dados Lake](https://mix.office.com/watch/1liuojvdx6sie)
* [Utilizar Apache Sqoop para mover dados entre relacionais origens e arquivo de Lake de dados do Azure](https://mix.office.com/watch/1butcdjxmu114)
* [Orchestration de dados com o Azure dados fábrica para Azure dados Lake arquivo](https://mix.office.com/watch/1oa7le7t2u4ka)
* [Proteger os dados do Azure arquivo de dados Lake](https://mix.office.com/watch/1q2mgzh9nn5lx)



