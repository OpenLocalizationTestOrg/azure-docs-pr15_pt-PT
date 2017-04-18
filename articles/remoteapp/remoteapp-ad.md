
<properties 
    pageTitle="Azure AD + requisitos do Active Directory para RemoteApp do Azure | Microsoft Azure" 
    description="Saiba como configurar o Active Directory para trabalhar com RemoteApp do Azure." 
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



# <a name="azure-ad--active-directory-requirements-for-azure-remoteapp"></a>Azure AD + requisitos do Active Directory para RemoteApp do Azure

> [AZURE.IMPORTANT]
> Está a ser descontinuada Azure RemoteApp. Leia o [anúncio](https://go.microsoft.com/fwlink/?linkid=821148) para obter detalhes.


Para a sua coleção de implementações do Azure RemoteApp ou para uma coleção de nuvem ao qual pretende federar utilizando AD Connect, tem de fazer o seguinte.

### <a name="connect-azure-ad-and-active-directory"></a>Ligar o Azure AD e o Active Directory

Se pretende ligar o seu inquilino do Azure AD e os ambientes de Active Directory no local, utilize AD Connect. Irá demorar apenas [4 cliques](https://blogs.technet.microsoft.com/enterprisemobility/2014/08/04/connecting-ad-and-azure-ad-only-4-clicks-with-azure-ad-connect/) para ligar os dois directórios.

Nota - da sincronização de diretórios é necessária para coleções de híbrido.

### <a name="make-sure-your-domaincom-match"></a>Certifique-se o "@domain.com" corresponder
Antes de começar, certifique-se de que o UPN para sua floresta no local corresponde ao sufixo do seu domínio do Azure AD. 

Depois de configurar o sufixo UPN do domínio no Azure AD, todos os utilizadores que iniciem sessão no Azure RemoteApp irão iniciar a sessão como “user@ <the suffix you set up>". Certifique-se de que os utilizadores também podem iniciar sessão com o mesmo user@suffix para o domínio no local. Em certos casos pode configurar o nome de um domínio no Azure AD ao especificar um sufixo de domínio diferente para o utilizador de prem. Neste caso, os utilizadores não será possível ligar a qualquer domínio computadores ou recursos através de Azure RemoteApp.

Por exemplo, se configurou o seu sufixo UPN de domínio no AAD como contoso.com, mas alguns utilizadores no local/AD estão configuradas para iniciar sessão com @contoso.uk, , em seguida, esses utilizadores não poderão corretamente inicie sessão na coleção de ARA. Os utilizadores UPN no AAD e AD tem de ser a mesma para o início de sessão ser possível"

### <a name="create-objects-for-azure-remoteapp"></a>Criar objetos para RemoteApp do Azure
Também terá de criar o seguinte procedimento no local objetos do Active Directory:

- Uma conta de serviço para fornecer acesso aos recursos do domínio para programas RemoteApp por participar RDSH pontos de fim para o domínio no local.
- Uma organizacional unidade (OU) para conter objetos de máquina RemoteApp. Utilização da or é recomendada (mas não é necessário) para identificar as políticas que irá utilizar com RemoteApp e contas.

Precisa destes objetos ao criar a sua coleção de RemoteApp, por isso, certifique-se de que efetue estes passos primeiro.