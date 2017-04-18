<properties
   pageTitle="Guia para a criação de um modelo de solução do Marketplace | Microsoft Azure"
   description="Obter instruções detalhadas sobre como criar, certificar e implementar um modelo de solução de imagem Multi VM para comprar no Azure Marketplace."
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
      ms.date="07/27/2016"
      ms.author="hascipio; v-divte" />

# <a name="guide-to-create-a-solution-template-for-azure-marketplace"></a>Guia para criar um modelo de solução para Azure Marketplace
Depois de concluir o passo 1, [criação de conta e registo][link-acct-creation], recomendamos instruções sobre a criação de um modelo de solução compatível com o Azure na [Pré-requisitos técnicos para criar um modelo de solução](marketplace-publishing-solution-template-creation-prerequisites.md). Agora iremos irá guiá-lo os passos para criar um modelo de solução para vários VMs no [Portal de publicação] [ link-pubportal] do Azure Marketplace.

## <a name="create-your-solution-template-offer-in-the-publishing-portal"></a>Criar a sua oferta de modelo de solução no Portal de publicação
Aceda a [https://publish.windowsazure.com](http://publish.windowsazure.com). Ao iniciar sessão pela primeira vez no [Portal de publicação](https://publish.windowsazure.com/), utilize a mesma conta com o qual o perfil de vendedor da sua empresa foi registado. Mais tarde, pode adicionar qualquer empregado da sua empresa como um administrador de co no Portal de publicação.

### <a name="1-select-solution-templates"></a>1. Selecione "Modelos de solução"

  ![desenho][img-pubportal-menu-sol-templ]

### <a name="2-create-a-new-solution-template"></a>2. Crie um novo modelo de solução

  ![desenho][img-pubportal-sol-templ-new]

### <a name="3-start-with-topologies"></a>3. iniciar com topologias
Um modelo de solução é um "principal" a todas as suas topologias. Pode definir várias topologias num modelo oferta/solução. Quando uma oferta é premida para transição, é enviado com todas as suas topologias. Siga os passos abaixo para definir a sua oferta:     

- Criar uma topologia de: "Identificador de topologia de" é normalmente o nome da topologia de para o modelo de solução. O identificador de topologia é utilizado no URL, conforme apresentado abaixo:

  Azure Marketplace: http://azure.microsoft.com/marketplace/partners/ {PublisherNamespace} / {OfferIdentifier} {TopologyIdentifier}

  Azure Portal: https://portal.azure.com/#gallery/ {PublisherNamespace}. {OfferIdentifier} {TopologyIdentifier}

- Adicione uma nova versão.

### <a name="4-get-your-topology-versions-certified"></a>4. a obter as versões de topologia certificadas
Carregar um ficheiro zip que contém todos os ficheiros necessários para aprovisionar essa versão específica da topologia de. Este ficheiro zip tem de conter o seguinte procedimento:

- ficheiro de *mainTemplate.json* e *createUiDefinition.json* no seu diretório de raiz.
- Qualquer modelos ligados e scripts todos necessários.

  > [AZURE.TIP] Enquanto os programadores trabalhar em criar a solução topologias de modelo e introdução-los certificada, o negócio, os departamentos de marketing e/ou legais da sua empresa podem trabalhar no conteúdo legal e de marketing.

## <a name="next-steps"></a>Próximos passos
Agora que criou o seu modelo de solução e carregar o ficheiro zip, siga as instruções no [Guia de conteúdo de marketing Marketplace](marketplace-publishing-push-to-staging.md) antes conduza a oferta para transição. Para ver o conjunto completo de marketplace publicação de artigos, visite [Introdução: como publicar uma oferta ao Azure Marketplace](marketplace-publishing-getting-started.md).

Também poderá estar interessado nestes artigos relacionados:

- Imagens VM: [Sobre imagens de Máquina Virtual no Azure](https://msdn.microsoft.com/library/azure/dn790290.aspx)
- Extensões VM: [VM agente e descrição geral de extensões VM](https://msdn.microsoft.com/library/azure/dn832621.aspx) e [extensões de VM Azure e funcionalidades](https://msdn.microsoft.com/library/azure/dn606311.aspx)
- Gestor de recursos do Azure: [Acerca dos processador Azure](../resource-group-authoring-templates.md) e [exemplos de modelo de processador simples](https://github.com/rjmax/ArmExamples)
- Conta de armazenamento acelera: [como Monitor para limitação de conta de armazenamento](http://blogs.msdn.com/b/mast/archive/2014/08/02/how-to-monitor-for-storage-account-throttling.aspx) e [armazenamento de Premium](../storage/storage-premium-storage.md#scalability-and-performance-targets-when-using-premium-storage)

[img-pubportal-menu-sol-templ]:media/marketplace-publishing-solution-template-creation/pubportal-menu-solution-templates.png
[img-pubportal-sol-templ-new]:media/marketplace-publishing-solution-template-creation/pubportal-solution-template-new.png
[link-acct-creation]:marketplace-publishing-accounts-creation-registration.md
[link-pubportal]:https://publish.windowsazure.com
