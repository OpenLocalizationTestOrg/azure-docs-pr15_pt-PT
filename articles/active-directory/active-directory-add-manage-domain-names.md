<properties
    pageTitle="Gerir nomes de domínio personalizado no Azure Active Directory | Microsoft Azure"
    description="Conceitos de gestão e procedimentos para gerir um domínio personalizado no Azure Active Directory"
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

# <a name="managing-custom-domain-names-in-your-azure-active-directory"></a>Gerir nomes de domínio personalizado no Azure Active Directory

Um nome de domínio é uma parte importante o identificador para muitos recursos de diretório:-faz parte de um utilizador nome ou endereço de e-mail para um utilizador, a parte do endereço para um grupo e pode ser parte da aplicação ID URI para uma aplicação. Um recurso no Azure Active Directory (Azure AD) pode incluir um nome de domínio que já está verificado a propriedade pelo diretório que contém o recurso. Apenas um administrador global pode executar tarefas de gestão de domínio no Azure AD.

## <a name="set-the-primary-domain-name-for-your-azure-ad-directory"></a>Definir o nome de domínio principal para o seu diretório do Azure AD

Quando é criado o seu diretório, o nome de domínio inicial, tal como 'contoso.onmicrosoft.com', também é o nome de domínio principal para o seu diretório. O domínio principal é o nome de domínio predefinido para um novo utilizador quando cria um novo utilizador do [Azure portal clássico](https://manage.windowsazure.com/)ou outros portais como o portal de administração do Office 365. Isto simplifica o processo de administrador para criar novos utilizadores no portal.

Para alterar o nome de domínio principal para o seu diretório:

1.  Inicie sessão no [portal clássica Azure](https://manage.windowsazure.com/) com uma conta de utilizador que é um administrador global do diretório da sua Azure AD.

2.  Selecione **Do Active Directory** na barra de navegação à esquerda.

3.  Abra o seu diretório.

4.  Selecione o separador de **domínios** .

5.  Selecione o botão **Alterar principal** na barra de comandos.

6.  Selecione o domínio que pretende que o novo domínio principal para o seu diretório.

Pode alterar o nome de domínio principal para o seu diretório ser de qualquer domínio personalizado verificado que não é Federado com. Alterar o domínio principal para o seu diretório não será alterada os nomes de utilizador para qualquer utilizadores existentes.

## <a name="add-custom-domain-names-to-your-azure-ad"></a>Adicionar nomes de domínio personalizado ao seu Azure AD

Pode adicionar até 900 nomes de domínio personalizado para cada directório Azure AD. O processo para [Adicionar um nome de domínio personalizado adicionais](active-directory-add-domain.md) é o mesmo para o primeiro nome de domínio personalizado.

## <a name="add-subdomains-of-a-custom-domain"></a>Adicionar subdomínios de um domínio personalizado

Se pretender adicionar um nome de domínio de terceiro nível tal como 'europe.contoso.com' ao seu diretório, primeiro deve adicionar e verificar o domínio de segundo nível, como contoso.com. O subdomínio será verificado automaticamente pelo Azure AD. Para ver que foi verificado o subdomínio que acabou de adicionar, atualize a página no browser que lista os domínios no seu diretório.

## <a name="what-to-do-if-you-change-the-dns-registrar-for-your-custom-domain-name"></a>O que fazer se alterar a entidade de registo de DNS para o seu nome de domínio personalizado

Se alterar a entidade de registo de DNS para o seu nome de domínio personalizado, pode continuar a utilizar o seu nome de domínio personalizado com o Azure AD propriamente dito sem interrupções e sem tarefas de configuração adicionais. Se utilizar o seu nome de domínio personalizado com o Office 365, Intune ou outros serviços que dependem de nomes de domínio personalizado no Azure AD, consulte a documentação para desses serviços.

## <a name="delete-a-custom-domain-name"></a>Eliminar um nome de domínio personalizado

Pode eliminar um nome de domínio personalizado a partir do seu Azure AD Se a organização já não utilizar esse nome de domínio ou se precisar de utilizar esse nome de domínio com outro Azure AD.

Para eliminar um nome de domínio personalizado, primeiro tem de se certificar de que não existem recursos no seu diretório dependem o nome de domínio. Não pode eliminar um nome de domínio a partir do diretório da sua se:

-   Qualquer utilizador tem um nome de utilizador, o endereço de e-mail ou o endereço proxy que inclua o nome de domínio.

-   Qualquer grupo tem um endereço de e-mail ou o endereço proxy que inclua o nome de domínio.

-   Qualquer aplicação no seu Azure AD tem uma aplicação do ID URI que inclua o nome de domínio.

Tem de alterar ou eliminar qualquer desse recurso no seu diretório do Azure AD antes de poder eliminar o nome de domínio personalizado.

## <a name="use-powershell-or-graph-api-to-manage-domain-names"></a>Utilizar o PowerShell ou Graph API para gerir os nomes de domínio

Também podem ser concluídas a maioria das tarefas de gestão de nomes de domínio no Azure Active Directory através do Microsoft PowerShell ou através de programação utilizar API do Azure AD Graph (na pré-visualização do público).

-   [Utilizar o PowerShell para gerir os nomes de domínio no Azure AD](https://msdn.microsoft.com/library/azure/e1ef403f-3347-4409-8f46-d72dafa116e0#BKMK_ManageDomains)

-   [Utilizar a Graph API para gerir nomes de domínio no Azure AD](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/domains-operations)

## <a name="next-steps"></a>Próximos passos

-   [Saiba mais sobre nomes de domínio no Azure AD](active-directory-add-domain-concepts.md)

-   [Gerir nomes de domínio personalizado](active-directory-add-manage-domain-names.md)
