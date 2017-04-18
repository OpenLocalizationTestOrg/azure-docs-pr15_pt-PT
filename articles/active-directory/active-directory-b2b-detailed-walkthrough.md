<properties
   pageTitle="Obter instruções detalhadas sobre utilizar a pré-visualização de colaboração do Azure Active Directory B2B | Microsoft Azure"
   description="Colaboração do Azure Active Directory B2B suporta as relações de empresa cruz ao ativar parceiros de negócios aceder a suas aplicações empresariais seletivamente"
   services="active-directory"
   documentationCenter=""
   authors="viv-liu"
   manager="cliffdi"
   editor=""
   tags=""/>

<tags
   ms.service="active-directory"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="identity"
   ms.date="05/09/2016"
   ms.author="viviali"/>

# <a name="azure-ad-b2b-collaboration-preview-detailed-walkthrough"></a>Pré-visualização de colaboração do Azure AD B2B: obter instruções detalhadas sobre

Este tutorial descreve como utilizar o Azure AD B2B colaboração. Como o administrador de TI da Contoso, queremos de partilha de aplicações com funcionários de três empresas de parceiro. Nenhuma das empresas parceiro tem de ter Azure AD.

- Alice a partir do parceiro simples organograma
- O Rodrigo, a partir da média parceiro organograma, precisa de aceder a um conjunto de aplicações
- Constância, a partir do organograma parceiro complexas, precisa de aceder a um conjunto de aplicações e associação a grupos na Contoso

Depois de convites são enviadas para utilizadores do parceiro, podemos pode configurá-los no Azure AD para conceder acesso a aplicações e associação a grupos através do portal do Azure. Vamos começar adicionando Alice.

## <a name="adding-alice-to-the-contoso-directory"></a>Adicionar Alice ao diretório da Contoso
1. Crie um ficheiro. csv com os cabeçalhos, conforme apresentado, preencher apenas Alice **E-Mail**, **DisplayName**e **InviteContactUsUrl**. **DisplayName** é o nome que aparece no convite e o nome que aparece no diretório da Contoso Azure AD. **InviteContactUsUrl** é uma forma para Alice entrar em contacto da Contoso. No exemplo seguinte, InviteContactUsUrl Especifica o perfil LinkedIn da Contoso. É importante escrever as etiquetas na primeira linha do ficheiro. csv exatamente conforme especificado na [referência de formato de ficheiro CSV](active-directory-b2b-references-csv-file-format.md).  
![Ficheiro CSV de exemplo para Alice](./media/active-directory-b2b-detailed-walkthrough/AliceCSV.png)

2. No portal do Azure, adicionar um utilizador para o directory da Contoso (do Active Directory > Contoso > utilizadores > Adicionar utilizador). Na lista pendente ""Tipo de utilizador premida, selecione "Utilizadores empresas de parceiro". Carregue o ficheiro. csv. Certifique-se de que o ficheiro. csv está fechado antes de carregar.  
![Carregamento de ficheiro CSV para Alice](./media/active-directory-b2b-detailed-walkthrough/AliceUpload.png)

3. Alice agora é representada como um utilizador externo no diretório da Contoso Azure AD.  
![Alice está listada na Azure AD](./media/active-directory-b2b-detailed-walkthrough/AliceInAD.png)

4. Alice recebe a seguinte mensagem de correio electrónico.  
![E-mail de convite para Alice](./media/active-directory-b2b-detailed-walkthrough/AliceEmail.png)

5. Alice clica na hiperligação e, posteriormente, é pedida para aceitar o convite e inicie sessão com a sua credenciais de trabalho. Se não estiver no diretório Azure AD, Alice Alice é-lhe pedida para se inscrever.  
![Inscrever-se, após o convite para Alice](./media/active-directory-b2b-detailed-walkthrough/AliceSignUp.png)

6. Alice é redirecionada para o painel de acesso de aplicação, vazia até ela é concedida acesso às aplicações.  
![Painel de acesso para Alice](./media/active-directory-b2b-detailed-walkthrough/AliceAccessPanel.png)

Este procedimento permite sob a forma mais simples de colaboração B2B. Como um utilizador no diretório da Contoso Azure AD, Alice pode lhe ser concedida acesso a aplicações e grupos através do portal do Azure. Agora vamos adicionar o Rodrigo quem precisa de aceder às aplicações Moodle e Salesforce.

## <a name="adding-bob-to-the-contoso-directory-and-granting-access-to-apps"></a>Adicionar o Rodrigo ao diretório da Contoso e conceder acesso a aplicações
1. Utilizar o Windows PowerShell com o módulo Azure AD instalado para localizar a aplicação IDs de Moodle e a equipa de vendas. Os IDs de podem ser recuperados utilizando o cmdlet: `Get-MsolServicePrincipal | fl DisplayName, AppPrincipalId` isto reúne uma lista de todas as aplicações disponíveis no Contoso e os respetivos AppPrincialIds.  
![Recuperar IDs para o João](./media/active-directory-b2b-detailed-walkthrough/BobPowerShell.png)

