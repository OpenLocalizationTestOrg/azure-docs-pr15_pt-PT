
<properties
    pageTitle="Utilizar o Office com o Azure RemoteApp | Microsoft Azure" 
    description="Saiba como o Office e RemoteApp do Azure funcionam em conjunto"
    services="remoteapp"
    documentationCenter=""
    authors="lizap"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/15/2016"
    ms.author="elizapo" />

# <a name="using-office-with-azure-remoteapp"></a>Utilizar o Office com Azure RemoteApp

> [AZURE.IMPORTANT]
> Está a ser descontinuada Azure RemoteApp. Leia o [anúncio](https://go.microsoft.com/fwlink/?linkid=821148) para obter detalhes.

Tem duas opções para as aplicações do Office no Azure RemoteApp de alojamento: Office 365 ProPlus ou a versão de avaliação do Office 2013 Professional Plus.

**Olhe, sabia que temos uma nova, melhor artigo mais cedo irá substituir isto? Consulte o artigo [como utilizar a sua subscrição do Office 365 RemoteApp do Azure](remoteapp-officesubscription.md). Cobrir toda a informação de que necessita para utilizar o Office 365 + Azure RemoteApp.**

## <a name="office-365-proplus"></a>Office 365 ProPlus
Pode criar uma coleção de RemoteApp utilizando a Office 365 ProPlus imagem do modelo. Esta opção permite-lhe estender o seu serviço do Office 365 para RemoteApp. Tem de ter um plano de subscrição existente e os seus utilizadores têm de ser licenciados para o serviço Office 365 ProPlus, quer autónomo ou através do Office 365 planos do serviço.

RemoteApp suporta a ativação de computador partilhado do Office 365. Quando ativar a ativação de computador partilhado e utilize a [ferramenta de implementação do Office](http://www.microsoft.com/download/details.aspx?id=36778) para a instalação, Office 365 ProPlus seja instalada sem a ser ativado. Quando um sinais de utilizador para uma coleção de que contém o Office 365, Office verifica se o utilizador ter sido aprovisionado para o Office 365 ProPlus. Se Sim, Office temporariamente activa Office 365 ProPlus - este ativação persiste até essa sinais de utilizadores terminar o serviço.

Para utilizar a ativação de computador partilhado do Office 365, é necessário criar um [modelo personalizado](remoteapp-create-custom-image.md) e instalar o Office 365 ProPlus aí, seguinte [estas indicações](https://technet.microsoft.com/library/dn782858.aspx).

Pode gerir licenças do Office 365 dos seus utilizadores no [Portal de administração do Office 365](https://portal.office365.com/). Leia mais informações sobre os [planos de serviço do Office 365](http://technet.microsoft.com/library/office-365-plan-options.aspx).  


## <a name="office-2013-professional-plus-trial"></a>Versão de avaliação do Office 2013 Professional Plus
Durante uma versão de avaliação de 30 dias do RemoteApp, pode utilizar a imagem do Office 2013 Professional Plus (versão de avaliação) modelo para criar uma colecção de RemoteApp. Pode atribuir aos utilizadores para esta coleção de avaliação utilizando as suas contas de trabalho do Azure Active Directory ou contas do Microsoft. Não é necessária nenhuma subscrição adicional.

Este é uma ótima opção funcionam como ricochete de pneus e obter um bom sentimento para o Office no RemoteApp. No entanto, esta opção está destinados a avaliação e de teste apenas. Coleções de RemoteApp criadas com a imagem do Office 2013 Professional Plus (versão de avaliação) modelo que não podem ser transitaram para o modo de produção e serão desactivadas no final do período de avaliação.

## <a name="switching-from-trial-to-production"></a>Mudar da versão de avaliação para produção
Quando começar a sua versão de avaliação gratuita de 30 dias, uma nota na secção RemoteApp do portal irá indicar quanto tempo que ainda lhe falta a avaliação antes de necessitar da transição a uma conta paga. Pode ativar a sua conta e mudar para o modo de produção utilizando a hiperligação nesta nota.

Quando ativar a sua conta, isto irá afectar todas as colecções de RemoteApp na sua conta.

- Coleções de sites que executam o com o Windows Server 2012 R2 ou as Office 365 ProPlus imagens de modelos de forma totalmente integrada irão transição para o produção. Todos os dados de utilizador e definições, incluindo sessões em curso, permanecem intactas.
- Se tiver transferido imagens de modelo personalizado, coleções de utilizar essas imagens também serão transição forma totalmente integrada.
- A imagem do Office 2013 Professional Plus (versão de avaliação) modelo destina-se apenas a avaliação. Não podem ser que transitaram coleções de executar com esta imagem do modelo de produção. Que irão ser colocados no estado "desativado".


Se não transição para o modo de produção da expiração da sua versão de avaliação, serão desativadas em suas coleções de RemoteApp. Não se preocupe - as definições e dados dos utilizadores são guardados para outro cerca de 90 dias, para que possa ainda ativar o seu serviço e mudar para o modo de produção sem quaisquer perdas de dados.
