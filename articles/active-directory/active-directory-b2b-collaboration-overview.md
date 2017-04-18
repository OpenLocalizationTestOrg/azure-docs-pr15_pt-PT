<properties
   pageTitle="Colaboração do Azure Active Directory B2B | Microsoft Azure"
   description="Colaboração do Azure Active Directory B2B permite parceiros de negócios para aceder ao seu aplicações empresariais, com cada um dos seus utilizadores representados por uma única Azure AD de conta"
   services="active-directory"
   documentationCenter=""
   authors="curtand"
   manager="femila"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="08/23/2016"
   ms.author="curtand"/>

# <a name="azure-active-directory-b2b-collaboration"></a>Azure Active Directory B2B colaboração

Colaboração do Azure Active Directory (Azure AD) B2B permite-lhe ativar o acesso às suas aplicações da empresa a partir do parceiro com gestão de identidades. Pode criar relações de publicação em empresa ao convidar e autorização de utilizadores de empresas de parceiro para aceder aos seus recursos. Complexidade é reduzida porque cada empresa federates uma vez com o Azure Active Directory e cada utilizador é representado por uma única conta Azure AD. Segurança é aumentada se parceiros de negócios a gestão das suas contas no Azure AD porque o access foi revogado quando os utilizadores de parceiro são terminados das suas organizações e acesso acidental através da associação internas directórios é impedido. Para parceiros de negócios que já não têm o Azure AD, colaboração B2B tem uma experiência de inscrição simplificada para fornecer contas do Azure AD para parceiros de negócios.

-   Parceiros de negócios utilizam os seus próprios início de sessão no credenciais, o que liberta de gestão de um diretório de parceiros externos e de precisar de remover o acesso quando os utilizadores deixarem a organização do parceiro.

-   Gerir o acesso às suas aplicações, independentemente do ciclo de vida do seu parceiro empresas conta. Isto significa que, por exemplo, pode revogar o acesso sem ter de perguntar o departamento de TI do seu parceiro empresas fazer nada.

## <a name="capabilities"></a>Capacidades de

Colaboração B2B simplifica a gestão e melhora a segurança do access de parceiro para recursos da empresa, incluindo SaaS aplicações, tais como o Office 365, Salesforce, dos serviços do Azure e cada mobile, na nuvem e aplicação deverá ter em consideração em afirmações no local. B2B colaboração permite parceiros gerem os seus próprios contas e empresas podem aplicar políticas de segurança para o access de parceiro.

Azure B2B diretório ativo é fácil de configurar com a colaboração simplificadas inscrição para parceiros de todos os tamanhos mesmo que não tenham os seus próprios Azure Active Directory através de um processo verificado por e-mail. Também é fácil manter com sem directórios externos ou por configurações de Federação do parceiro.

## <a name="b2b-collaboration-process"></a>Processo de colaboração B2B

1. Colaboração do Azure AD B2B permite ao administrador da empresa convidar e autorizar um conjunto de utilizadores externos por ao carregar um ficheiro de valores separados por vírgulas (CSV) das linhas não mais de 2000 ao portal de colaboração B2B.

  ![Caixa de diálogo de carregamento de ficheiro CSV](./media/active-directory-b2b-collaboration-overview/upload-csv.png)

2. O portal irá enviar convites por e-mail a estes utilizadores externos.

3. O utilizador convidado irá iniciar sessão para uma conta existente do trabalho com o Microsoft (gerido no Azure AD) ou obter uma nova conta do trabalho no Azure AD.

4. Assim que iniciar sessão, o utilizador será redirecionado para a aplicação que foi partilhada com elas.

Convites para endereços de e-mail do consumidor (por exemplo, Gmail ou [*comcast.net*](http://comcast.net/)) não são atualmente suportadas.

Para obter mais informações sobre como funciona a colaboração B2B, veja [Este vídeo](http://aka.ms/aadshowb2b).

## <a name="next-steps"></a>Próximos passos
Procure os nossos outros artigos sobre colaboração Azure AD B2B.

- [O que é o Azure AD B2B colaboração?](active-directory-b2b-what-is-azure-ad-b2b.md)
- [Como funciona](active-directory-b2b-how-it-works.md)
- [Obter instruções detalhadas sobre](active-directory-b2b-detailed-walkthrough.md)
- [Referência de formato de ficheiro CSV](active-directory-b2b-references-csv-file-format.md)
- [Formato de token de utilizadores externos](active-directory-b2b-references-external-user-token-format.md)
- [Alterações de atributo de objeto do utilizador externo](active-directory-b2b-references-external-user-object-attribute-changes.md)
- [Limitações de pré-visualização atual](active-directory-b2b-current-preview-limitations.md)
- [Índice de artigo para gestão de aplicações no Azure Active Directory](active-directory-apps-index.md)
