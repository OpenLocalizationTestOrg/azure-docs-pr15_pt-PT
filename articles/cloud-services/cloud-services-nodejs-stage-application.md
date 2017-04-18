<properties 
    pageTitle="Fase de uma implementação do serviço de nuvem (Node.js) | Microsoft Azure" 
    description="Saiba como implementar a aplicação do Azure para ambiente de teste, em seguida, implementar para um ambiente de produção com trocar IP Virtual (VIP)." 
    services="cloud-services" 
    documentationCenter="nodejs" 
    authors="rmcmurray" 
    manager="wpickett" 
    editor=""/>

<tags 
    ms.service="cloud-services" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="na" 
    ms.devlang="nodejs" 
    ms.topic="article" 
    ms.date="08/11/2016" 
    ms.author="robmcm"/>



# <a name="staging-an-application-in-azure"></a>Uma aplicação no Azure de transição

Uma aplicação embalada pode ser implementada para o ambiente de teste no Azure a ensaiar antes de movê-lo para o ambiente de produção na qual a aplicação está acessível na Internet. O ambiente de teste é exatamente como o ambiente de produção, exceto a que só pode aceder à aplicação faseada com um URL oculto que é gerado pelo Azure. Depois de ter verificar a sua aplicação está a funcionar corretamente, pode ser implementada para o ambiente de produção efetuando um trocar IP Virtual (VIP).

> [AZURE.NOTE] Os passos neste artigo aplicam-se apenas às aplicações nó alojadas como um serviço de nuvem Azure.

## <a name="step-1-stage-an-application"></a>Passo 1: Preparar uma aplicação

Esta tarefa aborda como fase uma aplicação ao utilizar o **PowerShell do Microsoft Azure**.

1.  Quando a publicação de um serviço, basta passar o **-ranhura** parâmetro para o cmdlet **AzureServiceProject publicar** .

    ```powershell
    Publish-AzureServiceProject -Slot staging
    ```

2.  Inicie a sessão [portal clássica Azure] e selecione **Serviços em nuvem**. Depois do serviço de nuvem é criado e o estado da coluna de **transição** foi atualizado **em execução**, clique no nome do serviço.

    ![apresentar um serviço em execução de portal][cloud-service]

3.  Selecione o **Dashboard**e, em seguida, selecione **teste**.

    ![dashboard de serviço de nuvem][cloud-service-dashboard]

4. Tenha em atenção o valor na entrada do **URL do Site** para a direita. O nome DNS é um ID interno oculto que gerou Azure.

    ![url do site][cloud-service-staging-url]

Agora pode verificar se a aplicação está a funcionar corretamente no ambiente de teste utilizando o URL do site de teste.

## <a name="step-2-upgrade-an-application-in-production-by-swapping-vips"></a>Passo 2: Atualizar uma aplicação de produção por VIP inversão

Depois de verificar a versão actualizada de uma aplicação de ambiente de teste, pode rapidamente disponibilizá-lo na produção por trocar a IPs virtual (VIP) ambientes de teste e de produção.

> [AZURE.NOTE] Este passo assume que já implementado uma aplicação para produção e testado a versão actualizada da aplicação.

1.  Inicie sessão no [portal clássica Azure], clique em **Serviços em nuvem** e, em seguida, selecione o nome do serviço.

2.  A partir do **Dashboard**, selecione a **transição**e, em seguida, clique em **trocar** na parte inferior da página. Esta ação abre a caixa de diálogo VIP trocar.

    ![caixa de diálogo do VIP trocar][vip-swap-dialog]

3.  Reveja as informações e, em seguida, clique em **OK**. As duas implementações começar como a implementação de transição muda para produção e a implementação de produção muda para a transição a atualizar.

Ter testado uma implementação e actualizados uma implementação de produção pelo trocar VIP com a implementação na transição com êxito.

## <a name="additional-resources"></a>Recursos adicionais

- [Como implementar uma atualização do serviço para produção por trocar VIP no Azure]

[Azure portal clássico]: http://manage.windowsazure.com
[cloud-service]: ./media/cloud-services-nodejs-stage-application/staging-cloud-service-running.png
[cloud-service-dashboard]: ./media/cloud-services-nodejs-stage-application/cloud-service-dashboard-staging.png
[cloud-service-staging-url]: ./media/cloud-services-nodejs-stage-application/cloud-service-staging-url.png
[vip-swap-dialog]: ./media/cloud-services-nodejs-stage-application/vip-swap-dialog.png
[Como implementar uma atualização do serviço para produção por trocar VIP no Azure]: cloud-services-how-to-manage.md#how-to-swap-deployments-to-promote-a-staged-deployment-to-production
