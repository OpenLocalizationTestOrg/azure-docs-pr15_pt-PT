<properties
   pageTitle="Testar a sua oferta de modelo de solução do Marketplace | Microsoft Azure"
   description="Compreenda como testar a sua oferta de modelo de solução do Azure Marketplace."
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
   ms.date="12/04/2015"
   ms.author="hascipio; v-divte" />

# <a name="test-your-solution-template-offer-in-staging"></a>Testar a sua oferta de modelo de solução na transição
Teste significa implementar a sua oferta em privado "sandbox" onde pode testar e verificar a sua funcionalidade antes da instalação push-produção. A oferta é apresentado na transição apenas ao que teria para um cliente que tenha implementado-lo. A sua oferta deve ser autenticada para ser enviados para transição.

Depois da oferta está testada, pode ver e testar a oferta no [Portal do Azure](https://portal.azure.com/).

Siga os passos abaixo para a sua oferta para transição de emissão e testá-la no [Portal do Azure](https://portal.azure.com/):

1.  Aceda ao [Portal de publicação](https://publish.windowsazure.com) > separador**Modelos de solução** > sua oferta > **Publicar** > **notificações Push para transição**.
2.  Forneça a lista de subscrições Azure que irá utilizar para pré-visualizar e testar a sua oferta.
3.  Inicie sessão no portal do Azure pré-visualização utilizando o ID da subscrição que utilizou no passo anterior.
4.  Proceder a round pelo menos um dos testes no portal do Azure pré-visualização dos pontos mencionados abaixo:
  - Certifique-se de que conteúdo de marketing apresentado corretamente no Azure Marketplace.
  - Implementação de ponto a ponto da topologia.
  - Efetue testes de desempenho e testes de limite.
  - Certifique-se de que a topologia de cumpre as melhores práticas.

## <a name="next-steps"></a>Próximos passos
Se estiver satisfeito com os resultados, em seguida, pode avançar para a fase de publicação final oferta, **passo 4**: [Implementar a oferta de mercado](marketplace-publishing-push-to-production.md). Caso contrário, efetue alterações na sua oferta e pedir certificação novamente.

> [AZURE.NOTE] Para alterações conteúdas marketing, certificação não é necessária.

Consulte o artigo [Introdução: como publicar uma oferta ao Azure Marketplace](marketplace-publishing-getting-started.md) para um guia para todas as tarefas do publisher.
