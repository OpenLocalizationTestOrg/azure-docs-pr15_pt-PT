<properties
    pageTitle="Aplicações na pilha Azure - problemas conhecidos e resolução de problemas da Web | Microsoft Azure"
    description="Orientação detalhada para a Web Apps no Azure pilha de implementação"
    services="azure-stack"
    documentationCenter=""
    authors="apwestgarth"
    manager="stefsch"
    editor=""/>

<tags
    ms.service="azure-stack"
    ms.workload="app-service"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/26/2016"
    ms.author="anwestg"/>
    
# <a name="web-apps-resource-provider---known-issues-and-troubleshooting"></a>Fornecedor de recursos de aplicações Web - problemas conhecidos e resolução de problemas

> [AZURE.NOTE] As seguintes informações só se aplica a implementações do Azure pilha TP1.

Se tiver problemas ao implementar ou trabalhar com Web Apps no Azure pilha consulte as informações abaixo.

## <a name="azure-stack-web-apps-not-available-in-the-portal"></a>Azure pilha Web Apps não está disponível no portal

![Pilha Azure Web Apps criar nova aplicação Web][1]

Depois de concluir o [registo do fornecedor de aplicações Web do Azure pilha](azure-stack-webapps-deploy.md#register-the-newly-deployed-azure-stack-web-apps-provider-with-arm) se de que não é possível localizar a Web + pá móvel, em seguida, tente o seguinte:
* **Termine a sessão do portal** e **feche o browser** e, em seguida, num **novo browser janela inicie sessão no portal** e tente novamente.

Se ainda não é possível ver na web e pá móvel, experimente o seguinte:

1.  No **Computador do Azure pilha anfitrião** no **Gestor de Hyper-V** localize os **xRPVM** e, em seguida, **ligar-se** para a VM.
2.  Abra uma **linha de comandos como administrador** e execute **IISRESET**
3.  Voltar para a **ClientVM** e abrir uma **nova janela do browser**, **Inicie sessão no portal** e tente novamente.

## <a name="deployment-fails-when-creating-a-web-app-in-a-new-resource-group"></a>Implementação Falha ao criar uma aplicação Web num novo grupo de recursos

Na primeira pré-visualização de aplicações Web, **Todas as aplicações Web** têm de ser criados no mesmo grupo de recursos do fornecedor de recursos de aplicações Web (**AppService LOCAL**).  Este é um problema conhecido e será resolvido numa pré-visualização futura.

## <a name="other-issues"></a>Outros problemas

Se se deparar com outros problemas com a Web Apps no Azure pilha publicam no [Azure pilha fórum,] (https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack) onde os membros da equipa são monitorização de mensagens.


<!--Image references-->
[1]: ./media/azure-stack-webapps-troubleshoot-known-issues/NewWebandMobile.png



<!--Links-->
[Azure_Stack_App_Service_preview_installer]: http://go.microsoft.com/fwlink/?LinkID=717531
[WebAppsDeployment]: http://go.microsoft.com/fwlink/?LinkId=723982
[AppServiceHelperScripts]: http://go.microsoft.com/fwlink/?LinkId=733525
