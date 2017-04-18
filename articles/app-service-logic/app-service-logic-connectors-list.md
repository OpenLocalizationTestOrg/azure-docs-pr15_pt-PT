<properties
    pageTitle="Lista de conectores e API Apps disponíveis | Aplicação de serviço do Microsoft Azure"
    description="Ler sobre o conectores e Apps API no Azure de aplicação de serviço"
    services="logic-apps"
    documentationCenter=""
    authors="MandiOhlinger"
    manager="erikre"
    editor="cgronlun"/>

<tags
    ms.service="logic-apps"
    ms.workload="integration"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="09/01/2016"
    ms.author="mandia"/>


# <a name="list-of-connectors-and-api-apps-to-use-in-your-logic-apps"></a>Lista de conectores e API Apps para utilizar nas suas aplicações de lógica
>[AZURE.NOTE] Esta versão do artigo aplica-se a versão do esquema de 2014-12-01-pré-visualização de aplicações de lógica. Para a versão de lógica aplicações das versões geral disponibilidade (DG), consulte o artigo [Nova lista de conexões](../connectors/apis-list.md).

Saiba mais sobre todas as conexões disponíveis e Apps API criado pela Microsoft para utilizar as aplicações do lógica.

Para preços informações e uma lista do que está incluído no cada camada de serviço, consulte o artigo [Preços de serviço de aplicação do Azure](https://azure.microsoft.com/pricing/details/app-service/).

> [AZURE.NOTE] Para começar a lógica Apps antes de inscrever-se para uma conta do Azure, aceda à [Aplicação de lógica tente](https://tryappservice.azure.com/?appservice=logic). Imediatamente pode criar uma aplicação de lógica starter curto na aplicação de serviço. Sem cartões de crédito necessários; sem compromissos.

## <a name="core-connectors"></a>Conectores de Core
A tabela seguinte lista de todas as aplicações de API criado pela Microsoft, que estão disponíveis como Core conectores e conectores disponíveis:

Nome | Descrição
--- | ---
[Bing Translator](https://azure.microsoft.com/marketplace/partners/bing/microsofttranslator/) | Utilize o Bing para traduzir texto para outro idioma.
[HTTP](app-service-logic-connector-http.md) | A escuta HTTP abre-se um ponto final que age como um servidor HTTP e recebe pedidos recebidos de HTTP ou HTTPS. A ação de HTTP não necessita de uma aplicação de API e é suportada vierem lógica de aplicações.
[Margem](app-service-logic-connector-slack.md) | Ligar a margem e publica mensagens nas folga canais.


## <a name="enterprise-integration-connectors"></a>Conectores de integração de empresa
A tabela seguinte lista todos os disponíveis conectores e Apps API criado pela Microsoft disponível como conectores de integração de empresa:

Nome  | Descrição
------------- | -------------
[Regras de BizTalk](app-service-logic-use-biztalk-rules.md) | Utilize regras de BizTalk para definir e controlar a lógica empresarial dentro de uma organização. Políticas de negócio podem ser atualizadas sem recompilar ou sem Reimplementar as aplicações associadas.
[BizTalk XPath Extractor](app-service-logic-xpath-extract.md) | Procura e extrai dados a partir do conteúdo XML com base num XPath que escolher.
[Conector DB2](app-service-logic-connector-db2.md) | Liga-se para uma IBM DB2 da base de dados no local e numa máquina virtual Azure execução de um sistema operativo Windows. Pode mapear operações Web API e OData API para comandos Informix Structured Query Language. <br/><br/>Sem accionadores. Acções incluem a seleccionar tabela, inserir, atualizar, eliminar e instrução personalizada<br/><br/>Este conector também inclui o cliente Microsoft para DRDA ligar a um servidor de Informix através de uma rede TCP/IP.
[Ficheiro](app-service-logic-connector-file.md) | Utilizar este conector, pode ligar para o sistema de ficheiros no local ou na rede e tarefas de ficheiro diferente completa, incluindo a carregar, eliminar, ficheiros de listagem e mais.
[Informix](app-service-logic-connector-informix.md) | Liga-se para uma base de dados do IBM Informix, no local e numa máquina virtual Azure com um sistema operativo do Windows. Pode mapear operações Web API e OData API para comandos Informix Structured Query Language.<br/><br/>Sem accionadores. Acções incluem tabela selecione, inserir, atualizar, eliminar e instrução personalizada.<br/><br/>Ao utilizar no local, VPN ou Azure ExpressRoute pode ser utilizado. Este conector também inclui um cliente da Microsoft para DRDA ligar a um servidor de Informix através de uma rede TCP/IP.
[Microsoft SQL Server](app-service-logic-connector-sql.md) | Liga-se para o local do SQL Server ou uma base de dados do Azure SQL. Pode criar, atualizar, obtenha e eliminar entradas numa tabela de base de dados SQL.
MQ | Liga-se ao IBM WebSphere MQ Server versão 8, no local e numa máquina virtual Azure com um sistema operativo do Windows. Ao utilizar no local, VPN ou Azure ExpressRoute pode ser utilizado. A conexão também inclui o cliente da Microsoft para MQ.<br/><br/>Sem accionadores. Não ações.<br/><br/>**Nota** Atualmente não podem ser utilizados com aplicações de lógica.

## <a name="connectors-as-triggers"></a>Conectores como accionadores
Conectores várias fornecem accionadores para aplicações de lógica. Estes accionadores são dos dois tipos:

1. Inquérito accionadores: Estes accionadores consultam o seu serviço de uma determinada frequência para verificar a existência de novos dados. Quando novos dados estiverem disponíveis, uma nova instância da sua aplicação de lógica é executado com os dados como entrada. Para impedir que os mesmos dados consumida várias vezes, o accionador poderá de limpeza dados que tenham sido ler e transmitidos para a aplicação de lógica. Exemplos de como conectores são de ficheiros, SQL e armazenamento do Windows Azure.
2. Push accionadores: Estes accionadores ouvir para dados sobre um ponto final ou para um evento ocorrer. Em seguida, acionar uma nova instância de uma aplicação de lógica. Exemplos de como conectores são escuta HTTP e Twitter.

## <a name="connectors-as-actions"></a>Conectores como acções
Conectores podem também ser utilizados como acções dentro da sua aplicação de lógica. Ações são úteis para procurar dados da aplicação de lógica pode então utilizado na execução. Por exemplo, poderá ter procurar dados a partir de uma base de dados do SQL para obter informações adicionais acerca de um cliente quando uma ordem de processamento. Em alternativa, poderá ter de escrever, atualizar ou eliminar dados de um destino. Pode fazê-lo com as acções fornecidas pelos conectores. Ações mapeiam para operações nas aplicações de API (conforme definido pelo respetivo metadados Swagger).

## <a name="create-your-own-connectors-and-api-apps"></a>Criar o seu próprio conexões e API Apps
[Referência da aplicações API e conectores](http://aka.ms/appservicesconnectorreference)  
[Azure Accionadores da aplicação API do serviço de aplicação](../app-service-api/app-service-api-dotnet-triggers.md)  
[Referência de aplicação de lógica](https://msdn.microsoft.com/library/azure/dn948510.aspx)

## <a name="more-on-connectors-and-api-apps"></a>Obter mais informações sobre aplicações API e conectores
[O que são conexões e BizTalk API aplicações](app-service-logic-what-are-biztalk-api-apps.md)  
[Utilizar o Gestor de ligação híbrido no Azure de aplicação de serviço](app-service-logic-hybrid-connection-manager.md)  
[Gerir e monitorizar o seu incorporada aplicações API e conectores](app-service-logic-monitor-your-connectors.md)
