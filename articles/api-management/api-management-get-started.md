<properties
    pageTitle="Gerir a sua primeira API na gestão de API do Azure | Microsoft Azure"
    description="Saiba como criar APIs, adicionar operações e começar a trabalhar com a gestão de API."
    services="api-management"
    documentationCenter=""
    authors="steved0x"
    manager="erikre"
    editor=""/>

<tags
    ms.service="api-management"
    ms.workload="mobile"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="10/25/2016"
    ms.author="sdanie"/>

# <a name="manage-your-first-api-in-azure-api-management"></a>Gerir a sua primeira API na gestão de API do Azure

## <a name="overview"> </a>Descrição geral

Este guia mostra-lhe como rapidamente começar a utilizar a gestão de API do Azure e efetuar a chamada API primeira.

## <a name="concepts"> </a>o que é a gestão de API do Azure?

Pode utilizar a gestão de API do Azure para tirar qualquer back-end e iniciar um programa de API desenvolvido com base no mesmo.

Cenários comuns incluem:

* **Proteger móvel infraestrutura** pelo access de retenção com as teclas de API, impedir que deve fazer ataques utilizando a limitação ou utilizar as políticas de segurança avançadas como JWT token validação.
* **Ativar ISV ecossistemas de parceiro** ao perguntar se a ativação de parceiro fast através do portal do programador e construir uma fachada API para separar a partir de implementações do internas que não estão faixa para consumo de parceiro.
* **Executar um programa de API interno** através da oferta de uma localização centralizada para a organização para comunicar informações sobre a disponibilidade e as alterações mais recentes para APIs, retenção acesso com base em contas organizacionais, todos os com base num canal seguro entre o gateway API e o back-end.


O sistema é constituído os seguintes componentes:

* O **API gateway** é o ponto final que:
  * Aceita API chamadas e encaminha-los ao seu back-ends.
  * Verifica se API chaves, JWT tokens, certificados e outras credenciais.
  * Impõe quotas de utilização e a taxa de limites.
  * Transforma seu API no momento sem modificações código.
  * Em cache respostas de back-end onde configurar.
  * Registos de chamadas metadados para fins de análise.

* O **portal do publisher** é a interface administrativa onde que configura o seu programa de API. Utilizá-lo para:
    * Definir ou importar API esquema.
    * Pacote de APIs nos produtos.
    * Configure as políticas de como as quotas de ou transformações sobre as APIs.
    * Obter informações a partir de análise.
    * Gerir utilizadores.

* O **portal de programador** serve como a presença na web principal para os programadores, onde pode:
    * Documentação da API de leitura.
    * Experimente uma API através da consola de interativa.
    * Criar uma conta e subscrever para obter API chaves.
    * Análise de acesso os seus próprios utilização.


## <a name="create-service-instance"> </a>Criar uma instância de gestão de API

>[AZURE.NOTE] Para concluir este tutorial, é necessária uma conta Azure. Se não tiver uma conta, pode criar uma conta gratuita apenas de duas minutos. Para obter detalhes, consulte o artigo [Versão de avaliação gratuita do Azure][].

Trabalhar com a gestão de API o primeiro passo é criar uma instância do serviço. Inicie sessão no [Portal clássica do Azure][] e clique em **Novo**, **Serviços de aplicação**, **Gestão de API**, **Criar**.

![Nova instância de gestão de API][api-management-create-instance-menu]

**URL**, especifique um nome de sub-domínio exclusivo para utilizar o URL do serviço.

Selecione a **subscrição** e **região** pretendida para a instância do serviço. Depois de efetuar as suas seleções, clique no botão **seguinte** .

![Novo serviço de gestão de API][api-management-create-instance-step1]

Introduza **Contoso Ltd.** para o **Nome da organização**e introduza o seu endereço de e-mail no campo **Administrador de E-Mail** .

>[AZURE.NOTE] Este endereço de correio eletrónico é utilizado para notificações a partir do sistema de gestão de API. Para mais informações, consulte o artigo [como configurar modelos de correio eletrónico na gestão de API do Azure e notificações][].

