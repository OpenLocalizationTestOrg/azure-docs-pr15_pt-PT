<properties
   pageTitle="Como exigir autenticação multifator | Microsoft Azure"
   description="Saiba como exigir autenticação multifator (MFA) para identidades privilegiadas com a extensão do Azure Active Directory privilegiados identidade Management."
   services="active-directory"
   documentationCenter=""
   authors="kgremban"
   manager="femila"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="07/01/2016"
   ms.author="kgremban"/>

# <a name="how-to-require-mfa-in-azure-ad-privileged-identity-management"></a>Como requerer MFA na gestão de identidades privilegiados do Azure AD

Recomendamos que requerem autenticação multifator (MFA) para todos os seus administradores. Este procedimento reduz o risco de um ataque devido a uma palavra-passe comprometida.

Pode exigir que os utilizadores concluir um desafio MFA quando iniciar sessão. A mensagem de blogue [MFA para Office 365 e MFA para Azure](https://blogs.technet.microsoft.com/ad/2014/02/11/mfa-for-office-365-and-mfa-for-azure/) compara que está incluído no Office e o Azure subscrições, com as funcionalidades incluídas na oferta de autenticação do Microsoft Azure Multifator.

Também pode exigir que os utilizadores concluir um desafio MFA quando ativar a uma função no Azure AD PIM. Desta forma, se o utilizador não concluir um desafio MFA quando estes tiver sessão iniciada, será pedido para fazê-lo por PIM.

## <a name="requiring-mfa-in-azure-ad-privileged-identity-management"></a>Exigir MFA na gestão de identidades do Azure AD privilegiados

Quando gerir identidades no PIM como um administrador de função privilegiados, poderá ver alertas que recomendamos MFA para contas privilegiadas. Clique no alerta de segurança no dashboard de PIM e será aberto um novo pá com uma lista de contas de administrador que deve requerem MFA.  Pode requerer MFA ao selecionar várias funções e, em seguida, clicar no botão **corrigir** ou pode clicar nas reticências junto funções individuais e, em seguida, clique no botão **corrigir** .

> [AZURE.IMPORTANT] Botão direito do rato agora, Azure MFA apenas funciona com o trabalho ou escola contas, não contas do Microsoft (normalmente, uma conta pessoal que é utilizado para iniciar sessão no serviços da Microsoft como o Skype, Xbox, Outlook.com, etc.). Por esta razão, qualquer pessoa com uma conta Microsoft não pode ser um administrador elegível, uma vez que estes não é possível utilizar MFA para ativar as suas funções. Se precisarem destes utilizadores continuar a gerir das cargas de trabalho com uma conta Microsoft, elevá-los para os administradores permanentes por agora.

Para além disso, pode alterar o requisito de MFA para uma função específica ao clicar no mesmo na secção funções do PIM dashboard. Em seguida, clique em **Definições** na pá a função e, em seguida, selecionando o **Ativar** em autenticação multifator.

## <a name="how-azure-ad-pim-validates-mfa"></a>Modo como o Azure AD PIM valida MFA

Existem duas opções para validar MFA quando um utilizador activa uma função.

A opção mais simples é depender Azure MFA para utilizadores que estão a ativar uma função privilegiada. Para executar esta tarefa, selecione pela primeira vez que esses utilizadores licenciados, se for necessário e tem registado para Azure MFA. Obter mais informações sobre como fazer isto são na [Introdução ao Azure a autenticação Multifator na nuvem](../multi-factor-authentication/multi-factor-authentication-get-started-cloud.md). É recomendado, mas não é necessário, que configure Azure AD para impor MFA para estes utilizadores quando iniciar sessão. Isto ocorre porque as verificações MFA vai ser efetuadas por Azure AD PIM própria.

Em alternativa, se os utilizadores autenticados no local pode fazer com que o seu fornecedor de identidade se responsabiliza por MFA. Por exemplo, se tiver configurado o AD dos serviços de Federação para exigir autenticação baseada em smart card antes de aceder ao Azure AD, [recursos de nuvem proteger com autenticação Multifator de Azure e o AD FS](../multi-factor-authentication/multi-factor-authentication-get-started-adfs-cloud.md) inclui instruções para configurar o AD FS para enviar afirmações para Azure AD. Quando um utilizador tenta ativar uma função, Azure AD PIM aceitará que MFA já foi validado para o utilizador assim que recebe as afirmações adequadas.


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Próximos passos
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]
