<properties
   pageTitle="Formato de ficheiro CSV para pré-visualização de colaboração do Azure Active Directory B2B | Microsoft Azure"
   description="Azure Active B2B de diretório suporta as relações de publicação em empresa ao ativar parceiros de negócios aceder a suas aplicações empresariais seletivamente"
   services="active-directory"
   documentationCenter=""
   authors="viv-liu"
   manager="cliffdi"
   editor=""
   tags=""/>

<tags
   ms.service="active-directory"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="identity"
   ms.date="05/09/2016"
   ms.author="viviali"/>

# <a name="azure-ad-b2b-collaboration-preview-csv-file-format"></a>Pré-visualização de colaboração do Azure AD B2B: formato de ficheiro CSV

A versão de pré-visualização do Azure AD B2B colaboração requer um ficheiro CSV especificar informações de utilizador do parceiro ser carregado através do portal do Azure AD. O ficheiro CSV deverá conter a etiquetas necessárias abaixo e campos opcionais conforme necessário. Modificar o ficheiro CSV de exemplo (abaixo) sem alterar a ortografia de etiquetas na primeira linha.

>[AZURE.NOTE] A primeira linha de etiquetas (tal como o E-Mail, DisplayName e assim sucessivamente) é necessária para o ficheiro CSV para possam ser analisadas com êxito. A ortografia tem de corresponder os campos especificados abaixo. Estas etiquetas identificam o conteúdo abaixo-los. Para campos opcionais que não são necessárias, as respetivas etiquetas podem ser removidas do ficheiro CSV. A coluna completa pode ser deixada em branco.

## <a name="required-fields-br"></a>Campos obrigatórios: <br/>
**E-mail:** Endereço de e-mail dos convidados utilizador. <br/>
**DisplayName:** Nome a apresentar para o utilizador convidado (normalmente, primeiro e último nome).<br/>


## <a name="optional-fields-br"></a>Campos opcionais: <br/>

**InvitationText:** Personalize o texto de mensagem de correio electrónico de convite após a aplicação de imagem corporativa e antes da ligação de reembolso.<br/>
**InvitedToApplications:** AppIDs às aplicações empresariais para atribuir aos utilizadores. AppIDs são recuperáveis no PowerShell ao contactar o suporte`Get-MsolServicePrincipal | fl DisplayName, AppPrincipalId`<br/>
**InvitedToGroups:** ObjectIDs para grupos para adicionar utilizador. ObjectIDs são recuperáveis no PowerShell ao contactar o suporte`Get-MsolGroup | fl DisplayName, ObjectId`<br/>
**InviteRedirectURL:** URL para direcionar um utilizador convidado após a aceitação do convite. Isto deve ser um URL de específico da empresa (por exemplo, [*contoso.my.salesforce.com*](http://contoso.my.salesforce.com/)). Se este campo opcional não for especificado, o utilizador convidado é direcionado para o painel de acesso de aplicação onde pode navegar para as suas aplicações empresariais que selecionou. O URL do painel de aplicação do Access é do formulário `https://account.activedirectory.windowsazure.com/applications/default.aspx?tenantId=<TenantID>`.<br/>
**CcEmailAddress**: endereço para copiar enviado convite de E-Mail. Se for utilizado o campo CcEmailAddress, este convite não pode ser utilizado para o utilizador verificado por e-mail ou criação de inquilino.<br/>
**Idioma:** Linguagem para a experiência de e-mail e de reembolso do convite, com "pt" (em inglês) como a predefinição quando não for especificado. Os outros 10 suportadas idioma códigos são:<br/>
1. de: alemão<br/>
2. es: Espanhol<br/>
3. FR: francês<br/>
4. -la: italiano<br/>
5. ja: japonês<br/>
6. ko: coreano<br/>
7. pt BR: Português (Brasil)<br/>
8. RU: russo<br/>
9. zh WEBLOG: chinês simplificado<br/>
10. zh HANT: chinês tradicional<br/>

## <a name="sample-csv-file"></a>Ficheiro CSV de exemplo
Eis um exemplo de CSV pode modificar.

>[AZURE.NOTE] Copiar e colar este bloco de notas e guardá-la com uma extensão de ficheiro '. csv'. Em seguida, edite isto no Excel. Será estruturado numa tabela com etiquetas na primeira linha.

> Adicione mais campos opcionais no Excel especificando a etiqueta e preencher a coluna abaixo da mesma.

```
Email,DisplayName,InvitationText,InviteRedirectUrl,InvitedToApplications,InvitedToGroups,CcEmailAddress,Language
wharp@contoso.com,Walter Harp,Hi Walter! I hope you’ve been doing well.,,cd3ed3de-93ee-400b-8b19-b61ef44a0f29,,you@yourcompany.com,en
jsmith@contoso.com,Jeff Smith,Hi Jeff! I hope you’ve been doing well.,,cd3ed3de-93ee-400b-8b19-b61ef44a0f29,,you@yourcompany.com,en
bsmith@contoso.com,Ben Smith,Hi Ben! I hope you’ve been doing well.,,cd3ed3de-93ee-400b-8b19-b61ef44a0f29,,you@yourcompany.com,en

```

## <a name="related-articles"></a>Artigos relacionados
Procure os nossos outros artigos sobre colaboração B2B do Azure AD

- [O que é o Azure AD B2B colaboração?](active-directory-b2b-what-is-azure-ad-b2b.md)
- [Como funciona](active-directory-b2b-how-it-works.md)
- [Obter instruções detalhadas sobre](active-directory-b2b-detailed-walkthrough.md)
- [Formato de token de utilizadores externos](active-directory-b2b-references-external-user-token-format.md)
- [Alterações de atributo de objeto do utilizador externo](active-directory-b2b-references-external-user-object-attribute-changes.md)
- [Limitações de pré-visualização atual](active-directory-b2b-current-preview-limitations.md)
- [Índice de artigo para gestão de aplicações no Azure Active Directory](active-directory-apps-index.md)
