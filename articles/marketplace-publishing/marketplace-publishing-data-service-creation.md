<properties
   pageTitle="Guia para a criação de um serviço de dados do Marketplace | Microsoft Azure"
   description="Obter instruções detalhadas sobre como criar, certificar e implementar um serviço de dados para comprar no Azure Marketplace."
   services="marketplace-publishing"
   documentationCenter=""
   authors="HannibalSII"
   manager="hascipio"
   editor=""/>

   <tags
      ms.service="marketplace"
      ms.devlang="na"
      ms.topic="article"
      ms.tgt_pltfrm="na"
      ms.workload="na"
      ms.date="08/26/2016"
      ms.author="hascipio; avikova" />

# <a name="data-service-publishing-guide-for-the-azure-marketplace"></a>Serviço de dados de publicação Guia do Azure Marketplace

>[AZURE.IMPORTANT] **Neste momento podemos já não são ativação qualquer novos fabricantes de serviço de dados. Novo dataservices não irá obter aprovado para obter uma lista.** Se tiver uma aplicação de empresas SaaS que gostaria de publicar no AppSource pode encontrar mais informações [aqui](https://appsource.microsoft.com/partners). Se tiver um aplicações IaaS ou serviço para programadores que gostaria de publicar no Azure Marketplace pode encontrar mais informações [aqui](https://azure.microsoft.com/marketplace/programs/certified/).

Depois de concluir o passo 1, [criação de conta e registo](marketplace-publishing-accounts-creation-registration.md), podemos orientado lhe pelo [Geral não técnica](marketplace-publishing-pre-requisites.md) e [Requisitos técnicos](marketplace-publishing-data-service-creation-prerequisites.md) , de uma oferta de serviço de dados no Azure Marketplace. Agora iremos irá guiá-lo os passos para criar uma oferta de serviço de dados no [Portal de publicação] [ link-pubportal] do Azure Marketplace.

## <a name="1---login-to-the-publishing-portal"></a>1. início de sessão de publicação Portal.

Aceda a [https://publish.windowsazure.com](https://publish.windowsazure.com. )

**Para o primeiro de início de sessão para o Portal de publicação, utilize a mesma conta com o qual foi registado vendedor perfil da sua empresa no Centro de programadores.**  (Mais tarde pode adicionar qualquer empregado da sua empresa como um administrador de co no Portal de publicação.)

Se este é o primeiro início de sessão para o portal de publicação, clique no mosaico **Publicar serviços de dados** .

## <a name="2---choose-data-services-in-the-navigation-menu-on-the-left-side"></a>2. Escolha os **Serviços de dados** no menu de navegação no lado esquerdo.

  ![desenho](media/marketplace-publishing-data-service-creation/pubportal-main-nav.png)

## <a name="3---create-a-new-data-service"></a>3. Crie um novo serviço de dados

Preencha o título para sua nova oferta de serviço de dados e clique no "+" à direita.

  ![desenho](media/marketplace-publishing-data-service-creation/step-3.png)

## <a name="4---review-the-sub-menu-under-the-newly-created-data-service-in-the-navigation-menu"></a>4. rever submenu no serviço de dados recentemente criado no menu de navegação.

Clique no separador **instruções passo a passo** e rever todos os passos necessários para publicar corretamente o serviço de dados no Azure Marketplace.

> [AZURE.TIP] Sempre pode clicar em hiperligações na página "Guiadas" ou utilizar os separadores no submenu a oferta de serviço de dados no lado esquerdo.

## <a name="5---create-a-new-plan"></a>5. Crie um novo plano.

### <a name="offers-plans-transactions"></a>Oferece, planos, as transações.

Cada oferta pode ter vários planos, mas tem de ter, pelo menos, um (1) plano. Quando os utilizadores finais subscrever a sua oferta eles subscrever para um plano da oferta. Cada um dos planos define como os utilizadores finais poderão utilizar o seu serviço.

Azure Marketplace atualmente, mensal subscrição da transação com base no modelo apenas para serviços de dados, ou seja, os utilizadores finais irão pagar taxa mensal de acordo com o preço do plano específico que subscrever e poderão consumir cada número do mês da transação definida pelo plano.

Cada transação geralmente definida como número de registos que irá devolver o seu serviço de dados com a consulta enviada para o serviço. A predefinição é 100. Número de transações devolvido para cada consulta será número de registos divididos por 100 e arredondado por excesso para o número inteiro mais próximo.

Cabe Azure Marketplace Service camada para monitorizar (medidor) número de transações média consumida por cada consulta.

> [AZURE.IMPORTANT] Os utilizadores finais das atingiu o limite da transação durante o mês vai ser bloqueada continuar a utilizar o serviço até ao fim do respetivo ciclo de subscrição mensal.

> O plano ou um dos planos pode (mas não tem) incluir número ilimitado de transações.

### <a name="create-a-plan"></a>Crie um plano.
1. Clique em **"+"** junto a "Adicionar um novo plano".

2. Selecione uma das opções: a utilização de **ilimitado** ou **limitado** para este plano.  Se limitado, em seguida, fornecer o número da transação o plano permitirá consumir num mês.

    ![desenho](media/marketplace-publishing-data-service-creation/step-5.1.png)  

    Portal de publicação também irá sugerir "Plano identificador", o que será utilizado para comunicar com os utilizadores finais o nome do plano na IU e também utilizado pelo serviço mercado local para identificar o plano. Pode alterar o "identificador planear" se pretender.

    > [AZURE.NOTE] "Identificador planear" tem de ser exclusivo no âmbito de cada oferta. Como muitos outros identificadores utilizada no identificador de publicação Portal planear será bloqueada após a primeira publicação produção e não conseguir alterar este identificador.

3. Clique para aceitar a sua escolha.

4. Em seguida, será pedido algumas questões adicionais relativas ao seu plano recentemente criado.

    ![desenho](media/marketplace-publishing-data-service-creation/step-5.2.png)


|Pergunta|Significância mais próximo|
|----|----|
|**Este plano é todo o mundo gratuito e está disponível?**|Pode criar um plano de completamente livre-de-gratuitas. Se é o plano apenas para esta oferta – significa que esteja a publicar "Oferecer livre" no mercado. Se é apenas para uma (das poucas) plano, o it fornece-lhe uma opção para oferecer utilizadores finais para saber mais sobre o serviço com um número relativamente pequeno de transações por mês.  Se a resposta "Sim", em seguida, sem questões serão pedidos.|

> [AZURE.NOTE] Os utilizadores finais sempre pode atualizar para os planos pagos.

|Pergunta|Significância mais próximo|
|----|----|
|**É a versão de avaliação gratuita disponível?**|Pode escolher entre "Sem versão de avaliação" sequer ou dar uma opção para utilizar o seu plano para "Um mês". Fabricantes de como utilizar esta opção para fornecer aos utilizadores finais a possibilidade de compreender as vantagens da oferta gratuitamente para um mês.|

> [AZURE.IMPORTANT] Os utilizadores finais apenas poderá comprar uma avaliação gratuita se definiram instrument de pagamento, por exemplo, cartão de crédito, acordo empresarial.

> Depois de um mês da versão de avaliação gratuita, Azure Marketplace iniciará clientes a carregar o preço à data da subscrição, a menos que o cliente iniciou o cancelamento da subscrição. Serão fornecidos especiais de notificação para os utilizadores finais.

|Pergunta|Significância mais próximo|
|----|----|
|**Este plano requer um código de promoção para comprar?**| Os fabricantes têm uma opção para limitar o acesso aos seus planos do serviço, fornecendo um código especial denominado "A Promocode" a clientes específicos. Apenas os utilizadores finais que terão este Promocode poderão subscrever o plano. Se optar por "Não", em seguida, concorda com que todas as pessoas a partir de região onde a oferta está disponível (consulte o artigo do [Guia de conteúdo Marketing Marketplace](marketplace-publishing-push-to-staging.md) para obter mais detalhes) conseguirão subscrever este plano. Serão pedidas sem mais perguntas.|
|**Também ocultar a este plano a partir de qualquer pessoa que não tem um código de promoção válida?**|Se a resposta à pergunta anterior "Sim" for o Publisher tem uma opção para remover completamente este plano não apareça na IU de mercado. Significa, clientes não verá este plano na página de detalhes da proposta. Os utilizadores finais das irão receber um promocode para comprar, poderão subscrever ao mesmo utilizando esta promocode.|

## <a name="6---create-your-marketplace-marketing-content"></a>6. Crie o seu Marketplace conteúdos de marketing
Para saber como fornecer a informação necessária nos separadores de **Marketing, preços, suporte e categorias** fórum visita [Marketplace Marketing manual de conteúdo](marketplace-publishing-push-to-staging.md) que é comuns a todos os artefactos publicado no Azure Marketplace.  

## <a name="7---connect-your-offer-to-your-service-sql-azure-based-or-web-service-based"></a>7. ligar a sua oferta ao seu serviço (SQL Azure com base ou serviço Web com base).

Clique no sub-menu de **Serviços de dados** .

Na metade parte superior da página ser-lhe-á pedido para fornecer a oferta **espaço de nomes**.  

  ![desenho](media/marketplace-publishing-data-service-creation/step-7.png)

A abaixo pergunta definirá como o Publisher vai exposição criada recentemente oferta ao Azure Marketplace. (Para obter mais detalhes, consulte o [Guia de pré-requisito técnico dados dos serviços](marketplace-publishing-data-service-creation-prerequisites.md)).

  ![desenho](media/marketplace-publishing-data-service-creation/step-7.2.png)

**O serviço de base de dados de publicação**

Clique em **base de dados**. A página seguinte será apresentada:

  ![desenho](media/marketplace-publishing-data-service-creation/step-7.3.png)

Para criar um mapeamento de CSDL para o conjunto de dados com base na base de dados SQL Azure:

  ![desenho](media/marketplace-publishing-data-service-creation/step-7.4.png)

E, em seguida, para cada tabela

  ![desenho](media/marketplace-publishing-data-service-creation/step-7.5.png)

  ![desenho](media/marketplace-publishing-data-service-creation/step-7.6.png)

Se o serviço Web

  ![desenho](media/marketplace-publishing-data-service-creation/step-7.7.png)

> [AZURE.IMPORTANT] Leia o [mapeamento de um serviço web existente a OData através de CSDL](marketplace-publishing-data-service-creation-odata-mapping.md) para obter instruções detalhadas e exemplos para a criação de um serviço Web de CSDL.

## <a name="next-steps"></a>Próximos passos
Agora que criou a sua oferta de serviço de dados, certifique-se de que conclua as instruções no [Guia de conteúdo Marketing Marketplace](marketplace-publishing-push-to-staging.md) antes de avançar para [testar o seu serviço de dados no teste](marketplace-publishing-data-service-test-in-staging.md).

## <a name="see-also"></a>Consulte também
- [Introdução: Como publicar uma oferta ao Azure Marketplace](marketplace-publishing-getting-started.md)
- Se estiver interessado em Noções sobre o processo de mapeamento de OData e a finalidade geral, leia este artigo [Mapeamento de OData do serviço de dados](marketplace-publishing-data-service-creation-odata-mapping.md) para rever as definições, estruturas e instruções.
- Se estiver interessado em aprendizagem e compreender os nós específicos e respectivos parâmetros, leia este artigo [OData mapeamento nós do serviço de dados](marketplace-publishing-data-service-creation-odata-mapping-nodes.md) para definições e explicações, exemplos e utilizar contexto de maiúsculas/minúsculas.
- Se estiver interessado em exemplos de revisão, leia este artigo [OData mapeamento de exemplos de serviço de dados](marketplace-publishing-data-service-creation-odata-mapping-examples.md) para ver o código de exemplo e compreender sintaxe do código e contexto.


[link-pubportal]:https://publish.windowsazure.com
