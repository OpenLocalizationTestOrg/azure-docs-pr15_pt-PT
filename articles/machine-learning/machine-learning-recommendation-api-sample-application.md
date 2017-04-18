<properties 
    pageTitle="Operações comuns na máquina aprendizagem recomendações API | Microsoft Azure" 
    description="Aplicação de exemplo do Azure ML recomendação" 
    services="machine-learning" 
    documentationCenter="" 
    authors="LuisCabrer" 
    manager="jhubbard" 
    editor="cgronlun"/>

<tags 
    ms.service="machine-learning" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/08/2016" 
    ms.author="luisca"/> 


# <a name="recommendations-api-sample-application-walkthrough"></a>Recomendações API exemplo aplicação instruções passo a passo

>[AZURE.NOTE]Deverá começar a utilizar o serviço de disfunção API recomendações em vez de nesta versão. O serviço de disfunção recomendações irá substituição deste serviço e todas as novas funcionalidades, serão desenvolvidas aí. Tem novas funcionalidades como lotes suporte, uma melhor API Explorer, uma experiência de início de sessão no/faturação superfície, mais consistente API mais limpeza, etc.
> Saiba mais sobre como [Migrar para o novo serviço disfunção](http://aka.ms/recomigrate)

##<a name="purpose"></a>Objetivo

Este documento mostra a utilização de API do Azure máquina aprendizagem recomendações através de uma [aplicação de exemplo](https://code.msdn.microsoft.com/Recommendations-144df403).

Esta aplicação não se destina a incluir a funcionalidade completa, nem, é utilizado o APIs do. Demonstra a algumas operações comuns para desempenhar quando pretende reproduzir com o serviço de recomendação máquina aprendizagem pela primeira vez. 

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

##<a name="introduction-to-machine-learning-recommendation-service"></a>Introdução ao serviço de recomendação máquina aprendizagem

Recomendações através do serviço de recomendação máquina aprendizagem estão activadas quando constrói um modelo de recomendação com base nos seguintes dados:

* Um repositório de itens que pretende recomendar, também conhecido como um catálogo
* Dados que representa a utilização de itens por utilizador ou na sessão (Isto pode ser adquirido ao longo do tempo através de aquisição de dados, não como parte da aplicação exemplo)

Depois de um modelo de recomendação é criado, pode utilizá-la para prever itens que um utilizador poderá estar interessado, de acordo com um conjunto de itens (ou um único item) o utilizador seleciona.

Para ativar o cenário anterior, efetue o seguinte procedimento no serviço de recomendação de aprendizagem automática:

* Criar um modelo: Este é um contentor lógico que contém os dados (catálogo e a utilização) e os modelos de previsão. Cada contentor de modelo é identificada através de um ID exclusivo, que é atribuído quando é criado. Este ID chama-se o ID de modelo e são utilizada pela maior parte das APIs. 
* Carregar para o catálogo: quando é criado um contentor de modelo, pode associar-lhe um catálogo.

**Nota**: criar um modelo e carregar a um catálogo normalmente são efetuadas uma vez para o ciclo de vida do modelo.

* Carregar a utilização: esta opção adiciona dados de utilização para o contentor de modelo.
* Construa um modelo de recomendação: depois de ter dados suficientes, pode criar o modelo de recomendação. Esta operação utiliza dos algoritmos máquina formação superiores para criar um modelo de recomendação. Cada compilação está associada a um ID exclusivo. Tem de manter o registo deste ID porque é necessário para a funcionalidade da algumas APIs.
* Monitorizar o processo de criação: uma compilação de modelo de recomendação é uma operação assíncrona e pode demorar a partir de vários minutos a várias horas, consoante a quantidade de dados (catálogo e a utilização) e os parâmetros de compilação. Por isso, é necessário monitorizar a compilação. É criado um modelo de recomendação apenas se a sua compilação associada é concluída com êxito.
* (Opcional) Selecione uma compilação de modelo de recomendação ativos: este passo só é necessário se tiver mais do que uma recomendação modelo compilado no seu contentor de modelo. Qualquer pedido para obter recomendações sem que indica o modelo de recomendação ativo é redirecionado automaticamente pelo sistema para a compilação ativo predefinido. 

**Nota**: um modelo de recomendação ativo é produção pronta e baseia-se para a carga de trabalho de produção. Isto é diferente de um modelo de recomendação não ativo, permanece num ambiente de teste gosto (por vezes denominada transição).

* Obter recomendações: depois de ter um modelo de recomendação, pode acionar as recomendações para um único item ou uma lista de itens que selecionar. 

Normalmente, vai invocar recomendação obter para um determinado período de tempo. Durante esse período de tempo, pode redireccionar dados de utilização para o sistema de recomendação formação de máquina, que adiciona estes dados para o contentor de modelo especificado. Quando tiver suficiente dados de utilização, pode criar um novo modelo de recomendação incorpora dados de utilização adicionais. 

##<a name="prerequisites"></a>Pré-requisitos

* Visual Studio 2013
* Acesso à Internet 
* Subscrição de recomendações, de API (https://datamarket.azure.com/dataset/amla/recommendations).

##<a name="azure-machine-learning-sample-app-solution"></a>Solução de aplicação de exemplo do Azure máquina aprendizagem

Esta solução contém o código fonte, utilização de exemplo, ficheiro de catálogo e diretivas para transferir os pacotes que são necessários para compilação.

##<a name="the-apis-used"></a>APIs utilizadas

A aplicação utiliza a funcionalidade de recomendação de aprendizagem máquina através de um subconjunto das APIs disponíveis. As seguintes APIs são demonstradas na aplicação:

* Criar modelo: criar um contentor lógico para os modelos de dados e recomendação colocar em espera. Um modelo está identificado por um nome e não é possível criar mais de um modelo com o mesmo nome.
* Carregue o ficheiro de catálogo: utilizar para carregar os dados do catálogo.
* Carregue o ficheiro de utilização: utilizar para carregar os dados de utilização.
* Acionar compilação: utilizar para criar um modelo de recomendação.
* Monitorizar a execução de compilação: utilizar para monitorizar o estado de uma compilação de modelo de recomendação.
* Escolha um modelo incorporado para Recomendação: Utilize para indicar que modelo recomendação utilizar por predefinição, para um determinado contentor de modelo. Este passo é necessário apenas se tiver mais do que um modelo de recomendação e pretende ativar uma compilação não ativo que o modelo de recomendação ativo.
* Obter Recomendação: utilizar para obter itens recomendadas, de acordo com um único item determinado ou um conjunto de itens. 

Para obter uma descrição completa das APIs, consulte a documentação do Microsoft Azure Marketplace. 

**Nota**: um modelo pode ter vários compilações ao longo do tempo (não em simultâneo). Cada compilação é criada com o mesmo ou um catálogo atualizado e dados de utilização adicionais.

## <a name="common-pitfalls"></a>Falhas comuns

* Tem de fornecer o seu nome de utilizador e a sua chave da conta principal do Microsoft Azure Marketplace para executar a aplicação de exemplo.
* Executar a aplicação de exemplo consecutivamente irá falhar. O fluxo de aplicação inclui criar, carregar, construir o monitor e obter recomendações a partir de um modelo predefinido; Por conseguinte, irá falhar em execução consecutiva se não mudar o nome do modelo entre exe.
* Recomendações poderão devolver sem dados. A aplicação de exemplo utiliza um ficheiro de catálogo e a utilização muito pequeno. Por conseguinte, alguns itens do catálogo terá não existem itens recomendadas.

## <a name="disclaimer"></a>Exclusão de responsabilidade
A aplicação de exemplo não se destina a ser executado num ambiente de produção. Os dados fornecidos no catálogo de forem muito pequenos, e não fornecerá um modelo de recomendação significativo. Os dados são fornecidos como uma demonstração sobre. 
 
