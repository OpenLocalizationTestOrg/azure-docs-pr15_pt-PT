<properties
    pageTitle="Mudar a sua subscrição Azure noutra oferta | Microsoft Azure"
    description="Saiba mais sobre como alterar a sua subscrição do Azure e mudar para uma oferta diferentes, utilizando o portal de gestão de subscrição"
    services=""
    documentationCenter=""
    authors="genlin"
    manager="mbaldwin"
    editor=""
    tags="billing,top-support-issue"/>

<tags
    ms.service="billing"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/30/2016"
    ms.author="genli"/>

# <a name="switch-your-azure-subscription-to-another-offer"></a>Mudar a sua subscrição Azure noutra oferta

Como um cliente [repartição](https://azure.microsoft.com/offers/ms-azr-0003p/) , poderá mudar a sua subscrição Azure noutra oferta no [Centro de conta](https://account.windowsazure.com/Subscriptions). Por exemplo, pode utilizar esta funcionalidade para tirar partido dos [créditos mensais para subscritores do Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/). Se estiver na [Versão de avaliação gratuita](https://azure.microsoft.com/free/), saiba como [Atualizar para repartição](billing-upgrade-azure-subscription.md).

#### <a name="whats-supported"></a>O que é suportado:

| A partir do                                                              | Para                                                                                      |
|-------------------------------------------------------------------|-----------------------------------------------------------------------------------------|
| [Repartição](https://azure.microsoft.com/offers/ms-azr-0003p/) | [Repartição Dev Center/testar](https://azure.microsoft.com/offers/ms-azr-0023p/)              |
| [Repartição](https://azure.microsoft.com/offers/ms-azr-0003p/) | [Professional Visual Studio](https://azure.microsoft.com/offers/ms-azr-0059p/)          |
| [Repartição](https://azure.microsoft.com/offers/ms-azr-0003p/) | [Professional de teste do Visual Studio](https://azure.microsoft.com/offers/ms-azr-0060p/)     |
| [Repartição](https://azure.microsoft.com/offers/ms-azr-0003p/) | [MSDN plataformas](https://azure.microsoft.com/offers/ms-azr-0062p/)                      |
| [Repartição](https://azure.microsoft.com/offers/ms-azr-0003p/) | [Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p/)            |
| [Repartição](https://azure.microsoft.com/offers/ms-azr-0003p/) | [Visual Studio Enterprise (Bizspark)](https://azure.microsoft.com/offers/ms-azr-0064p/) |

> [AZURE.NOTE] Para outros oferecem alterações, [contacte o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
    
## <a name="switch-subscription-offer"></a>Oferta de subscrição de parâmetro

> [AZURE.VIDEO switch-to-a-different-azure-offer]

1.  Iniciar sessão no [Centro de conta Azure](https://account.windowsazure.com/Subscriptions).

2.  Selecione a sua subscrição repartição.

3.  Clique em **Mudar para outra oferta**. O botão só está disponível se estiver na repartição e concluído com o seu primeiro período de faturação.

    ![Repare no botão de oferta mudar no lado direito da página](./media/billing-how-to-switch-azure-offer/switchbutton.png)
    
4.  **Selecione a oferta que pretende** na lista de ofertas que pode mudar a sua subscrição. Esta lista varia consoante os membros que está associada à sua conta. Se não existir nada disponível, verifique a [lista de ofertas disponíveis que quais pode mudar](#whats-supported) e certifique-se de que tem os membros à direita. 

    ![Selecione uma oferta ao qual pretende mudar para](./media/billing-how-to-switch-azure-offer/selectoffer.png)

5.  Dependendo da oferta que está a mudar para, poderá ver uma nota sobre o impacto das mudar. Aceda através desta lista cuidadosamente e siga as instruções antes de continuar.

    ![Reveja as notas](./media/billing-how-to-switch-azure-offer/thingstonote.png)

6.  Pode mudar o nome da sua subscrição. Por predefinição, podemos defini-lo o novo nome de oferta. Clique em **Mudar oferecer** para concluir o processo.

    ![Clique no botão verde](./media/billing-how-to-switch-azure-offer/confirmpage.png)

7.  Sucesso! A sua subscrição é agora ligada à nova oferta.

## <a name="why-cant-i-switch-offers"></a>Por que motivo não posso mudar ofertas?

Não poderá ver **Mudar para outra oferta** se:

- Não está no [repartição](https://azure.microsoft.com/offers/ms-azr-0003p/). Atualmente apenas as subscrições de repartição podem ser mudadas noutra oferta.

    - Se estiver na [Versão de avaliação gratuita](https://azure.microsoft.com/free/), saiba como [Atualizar para repartição](billing-upgrade-azure-subscription.md).

    - Para mudar a oferta de uma subscrição diferente, [contacte o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

- Estiver ainda na sua primeiro período de faturação; tem de aguardar que o primeiro período de faturação terminar antes de mudar de ofertas.

Poderá ver **não existem ofertas disponíveis na sua região ou país neste momento** se:

- Não está elegível para quaisquer parâmetros de oferta. Verifique a [lista de ofertas disponíveis que quais pode mudar](#whats-supported).

## <a name="what-does-switching-azure-offers-do-to-my-service-and-billing"></a>O que significa mudar ofertas Azure fazer ao meu serviço e de faturação?

Aqui estão os detalhes do que acontece quando muda de Azure planos no Centro de conta.

### <a name="access-to-services"></a>Acesso a serviços

Não existe nenhuma tempo de inatividade do serviço para quaisquer utilizadores associados à subscrição. No entanto, a oferta que mudar para poderá ter restrições. Por exemplo, os algumas ofertas proibi-utilização de produção, para que o que precisa para mover os recursos de produção para outra subscrição.

### <a name="billing"></a>Faturação

No dia que mudar, uma fatura é gerada para todos os encargos pendentes. Em seguida, a sua subscrição é faturada por termos comparar a oferta de novo. O aniversário de faturação da subscrição muda para a data em que alterou ofertas. A utilização e de faturação dados antes da alteração de oferta não é retida, pelo que recomendamos que transferir uma cópia antes de mudar.

> [AZURE.NOTE] Parâmetros de oferta devido a restrições relacionadas com faturação, não são possíveis dentro do primeiro ciclo de faturação depois de criar uma subscrição.

## <a name="can-i-migrate-from-pay-as-you-go-to-cloud-solution-providerhttpspartnermicrosoftcomsolutionscloud-reseller-overview-csp-or-enterprise-agreementhttpsazuremicrosoftcompricingenterprise-agreement-ea"></a>Pode posso migrar a partir da repartição para o [Fornecedor de solução da nuvem](https://partner.microsoft.com/Solutions/cloud-reseller-overview) (CSP) ou [Enterprise Agreement](https://azure.microsoft.com/pricing/enterprise-agreement/) (EA)?

Atualmente não suportamos mudar de oferta para CSP ou EA no Centro de contas. Para mover a sua subscrição existente para EA, peça ao administrador de inscrição adicionar a sua conta para o EA. Em seguida, recebe uma mensagem de e-mail de convite. Ao seguir as instruções para aceitar o convite, são movidas automaticamente as suas subscrições em Enterprise Agreement. Migrar para CSP, consulte o artigo [Azure subscrição migração para CSP](https://blogs.technet.microsoft.com/hybridcloudbp/2016/08/26/azure-subscription-migration-to-csp/).

## <a name="next-steps"></a>Próximos passos

- Saiba como [Gerir funções de administrador](billing-add-change-azure-subscription-administrator.md) para a sua subscrição

- Controlar a utilização do transferindo [dados de utilização e a fatura](billing-download-azure-invoice-daily-usage-date.md)

## <a name="need-help-contact-support"></a>Precisa de ajuda? Contacte o suporte.

Se ainda tiver ainda mais frequentes, consulte [contactar o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para obter o seu problema resolvido rapidamente.
