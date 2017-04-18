<properties
    pageTitle="Adicionar o seu nome de domínio personalizado e definir o federados início de sessão para o Azure Active Directory | Microsoft Azure"
    description="Federados como adicionar nomes de domínio da sua empresa para o Azure Active Directory e como configurar o início de sessão entre o Azure Active Directory e a sua solução de Federação no local."
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
    ms.topic="get-started-article"
    ms.date="10/04/2016"
    ms.author="curtand;jeffsta"/>

# <a name="add-your-custom-domain-name-to-azure-active-directory"></a>Adicionar o seu nome de domínio personalizado para o Azure Active Directory

Pode configurar um nome de domínio personalizado, tal como 'contoso.com,' para que os utilizadores com contoso.com podem ter uma federados única início de sessão experiência a partir da rede da sua empresa. Se já tiver serviços de Federação do Active Directory (AD FS) ou um servidor de Federação diferentes ser executada numa rede da sua empresa, pode configurar Azure AD para utilizar o seu nome de domínio personalizado utilizando a ferramenta de ligação do Azure AD. Pode também utilizar a ligação do Azure AD para implementar um novo ambiente do AD FS e configurar que para federados single sign-on para Azure AD.

Se não possui e não tenciona implementar o AD FS ou outro servidor de Federação, siga estas instruções: [Adicionar um nome de domínio personalizado para o Azure Active Directory](active-directory-add-domain.md).

## <a name="add-a-custom-domain-name-to-your-directory"></a>Adicionar um nome de domínio personalizado ao seu diretório

1. Inicie sessão no [portal clássica Azure](https://manage.windowsazure.com/) com uma conta de utilizador que é um administrador global do diretório da sua Azure AD.

2. No **Active Directory**, abra o seu diretório e selecione no separador **Domains** .

3. Na barra de comando, selecione **Adicionar**e, em seguida, introduza o nome do seu domínio personalizado, como 'contoso.com'. Certifique-se de que incluem a com, .net ou outra extensão de nível superior.

4. Selecione a caixa de verificação **Planear a configurar este domínio para o único início de sessão com o meu Active Directory local** .

5. Selecione **Adicionar**.

Execute a ferramenta de ligação do Azure AD para obter a entrada DNS que irá utilizar Azure AD para verificar o domínio. Irá ver a entrada DNS no passo no Assistente de **Domínio do Azure AD** . Pode ver o quê esse passo do assistente aspeto [nestas instruções](active-directory-aadconnect-get-started-custom.md#verify-the-azure-ad-domain-selected-for-federation). Se não tiver a ferramenta de ligação do Azure AD, pode [transferi-lo aqui](http://go.microsoft.com/fwlink/?LinkId=615771).

## <a name="add-the-dns-entry-at-the-domain-name-registrar-for-the-domain"></a>Adicionar a entrada DNS na entidade de registo de nome de domínio para o domínio

É o próximo passo para utilizar o seu nome de domínio personalizado com o Azure AD atualizar o ficheiro de zona DNS do domínio. Permite que Azure AD verificar que a sua organização possui o nome de domínio personalizado.

1. Inicie sessão no Web site da entidade de registo de nome de domínio para o seu nome de domínio. Se não tiver o access para fazer isto, peça a pessoa ou equipa na sua organização que tenha acesso a este para concluir o passo 2 e a permitir-lhe saber quando estiver concluída.

2. Atualize o ficheiro de zona DNS para o domínio ao adicionar a entrada DNS fornecida pela Azure AD. Esta entrada DNS permite Azure AD confirmar que é o proprietário do domínio. A entrada DNS não altere quaisquer comportamentos, como o encaminhamento de correio ou o alojamento na web.

Para obter ajuda com este passo, leia as [instruções para adicionar uma entrada de DNS em entidades de registo DNS populares](https://support.office.com/article/Create-DNS-records-for-Office-365-when-you-manage-your-DNS-records-b0f3fdca-8a80-4e8e-9ef3-61e8a2a9ab23/)

## <a name="verify-the-domain-name-with-azure-ad"></a>Verifique se o nome de domínio com o Azure AD

Assim que tiver adicionado a entrada DNS, está pronto para verificar o nome de domínio com o Azure AD.

Para verificar o domínio, selecione **seguinte** no **Domínio do Azure AD** passo do Assistente de ligação do Azure AD. Azure AD irá procurar a entrada DNS no ficheiro de zona de DNS do domínio. Azure AD verificar apenas o nome de domínio depois de tem propagadas os registos DNS. Muitas vezes a propagação demora apenas segundos, mas, por vezes, pode demorar algum espaço de uma ou mais. Se a verificação não funcionar pela primeira vez, tente novamente mais tarde.

Em seguida, avance para os restantes passos no Assistente de ligação do Azure AD. Isto irá sincronizar os utilizadores a partir do seu servidor AD do Windows Azure AD. Utilizadores sincronizados no domínio de que configurou para a Federação poderão obter uma federados única início de sessão experiência do rede da sua empresa para o Azure AD.

## <a name="troubleshooting"></a>Resolução de problemas

Se não consegue verificar o nome de domínio personalizado, experimente o seguinte. Vamos começar com mais comuns e trabalhar para baixo até menos comuns.

1.  **Aguarde uma hora**. Precisam de registos DNS a serem propagadas antes de Azure AD pode verificar o domínio. Isto pode levar uma hora ou mais.

2.  **Certifique-se o registo DNS foi introduzido, e que o mesmo está correto**. Conclua este passo no Web site para a entidade de registo de nome de domínio para o domínio. Azure AD não consegue verificar o nome de domínio, se a entrada DNS não estiver presente no ficheiro de zona de DNS, ou se não for uma correspondência exata com a entrada DNS desse Azure AD fornecido. Se não possui acesso ao atualizar os registos DNS do domínio, o registo de nome de domínio, partilhar a entrada DNS com a pessoa ou equipa na sua organização que tenha acesso a este e peça-lhes para adicionar a entrada DNS.

3.  **Eliminar o nome de domínio a partir do diretório outra no Azure AD**. Um nome de domínio pode ser verificado apenas num único diretório. Se um nome de domínio anteriormente foi verificado noutro directório,-deve ser eliminada aí antes de que podem ser verificada no diretório do seu novo. Para obter informações sobre como eliminar nomes de domínio, leia [Gerir nomes de domínio personalizado](active-directory-add-manage-domain-names.md).

## <a name="add-more-custom-domain-names"></a>Adicionar mais nomes de domínio personalizado

Se a sua organização utiliza vários nomes de domínio personalizado, como 'contoso.com' e 'contosobank.com', pode adicioná-los até um máximo de 900 nomes de domínio. Utilize os mesmos passos neste artigo para adicionar cada um dos nomes de domínio.

## <a name="next-steps"></a>Próximos passos

-   [Gerir nomes de domínio personalizado](active-directory-add-manage-domain-names.md)
-   [Saiba mais sobre os conceitos de gestão de domínio no Azure AD](active-directory-add-domain-concepts.md)
-   [Mostrar a que sua empresa da imagem corporativa quando os utilizadores sessão](active-directory-add-company-branding.md)
-   [Utilizar o PowerShell para gerir os nomes de domínio no Azure AD](https://msdn.microsoft.com/library/azure/e1ef403f-3347-4409-8f46-d72dafa116e0#BKMK_ManageDomains)
