<properties 
    pageTitle="Resolução de problemas de RemoteApp Azure - falhas de iniciação e ligação à aplicação | Microsoft Azure" 
    description="Saiba como resolver problemas com a começar e ligar a aplicações no Azure RemoteApp." 
    services="remoteapp" 
    documentationCenter="" 
    authors="ericorman" 
    manager="mbaldwin" />

<tags 
    ms.service="remoteapp" 
    ms.workload="compute" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/15/2016" 
    ms.author="elizapo" />



#<a name="troubleshoot-azure-remoteapp---application-launch-and-connection-failures"></a>Resolver problemas de Azure RemoteApp - falhas de ligação de iniciação e de aplicações 

> [AZURE.IMPORTANT]
> Está a ser descontinuada Azure RemoteApp. Leia o [anúncio](https://go.microsoft.com/fwlink/?linkid=821148) para obter detalhes.

Aplicações alojadas no Azure RemoteApp podem falhar à iniciação para algumas razões diferentes. Este artigo descreve várias razões e mensagens de erro que os utilizadores poderão receber quando tentar iniciar as aplicações. Também fala sobre falhas de ligação. (Mas este artigo não descrevem problemas ao iniciar sessão no cliente do Azure RemoteApp.)  

Continue a ler para obter informações sobre as mensagens de erro comuns devido a falhas de ligação de iniciação e de aplicação.

##<a name="were-getting-you-set-up-try-again-in-10-minutes"></a>Vamos está a receber que configura o... Tente novamente em 10 minutos.

Este erro significa que RemoteApp do Azure é escalar para cima para cumprir a necessidade de capacidade dos seus utilizadores. Em segundo plano mais ocorrências do Azure RemoteApp VM são a ser criadas para processar as necessidades de capacidade dos seus utilizadores. Normalmente, isto leva-o até cerca de cinco minutos mas pode demorar até 10 minutos. Por vezes, este não acontece suficientemente rápido e os recursos são necessários imediatamente. Por exemplo um cenário 9 AM onde precisa de muitos utilizadores para utilizar a aplicação no Azure RemoteAppn ao mesmo tempo. Se tal acontecer, para si, que podemos pode ativar o **modo de capacidade** de back-end. Para fazer isto abrir uma bilhetes de suporte do Azure e ou enviar por correio eletrónico-na [remoteappforum@microsoft.com](mailto:remoteappforum@microsoft.com). Certifique-se incluir o seu ID da subscrição no pedido de.  

![Recomendamos que configura o está a ser obter](./media/remoteapp-apptrouble/ra-apptrouble1.png)

## <a name="could-not-auto-reconnect-to-your-applications-please-re-launch-your-application"></a>Não foi possível reestabelecer ligação automática para as suas aplicações, introduza novamente iniciar a sua aplicação  

Esta mensagem de erro é frequente se estava a utilizar RemoteApp do Azure e, em seguida, coloque o seu PC para a suspensão já 4 horas e, em seguida, reactivou o seu PC e o cliente de RemoteApp do Azure tenta para automaticamente restabelecer a ligação e tempo limite foi excedido.  Informe os utilizadores para navegar de volta para a aplicação e tentar abri-lo a partir do cliente RemoteApp do Azure.

![Poderá não automática-restabelecer a ligação para as suas aplicações](./media/remoteapp-apptrouble/ra-apptrouble2.png) 

## <a name="problems-with-the-temp-profile"></a>Problemas com o perfil temp 

Este erro ocorre quando o seu perfil de utilizador (disco de perfil de utilizador) falha ao montagem e o utilizador recebido um perfil temporário.  Os administradores devem navegue para a coleção no portal do Azure e, em seguida, aceda ao separador **sessões** e tentar **Terminar sessão** do utilizador. Isto irá forçar um registo cheio fora de sessão do utilizador -, em seguida, o utilizador tentar iniciar uma aplicação novamente. Se isso falhar contactar o suporte do Azure e ou correio eletrónico-na [remoteappforum@microsoft.com](mailto:remoteappforum@microsoft.com).

## <a name="azure-remoteapp-has-stopped-working"></a>Azure RemoteApp deixou de funcionar

Esta mensagem de erro significa que o cliente RemoteApp do Azure está a ter um problema e tem de ser reiniciado. Peça aos utilizadores para fechar navegarem: selecione **Fechar programa** e, em seguida, volte a iniciar o cliente do Azure RemoteApp.  Se o problema persistir abrir e bilhetes de suporte do Azure e de e-mail ou-na [remoteappforum@microsoft.com](mailto:remoteappforum@microsoft.com).

![Azure RemoteApp deixou de funcionar](./media/remoteapp-apptrouble/ra-apptrouble3.png)  

## <a name="an-error-occurred-while-remote-desktop-connection-was-accessing-this-resource-retry-the-connection-or-contact-your-system-administrator"></a>Ocorreu um erro enquanto a ligação de ambiente de trabalho remoto estava a aceder a este recurso. Repetir a ligação ou contacte o administrador de sistema

Esta é uma mensagem de erro genérica - contactar o suporte do Azure e ou enviar por correio eletrónico-na [remoteappforum@microsoft.com](mailto:remoteappforum@microsoft.com) para podemos investigar. 

![Mensagem genérica do RemoteApp do Azure](./media/remoteapp-apptrouble/ra-apptrouble4.png) 