<properties
   pageTitle="Fornecer detalhes de contacto de segurança no Centro de segurança do Azure | Microsoft Azure"
   description="Este documento mostra-lhe como fornecer detalhes de contacto de segurança no Centro de segurança do Azure."
   services="security-center"
   documentationCenter="na"
   authors="TerryLanfear"
   manager="MBaldwin"
   editor=""/>

<tags
   ms.service="security-center"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/17/2016"
   ms.author="terrylan"/>

# <a name="provide-security-contact-details-in-azure-security-center"></a>Fornecer detalhes de contacto de segurança no Centro de segurança do Azure

Centro de segurança do Azure será que forneça detalhes de contacto de segurança para a sua subscrição Azure se ainda não o fez. Estas informações serão utilizadas pela Microsoft para contactá-lo se a segurança resposta centro MSRC (Microsoft) descobre que os dados dos clientes tem sido acedidos por uma festa ilícita ou não autorizada. MSRC executa a monitorização de segurança select da rede Azure e infraestrutura e recebe queixas de informações da empresa e abuse ameaça de terceiros.

Uma notificação de correio eletrónico é enviada na primeira ocorrência diária de um alerta e apenas para os alertas de gravidade alta. Preferências de e-mail apenas podem ser configuradas para as políticas de subscrição. Grupos de recursos dentro de uma subscrição irão herdar estas definições.

> [AZURE.NOTE] Este documento apresenta o serviço utilizando uma implementação de exemplo.  Não se trata de um guia passo a passo.

## <a name="implement-the-recommendation"></a>Implementar a recomendação

1. Na pá **recomendações** , selecione **fornecer segurança detalhes de contactos**.
![Fornecer contacto de segurança][1]

2. Esta ação abre a pá **fornecer segurança detalhes de contactos**. Selecione a subscrição Azure de fornecer informações de contacto.
![Fornecer detalhes de contacto de segurança][2]

3. É aberta uma segunda pá **fornecer detalhes de contacto de segurança** .

  - Introduza o endereço de e-mail do contacto de segurança ou endereços separados por vírgulas. Não existe um limite para o número de endereços de e-mail que pode introduzir.
  - Introduza um número de telefone internacionais contactos de segurança.
  - Para receber e-mails acerca de alertas de gravidade alta, ative a opção **Enviar-me mensagens de correio eletrónico sobre alertas**.
  - No futuro, terá a opção para enviar notificações de correio eletrónico para os proprietários de subscrição. Esta opção está atualmente a cinzento.
  - Selecione **OK** para aplicar as informações de contacto de segurança a sua subscrição.

## <a name="see-also"></a>Consulte também

Para saber mais sobre o Centro de segurança, consulte o seguinte:

- [Definir políticas de segurança no Centro de segurança do Azure](security-center-policies.md) – Saiba como configurar políticas de segurança do seu subscrições Azure e grupos de recursos.
- [Gerir as recomendações de segurança no Centro de segurança do Azure](security-center-recommendations.md) – Saiba como recomendações ajudam a proteger os recursos do Azure.
- [Monitorização de estado de funcionamento de segurança no Centro de segurança do Azure](security-center-monitoring.md) – Saiba como monitorizar o estado de funcionamento dos seus recursos Azure.
- [Gerir e a responder a alertas de segurança no Centro de segurança do Azure](security-center-managing-and-responding-alerts.md) – Saiba como gerir e responder a alertas de segurança.
- [Soluções de parceiros de monitorização com o Centro de segurança do Azure](security-center-partner-solutions.md) – Saiba como monitorizar o estado de funcionamento de soluções de parceiros.
- [Perguntas mais frequentes do Centro de segurança do azure](security-center-faq.md) localizar perguntas mais frequentes sobre como utilizar o serviço.
- [Blogue do azure Security](http://blogs.msdn.com/b/azuresecurity/) – obter as notícias mais recentes do Azure segurança e informações.

<!--Image references-->
[1]: ./media/security-center-provide-security-contacts/provide-contacts.png
[2]:./media/security-center-provide-security-contacts/provide-contact-details.png