![Novo serviço de gestão de API][api-management-create-instance-step2]

Instâncias do serviço de gestão de API estão disponíveis no três camadas: programador, padrão e Premium. Por predefinição, as novas instâncias de serviço de gestão de API são criadas na camada programador. Para selecionar a camada padrão ou Premium, selecione a caixa de verificação **Definições avançadas** e selecione a camada pretendida no ecrã seguinte.

>[AZURE.NOTE] É a camada de Programador de desenvolvimento, testar e programas de API piloto onde elevada disponibilidade não é um problema. Em camadas Standard e Premium, pode dimensionar a contagem de unidade reservadas para lidar com mais tráfego. As camadas padrão e Premium fornecem o serviço de gestão de API com mais de processamento power e o desempenho. Pode concluir este tutorial utilizando qualquer camada. Para mais informações sobre camadas de gestão de API, consulte [Gestão de API preços][].

Clique na caixa de verificação para criar a instância do serviço.

![Novo serviço de gestão de API][api-management-instance-created]

Quando estiver criada a instância do serviço, o próximo passo é criar ou importar uma API.

## <a name="create-api"> </a>Importar uma API

Uma API é composta por um conjunto de operações que pode ser chamado a partir de uma aplicação de cliente. Operações de API são através do proxy para os serviços web existentes.

APIs podem ser criados (e podem ser adicionadas operações) manualmente, ou podem ser importados. Neste tutorial, vamos importar a API para um serviço do exemplo Calculadora web fornecida pela Microsoft e alojado no Azure.

>[AZURE.NOTE] Para obter orientações sobre como criar uma API e operações de adicionar manualmente, consulte o artigo [como criar APIs](api-management-howto-create-apis.md) e [como adicionar operações para uma API](api-management-howto-add-operations.md).

APIs estão configurados a partir do portal do publisher, que pode é acedido através do Portal clássica do Azure. Para aceder ao portal do publisher, clique em **Gerir** no Portal clássica do seu serviço de gestão de API do Azure.

![Portal do Publisher][api-management-management-console]

Para importar a Calculadora API, clique em **APIs** a partir do menu de **Gestão de API** à esquerda e, em seguida, clique em **Importar API**.

![Botão Importar API][api-management-import-api]

Execute os seguintes passos para configurar a Calculadora API:

1. Clique na **Partir de URL**, introduza **http://calcapi.cloudapp.net/calcapi.json** na caixa de texto **especificação URL do documento** e clique no botão de opção **Swagger** .
2. Escreva **calc** na caixa de texto **URL da Web API sufixo** .
3. Clique na caixa **produtos (opcionais)** e selecione **Starter**.
4. Clique em **Guardar** para importar a API.

![Adicionar a nova API][api-management-import-new-api]

