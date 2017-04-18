<properties
    pageTitle="Configurar a faturação alertas para as subscrições do Microsoft Azure | Microsoft Azure"
    description="Descreve como pode configurar alertas na sua fatura Azure para que pode evitar surpresas faturaçãohttps."
    services=""
    documentationCenter=""
    authors="vikdesai"
    manager="mbaldwin"
    editor=""
    tags="billing"
    />

<tags
    ms.service="billing"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/18/2016"
    ms.author="vikdesai"/>

# <a name="set-up-billing-alerts-for-your-microsoft-azure-subscriptions"></a>Configurar alertas de faturaçãohttps para as subscrições do Microsoft Azure

Estão preocupados sobre quanto está a despesas cada mês para a sua subscrição Azure? Se for o administrador da conta para uma subscrição do Azure, pode utilizar o Azure faturação serviço de alerta para criar personalizada alertas de faturaçãohttps que o ajudam a monitorizar e gerir a faturação atividade para as suas contas Azure.

Este serviço é um serviço de pré-visualização, pelo que a primeira coisa que tem de fazer é início de sessão para cima para o mesmo. Visite [a página funcionalidades da pré-visualização](https://account.windowsazure.com/PreviewFeatures) no portal de gestão de conta Azure para ativar esta funcionalidade.

## <a name="set-the-alert-threshold-and-email-recipients"></a>Definir os destinatários alertas do limiar e e-mail

Depois de receber a confirmação de correio eletrónico que o serviço de faturação está ativado para a sua subscrição, visite a [página subscrições](https://account.windowsazure.com/Subscriptions) no portal do conta. Clique na subscrição que pretende monitorizar e, em seguida, clique em **alertas**.

![][Image1]

Em seguida, clique em **Adicionar alerta** para criar o primeiro - pode configurar um total de cinco alertas faturaçãohttps por subscrição, com um limite diferente e até duas destinatários de correio eletrónico para cada alerta.

![][Image2]

Quando adiciona um alerta, atribua um nome exclusivo, selecione um determinado limiar despesas e, selecione os endereços de e-mail onde serão enviados os alertas. Quando a configurar o limiar, pode escolher um **Total de faturação** ou um **Crédito monetários** a partir da lista de **Alerta para** . Para um total de faturação, um alerta é enviado quando despesas de subscrição excede o limite. Para um crédito monetário, um alerta é enviado quando créditos monetários largar abaixo do limite. Créditos monetários aplicam-se normalmente para as versões de avaliação gratuitas e subscrições associadas MSDN contas.

![][Image3]

Azure suporta qualquer endereço de e-mail, mas não Certifique-se de que o endereço de e-mail funciona, por isso, verifique novamente se existem erros ortográficos.

## <a name="check-on-your-alerts"></a>Verificar os alertas

Depois de configurar alertas, o Centro de conta apresenta-os e quantos mais pode configurar o. Para cada alerta, verá a data e hora que foi enviada, quer seja um alerta para faturação Total ou crédito monetárias e o limite de que configurar. Coordenar de hora Universal 24 horas (UTC) que é o formato de data e hora e a data é aaaa-mm-dd formato. Clique no sinal de adição de um alerta na lista para editá-lo, ou clique de lata do lixo para eliminá-la.

[Image1]: ./media/azure-billing-set-up-alerts/billingalert1.png
[Image2]: ./media/azure-billing-set-up-alerts/billingalert2.png
[Image3]: ./media/azure-billing-set-up-alerts/billingalerts3.png
