<properties
    pageTitle="Como funciona: gestão de palavras-passe do Azure AD | Microsoft Azure"
    description="Saiba mais sobre as diferentes componentes da gestão de palavra-passe do Azure AD, incluindo onde os utilizadores registar, repor e alterarem a palavra-passe e onde os administradores configurar, comunicar e ativar a gestão das palavras-passe no local do Active Directory."
    services="active-directory"
    documentationCenter=""
    authors="asteen"
    manager="femila"
    editor="curtand"/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/12/2016"
    ms.author="asteen"/>

# <a name="how-password-management-works"></a>Como funciona a gestão de palavra-passe

> [AZURE.IMPORTANT] **Está aqui porque está a ter problemas ao iniciar sessão?** Se Sim, [Eis como pode alterar e repor a sua própria palavra-passe](active-directory-passwords-update-your-own-password.md).

Gestão de palavras-passe no Azure Active Directory é composto por vários componentes lógicos que são descritos abaixo.  Clique em cada ligação para saber mais sobre esse componente.

- [**Portal de configuração de gestão de palavra-passe**](#password-management-configuration-portal) – os administradores podem controlar facetas diferentes da forma como palavras-passe são geridas na suas inquilinos ao navegar para o separador de configurar os respetivos diretório no [Portal de gestão do Azure](https://manage.windowsazure.com).
- [**Portal de registo de utilizador**](#user-registration-portal) – os utilizadores personalizada podem registar para repor através deste web portal de palavra-passe.
- [**Portal de reposição de palavra-passe de utilizador**](#user-password-reset-portal) – os utilizadores podem repor os seus próprios utilizar um número de desafios diferentes de acordo com a política de reposição de palavra-passe controlado pelo administrador de palavras-passe
- [**Portal de alterar palavra-passe de utilizador**](#user-password-change-portal) – os utilizadores podem alterar os seus próprios palavras-passe em qualquer altura ao introduzir a sua palavra-passe antiga e selecionar uma nova palavra-passe utilizando este portal da web
- [**Relatórios de gestão de palavra-passe**](#password-management-reports) – os administradores podem ver e analisar atividade de reposição e registo de palavra-passe no seu inquilino ao navegar para a secção "Relatórios de atividade" "Relatórios" separador respectivo directório no [Portal de gestão do Azure](https://manage.windowsazure.com)
- [**Palavra-passe de escrita não consolidada componente de ligação do Azure AD**](#password-writeback-component-of-azure-ad-connect) – os administradores, opcionalmente, podem ativar a funcionalidade de palavra-passe de escrita não consolidada quando instalar a ligação do Azure AD para permitir a gestão de federado ou palavra-passe sincronizada palavras-passe de utilizador a partir da nuvem.

## <a name="password-management-configuration-portal"></a>Portal de configuração de gestão de palavra-passe
Pode configurar as políticas de gestão de palavra-passe de um diretório específico utilizando o [Portal de gestão do Azure](https://manage.windowsazure.com) ao navegar para a secção de **Política de reposição de palavra-passe de utilizador** no separador de **Configurar** o directório.  A partir desta página de configuração, pode controlar muitos aspetos da forma como as palavras-passe são geridas na sua organização, incluindo:

- Activar e desactivar a palavra-passe repor para todos os utilizadores de um diretório
- Definir o número de desafios (um ou dois) um utilizador tem de percorrer para repor a palavra-passe dele
- Definir os tipos específicos de desafios que pretende ativar para os utilizadores na sua organização a partir das opções abaixo:
 - Número de telemóvel (um código de verificação através de texto ou uma chamada de voz)
 - Telefone do escritório (uma chamada de voz)
 - E-mail alternativo (um código de verificação por correio eletrónico)
 - Perguntas de segurança (autenticação baseada em dados de conhecimento)
- Definir o número de perguntas um utilizador tem de registar para poder utilizar o método de autenticação de perguntas de segurança (visível apenas se estiverem ativadas perguntas de segurança)
- Definir o número de perguntas um utilizador tem de fornecer durante repor para utilizar o método de autenticação de perguntas de segurança (visível apenas se estiverem ativadas perguntas de segurança)
- Utilizar perguntas de segurança previamente em lata, localizados, que um utilizador poderá optar por utilizar quando o registo de palavra-passe repor (visíveis apenas se estiverem ativadas perguntas de segurança)
- Definir as perguntas de segurança personalizada que um utilizador poderá optar por utilizar quando o registo de palavra-passe repor (visíveis apenas se estiverem ativadas perguntas de segurança)
- Pedir aos utilizadores para se registar no repor quando acederem à aplicação do painel de acesso na [http://myapps.microsoft.com](http://myapps.microsoft.com)de palavra-passe.
- Os utilizadores que exigem para confirmar novamente os respetivos dados registados anteriormente depois de um número de dias configurável passaram (visíveis apenas se imposta registo está ativado)
- Fornecer um URL que será mostrada aos utilizadores caso ocorra algum problema reposição da palavra-passe ou o correio eletrónico de suporte técnico personalizado
- Ativar ou desativar a funcionalidade de palavra-passe de escrita não consolidada (quando foi implementada palavra-passe de escrita não consolidada com ligação AAD)
- Ver o estado do agente de palavra-passe de escrita não consolidada (quando a palavra-passe de escrita não consolidada foi implementada com ligação AAD)
- Ativar as notificações de e-mail para os utilizadores quando os seus próprios palavra-passe tiver sido reposta (encontrado na secção **notificações** do [Portal de gestão do Azure](https://manage.windowsazure.com))
- Ativar notificações de correio eletrónico para administradores de quando outros administradores repor os seus próprios palavras-passe (que se encontram na secção **notificações** do [Portal de gestão do Azure](https://manage.windowsazure.com)
- Imagem corporativa a palavra-passe de utilizador repor portal e palavra-passe repor e-mails com logótipo e o nome da sua organização, utilizando o inquilino imagem corporativa a funcionalidade de personalização (que se encontram na secção **Propriedades de diretório** do [Portal de gestão do Azure](https://manage.windowsazure.com)

Para saber mais sobre como configurar a gestão de palavra-passe na sua organização, consulte o artigo [Introdução: gestão de palavra-passe do Azure AD](active-directory-passwords-getting-started.md).

##<a name="user-registration-portal"></a>Portal de registo de utilizador
Antes dos utilizadores forem capazes de utilizar a reposição de palavra-passe, as respetivas contas de utilizador da nuvem têm de ser atualizadas com os dados de autenticação corretas para se certificar de que pode passar através do número de adequado dos desafios de reposição de palavra-passe definidos pelo seu administrador.  Os administradores também podem definir estas informações de autenticação em nome de utilizador, os respetivos utilizando os Azure ou o Office web portais, o DirSync / Azure AD Connect ou o Windows PowerShell.

No entanto, se preferir que os utilizadores em Registe-se os seus próprios dados, fornecemos também uma página web que os utilizadores podem ir para fornecer estas informações.  Esta página permite que os utilizadores especifiquem informações de autenticação em conformidade com a palavra-passe repor as políticas que foram ativadas na sua organização.  Depois de verificar estes dados, este é armazenado na sua conta de utilizador na nuvem para ser utilizado para recuperação da conta tarde. Eis o que o registo portal é semelhante a:

  ![][001]

Para obter mais informações, consulte o artigo [Introdução: gestão de palavra-passe do Azure AD](active-directory-passwords-getting-started.md) e [melhores práticas: gestão de palavra-passe do Azure AD](active-directory-passwords-best-practices.md).

##<a name="user-password-reset-portal"></a>Portal de reposição de palavra-passe de utilizador
Assim que tiver activado personalizada de palavra-repor, configura a política de reposição de gestão personalizada de palavra-passe da sua organização e garantir que os utilizadores têm os dados de contacto adequados no diretório, os utilizadores na sua organização poderão repor as suas próprias palavras-passe automaticamente a partir de qualquer página web que utiliza uma conta escolar ou de trabalho para o início de sessão (por exemplo, [portal.microsoftonline.com](https://portal.microsoftonline.com)). Em páginas, como estes, os utilizadores verão um **não é possível aceder à sua conta?** ligação.

  ![][002]

Ao clicar nesta ligação será executado o portal de reposição personalizada de palavra-passe.

  ![][003]

Para saber mais sobre como os utilizadores podem repor as suas próprias palavras-passe, consulte o artigo [Introdução: gestão de palavra-passe do Azure AD](active-directory-passwords-getting-started.md).

##<a name="user-password-change-portal"></a>Portal de alteração de palavra-passe de utilizador
Se pretenderem que os utilizadores alterar as suas próprias palavras-passe, pode fazê-lo utilizando o portal de alteração de palavra-passe em qualquer altura.  Os utilizadores podem aceder ao portal de alteração de palavra-passe através da página de perfil do painel de acesso ou clicar na ligação "Alterar palavra-passe" a partir de dentro das aplicações do Office 365.  No caso quando expiram a palavra-passe, os utilizadores serão também pedidos para alterá-las automaticamente ao iniciar sessão.

  ![][004]

Em ambos os casos, se a palavra-passe de escrita não consolidada ter sido ativada e o utilizador quer esteja federado ou faria a sincronização de palavra-passe, estas palavras-passe alteradas é escritos novamente para o seu no local Active Directory. Eis o que a palavra-passe alterar portal é semelhante a:

  ![][005]

Para saber mais sobre como os utilizadores podem alterar as suas próprias palavras-passe no local do Active Directory, consulte o artigo [Introdução: gestão de palavra-passe do Azure AD](active-directory-passwords-getting-started.md).

##<a name="password-management-reports"></a>Relatórios de gestão de palavra-passe
Ao navegar para o separador **relatórios** e está à procura na secção **Registos de atividade** , irá ver relatórios de gestão de palavra-passe de dois: **atividade repor a palavra-passe** e **Repor a palavra-passe actividade de registo**.  Utilizar estes dois relatórios, pode obter uma vista de utilizadores registar-se para e utilizar a palavra-passe repor na sua organização. Eis o aspeto destes relatórios no [Portal de gestão do Azure](https://manage.windowsazure.com):

  ![][006]

Para obter mais informações, consulte o artigo [obter informações: relatórios de gestão de palavra-passe do Azure AD](active-directory-passwords-get-insights.md).

##<a name="password-writeback-component-of-azure-ad-connect"></a>Componente de repetição de escrita de palavra-passe da ligação do Azure AD
Se as palavras-passe de utilizadores na sua organização origem a partir do seu ambiente no local (quer através de Federação ou sincronização de palavra-passe), pode instalar a versão mais recente da ligação do Azure AD para ativar a atualizar essas palavras-passe diretamente a partir da nuvem.  Isto significa que quando os utilizadores esquecer ou se pretende modificar a sua palavra-passe de AD, ele pode fazer por isso, diretamente a partir da web.  Eis como localizar a palavra-passe de escrita não consolidada no Assistente de ligação do Azure AD instalação:

  ![][007]

Para mais informações sobre a ligação do Azure AD, consulte o artigo [começar: ligação do Azure AD](active-directory-aadconnect.md). Para mais informações sobre palavra-passe de escrita não consolidada, consulte o artigo [Introdução: gestão de palavra-passe do Azure AD](active-directory-passwords-getting-started.md).


<br/>
<br/>
<br/>

## <a name="links-to-password-reset-documentation"></a>Ligações para a palavra-passe repor documentação
Abaixo estão ligações para todas as páginas de documentação de reposição de palavra-passe do Azure AD:

* **Está aqui porque está a ter problemas ao iniciar sessão?** Se Sim, [Eis como pode alterar e repor a sua própria palavra-passe](active-directory-passwords-update-your-own-password.md).
* [**Introdução ao**](active-directory-passwords-getting-started.md) - Saiba como permitir os utilizadores para repor e alterar a palavra-passe na nuvem ou no local
* [**Personalizar**](active-directory-passwords-customize.md) - Saiba como personalizar o aspeto e funcionamento e comportamento do serviço para necessidades da sua organização
* [**Práticas recomendadas**](active-directory-passwords-best-practices.md) - Saiba como implementar rapidamente e eficazmente gerir palavras-passe na sua organização
* [**Obter informações**](active-directory-passwords-get-insights.md) - Saiba mais sobre os nossos integradas capacidades de relatórios
* [**Perguntas mais frequentes**](active-directory-passwords-faq.md) : Obtenha respostas às perguntas mais frequentes
* [**Resolução de problemas**](active-directory-passwords-troubleshoot.md) - aprender rapidamente resolver problemas relacionados com o serviço
* [**Saiba mais**](active-directory-passwords-learn-more.md) - aceda abrangente para os detalhes técnicos de como funciona o serviço



[001]: ./media/active-directory-passwords-how-it-works/001.jpg "Image_001.jpg"
[002]: ./media/active-directory-passwords-how-it-works/002.jpg "Image_002.jpg"
[003]: ./media/active-directory-passwords-how-it-works/003.jpg "Image_003.jpg"
[004]: ./media/active-directory-passwords-how-it-works/004.jpg "Image_004.jpg"
[005]: ./media/active-directory-passwords-how-it-works/005.jpg "Image_005.jpg"
[006]: ./media/active-directory-passwords-how-it-works/006.jpg "Image_006.jpg"
[007]: ./media/active-directory-passwords-how-it-works/007.jpg "Image_007.jpg"