>[AZURE.NOTE] **Gestão de API** suporta atualmente versão 1.2 e 2.0 do documento Swagger para importação. Certifique-se de que, apesar de [especificação Swagger 2.0](http://swagger.io/specification) declara que `host`, `basePath`, e `schemes` propriedades são opcionais, documento Swagger 2.0 **devem** conter essas propriedades; caso contrário-não é importado. 

Assim que a API é importada, é apresentada a página de resumo para a API no portal do publisher.

![Resumo API][api-management-imported-api-summary]

A secção API tem vários separadores. No separador **Resumo** apresenta métricas básicas e informações sobre a API. No separador [Definições](api-management-howto-create-apis.md#configure-api-settings) é utilizado para ver e editar a configuração de uma API. O separador de [operações](api-management-howto-add-operations.md) é utilizado para gerir operações a API. No separador **segurança** pode ser utilizado para configurar a autenticação de gateway para o servidor de back-end utilizando a autenticação básica ou [a autenticação de certificados comum](api-management-howto-mutual-certificates.md)e para configurar a [autorização de utilizador utilizando OAuth 2.0](api-management-howto-oauth2.md).  No separador de **problemas** é utilizado para ver os problemas indicados pelos programadores que estão a utilizar o seu APIs. Separador **produtos** é utilizado para configurar os produtos que contêm esta API.

Por predefinição, cada instância de gestão de API vem com dois produtos de exemplo:

-   **Starter**
-   **Ilimitado**

Neste tutorial, básicas API Calculadora foi adicionado o produto Starter quando API foi importada.

Para poder efetuar chamadas para uma API, primeiro tem de subscrever os programadores para um produto que dá-lhes acesso à mesma. Podem subscrever os programadores de produtos no portal do programador ou os administradores podem subscrever os programadores de produtos no portal do publisher. Que é um administrador, desde que criou a instância de gestão de API nos passos anteriores tutorial, para que já subscrito para todos os produtos por predefinição.

## <a name="call-operation"> </a>Uma operação de chamadas a partir do portal de programador

Operações podem ser chamadas diretamente a partir do portal do programador, que fornece uma forma conveniente para ver e testar a operações de uma API. Neste passo tutorial, irá efetuar a chamada operação de **Adicionar dois números inteiros** a API calculadora básica. Clique em **Developer portal para** a partir do menu na parte superior direita da portal do publisher.

![Portal de programador][api-management-developer-portal-menu]

Clique **APIs** a partir do menu superior e, em seguida, clique em **Calculadora básica** para ver as operações disponíveis.

![Portal de programador][api-management-developer-portal-calc-api]

Tenha em atenção as descrições de exemplo e parâmetros que foram importados juntamente com a API e operações, fornecer documentação para os programadores de que irá utilizar esta operação. Estas descrições também podem ser adicionadas quando operações são adicionadas manualmente.

Para ligar a operação de **Adicionar dois números inteiros** , clique em **experimentar**.

![Experimente][api-management-developer-portal-calc-api-console]

Pode introduzir alguns valores para os parâmetros ou mantenha as predefinições e, em seguida, clique em **Enviar**.

![HTTP Get][api-management-invoke-get]

Depois de uma operação é chamada, o portal do programador apresenta o **Estado da resposta**, os **cabeçalhos de resposta**e qualquer tipo de **conteúdo de resposta**.

![Resposta][api-management-invoke-get-response]

## <a name="view-analytics"> </a>Visualizar a análise

Para ver a análise do calculadora básica, mude para o portal do publisher ao selecionar **Gerir** a partir do menu na parte superior direita do developer portal.

![Gerir][api-management-manage-menu]

A vista predefinida para o portal do publisher é de **Dashboard**, que fornece uma descrição geral do seu instância de gestão de API.

![Dashboard][api-management-dashboard]

Paire com o rato sobre o gráfico para **Calculadora básica** ver as métricas específicas para a utilização de API para um determinado período de tempo.

>[AZURE.NOTE] Se não vir nenhuma das linhas no seu gráfico, mude novamente para o portal do programador e fazer algumas chamadas para a API, aguarde alguns minutos e, em seguida, regresse ao dashboard.

Clique em **Ver detalhes** para ver a página de resumo para a API, incluindo uma versão maior as métricas apresentado.

![Analytics][api-management-mouse-over]

![Resumo][api-management-api-summary-metrics]

Para métricas detalhadas e relatórios, clique em **análise** a partir do menu de **Gestão de API** à esquerda.

![Descrição geral][api-management-analytics-overview]

A secção **Analytics** tem os seguintes quatro separadores:

-   **De imediato** fornece métricas de utilização e estado de funcionamento gerais, bem como os programadores superiores, principais produtos, APIs superiores e operações superiores.
-   **A utilização** fornece um olhar aprofundado ao chamadas à API e a largura de banda, incluindo uma representação geográfica.
-   Códigos de estado, realça o **Estado de funcionamento** em cache taxas de sucesso, tempos de resposta e API e tempos de resposta do serviço.
-   **Atividade** fornece relatórios que desagregar a actividade específica pelo programador, produto, API e operação.

## <a name="next-steps"> </a>Passos seguintes

- Obter informações sobre como [proteger o seu API com limites taxa](api-management-howto-product-with-rules.md).

[Versão de avaliação gratuita Azure]: http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=api_management_hero_a

[Create an API Management instance]: #create-service-instance
[Create an API]: #create-api
[Add an operation]: #add-operation
[Add the new API to a product]: #add-api-to-product
[Subscribe to the product that contains the API]: #subscribe
[Call an operation from the Developer Portal]: #call-operation
[View analytics]: #view-analytics
[Next steps]: #next-steps


[How to manage developer accounts in Azure API Management]: api-management-howto-create-or-invite-developers.md
[Configure API settings]: api-management-howto-create-apis.md#configure-api-settings
[Como configurar as notificações e modelos de correio eletrónico na gestão de API do Azure]: api-management-howto-configure-notifications.md
[Responses]: api-management-howto-add-operations.md#responses
[How create and publish a product]: api-management-howto-add-products.md
[Gestão de API preços]: http://azure.microsoft.com/pricing/details/api-management/

[Azure Portal clássico]: https://manage.windowsazure.com/

[api-management-management-console]: ./media/api-management-get-started/api-management-management-console.png
[api-management-create-instance-menu]: ./media/api-management-get-started/api-management-create-instance-menu.png
[api-management-create-instance-step1]: ./media/api-management-get-started/api-management-create-instance-step1.png
[api-management-create-instance-step2]: ./media/api-management-get-started/api-management-create-instance-step2.png
[api-management-instance-created]: ./media/api-management-get-started/api-management-instance-created.png
[api-management-import-api]: ./media/api-management-get-started/api-management-import-api.png
[api-management-import-new-api]: ./media/api-management-get-started/api-management-import-new-api.png
[api-management-imported-api-summary]: ./media/api-management-get-started/api-management-imported-api-summary.png
[api-management-calc-operations]: ./media/api-management-get-started/api-management-calc-operations.png
[api-management-list-products]: ./media/api-management-get-started/api-management-list-products.png
[api-management-add-api-to-product]: ./media/api-management-get-started/api-management-add-api-to-product.png
[api-management-add-myechoapi-to-product]: ./media/api-management-get-started/api-management-add-myechoapi-to-product.png
[api-management-api-added-to-product]: ./media/api-management-get-started/api-management-api-added-to-product.png
[api-management-developers]: ./media/api-management-get-started/api-management-developers.png
[api-management-add-subscription]: ./media/api-management-get-started/api-management-add-subscription.png
[api-management-add-subscription-window]: ./media/api-management-get-started/api-management-add-subscription-window.png
[api-management-subscription-added]: ./media/api-management-get-started/api-management-subscription-added.png
[api-management-developer-portal-menu]: ./media/api-management-get-started/api-management-developer-portal-menu.png
[api-management-developer-portal-calc-api]: ./media/api-management-get-started/api-management-developer-portal-calc-api.png
[api-management-developer-portal-calc-api-console]: ./media/api-management-get-started/api-management-developer-portal-calc-api-console.png
[api-management-invoke-get]: ./media/api-management-get-started/api-management-invoke-get.png
[api-management-invoke-get-response]: ./media/api-management-get-started/api-management-invoke-get-response.png
[api-management-manage-menu]: ./media/api-management-get-started/api-management-manage-menu.png
[api-management-dashboard]: ./media/api-management-get-started/api-management-dashboard.png

[api-management-add-response]: ./media/api-management-get-started/api-management-add-response.png
[api-management-add-response-window]: ./media/api-management-get-started/api-management-add-response-window.png
[api-management-developer-key]: ./media/api-management-get-started/api-management-developer-key.png
[api-management-mouse-over]: ./media/api-management-get-started/api-management-mouse-over.png
[api-management-api-summary-metrics]: ./media/api-management-get-started/api-management-api-summary-metrics.png
[api-management-analytics-overview]: ./media/api-management-get-started/api-management-analytics-overview.png
[api-management-analytics-usage]: ./media/api-management-get-started/api-management-analytics-usage.png
[api-management-]: ./media/api-management-get-started/api-management-.png
[api-management-]: ./media/api-management-get-started/api-management-.png
