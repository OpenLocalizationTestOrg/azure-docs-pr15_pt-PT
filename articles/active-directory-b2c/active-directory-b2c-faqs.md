<properties
    pageTitle="Azure Active Directory B2C: Perguntas mais frequentes | Microsoft Azure"
    description="Perguntas mais frequentes sobre o Azure Active Directory B2C"
    services="active-directory-b2c"
    documentationCenter=""
    authors="swkrish"
    manager="mbaldwin"
    editor="bryanla"/>

<tags
    ms.service="active-directory-b2c"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/09/2016"
    ms.author="swkrish"/>

# <a name="azure-active-directory-b2c-faqs"></a>Azure Active Directory B2C: Perguntas mais frequentes

Esta página respostas a perguntas mais frequentes sobre o B2C Azure Active Directory (Azure AD). Manter a verificar existência de atualizações.

### <a name="can-i-use-azure-ad-b2c-features-in-my-existing-employee-based-azure-ad-tenant"></a>Pode utilizar funcionalidades do Azure AD B2C no meu inquilino existente, com base em empregado do Azure AD?

Atualmente Azure AD B2C funcionalidades não podem ser ativadas no inquilino existente do Azure AD. Recomendamos que cria um inquilino em separado para utilizar as funcionalidades do Azure AD B2C para gerir os utilizadores poderão.

### <a name="can-i-use-azure-ad-b2c-to-provide-social-login-facebook-and-google-into-office-365"></a>Posso utilizar o Azure AD B2C para fornecer o início de sessão social (Facebook e Google +) para o Office 365?

Azure AD B2C não podem ser utilizados com o Microsoft Office 365. Em geral, não podem ser utilizado para fornecer autenticação para qualquer aplicações SaaS (Office 365, Salesforce, DIATRABALHO, etc.). Fornece a gestão de identidade e acesso apenas para o consumidor destinado web e aplicações móveis e não se aplica aos cenários de empregado ou parceiro.

### <a name="what-are-local-accounts-in-azure-ad-b2c-how-are-they-different-from-work-or-school-accounts-in-azure-ad"></a>O que são contas locais no Azure AD B2C? Como são diferentes a partir de contas escolar ou profissional no Azure AD?

Num inquilino Azure AD, cada utilizador no inquilino (exceto os utilizadores com contas do Microsoft existentes) inicia com um endereço de e-mail do formulário `<xyz>@<tenant domain>`, onde `<tenant domain>` é uma dos domínios verificados no inquilino ou inicial `<...>.onmicrosoft.com` domínio. Este tipo de conta é uma conta escolar ou profissional.

Num inquilino do Azure AD B2C, a maioria das aplicações pretende que o utilizador para iniciar sessão com qualquer endereço de e-mail arbitrário (por exemplo, joe@comcast.net, bob@gmail.com, sarah@contoso.com, ou jim@live.com). Este tipo de conta é uma conta local. Hoje em dia, também suportamos nomes de utilizador arbitrário (apenas simples cadeias) como contas locais (por exemplo, João, o Rodrigo, a Susana ou Jaime). Pode escolher um destes dois tipos de conta local no serviço do Azure AD B2C.

### <a name="which-social-identity-providers-do-you-support-now-which-ones-do-you-plan-to-support-in-the-future"></a>Quais os fornecedores de identidade social suporta agora? Os que está a planear a suportar no futuro?

Atualmente, é Facebook, Google +, LinkedIn e Amazon. Vamos adicionar suporte para outros fornecedores de identidade sociais mais populares, com base no pedido de cliente.

### <a name="can-i-configure-scopes-to-gather-more-information-about-consumers-from-various-social-identity-providers"></a>Pode configurar âmbitos para recolher mais informações sobre os consumidores a partir de vários fornecedores de identidade social?

Não, mas esta funcionalidade está no nosso guia. Os âmbitos de predefinido utilizados para os nossos suportado conjunto de fornecedores de identidade sociais são:

- Facebook: correio eletrónico
- Google +: correio eletrónico
- Conta Microsoft: openid perfil de e-mail
- Amazon: perfil
- LinkedIn: r_emailaddress, r_basicprofile

### <a name="does-my-application-have-to-be-run-on-azure-for-it-work-with-azure-ad-b2c"></a>A minha aplicação tem a ser executado no Azure para é funcione com o Azure AD B2C?

Não, pode alojar o seu pedido em qualquer lugar (na nuvem ou no local). Tudo o que necessita para interagir com Azure AD B2C é a capacidade para enviar e receber pedidos de HTTP em pontos finais acessível publicamente.

