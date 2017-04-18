<properties
    pageTitle="Adicionar um utilizador à coleção de RemoteApp do Azure | Microsoft Azure"
    description="Saiba como adicionar utilizadores à coleção de RemoteApp do Azure"
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

# <a name="how-to-add-a-user-to-your-azure-remoteapp-collection"></a>Como adicionar um utilizador à coleção de RemoteApp do Azure

> [AZURE.IMPORTANT]
> Está a ser descontinuada Azure RemoteApp. Leia o [anúncio](https://go.microsoft.com/fwlink/?linkid=821148) para obter detalhes.

Antes dos seus utilizadores podem ver e utilize as suas aplicações no RemoteApp do Azure, tem de conceder-lhes acesso à sua colecção. Esta é a parte fácil: no separador de **Acesso do utilizador** , introduza as informações de conta para o utilizador e, em seguida, clique na marca de verificação.

Informações da conta necessita? Que depende do tipo de coleção de que criou (na nuvem ou híbrido) e se estiver a utilizar o Office 365 ProPlus nessa coleção.

## <a name="supported-user-identities"></a>Identidades dos utilizadores suportados

Os tipos de coleções de sites diferente (nuvem vs. híbrido) suportam utilizar diferentes identidades de utilizador para aceder às aplicações.  

Para uma coleção de implementações do RemoteApp, tem de configurar uma infraestrutura de domínio do Active Directory no local e um inquilino do Azure Active Directory com integração de diretórios (e, opcionalmente, único início de sessão). Para além disso, tem de criar alguns objetos do Active Directory no diretório no local.  

Para uma coleção de nuvem de RemoteApp, qualquer utilizador que tem o Azure Active Directory identidades de suporte poderá ser concedido acesso do utilizador para RemoteApp para incluir Accounts do Microsoft.  Consulte a tabela abaixo.

Utilizadores do Office 365 são os utilizadores do Azure Active Directory. Se tiverem híbrido do Azure Active Directory, diretório sincronizados contas,-lhes podem ser concedido acesso dos utilizadores numa implementação híbrida RemoteApp.   

Pode utilizar esta tabela como uma referência rápida para o qual identidade é suportada no seu recolha e quais são os requisitos do Active Directory.

|Contas de utilizador |Nuvem   |Híbrido|
|--------------|--------|------|
|Conta Microsoft|     Sim|    N|
|Azure Active Directory (Azure AD)| | |
|Azure AD na nuvem apenas    |Sim    |N |
|ADsync com a sincronização de palavra-passe  |Sim    |Sim    |
|ADsync sem sincronização de palavra-passe|  Sim |N |
|ADsync com o AD FS  |Sim    |Sim    |
|[parte 3 Azure suportadas fornecedores de identidade](https://msdn.microsoft.com/library/azure/jj679342.aspx)  (por exemplo Ping) |Sim    |Sim|
|Autenticação Multifator    |Sim    |Sim    |

Consulte o artigo [obter mais informações](remoteapp-ad.md) sobre a configuração do Active Directory para RemoteApp.


> [AZURE.NOTE] Os utilizadores do Azure Active Directory tem de ser do inquilino de que está associada a sua subscrição. (Pode ver e modificar a sua subscrição no separador **Definições** no portal. Consulte o artigo [alterar o inquilino do Azure Active Directory utilizado pelo RemoteApp](remoteapp-changetenant.md) para obter mais informações.)

## <a name="office-365-proplus-user-account-information"></a>Informações de conta do Office 365 ProPlus de utilizador
Se estiver a utilizar a Office 365 ProPlus imagem do modelo na sua coleção de *ou* se tiver criado uma imagem personalizada que utiliza o Office 365, apenas são permitidos para adicionar utilizadores do Azure Active Directory que tem subscrições do Office 365 para o domínio predefinido da sua subscrição. Para mais informações, consulte [utilizar o Office 365 com RemoteApp do Azure](remoteapp-o365.md) .
