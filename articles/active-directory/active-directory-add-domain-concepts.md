<properties
    pageTitle="Descrição geral conceptual dos nomes de domínio personalizado no Azure Active Directory | Microsoft Azure"
    description="Explica a arquitetura conceptual para utilizar nomes de domínio personalizado no Azure Active directory, incluindo a Federação do single sign-on"
    services="active-directory"
    documentationCenter=""
    authors="jeffsta"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/04/2016"
    ms.author="curtand;jeffsta"/>

# <a name="conceptual-overview-of-custom-domain-names-in-azure-active-directory"></a>Descrição geral conceptual dos nomes de domínio personalizado no Azure Active Directory

Um nome de domínio é uma parte importante o identificador para muitos recursos de diretório:-faz parte de um utilizador nome ou endereço de e-mail para um utilizador, a parte do endereço para um grupo e pode ser parte da aplicação ID URI para uma aplicação. Um recurso no Azure Active Directory (Azure AD) pode incluir um nome de domínio que já está verificado a propriedade pelo diretório que contém o recurso. Apenas um administrador global pode executar tarefas de gestão de domínio no Azure AD.

Nomes de domínio no Azure AD são globalmente exclusivos. Um nome de domínio pode ser utilizado por uma única Azure AD. Se um diretório do Azure AD tem verificado um nome de domínio, nenhum directório de Azure AD pode verificar ou utilizar esse mesmo nome de domínio.

## <a name="initial-and-custom-domain-names"></a>Nomes de domínio inicial e personalizados

Cada nome de domínio no Azure AD é um nome de domínio inicial, ou um nome de domínio personalizado.

Cada Azure AD vem com um nome de domínio inicial contoso.onmicrosoft.com o formulário. Este nome de domínio de nível terceiro, neste exemplo, "contoso.onmicrosoft.com," estabelecida quando o diretório foi criado, normalmente pelo administrador do que criou o diretório. O nome de domínio inicial para um diretório não pode ser alterado ou eliminado. O nome de domínio inicial, enquanto totalmente funcional destina-se principalmente a ser utilizado como um mecanismo bootstrapping até é verificado um nome de domínio personalizado.

Na maioria dos ambientes de produção, um diretório tem pelo menos um domínio verificado do personalizado, como "contoso.com," e é esse domínio personalizado que está visível para os utilizadores finais. Um nome de domínio personalizado é um nome de domínio que é proprietário e utilizado por essa organização, como "contoso.com," para utilizações como alojamento do seu web site. Este nome de domínio é familiar aos seus funcionários porque faz parte do nome de utilizador que utilizar para iniciar sessão na rede da empresa, ou para enviar e obter o correio eletrónico.

Antes de poder ser utilizada por Azure AD, o nome de domínio personalizado deve ser adicionado ao seu diretório e verificado.

## <a name="verified-and-unverified-domain-names"></a>Nomes de domínio verificado e não verificado

O nome de domínio inicial para um diretório implicitamente é avaliado como verificado por Azure AD. Quando um administrador adiciona um nome de domínio personalizado para um para Azure AD, é inicialmente num Estado não verificado. Azure AD não permitirá dos recursos de diretório utilizar um nome de domínio não verificado. Isto garante que só directório pode utilizar um nome de domínio específico e que a organização utiliza o nome de domínio ao proprietário desse nome de domínio.

Azure AD verifica a propriedade de um nome de domínio por está à procura de um determinado movimento no ficheiro de zona do serviço (DNS) do nome do domínio para o nome de domínio. Para verificar que é o proprietário de um nome de domínio, um administrador obtém a entrada DNS a partir do Azure AD que Azure AD irá procurar e adiciona essa entrada para o ficheiro de zona DNS para o nome de domínio. O ficheiro de zona DNS é mantido pela entidade de registo de nome de domínio para esse domínio. Os passos para verificar um domínio são apresentados no artigo para [Adicionar um domínio personalizado ao seu diretório do Azure AD](active-directory-add-domain.md).

Adicionar uma entrada de DNS para o ficheiro de zona para o nome de domínio não vai afetar outros serviços de domínio como e-mail ou web de alojamento.

## <a name="federated-and-managed-domain-names"></a>Nomes de domínio Federado e geridos

Um nome de domínio personalizado no Azure AD pode ser configurado para dar aos utilizadores um sinal de federados na experiência entre o seu do Active Directory no local e o Azure AD. Configurar um domínio para a Federação requer actualizações para recursos privilegiados no Azure AD e também para o Windows Server Active Directory. Configurar que num domínio federado têm de ser concluído a partir de ligação do Azure AD ou através do PowerShell. Federar um domínio personalizado não pode ser iniciada a partir do Azure portal clássico. [Veja este vídeo para saber mais sobre como configurar o AD FS para iniciar sessão de utilizador com a ligação do Azure AD](http://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Configuring-AD-FS-for-user-sign-in-with-Azure-AD-Connect).

Domínios que não Federado com estão por vezes denominados geridos domínios. O domínio inicial para um diretório do Azure AD implicitamente é avaliado como um domínio gerido.

## <a name="primary-domain-names"></a>Nomes de domínio principal

O nome de domínio principal para um diretório é o nome de domínio que está seleccionado previamente como o valor predefinido para a parte 'domínio' o nome de utilizador, quando um administrador cria um novo utilizador do [Azure portal clássico](https://manage.windowsazure.com/) ou outro portal como o portal de administração do Office 365. Um diretório pode ter apenas um nome de domínio principal. Um administrador pode alterar o nome de domínio principal para ser verificadas qualquer domínio personalizado que não esteja federado, ou para o domínio inicial.

## <a name="domain-names-in-azure-ad-and-other-microsoft-online-services"></a>Nomes de domínio no Azure AD e outros serviços Online da Microsoft

Um nome de domínio tem de ser validado no Azure AD antes de poder ser utilizado por outro serviço Online da Microsoft como o Exchange Online, SharePoint Online e Intune. Estes outros serviços normalmente necessitam de um administrador adicionar um ou mais entradas DNS que são específicas para o serviço.

Uma aplicação Azure web utiliza os suas próprias mecanismo para confirmar a propriedade de um domínio. Um domínio tem de ser validado para utilização com o Azure AD mesmo que tenha sido previamente verificada para utilizar por uma aplicação Azure web numa subscrição que depende essa Azure AD. Uma aplicação Azure web pode utilizar um nome de domínio tiver sido verificado num directório diferente do diretório que protege a aplicação web.

## <a name="managing-domain-names"></a>Gerir nomes de domínio

Podem ser concluídas tarefas de gestão de domínio a partir do portal clássico do Azure e a partir do PowerShell. Muitas tarefas podem ser concluídas utilizar a API do Azure AD Graph (na pré-visualização do público).

-   [Adicionar e verificar um nome de domínio personalizado](active-directory-add-domain.md)

-   [Gerir domínios no portal do Azure clássico](active-directory-add-manage-domain-names.md)

-   [Utilizar o PowerShell para gerir os nomes de domínio no Azure AD](https://msdn.microsoft.com/library/azure/e1ef403f-3347-4409-8f46-d72dafa116e0#BKMK_ManageDomains)

-   [Utilizar a API do Azure AD Graph para gerir os nomes de domínio no Azure AD](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/domains-operations)
