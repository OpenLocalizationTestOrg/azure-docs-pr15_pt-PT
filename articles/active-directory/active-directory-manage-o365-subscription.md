<properties
   pageTitle="Gerir o diretório para a sua subscrição do Office 365 no Azure | Microsoft Azure"
   description="Gerir um diretório de subscrição do Office 365 com o Azure Active Directory e portal clássico do Azure"
   services="active-directory"
   documentationCenter=""
   authors="curtand"
   manager="femila"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="08/23/2016"
   ms.author="curtand"/>

# <a name="manage-the-directory-for-your-office-365-subscription-in-azure"></a>Gerir o diretório para a sua subscrição do Office 365 no Azure

Este artigo descreve como gerir um diretório que foi criado para uma subscrição do Office 365 através do portal clássico Azure. Tem de ser o administrador do serviço ou um administrador de uma subscrição do Azure colega para iniciar sessão no portal do Azure clássico. Se ainda não tiver uma subscrição do Azure, pode inscrever-se para uma [versão de avaliação de 30 dias gratuita](https://azure.microsoft.com/trial/get-started-active-directory/) hoje e implementar a sua primeira solução de nuvem em 5 minutos, utilizar esta ligação. Certifique-se de que utilize a conta escolar ou profissional que utiliza para iniciar sessão no Office 365.

Depois de concluir a subscrição Azure, pode iniciar sessão portal clássico do Azure e aceder a serviços Azure. Clique na extensão do Active Directory para gerir o mesmo directório autentica os utilizadores do Office 365.

Se já tiver uma subscrição do Azure, o processo para gerir um directory adicional também é simples. Por exemplo, Michael Silva poderá ter uma subscrição do Office 365 de Contoso.com. Ele também tem uma subscrição do Azure que ele inscreveu utilizando a sua conta Microsoft, msmith@hotmail.com. Neste caso, ele faz a gestão dos dois directórios.

  Subscrição |  Office 365  |  Azure
  -------------- | ------------- | -------------------------------
  Nome a apresentar |  Contoso  |     Directório do Azure Active Directory (Azure AD) predefinido
  Nome de domínio  |  contoso.com  | msmithhotmail.onmicrosoft.com

Ele pretende gerir identidades de utilizador no diretório da Contoso enquanto ele iniciou sessão no Azure utilizando a sua conta Microsoft, para que ele pode ativar as funcionalidades do Azure AD como a autenticação multifator. O diagrama seguinte poderão ajudar a ilustrar o processo.

![Diagrama para gerir dois directórios independentes](./media/active-directory-manage-o365-subscription/AAD_O365_03.png)

Neste caso, os dois directórios são independentes uns dos outros.

## <a name="to-manage-two-independent-directories"></a>Para gerir dois directórios independentes
Ordem para Michael Silva gerir ambos os directórios enquanto ele iniciou sessão no Azure como msmith@hotmail.com, ele tem de concluir os passos seguintes:

> [AZURE.NOTE]
> Podem ser concluídos estes passos apenas quando um utilizador tem sessão iniciada com uma conta Microsoft. Se o utilizador está a sessão iniciado com uma trabalho ou conta escolar, a opção para **Utilizar directório existente** não está disponível. Uma conta escolar ou profissional pode ser autenticada apenas pelo seu diretório (ou seja, o directório onde a conta escolar ou profissional é armazenada e proprietária empresa ou escola).

1.  Início de sessão do [portal clássica Azure](https://manage.windowsazure.com) como msmith@hotmail.com.
2.  Clique em **Novo** > **Serviços de aplicação** > **Do Active Directory** > **diretório** > **Criar personalizado**.
3.  Clique em utilizar directório existente e selecione a caixa de verificação **estou pronto para ser assinado agora** .
4.  Inicie sessão no portal do Azure clássico como administrador global do Contoso.onmicrosoft.com (por exemplo, msmith@contoso.com).
5.  Quando lhe for pedido para **utilizar o directory da Contoso com o Azure?**, clique em **continuar**.
6.  Clique em **Terminar sessão agora**.
7.  Inicie sessão no portal do Azure clássico como msmith@hotmail.com. O directory da Contoso e o directório predefinido são apresentados na extensão do Active Directory.

Depois de concluir estes passos, msmith@hotmail.com é um administrador global no diretório da Contoso.

## <a name="to-administer-resources-as-the-global-admin"></a>Administrar recursos como o administrador global
Agora vamos supor que a Teresa Andrade precisa administrar recursos de Web sites e de base de dados que estão associados a subscrição do Azure msmith@hotmail.com. Antes de ela pode fazê-lo, Michael Silva tem de concluir estes passos adicionais:

1.  Inicie sessão no [portal clássica Azure](https://manage.windowsazure.com) utilizando a conta de administrador do serviço para a subscrição Azure (neste exemplo, msmith@hotmail.com).
2.  Transferir a subscrição para o directory da Contoso: clique em **Definições** > **subscrições** > selecione a subscrição > **Editar diretório** > selecione **Contoso (Contoso.com)**. Como parte da transferência, qualquer trabalho ou escola serão removidas contas que estão coadministradores da subscrição.
3.  Adicione Teresa Andrade como administrador da cocriação da subscrição: clique em **Definições** > **administradores** > selecione a subscrição > **Adicionar** > tipo **JohnDoe@Contoso.com**.

## <a name="next-steps"></a>Próximos passos
Para mais informações sobre a relação entre subscrições e diretórios, consulte o artigo [como uma subscrição está associada a um diretório](active-directory-how-subscriptions-associated-directory.md).
