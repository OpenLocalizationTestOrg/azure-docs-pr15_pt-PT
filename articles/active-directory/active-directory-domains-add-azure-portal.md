<properties
    pageTitle="Adicionar o seu nome de domínio personalizado para a pré-visualização do Azure Active Directory | Microsoft Azure"
    description="Como adicionar nomes de domínio da sua empresa para o Azure Active Directory e como verificar o nome de domínio."
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
    ms.date="10/17/2016"
    ms.author="curtand"/>

# <a name="add-a-custom-domain-name-to-azure-active-directory-preview"></a>Adicionar um nome de domínio personalizado à pré-visualização do Azure Active Directory

> [AZURE.SELECTOR]
- [Portal do Azure](active-directory-domains-add-azure-portal.md)
- [Azure portal clássico](active-directory-add-domain.md)

Já tem um ou mais nomes de domínio que a sua organização utiliza para efetue negócios e os seus utilizadores, inicie sessão à rede empresarial com o seu nome de domínio empresarial. Utilizar a pré-visualização do Azure Active Directory (Azure AD), pode adicionar o nome de domínio empresarial para Azure AD também. [Qual é a pré-visualização?](active-directory-preview-explainer.md) Esta opção permite-lhe atribuir nomes de utilizador no diretório estão familiarizados aos seus utilizadores, tais como ‘alice@contoso.com.’ o processo é simples:

1. Adicione o nome de domínio personalizado ao seu diretório
2. Adicionar uma entrada DNS para o nome de domínio, o registo de nome de domínio
3. Verificar o nome de domínio personalizado no Azure AD

## <a name="how-do-i-add-a-domain-name"></a>Como posso adicionar um nome de domínio?

