<properties
    pageTitle="Como obter um inquilino do Azure AD | Microsoft Azure"
    description="Como obter um inquilino do Azure Active Directory para registar e criação de aplicações."
    services="active-directory"
    documentationCenter=""
    authors="dstrockis"
    manager="terrylan"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="09/28/2015"
    ms.author="dastrock"/>

# <a name="how-to-get-an-azure-active-directory-tenant"></a>Como obter um inquilino do Azure Active Directory

No Azure Active Directory (Azure AD), um [inquilino](https://msdn.microsoft.com/library/azure/jj573650.aspx#BKMK_WhatIsAnAzureADTenant) é representante de uma organização.  É uma instância dedicada do serviço do Azure AD que uma organização recebe e é o proprietário quando-lo se inscreve para um serviço de nuvem da Microsoft como Azure, Microsoft Intune ou Office 365.  Cada inquilino do Azure AD é distinta e separada a partir de outras inquilinos do Azure AD.  

Um inquilino aloja aos utilizadores numa empresa e as informações sobre os mesmos - os respetivos palavras-passe, dados de perfil de utilizador, permissões e assim sucessivamente.  Também contém grupos, aplicações e outras informações sobre a sua segurança e uma organização.

Para permitir que os utilizadores do Azure AD iniciar sessão na sua aplicação, tem de registar a sua aplicação num inquilino do seu próprio.  Publicação de uma aplicação num inquilino Azure AD é **realmente gratuita**.  Na verdade, irão criar os programadores a maior parte dos vários inquilinos e aplicações para o pioneira, desenvolvimento, teste e efeitos de teste.  As organizações que inscrever-se no e consumam a aplicação, opcionalmente, podem optar por adquirir licenças se pretendem tirar partido das funcionalidades avançadas de diretório.

Por isso, como aceda sobre como obter um inquilino do Azure AD?  O processo poderá ser um pouco se diferentes:

- [Tenho uma subscrição existente do Office 365](#use-an-existing-office-365-subscription)
- [Tenho uma subscrição existente do Azure associada a uma Account Microsoft](#use-an-msa-azure-subscription)
- [Tenho uma subscrição existente do Azure associada a uma conta institucional](#use-an-organizational-azure-subscription)
- [Tem nenhuma das respostas anteriores e pretende começar de raiz](#start-from-scratch)

## <a name="use-an-existing-office-365-subscription"></a>Utilizar uma subscrição existente do Office 365
Se tiver uma subscrição existente do Office 365, já tem um inquilino do Azure AD! Pode iniciar sessão no [portal do Azure](https://portal.azure.com) com a sua conta do Office 365 e começar a utilizar o Azure AD.

## <a name="use-an-msa-azure-subscription"></a>Utilizar uma subscrição do MSA Azure
Se já tiver feito para uma subscrição do Azure com a sua Account Microsoft individuais, já tem um inquilino!  Quando inicia sessão no [Portal do Azure](https://portal.azure.com), será automaticamente ser iniciou sessão ao seu inquilino predefinido. É gratuitas utilizar este inquilino conforme pretender - mas pretende criar uma conta de administrador organizacional.

Para fazê-lo, siga estes passos.  Em alternativa, pode pretender criar um novo inquilino e criar um administrador nesse inquilino seguir um processo semelhante.

1.  Inicie sessão no [Portal do Azure](https://portal.azure.com) com a sua conta individual
2.  Navegue para a secção "Azure Active Directory" do portal (encontrado na barra de navegação esquerda, em **Mais serviços**)
3.  Deve automaticamente ter sessão iniciada no "Diretório predefinido", se não pode mudar directórios ao clicar no seu nome de conta no canto superior direito.
4.  A partir da secção de **Tarefas rápidas** , selecione **Adicionar um utilizador**.
5.  No formulário de utilizador de adicionar, forneça os seguintes detalhes:

    - Nome: (escolher um valor adequado)
    - Nome de utilizador: (selecione um nome de utilizador para este administrador)
    - Perfil: (preencher os valores adequados para o nome próprio, Apelido Nome, cargo e departamento)
    - Função: Administrador Global

6.  Quando tiver concluído o formulário de utilizador adicionar e, recebe a palavra-passe temporária para o novo utilizador administrativo, certifique-se de que esta palavra-passe de registo que precisar de iniciar sessão com este novo utilizador para poder alterar a palavra-passe. Também pode enviar a palavra-passe diretamente para o utilizador, utilizar um e-mail alternativo.
7.  Clique em **Criar** para criar o novo utilizador.
8.  Para alterar a palavra-passe temporária, inicie sessão no [https://login.microsoftonline.com](https://login.microsoftonline.com) com esta nova conta de utilizador e alterar a palavra-passe quando solicitado.


## <a name="use-an-organizational-azure-subscription"></a>Utilizar uma subscrição do Azure organizacional
Se já tiver feito para uma subscrição do Azure com a sua conta organizacional, já tem um inquilino!  No [Portal do Azure](https://portal.azure.com), deverá encontrar um inquilino quando navegar para "Mais serviços" e "Azure Active Directory."  Está livre utilizar este inquilino conforme pretender. 


## <a name="start-from-scratch"></a>Comece do zero
Se todas as operações anteriores for qualquer para si, não se preocupe.  Visite simplesmente [https://account.windowsazure.com/organization](https://account.windowsazure.com/organization) para se inscrever Azure com uma organização nova.  Depois de concluir o processo, terá o seu próprio inquilino do Azure AD com o nome de domínio que escolheu durante a inscrição para cima.  No [Portal do Azure](https://portal.azure.com), pode encontrar o seu inquilino ao navegar para "Azure Active Directory" na navegação no lado esquerdo

Como parte do processo de inscrição no Azure, será exigido para fornecer detalhes do cartão de crédito.  Pode continuar com confiança - será não cobrado para publicação de aplicações no Azure AD ou criar novos inquilinos.
