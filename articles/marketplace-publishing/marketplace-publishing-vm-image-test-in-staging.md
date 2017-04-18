<properties
   pageTitle="Testar a sua oferta VM do Marketplace | Microsoft Azure"
   description="Compreenda como testar a sua imagem VM Azure Marketplace."
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
   ms.date="08/01/2016"
   ms.author="hascipio" />

# <a name="test-your-vm-offer-for-the-azure-marketplace-in-staging"></a>Testar a sua oferta VM do Azure Marketplace na transição

Teste significa implementar o SKU em privado "sandbox" onde pode testar e validar respectiva funcionalidade antes de implementá-lo para mercado. O SKU é apresentado na transição apenas ao que teria para um cliente que tenha implementado-lo. A imagem VM deve ser autenticada para ser enviados para transição.

## <a name="step-1-push-your-offer-to-staging"></a>Passo 1: Emissão a oferta de transição

1. No separador **Publicar** , clique em **notificações Push para transição**.

    ![desenho](media/marketplace-publishing-vm-image-test-in-staging/vm-image-push-to-staging.png)

2. Se o Portal de publicação notifica-o de erros, corrigi-los.
3.  No **quem pode aceder a sua oferta faseada?** diálogo caixa, introduza a lista de subscrições Azure que irá utilizar para pré-visualizar a sua oferta no [portal do Azure pré-visualização](https://portal.azure.com).

    >[AZURE.NOTE] Em caso de máquinas virtuais e solução modelos, pedimos que **não faça** lista branca as subscrições do tipo CSP, DreamSpark ou Azure aberta.


    > Em caso de máquinas virtuais, quando clica no botão **De emissão para transição**, os passos seguintes são executados atrás da cena. Poderá ver o progresso da cada passo no separador Publicar de publicação portal. Tem de verificar esta página em intervalos regulares (até que o estado apresenta FASEADA) para quaisquer informações de falha que precisa de correção a partir do seu expiram.

    > - Na primeira o seu pedido de transição vai para a equipa de certificação que validar o vhd. No entanto, se o seu pedido tem tem apenas de marketing alterar, em seguida, o passo de certificação será ignorado.
    > - Assim que a certificação estiver concluída, a replicação da oferta iniciar em todos os centros de dados Azure. Geralmente leva-o até 24-48hours para a replicação concluir mas poderá demorar uma semana, dependendo do tamanho do vhd. No entanto, se o seu pedido tem tem apenas de marketing alterar, em seguida, a replicação é mais rápida.
    > - Quando a replicação estiver concluída, em seguida, a oferta estarão disponível no [portal do Azure](http:/portal.azure.com). Tornar-se TESTADO pelo que o estado de hora de publicação portal. Uma oferta faseada está visível no [portal do Azure](http:/portal.azure.com) utilizando apenas o ID de e-mail associado à subscrição com a qual está testada a oferta.

4. Inicie sessão no [Azure pré-visualizar portal](https://portal.azure.com) utilizando uma das subscrições Azure listadas no passo anterior.
5. Localizar a sua oferta e validar os pontos de imagem VM:
  - Certifique-se de que conteúdo de marketing apresentado corretamente no mercado.
  - Implementação de ponto a ponto da imagem VM.

      ![portal de mapa img](media/marketplace-publishing-push-to-staging/pubportal-mapping-azure-portal.jpg)

> [AZURE.IMPORTANT] A sua oferta vai permanecer no até que a notificar a Microsoft através do Portal de publicação de transição [separador**Publicar** > clique no botão **"Pedir aprovação para Push para produção"**] que está pronto para transmitir para produção. Este é um tempo ideal para que todos os membros da sua equipa verificação sobre tudo preparação para a sua oferta aceder listados.

> A plataforma de transição destina-se para testar a oferta em modo de pré-visualização pelo fabricante. Recomendamos vivamente impedir a utilizar este platofrm para fins de comercial.

## <a name="next-steps"></a>Próximos passos
Agora que a sua oferta está "testada" e ter testado suas funcionalidades e conteúdos de marketing, pode avançar para a fase de publicação final, **passo 4**: [Implementar a oferta de mercado](marketplace-publishing-push-to-production.md).

## <a name="see-also"></a>Consulte também
- [Introdução: como publicar uma oferta ao Azure Marketplace](marketplace-publishing-getting-started.md)
