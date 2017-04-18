<properties
   pageTitle="Proteger o serviço do Azure SQL no Centro de segurança do Azure | Microsoft Azure"
   description="Este endereços de documento recomendações no Centro de segurança do Azure que o ajudam a proteger o serviço do Azure SQL e manter-se em conformidade com as políticas de segurança."
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
   ms.date="08/04/2016"
   ms.author="terrylan"/>

# <a name="protecting-azure-sql-service-in-azure-security-center"></a>Proteger o serviço do Azure SQL no Centro de segurança do Azure

Centro de segurança do Azure analisa o estado de segurança dos seus recursos Azure. Quando o Centro de segurança identifica potenciais vulnerabilidade de segurança, cria recomendações orientá-lo durante o processo de configuração controlos necessários.  Recomendações aplicam-se para tipos de recursos Azure: máquinas virtuais (VMs), de rede, SQL e aplicações.

Este artigo aborda recomendações que se aplicam ao serviço do Azure SQL.  Azure Centro recomendações de serviço de SQL à volta de ativação de auditoria para os servidores de Azure SQL e bases de dados e ativar a encriptação para bases de dados SQL.  Utilize a tabela abaixo como uma referência para ajudá-lo a compreender as recomendações de serviço do SQL disponíveis e o que cada um deles irá fazer se aplicá-lo.

## <a name="available-sql-service-recommendations"></a>Recomendações de serviço do SQL disponíveis

|Recomendação|Descrição|
|-----|-----|
|[Activar o servidor de auditoria de SQL](security-center-enable-auditing-on-sql-servers.md)|Recomenda-se de que ativar auditoria para servidores do Azure SQL (serviço do Azure SQL; não incluir apenas SQL em execução no seu máquinas virtuais).|
|[Ativar a base de dados SQL de auditoria](security-center-enable-auditing-on-sql-databases.md)|Recomenda-se de que ativar auditoria para bases de dados do Azure SQL (serviço do Azure SQL; não incluir apenas SQL em execução no seu máquinas virtuais).|
|[Activar a encriptação de dados transparente no bases de dados SQL](security-center-enable-transparent-data-encryption.md)|Recomenda-se de que ativar encriptação para bases de dados do SQL (apenas para serviço do Azure SQL).|

## <a name="see-also"></a>Consulte também

Para saber mais sobre recomendações que se aplicam a outros tipos de recursos Azure, consulte o seguinte:

- [Proteger as suas máquinas virtuais no Centro de segurança do Azure](security-center-virtual-machine-recommendations.md)
- [Proteger as suas aplicações no Centro de segurança do Azure](security-center-application-recommendations.md)
- [Proteger a sua rede no Centro de segurança do Azure](security-center-network-recommendations.md)

Para saber mais sobre o Centro de segurança, consulte o seguinte:

- [Definir políticas de segurança no Centro de segurança do Azure](security-center-policies.md) – Saiba como configurar políticas de segurança do seu subscrições Azure e grupos de recursos.
- [Gerir e a responder a alertas de segurança no Centro de segurança do Azure](security-center-managing-and-responding-alerts.md) – Saiba como gerir e responder a alertas de segurança.
- [Perguntas mais frequentes do Centro de segurança do azure](security-center-faq.md) localizar perguntas mais frequentes sobre como utilizar o serviço.