### <a name="i-have-multiple-azure-ad-b2c-tenants-how-can-i-manage-them-on-the-azure-portal"></a>Tenho de ter múltiplos inquilinos do Azure AD B2C. Como posso geri-los no Portal do Azure?

Cada inquilino do Azure AD B2C tem as suas próprias pá de funcionalidades B2C no portal do Azure. Consulte o artigo [Azure AD B2C: registar a sua aplicação](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade) para saber como pode navegar para pá de funcionalidades específicas de um inquilino B2C no portal do Azure. Alternar entre o Azure AD B2C diretórios no portal do Azure não irão manter as funcionalidades de B2C pá abrir na maioria dos browsers.

### <a name="how-do-i-customize-verification-emails-the-content-and-the-from-field-sent-by-azure-ad-b2c"></a>Como personalizar e-mails de verificação (o conteúdo e o "de:" campo) que foi enviado por Azure AD B2C?

Pode utilizar a [funcionalidade de imagem corporativa da empresa](../active-directory/active-directory-add-company-branding.md) para personalizar o conteúdo dos e-mails de verificação. Especificamente, podem ser personalizados estes dois elementos do correio eletrónico:

- **Logótipo da faixa**: mostrada na parte inferior direita.
- **Cor de fundo**: apresentado na parte superior.

    ![Captura de ecrã de um e-mail de verificação personalizada](./media/active-directory-b2c-faqs/company-branded-verification-email.png)

A assinatura de correio eletrónico contém o nome do inquilino B2C que forneceu quando criou o inquilino B2C pela primeira vez. Pode alterar o nome utilizando estas instruções:

