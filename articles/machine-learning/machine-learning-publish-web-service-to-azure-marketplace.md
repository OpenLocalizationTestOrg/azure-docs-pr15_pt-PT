<properties 
    pageTitle="Publicar a aprendizagem máquina serviço ao Azure Marketplace web | Microsoft Azure" 
    description="Como publicar o seu serviço de Web de aprendizagem do Azure máquina ao Azure Marketplace" 
    services="machine-learning" 
    documentationCenter="" 
    authors="BharathS" 
    manager="jhubbard" 
    editor="cgronlun"/>

<tags 
    ms.service="machine-learning" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/08/2016" 
    ms.author="bharaths"/>

# <a name="publish-azure-machine-learning-web-service-to-the-azure-marketplace"></a>Publicar o serviço de Web de aprendizagem máquina Azure no Azure Marketplace 

Azure Marketplace fornece a capacidade de gratuito serviços para consumo por clientes externos ou publicar serviços web de aprendizagem do Azure máquina como paga. Este artigo fornece uma descrição geral do processo com ligações para as diretrizes para começar a utilizar. Ao utilizar este processo, pode tornar os serviços web disponível para outros programadores consumir nas suas aplicações.


[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## <a name="overview-of-the-publishing-process"></a>Descrição geral do processo de publicação 

Seguem-se os passos para publicar um serviço web de aprendizagem do Azure máquina Azure Marketplace:

1. Criar e publicar um serviço de formação de máquina pedido-resposta (RRS)
2. Implementar o serviço de produção e obter as informações de ponto final de chave de API e OData.
3. Utilizar o URL do serviço web publicada para publicar o [Azure Marketplace (Data Market)](https://publish.windowsazure.com/workspace/) 
4. Depois de submetido, a sua oferta for revista e precisa de ser aprovadas antes dos seus clientes pode começar a compra-lo. O processo de publicação pode demorar alguns dias de empresas. 

## <a name="walk-through"></a>Percorrer
###<a name="step-1-create-and-publish-a-machine-learning-request-response-service-rrs"></a>Passo 1: Criar e publicar um serviço de formação de máquina pedido-resposta (RRS)###
 Caso ainda não o tenha este já, consulte veja este [ajudasse](machine-learning-walkthrough-5-publish-web-service.md).

###<a name="step-2-deploy-the-service-to-production-and-obtain-the-api-key-and-odata-endpoint-information"></a>Passo 2: Implementar o serviço de produção e obter a chave de API e informações de ponto final de OData###
1. A partir do [Portal clássica Azure](http://manage.windowsazure.com), selecione a opção de **Aprendizagem automática** a partir da barra de navegação do lado esquerdo e, selecione a área de trabalho. 

2. Clique no separador **Serviços WEB** e, selecione o serviço web que pretende publicar no mercado.

    ![Azure Marketplace][workspace]

3. Selecione o ponto final que pretende ter marketplace consumir. Se não tiver criado qualquer os pontos finais adicionais, pode selecionar o ponto final **predefinido** .

4. Depois de ter clicado no ponto final, vai conseguir ver a **Chave de API**. Terá este bloco de informações posterior no passo 3, por isso, fazer uma cópia do mesmo.

    ![Azure Marketplace][apikey]

5. Clique no método **Pedido/resposta** , neste momento, que não suportamos publicação lote execução serviços mercado. Que será direcionado para a página de ajuda API para o método de pedido/resposta.

6. Copie o **Endereço de ponto final de OData**, terá destas informações mais tarde no passo 3.

    ![Azure Marketplace][odata]




Implemente o serviço de produção.



###<a name="step-3-use-the-url-of-the-published-web-service-to-publish-to-azure-marketplace-datamarket"></a>Passo 3: Utilizar o URL do serviço web publicada para publicar o Azure Marketplace (DataMarket)###

1.  Navegue até ao [Azure Marketplace (Data Market)](http://datamarket.azure.com/home) 
2.  Clique na ligação **Publicar** na parte superior da página. Esta será direcionado para o [Portal de publicação do Microsoft Azure](https://publish.windowsazure.com)
3.  Clique na secção **fabricantes** para registar como um fabricante.
4.  Ao criar uma nova oferta, selecione os **Serviços de dados**, em seguida, clique em **criar um novo serviço de dados**. 
 
    ![Azure Marketplace][image1]

    <br />


5.  Em **planos** fornecem informações sobre a sua oferta, incluindo um plano de preços. Decida se irá oferecer um serviço gratuito ou pago. Para obter pagas, fornece informações de pagamento, como as informações de identificação bancária e impostos.

6.  Em **Marketing** fornecem informações sobre a sua oferta, tal como o título e descrição para a sua oferta.

7.  Em **preços** pode configurar o preço para os planos para países/regiões específicos, ou deixar que o sistema "autoprice" a sua oferta.

8. No separador do **Serviço de dados** , clique em **Serviço Web** como a **Origem de dados**.

    ![Azure Marketplace][image2]

9.  Obtenha o URL e API chave do serviço web do Portal clássica do Azure, tal como é explicado no passo 2 acima.

10. Na caixa de diálogo Configurar serviço de dados Marketplace, cole o endereço de ponto final de OData a caixa de texto do **URL do serviço** .

11. Para **autenticação**, selecione **cabeçalho** como o **Esquema de autenticação**.

    - Introduza "Autorização" para o **nome de cabeçalho**.
    - Para o **Valor de cabeçalho**, introduza "Portadores" (sem as aspas), clique na barra de **espaço** e, em seguida, cole a chave de API.
    - Selecione a caixa de verificação **este serviço está OData** .
    - Clique em **Testar ligação** para testar a ligação.

12. Em **categorias**, certifique-se de **Que aprendizagem automática** está selecionada.

13. Quando terminar introduzir todos os metadados sobre a sua oferta, clique em **Publicar**e, em seguida, **notificações Push para transição**. Neste momento, será notificado de que precisa de corrigir problemas restantes.

14. Depois de ter assegurado conclusão de todos os problemas pendentes, clique em **Pedir aprovação para transmitir para produção**. O processo de publicação pode demorar alguns dias de empresas. 


[image1]:./media/machine-learning-publish-web-service-to-azure-marketplace/image1.png
[image2]:./media/machine-learning-publish-web-service-to-azure-marketplace/image2.png
[workspace]:./media/machine-learning-publish-web-service-to-azure-marketplace/selectworkspace.png
[apikey]:./media/machine-learning-publish-web-service-to-azure-marketplace/apikey.png
[odata]:./media/machine-learning-publish-web-service-to-azure-marketplace/odata.png
 
