
<properties 
    pageTitle="Como utilizar RemoteApp do Azure com contas de utilizador do Office 365 | Microsoft Azure"
    description="Saiba como utilizar RemoteApp do Azure com as minhas contas de utilizador do Office 365"
    services="remoteapp"
    documentationCenter="" 
    authors="piotrci" 
    manager="mbaldwin" />

<tags 
    ms.service="remoteapp" 
    ms.workload="compute" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/15/2016" 
    ms.author="elizapo" />



# <a name="how-to-use-azure-remoteapp-with-office-365-user-accounts"></a>Como utilizar RemoteApp do Azure com contas de utilizador do Office 365

> [AZURE.IMPORTANT]
> Está a ser descontinuada Azure RemoteApp. Leia o [anúncio](https://go.microsoft.com/fwlink/?linkid=821148) para obter detalhes.

Se tiver uma subscrição do Office 365 tem um Azure Active Directory que armazena os nomes de utilizador e palavras-passe utilizadas para aceder aos serviços do Office 365. Por exemplo, quando os utilizadores ativar o Office 365 ProPlus eles autenticam-se contra Azure AD para verificar a existência de licenças. A maioria dos clientes, optar por utilizar o mesmo directório com RemoteApp do Azure.

Se estiver a implementar o Azure RemoteApp provavelmente estiver a utilizar uma subscrição do Azure que está associada um Azure AD diferente. Para poder utilizar o seu diretório do Office 365, terá de mover a subscrição Azure para esse diretório.

Para obter informações sobre como implementar aplicações de cliente do Office 365, consulte o artigo [como utilizar a sua subscrição do Office 365 RemoteApp do Azure](remoteapp-officesubscription.md).
 
## <a name="phase-1-register-your-free-office-365-azure-active-directory-subscription"></a>Fase 1: Registar a sua subscrição do Office 365 Azure Active Directory gratuita
Se estiver a utilizar o portal clássico Azure, utilize os passos em [Registe-se a sua subscrição gratuita do Azure Active Directory](https://technet.microsoft.com/library/dn832618.aspx) para obter acesso administrativo ao seu Azure AD através do Portal de gestão do Azure. Como resultado deste processo deverá conseguir inicie sessão no portal do Azure e ver no diretório da sua aí – neste momento não verá muito mais uma vez que é a subscrição completa Azure que estiver a utilizar com RemoteApp do Azure num directório diferente.

Lembre-se de que o nome e palavra-passe da conta de administrador que criou neste passo – serão necessárias na fase 2.

Se estiver a utilizar o portal do Azure, consulte o artigo [como registar e ativar uma gratuito Azure Active Directory através do portal do Office 365](http://azureblogger.com/2016/01/how-to-register-and-activate-a-free-azure-active-directory-using-office-365-portal/).

## <a name="phase-2-change-the-azure-ad-associated-with-your-azure-subscription"></a>Fase 2: Altere o Azure AD associado à sua subscrição Azure.
Vamos alterar a sua subscrição Azure a partir do seu diretório atual no diretório do Office 365 que recomendamos trabalhou com na fase 1.

Siga as instruções que é descritas em [alterar o inquilino do Azure Active Directory em RemoteApp do Azure](remoteapp-changetenant.md). Paga uma atenção especial para os seguintes passos:

- Passo #1: Se tiver implementado Azure RemoteApp (ARA) nesta subscrição, certifique-se de que remove todas as contas de utilizador do Azure AD de todas as colecções ARA em primeiro lugar, antes de tentar mais nada. Em alternativa, pode considerar eliminando qualquer coleções de sites existentes.
- Passo #2: Este é um passo crítico. Tem de utilizar uma conta Microsoft (por exemplo, @outlook.com) como administrador do serviço da subscrição; Isto acontece porque não é possível temos quaisquer contas de utilizador a partir de existente Azure AD anexados a subscrição – se fizer podemos, podemos não será possível movê-lo para um Azure AD diferente.
- Passo #4: Ao adicionar um directório existente, o sistema irá pedir-lhe para iniciar sessão com a conta de administrador para esse directório. Certifique-se utilizar a conta de administrador a partir da fase 1.
- Passo #5: Altere o directório de elemento principal da subscrição para o seu diretório do Office 365. O resultado final deve ser que em Definições -> subscrições a sua subscrição listas do diretório do Office 365. 
![Alterar o diretório de elemento principal da subscrição](./media/remoteapp-o365user/settings.png)
 

Neste momento a sua subscrição do Azure RemoteApp está associada com o Office 365 Azure AD; Pode utilizar as contas de utilizador do Office 365 existentes com o Azure RemoteApp!