- Inicie sessão no [portal clássica Azure](https://manage.windowsazure.com/) como o administrador de subscrição.
- Navegue até ao seu inquilino B2C.
- Clique no separador **Configurar** .
- Altere o **nome** de campo na secção **Propriedades de directório** .
- Clique em **Guardar** na parte inferior da página.

Neste momento não existe nenhuma forma para alterar o "de:" campo no e-mail. Se estiver interessado nesta capacidade e no personalizar completamente corpo do e-mail de verificação, vote para a funcionalidade de [site](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/15334335-fully-customizable-verification-emails).

### <a name="how-can-i-migrate-my-existing-user-names-passwords-and-profiles-from-my-database-to-azure-ad-b2c"></a>Como posso migrar meu perfis, existente nomes de utilizador e palavras-passe da minha base de dados para Azure AD B2C?

Pode utilizar a API do Azure AD Graph para escrever a ferramenta de migração. Consulte o artigo a [API do gráfico de exemplo](active-directory-b2c-devquickstarts-graph-dotnet.md) para obter detalhes. Irá fornecemos várias opções de migração e ferramentas out-de-de-box no futuro.

### <a name="what-password-policy-is-used-for-local-accounts-in-azure-ad-b2c"></a>O que política de palavra-passe é utilizada para contas locais no Azure AD B2C?

A política de palavra-passe do Azure AD B2C para contas locais é baseada a política do Azure AD. Azure AD B2C inscrição, inscrição ou da sessão e palavra-passe repor políticas utiliza a intensidade do "forte" palavra-passe e não expirarem quaisquer palavras-passe. Leia a [política de palavra-passe do Azure AD](https://msdn.microsoft.com/library/azure/jj943764.aspx) para obter mais detalhes.

### <a name="can-i-use-azure-ad-connect-to-migrate-consumer-identities-that-are-stored-on-my-on-premises-active-directory-to-azure-ad-b2c"></a>Pode utilizar ligação do Azure AD para migrar identidades do consumidor que estão armazenadas no meu no local Active Directory para Azure AD B2C?

Não, Azure AD Connect não foi concebido para funcionar com o Azure AD B2C. Irá fornecemos várias opções de migração e ferramentas out-de-de-box no futuro.

### <a name="does-azure-ad-b2c-work-with-crm-systems-such-as-microsoft-dynamics"></a>Azure AD B2C funciona com sistemas CRM como Microsoft Dynamics?

Não é atualmente. Integrar o estes sistemas está no nosso guia.

### <a name="does-azure-ad-b2c-work-with-sharepoint-on-premises-2016-or-earlier"></a>Faz Azure AD B2C funcionam com o SharePoint no local 2016 ou anterior?

Não é atualmente. Azure AD B2C não existe suporte para tokens de SAML 1.1 portais e aplicações de comércio electrónico criada com base em necessidade do SharePoint no local. Tenha em atenção que Azure AD B2C não destina-se para o SharePoint externo partilha de parceiro cenário; consulte o artigo [Azure AD B2B](http://blogs.technet.com/b/ad/archive/2015/09/15/learn-all-about-the-azure-ad-b2b-collaboration-preview.aspx) em vez disso.

### <a name="should-i-use-azure-ad-b2c-or-b2b-to-manage-external-identities"></a>Posso utilizar Azure AD B2C ou B2B para a gestão de identidades externas?

Leia este artigo sobre [identidades externas](../active-directory/active-directory-b2b-compare-external-identities.md) para saber mais sobre como aplicar as funcionalidades adequadas para os cenários de identidades externas.

### <a name="what-reporting-and-auditing-features-does-azure-ad-b2c-provide-are-they-the-same-as-in-azure-ad-premium"></a>Que funcionalidades de auditoria e elaboração de relatórios do Azure AD B2C fornecida? Estão o mesmo, tal como no Azure AD Premium?

Não, Azure AD B2C não suporta o mesmo conjunto de relatórios que Azure AD Premium. Azure AD B2C vai ser lançar relatórios básicos e auditoria APIs mais cedo.

### <a name="can-i-localize-the-ui-of-pages-served-by-azure-ad-b2c-what-languages-are-supported"></a>Possível localizar a IU de páginas fornecido pelo Azure AD B2C? Que idiomas são suportados?

Atualmente, Azure AD B2C está optimizada para o inglês apenas. Planeamos implementar o mais cedo possível funcionalidades de localização.

### <a name="can-i-use-my-own-urls-on-my-sign-up-and-sign-in-pages-that-are-served-by-azure-ad-b2c-for-instance-can-i-change-the-url-from-loginmicrosoftonlinecom-to-logincontosocom"></a>Posso utilizar o meus próprio URLs no meu inscrição e início de sessão no páginas que devem ser servidas pelos Azure AD B2C? Por exemplo, alterar o URL do login.microsoftonline.com para login.contoso.com?

Não é atualmente. Esta funcionalidade está no nosso guia. Tenha em atenção que verificação do seu domínio no separador **domínios** do seu inquilino no portal do Azure clássico não irá fazer isto.

### <a name="how-do-i-delete-my-azure-ad-b2c-tenant"></a>Como posso eliminar meu inquilino do Azure AD B2C?

Siga estes passos para eliminar o inquilino do Azure AD B2C:

- Siga estes passos para [Navegar para o pá funcionalidades B2C](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade) no portal do Azure.
- Navegue para as **aplicações**, **fornecedores de identidade** e **todas as políticas** lâminas e eliminar todas as entradas em cada um deles.
- Agora para iniciar sessão no [portal clássica Azure](https://manage.windowsazure.com/) como o administrador de subscrição. (Isto é o mesmo trabalho ou conta escolar ou a mesma conta Microsoft que utilizou para se inscrever Azure.)
- Navegue para a extensão do Active Directory no lado esquerdo e clique em seu inquilino B2C.
- Clique no separador de **utilizadores** .
- Selecione cada utilizador ativar (excluir o utilizador tem atualmente sessão iniciada como, por exemplo, a subscrição administrador). Clique em **Eliminar** na parte inferior da página e clique em **Sim** quando lhe for pedido.
- Clique no separador **aplicações** .
- Selecione **a minha empresa ao proprietário de aplicações** no campo **Mostrar** pendente e clique na marca de verificação.
- Verá uma aplicação designado por **aplicação de extensões b2c** listados abaixo. Clique em **Eliminar** na parte inferior da página e clique em **Sim** quando lhe for pedido.
- Navegue para a extensão do Active Directory novamente e selecione o seu inquilino B2C.
- Clique em **Eliminar** na parte inferior da página. Siga as instruções no ecrã para concluir o processo.

### <a name="can-i-get-azure-ad-b2c-as-part-of-enterprise-mobility-suite"></a>Posso obter o Azure AD B2C como parte do conjunto de aplicações de mobilidade Enterprise?

Não, Azure AD B2C é uma serviço Azure repartição e não faz parte do conjunto de aplicações de mobilidade da empresa.

### <a name="how-do-i-report-issues-with-azure-ad-b2c"></a>Como relato problemas com o Azure AD B2C?

Consulte o artigo [ficheiro pedidos de Azure Active Directory B2C de suporte](active-directory-b2c-support.md).

## <a name="more-information"></a>Obter mais informações

Também poderá pretender rever atuais [limitações do serviço, restrições e restrições](active-directory-b2c-limitations.md).