2. Crie um ficheiro. csv que contém de o Rodrigo correio eletrónico e DisplayName em grupos, **InviteAppID**, **InviteAppResources**e InviteContactUsUrl. Preencher **InviteAppResources** com AppPrincipalIds de Moodle e Salesforce encontrado a partir do PowerShell, separado por um espaço. Preencha o **InviteAppId** com AppPrincipalId mesmo de Moodle para criar marca para o e-mail e inicie sessão em páginas com um logótipo de Moodle.  
![Ficheiro CSV de exemplo para o Rodrigo](./media/active-directory-b2b-detailed-walkthrough/BobCSV.png)

3. Carregue o ficheiro. csv através do Portal do Azure, tal como foi feito Alice. O Rodrigo é agora um utilizador externo no diretório da Contoso Azure AD.

4. O Rodrigo recebe a seguinte mensagem de correio electrónico.  
![E-mail de convite para o Rodrigo](./media/active-directory-b2b-detailed-walkthrough/BobEmail.png)

5. O Rodrigo clica na ligação e lhe for pedido para aceitar o convite. Depois de ele sessão iniciado no, ele é direcionado para o painel de acesso e pode utilizar já Moodle e Salesforce.  
![Painel de acesso para o Rodrigo](./media/active-directory-b2b-detailed-walkthrough/BobAccessPanel.png)

Vamos adicionar Beatriz em seguida, quem tem acesso a aplicações e associação a grupos no diretório da Contoso.

## <a name="adding-carol-to-the-contoso-directory-granting-access-to-apps-and-giving-group-membership"></a>Adicionar Beatriz ao diretório da Contoso, conceder acesso a aplicações e que lhe dá uma associação a grupos

1. Utilizar o Windows PowerShell com o módulo Azure AD instalado para encontrar os IDs de aplicações e os IDs de grupo dentro de Contoso.
 - Obter AppPrincipalId utilizando cmdlet `Get-MsolServicePrincipal | fl DisplayName, AppPrincipalId`, mesmo que o Rodrigo
 - Obter o ID de objecto para grupos utilizando o cmdlet `Get-MsolGroup | fl DisplayName, ObjectId`. Isto reúne uma lista de todos os grupos em Contoso e os respetivos ObjectIds. Também podem ser obtidos os IDs de grupo como o ID do objeto no separador Propriedades do grupo no portal do Azure.  
![Recuperar IDs e grupos para Beatriz](./media/active-directory-b2b-detailed-walkthrough/CarolPowerShell.png)

2. Crie o ficheiro. csv, preencher de Beatriz E-Mail, DisplayName em grupos, InviteAppID, InviteAppResources, **InviteGroupResources**e InviteContactUsUrl. **InviteGroupResources** é preenchida por ObjectIds dos grupos MyGroup1 e externos, separados por um espaço.  
![Ficheiro CSV de exemplo para a Beatriz](./media/active-directory-b2b-detailed-walkthrough/CarolCSV.png)

3. Carregue o ficheiro. csv através do portal do Azure.

4. Constância é um utilizador no diretório da Contoso e também é um membro dos grupos MyGroup1 e externos, conforme visto no portal do Azure.  
![Constância está num grupo na lista Azure AD](./media/active-directory-b2b-detailed-walkthrough/CarolGroup.png)

5. Constância recebe uma mensagem de e-mail que contém uma ligação para aceitar o convite. Depois de ela a iniciar sessão, ela é redirecionada para o painel de acesso de aplicação para ter acesso a Moodle e Salesforce.  

Há existe para adicionar utilizadores a partir de empresas de parceiro na colaboração do Azure AD B2B. Este tutorial mostrava como adicionar utilizadores Alice, Bruno e Isabel ao directório Contoso através de três ficheiros. csv separados. Este processo pode ser efetuado mais fácil por condensação os ficheiros. csv separados num único ficheiro.  
![Ficheiro CSV de exemplo para Alice, Bruno e Isabel](./media/active-directory-b2b-detailed-walkthrough/CombinedCSV.png)

## <a name="related-articles"></a>Artigos relacionados
Procure os nossos outros artigos no Azure AD B2B colaboração:

- [O que é o Azure AD B2B colaboração?](active-directory-b2b-what-is-azure-ad-b2b.md)
- [Como funciona](active-directory-b2b-how-it-works.md)
- [Referência de formato de ficheiro CSV](active-directory-b2b-references-csv-file-format.md)
- [Formato de token de utilizadores externos](active-directory-b2b-references-external-user-token-format.md)
- [Alterações de atributo de objeto do utilizador externo](active-directory-b2b-references-external-user-object-attribute-changes.md)
- [Limitações de pré-visualização atual](active-directory-b2b-current-preview-limitations.md)
- [Índice de artigo para gestão de aplicações no Azure Active Directory](active-directory-apps-index.md)