1.  Inicie sessão no [Azure portal](https://portal.azure.com) com uma conta que é um administrador global para o diretório.

2.  Selecione **mais serviços**, introduza o **Azure Active Directory** na caixa de texto e, em seguida, selecione **Enter**.

    ![Gestão de utilizadores de abertura](./media/active-directory-domains-add-azure-portal/user-management.png)

3. No pá ***nome do diretório*** , selecione **os nomes de domínio**.

4. No pá **do *nome do directório* - nomes de domínio** , selecione o comando **Adicionar** .

  ![Selecionar o comando Adicionar](./media/active-directory-domains-add-azure-portal/add-command.png)

5. No pá **nome de domínio** , introduza o nome do seu domínio personalizado na caixa, como 'contoso.com' e, em seguida, selecione **Adicionar domínio**. Certifique-se de que incluem a com, .net ou outra extensão de nível superior.

6. No pá de ***NomeDomínio*** (ou seja, o pá que se abre com o novo nome de domínio no título), obtenha as informações de entrada DNS que irá utilizar Azure AD para verificar que a sua organização possui o nome de domínio personalizado.

  ![obter informações de entrada DNS](./media/active-directory-domains-add-azure-portal/get-dns-info.png)

Agora que adicionou o nome de domínio, Azure AD tem de verificar que a sua organização possui o nome de domínio. Azure AD pode executar esta verificação, tem de adicionar uma entrada de DNS no ficheiro de zona de DNS para o nome de domínio. Esta tarefa é executada no Web site da entidade de registo de nome de domínio para o nome de domínio.

## <a name="add-the-dns-entry-at-the-domain-name-registrar-for-the-domain"></a>Adicionar a entrada DNS na entidade de registo de nome de domínio para o domínio

É o próximo passo para utilizar o seu nome de domínio personalizado com o Azure AD atualizar o ficheiro de zona DNS do domínio. Permite que Azure AD verificar que a sua organização possui o nome de domínio personalizado.

1.  Inicie sessão na entidade de registo de nome de domínio para o domínio. Se não tiver acesso a atualizar a entrada DNS, peça a pessoa ou equipa quem tem acesso a este para concluir o passo 2 e a permitir-lhe saber quando estiver concluída.

2.  Atualize o ficheiro de zona DNS para o domínio ao adicionar a entrada DNS fornecida pela Azure AD. Esta entrada DNS permite Azure AD confirmar que é o proprietário do domínio. A entrada DNS não altere quaisquer comportamentos, como o encaminhamento de correio ou o alojamento na web.

Para obter ajuda com este tipo de adicionar a entrada DNS, leia as [instruções para adicionar uma entrada de DNS em entidades de registo DNS populares](https://support.office.com/article/Create-DNS-records-for-Office-365-when-you-manage-your-DNS-records-b0f3fdca-8a80-4e8e-9ef3-61e8a2a9ab23/)

## <a name="verify-the-domain-name-with-azure-ad"></a>Verifique se o nome de domínio com o Azure AD

Assim que tiver adicionado a entrada DNS, está pronto para verificar o nome de domínio com o Azure AD.

Um nome de domínio pode ser verificado apenas depois de tem propagadas os registos DNS. Nesta propagação frequentemente demora apenas segundos, mas, por vezes, pode demorar algum espaço de uma ou mais. Se a verificação não funcionar pela primeira vez, tente novamente mais tarde.

1.  Inicie sessão no [Azure portal](https://portal.azure.com) com uma conta que é um administrador global para o diretório.

2.  Selecione **Procurar**, introduza a gestão de utilizadores na caixa de texto e, em seguida, selecione **introduzir**.

    ![Gestão de utilizadores de abertura](./media/active-directory-domains-add-azure-portal/user-management.png)

3. No pá **Gestão de utilizadores - nomes de domínio** , selecione o nome de domínio não verificado que pretende verificar.

4. No pá de ***NomeDomínio*** (ou seja, o pá que se abre com o novo nome de domínio no título), selecione **Verificar** para concluir a verificação.

Agora pode [atribuir nomes de utilizador que incluam o seu nome de domínio personalizado](active-directory-users-create-azure-portal.md).

## <a name="troubleshooting"></a>Resolução de problemas

Se não consegue verificar o nome de domínio personalizado, experimente o seguinte. Vamos começar com mais comuns e trabalhar para baixo até menos comuns.

1.  **Aguarde uma hora**. Precisam de registos DNS a serem propagadas antes de Azure AD pode verificar o domínio. Isto pode levar uma hora ou mais.

2.  **Certifique-se o registo DNS foi introduzido, e que o mesmo está correto**. Conclua este passo no Web site para a entidade de registo de nome de domínio para o domínio. Azure AD não consegue verificar o nome de domínio, se a entrada DNS não estiver presente no ficheiro de zona de DNS, ou se não for uma correspondência exata com a entrada DNS desse Azure AD fornecido. Se não possui acesso ao atualizar os registos DNS do domínio, o registo de nome de domínio, partilhar a entrada DNS com a pessoa ou equipa na sua organização que tenha acesso a este e peça-lhes para adicionar a entrada DNS.

3.  **Eliminar o nome de domínio a partir do diretório outra no Azure AD**. Um nome de domínio pode ser verificado apenas num único diretório. Se um nome de domínio anteriormente foi verificado noutro directório,-deve ser eliminada aí antes de que podem ser verificada no diretório do seu novo. Para obter informações sobre como eliminar nomes de domínio, leia [Gerir nomes de domínio personalizado](active-directory-domains-manage-azure-portal.md).    

## <a name="add-more-custom-domain-names"></a>Adicionar mais nomes de domínio personalizado

Se a sua organização utiliza vários nomes de domínio personalizado, como 'contoso.com' e 'contosobank.com', pode adicioná-los até um máximo de 900 nomes de domínio. Utilize os mesmos passos neste artigo para adicionar cada um dos nomes de domínio.

## <a name="next-steps"></a>Próximos passos

[Gerir nomes de domínio personalizado](active-directory-domains-manage-azure-portal.md)
