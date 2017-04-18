
<properties
    pageTitle="Alterar o inquilino do Azure Active Directory em RemoteApp do Azure | Microsoft Azure"
    description="Saiba como alterar o inquilino do Azure Active Directory associado RemoteApp do Azure"
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



# <a name="change-the-azure-active-directory-tenant-in-azure-remoteapp"></a>Alterar o inquilino do Azure Active Directory em RemoteApp do Azure

> [AZURE.IMPORTANT]
> Está a ser descontinuada Azure RemoteApp. Leia o [anúncio](https://go.microsoft.com/fwlink/?linkid=821148) para obter detalhes.

Azure RemoteApp utiliza Azure Active Directory (Azure AD) para permitir o acesso do utilizador. O inquilino apenas Azure AD que pode utilizar no Azure RemoteApp é aquele associados à subscrição Azure. Pode ver a subscrição associada na página **Definições** no portal. Observe a coluna de **diretório** no separador **subscrições** .

> [AZURE.NOTE] Para que esta alteração ser concluída com êxito, primeiro, remova todos os utilizadores do inquilino existente do Azure Active Directory de todas as coleções de RemoteApp do Azure. Para fazer isto, aceda ao Portal do Azure, aceda ao separador **RemoteApp do Azure** e abra cada coleção de RemoteApp do Azure. Aceda ao separador de **utilizadores** e remover utilizadores que pertencem ao seu inquilino do Azure Active Directory atual. Repita para todas as colecções RemoteApp do Azure existentes. Sem ter de fazer isto, não poderá criar ou correcção coleções de sites.

Se quiser utilizar um inquilino diferentes, utilize estes passos para alterar a associação com a sua subscrição:

1. No portal do, remova quaisquer utilizadores do Azure AD para o qual tenha concedido acesso RemoteApp do Azure coleções de sites. (Consulte a nota acima para obter passos sobre como fazer isto.)


2. Configurar uma conta Microsoft (anteriormente chamada uma conta Live ID) como o administrador do serviço. (Não sabe se já for o administrador do serviço? Pode encontrar ao clicar em **Definições -> administradores**.) Agora, eis como alterar que:
    1. Clique no utilizador no canto superior direito e, em seguida, clique em **Ver a minha conta**.
    2. Clique na subscrição. Em seguida, na página novo, desloque-se para baixo e clique em **Editar detalhes da subscrição** no lado direito. (Ordenar do segundo nome na parte inferior direita, se esse ajuda-o a localizá-lo.)
    3. Escreva a conta Microsoft para o utilizador que deve ser administrador do serviço.

3. Agora, terminar sessão no portal do e, em seguida, inicie sessão novamente com a conta Microsoft que especificou no passo anterior.


4. Clique em **New -> aplicação serviços -> Active Directory -> diretório -> personalizada criar**.
5. Em **diretório**, selecione **Utilizar directório existente**. Iremos tem de terminar a o portal de agora, por isso escolha **que estou pronto para ser assinado agora**.
6. Voltar a iniciar sessão no portal do como um administrador global do directório que pretende adicionar. (Se de que não já foram um administrador global, será após um round de início de sessão e, em seguida, terminar sessão.)
7. É-lhe perguntado quando iniciar sessão se pretende utilizar o seu inquilino existente do AD com a sua subscrição. Clique em **continuar**e, em seguida, clique em **Terminar sessão agora**.
5. Volta a iniciar sessão novamente e voltar à **Definições -> subscrições**. Selecione a sua subscrição e, em seguida, clique em **Editar no diretório**. Selecione o inquilino do Azure AD que pretende utilizar.



Pode inquilino de utilizar o novo Azure AD agora para controlar o acesso à subscrição Azure e para configurar o acesso do utilizador no Azure RemoteApp.
