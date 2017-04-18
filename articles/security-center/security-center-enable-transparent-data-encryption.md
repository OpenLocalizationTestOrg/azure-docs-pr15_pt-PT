<properties
   pageTitle="Activar a encriptação de dados transparente no Centro de segurança do Azure | Microsoft Azure"
   description="Este documento mostra-lhe como implementar o recomendação do Centro de segurança do Azure **Ativar transparente encriptação de dados**."
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
   ms.date="07/29/2016"
   ms.author="terrylan"/>

# <a name="enable-transparent-data-encryption-in-azure-security-center"></a>Activar a encriptação de dados transparente no Centro de segurança do Azure

Centro de segurança do Azure será que que permitem transparente dados encriptação (TDE) bases de dados SQL se TDE já não estiver ativado. TDE protege os seus dados e ajuda-o a cumprir requisitos de conformidade ao encriptá a sua base de dados, associadas cópias de segurança e ficheiros de registo da transação em repouso, sem necessidade de alterações à aplicação. Para saber que mais, consulte [Transparente encriptação de dados com base de dados do SQL Azure](https://msdn.microsoft.com/library/dn948096).

Esta recomendação aplica-se ao serviço do Azure SQL apenas; não inclui SQL em execução no seu máquinas virtuais.

> [AZURE.NOTE] Este documento apresenta o serviço utilizando uma implementação de exemplo.  Não se trata de um guia passo a passo.

## <a name="implement-the-recommendation"></a>Implementar a recomendação

1. Na pá **recomendações** , selecione **Ativar transparente encriptação de dados**.
![Activar a encriptação de dados transparente][1]

2. Esta ação abre a pá **Ativar transparente dados encriptação bases de dados SQL** . Selecione uma base de dados do SQL para ativar o TDE.
![Selecione SQL DB para ativar o TDE][2]
3. No pá **encriptação de dados transparente** , selecione **Sucessivamente** em encriptação de dados e selecione **Guardar** no Friso superior da pá.
![Ativar TDE][3]

  Assim que TDE está ativado na base de dados SQL selecionada, o **Estado de encriptação** será alterado para **encriptado**.    

  ![Estado de encriptação][4]

## <a name="see-also"></a>Consulte também

Este artigo mostrava como implementar o recomendação do Centro de segurança "Ativar transparente encriptação de dados." Para saber mais sobre SQL TDE, consulte o seguinte:

- [Encriptação de dados transparente com base de dados Azure SQL](https://msdn.microsoft.com/library/dn948096)
- [Começar com transparente dados encriptação (TDE)](../sql-data-warehouse/sql-data-warehouse-encryption-tde.md)

Para saber mais sobre o Centro de segurança, consulte o seguinte:

- [Definir políticas de segurança no Centro de segurança do Azure](security-center-policies.md) – Saiba como configurar políticas de segurança do seu subscrições Azure e grupos de recursos.
- [Gerir as recomendações de segurança no Centro de segurança do Azure](security-center-recommendations.md) – Saiba como recomendações ajudam a proteger os recursos do Azure.
- [Monitorização de estado de funcionamento de segurança no Centro de segurança do Azure](security-center-monitoring.md) – Saiba como monitorizar o estado de funcionamento dos seus recursos Azure.
- [Gerir e a responder a alertas de segurança no Centro de segurança do Azure](security-center-managing-and-responding-alerts.md) – Saiba como gerir e responder a alertas de segurança.
- [Soluções de parceiros de monitorização com o Centro de segurança do Azure](security-center-partner-solutions.md) – Saiba como monitorizar o estado de funcionamento de soluções de parceiros.
- [Perguntas mais frequentes do Centro de segurança do azure](security-center-faq.md) localizar perguntas mais frequentes sobre como utilizar o serviço.
- [Blogue do azure Security](http://blogs.msdn.com/b/azuresecurity/) – obter as notícias mais recentes do Azure segurança e informações.

<!--Image references-->
[1]: ./media/security-center-enable-tde-on-sql-databases/enable-tde.png
[2]:./media/security-center-enable-tde-on-sql-databases/transparent-data-encryption-blade.png
[3]: ./media/security-center-enable-tde-on-sql-databases/turn-on-tde.png
[4]: ./media/security-center-enable-tde-on-sql-databases/encrypted.png
