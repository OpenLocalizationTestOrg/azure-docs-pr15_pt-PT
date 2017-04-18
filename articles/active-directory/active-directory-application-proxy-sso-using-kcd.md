<properties
    pageTitle="Serviço Single sign-on com Proxy de aplicação | Microsoft Azure"
    description="Aborda como fornecer o serviço single sign-on utilizando o Proxy de aplicação do Azure AD."
    services="active-directory"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/10/2016"
    ms.author="kgremban"/>


# <a name="single-sign-on-with-application-proxy"></a>Serviço Single sign-on com Proxy de aplicação

O início de sessão único é um elemento de chave do Azure AD para o Proxy de aplicação. Fornece-lhe a melhor experiência de utilizador com os seguintes passos:

1. Um utilizador inicia sessão para a nuvem  
2. Todos os validações de segurança ocorrem na nuvem (pré-autenticação)  
3. Quando o pedido é enviado para a aplicação no local, o conector de Proxy de aplicação representa o utilizador. A aplicação de back-end pensa que este é um utilizador habitual provenientes de um dispositivo de domínio.

![Diagrama de acesso do utilizador final, através de Proxy de aplicação à rede empresarial](./media/active-directory-application-proxy-sso-using-kcd/app_proxy_sso_diff_id_diagram.png)

Proxy de aplicação do Azure AD ajuda-o a fornecer uma experiência de início de sessão único (SSO) para os seus utilizadores. Utilize as instruções seguintes para publicar as suas aplicações a utilizar o SSO:


## <a name="sso-for-on-prem-iwa-apps-using-kcd-with-application-proxy"></a>SSO para as aplicações IWA no local utilizar KCD com o Proxy de aplicação
Pode ativar o início de sessão único para as suas aplicações utilizando a autenticação de Windows integrada (IWA), dar permissão de aplicação Proxy conectores no Active Directory para representar utilizadores e enviar e receber tokens em nome dos utilizadores.


### <a name="network-diagram"></a>Diagrama de rede

Este diagrama explica o fluxo quando um utilizador tenta aceder a uma aplicação no local que utiliza IWA.

![Diagrama de fluxo de autenticação do Microsoft AAD](./media/active-directory-application-proxy-sso-using-kcd/AuthDiagram.png)

1. O utilizador introduz o URL para aceder a aplicação no local através do Proxy de aplicação.
2. Proxy de aplicação redireciona o pedido para serviços de autenticação do Azure AD para pré-autenticar. Neste momento, aplica-se Azure AD qualquer autenticação aplicável e políticas de autorização, tal como a autenticação multifator. Se o utilizador for validado, Azure AD cria um token e envia-lo para o utilizador.
3. O utilizador passa o token para o Proxy de aplicação.
4. Proxy de aplicação de valida o token e obtém o nome de utilizador Principal (UPN) da mesma e, em seguida, envia o pedido, o UPN e o nome Principal do serviço (SPN) ao conector através de um canal seguro dually autenticado.
5. A conexão executa negociação Kerberos restringido delegação (KCD) com no local AD, o utilizador para obter um token de Kerberos para a aplicação a representar.
6. O Active Directory envia o token de Kerberos para a aplicação ao conector.
7. A conexão envia o pedido original para o servidor de aplicação, utilizando o token de Kerberos que recebeu do AD.
8. A aplicação de envia a resposta ao conector que, em seguida, é devolvido para o serviço de Proxy da aplicação e, por último ao utilizador.

### <a name="prerequisites"></a>Pré-requisitos

Antes de começar com SSO para Proxy de aplicação, certifique-se de que o seu ambiente está pronto com as seguintes definições e as configurações:

- As aplicações, como aplicações Web do SharePoint, estão definidas para utilizar integrada autenticação do Windows. Para obter mais informações consulte o artigo [Ativar o suporte para autenticação Kerberos](https://technet.microsoft.com/library/dd759186.aspx)ou para o SharePoint consulte o artigo [Planear uma autenticação Kerberos no SharePoint 2013](https://technet.microsoft.com/library/ee806870.aspx).

- Todas as suas aplicações têm nomes principais de serviço.

- Servidor a executar o conector e servidor a executar a aplicação são domínio aderido e parte do mesmo domínio ou domínios fidedignos. Para mais informações sobre a associação de domínio, consulte o artigo [participar um computador para um domínio](https://technet.microsoft.com/library/dd807102.aspx).

- Servidor a executar o conector tem acesso ao ler o TokenGroupsGlobalAndUniversal para utilizadores. Esta é uma definição de predefinido que poderá ter sido afetada pelo protecção o ambiente de segurança. Obter mais ajuda com o seguinte na [KB2009157](https://support.microsoft.com/en-us/kb/2009157).

### <a name="active-directory-configuration"></a>Configuração do Active Directory

A configuração do Active Directory varia, dependendo se a conexão de Proxy de aplicação e publicados num servidor são no mesmo domínio ou não.

#### <a name="connector-and-published-server-in-the-same-domain"></a>Conector e publicados num servidor no mesmo domínio

1. No Active Directory, aceda a **Ferramentas** > **utilizadores e computadores**.
2. Selecione o servidor a executar o conector.
3. Botão direito do rato e selecione **Propriedades** > **delegação**.
4. Selecione **Confiar neste computador para delegação especificados apenas a serviços** e em **Serviços para o qual esta conta pode apresentar as credenciais delegadas**, adicione o valor para a identidade SPN do application server.
5. Permite que o conector de Proxy de aplicação representar os utilizadores no AD contra as aplicações definidas na lista.

![Captura de ecrã de janela de propriedades de conexão SVR](./media/active-directory-application-proxy-sso-using-kcd/Properties.jpg)

#### <a name="connector-and-published-server-in-different-domains"></a>Conector e publicados num servidor em domínios diferentes

1. Para obter uma lista de pré-requisitos para trabalhar com KCD em domínios, consulte o artigo [Restringido a delegação Kerberos em domínios](https://technet.microsoft.com/library/hh831477.aspx).
2. No Windows 2012 R2, utilize o `principalsallowedtodelegateto` propriedade no servidor conexão para ativar o Proxy de aplicação ao delegado para o servidor de conexão, onde está o servidor publicado `sharepointserviceaccount` e o servidor delegado é `connectormachineaccount`.

        $connector= Get-ADComputer -Identity connectormachineaccount -server dc.connectordomain.com

        Set-ADComputer -Identity sharepointserviceaccount -PrincipalsAllowedToDelegateToAccount $connector

        Get-ADComputer sharepointserviceaccount -Properties PrincipalsAllowedToDelegateToAccount


>[AZURE.NOTE] `sharepointserviceaccount`pode ser a conta de computador SPS ou uma conta de serviço em qual o conjunto de aplicação SPS está a ser executado.


### <a name="azure-classic-portal-configuration"></a>Configuração do portal clássica Azure

1. Publica a sua aplicação de acordo com as instruções descritas [publicar aplicações com o Proxy de aplicação](active-directory-application-proxy-publish.md). Certifique-se selecionar o **Azure Active Directory** como o **Método de pré-autenticação**.
2. Depois da aplicação aparece na lista de aplicações, selecione-o e clique em **Configurar**.
3. Em **Propriedades**, defina o **Método de autenticação interno** para **Integrada autenticação do Windows**.  
  ![Configuração da aplicação avançadas](./media/active-directory-application-proxy-sso-using-kcd/cwap_auth2.png)  
4. Introduza o **Interno SPN de aplicação** do application server. Neste exemplo, o SPN de nossa aplicação publicada é http/lob.contoso.com.  

>[AZURE.IMPORTANT] Se o UPN no local e o UPN no Azure Active Directory não sejam idênticas, terá de configurar [delegada identidade de início de sessão](#delegated-login-identity) para que pré-autenticação trabalhar.

|  |  |
| --- | --- |
| Método de autenticação interno | Se utiliza o Azure AD para pré-autenticação, pode definir um método de autenticação interna para permitir que os utilizadores beneficiar de sessão único (SSO) para esta aplicação. <br><br> Selecione **Integrada autenticação do Windows** (IWA) se a sua aplicação utiliza IWA e pode configurar Kerberos restringido delegação (KCD) para activar SSO para esta aplicação. As aplicações que utilizam IWA tem de ser configuradas utilizando KCD, caso contrário, o Proxy de aplicação não será possível publicar estas aplicações. <br><br> Selecione **nenhum** se a sua aplicação não utilizar IWA. |
| Aplicação interna SPN | Este é o nome do Principal do serviço (SPN) da aplicação interno conforme configurado no no local Azure AD. O SPN é utilizado pelo conector de Proxy de aplicação para obter tokens de Kerberos para a aplicação utilizando KCD. |


## <a name="sso-for-non-windows-apps"></a>SSO de aplicações não Windows
O fluxo de delegação de Kerberos no Proxy de aplicação do Azure AD é iniciado Azure AD autentica o utilizador na nuvem. Assim que o pedido chega no local, o conector de Proxy de aplicação AD Azure problemas uma permissão Kerberos em nome de utilizador ao interagir com o Active Directory local. Este processo é referido como Kerberos restringido delegação (KCD). Na fase seguinte, é enviado um pedido para a aplicação de back-end com esta permissão Kerberos. Existem um número de protocolos que definir como enviar esses pedidos. A maioria dos servidores não Windows esperar negociação/SPNego ser agora suportado no Azure AD para o Proxy de aplicação.

![Diagrama de SSO não pertencente ao Windows](./media/active-directory-application-proxy-sso-using-kcd/app_proxy_sso_nonwindows_diagram.png)

### <a name="delegated-login-identity"></a>Identidade de início de sessão delegada
Identidade de início de sessão delegada ajuda-o a alça de dois cenários de início de sessão diferente:

- Aplicações do Windows não que normalmente obter identidade do utilizador sob a forma de um nome de utilizador ou o nome da conta SAM, não um endereço de e-mail (username@domain).
- Configurações de início de sessão alternativo onde o UPN no Azure AD e o UPN no Active Directory no local são diferentes.

Com o Proxy de aplicação, pode selecionar qual é a identidade a utilizar para obter a permissão de Kerberos. Esta definição está por aplicação. Algumas destas opções são adequadas para sistemas de que não aceitar a formato de endereço de e-mail, as outras pessoas foram criadas para o início de sessão alternativo.

![Captura de ecrã do início de sessão delegada identidade parâmetro](./media/active-directory-application-proxy-sso-using-kcd/app_proxy_sso_diff_id_upn.png)

Se for utilizada identidade de início de sessão delegada, o valor poderá não ser exclusivo para todos os domínios ou florestas na sua organização. Pode evitar este problema publicando estas aplicações de duas vezes com dois grupos diferentes de conexão. Uma vez que cada aplicação tem uma audiência utilizador diferente, pode participar respetivos conexões para um domínio diferente.


## <a name="working-with-sso-when-on-premises-and-cloud-identities-are-not-identical"></a>Trabalhar com SSO quando no local e na nuvem identidades não são idênticas
A menos que configurado caso contrário, o Proxy de aplicação assume que os utilizadores têm exatamente a mesma identidade na nuvem e no local. Pode configurar, para cada aplicação, qual é a identidade deve ser utilizada quando efetuar o início de sessão único.  

Esta funcionalidade permite muitas organizações que têm diferentes identidades no local e na nuvem ter SSO a partir da nuvem de aplicações no local, sem necessidade dos utilizadores introduzir nomes de utilizador diferentes e palavras-passe. Isto inclui organizações que:

- Tiver múltiplos domínios internamente (joe@us.contoso.com, joe@eu.contoso.com) e um único domínio na nuvem (joe@contoso.com).

- Tem o nome de domínio não encaminháveis internamente (joe@contoso.usa) e um legal na nuvem.

- Não utilização interna nomes de domínio (João)

- Utilizar aliases diferente no local e na nuvem. Por exemplo joe-johns@contoso.comvs.joej@contoso.com  

Também poderá ajudar com aplicações que não aceitar a endereços sob a forma de endereço de e-mail, que é um cenário muito comum para servidores não Windows do back-end.


### <a name="setting-sso-for-different-cloud-and-on-prem-identities"></a>Definir o SSO para diferentes identidades na nuvem e no local

1. Configure definições de ligação do Azure AD, de modo a identidade principal será o endereço de e-mail (correio). Isto é feito como parte do processo de personalizar, alterando o campo **Nome de utilizador princípio** nas definições de sincronização. Nota que estas definições também determinam como os utilizadores iniciar sessão no Office 365, Windows10 dispositivos e outras aplicações que utilizam o Azure AD como os respetivos arquivo de identidade.  
  ![Identificar a captura de ecrã de utilizadores - pendente Nome Principal de utilizador](./media/active-directory-application-proxy-sso-using-kcd/app_proxy_sso_diff_id_connect_settings.png)  
2. Nas definições de configuração da aplicação para a aplicação que pretende modificar, selecione a **Identidade de início de sessão delegada** para ser utilizado:
  - Nome do princípio de utilizador:joe@contoso.com  
  - Nome do princípio alternativa do utilizador:joed@contoso.local  
  - Parte do nome de utilizador do nome do princípio de utilizador: João  
  - Parte do nome de utilizador alternativo princípio de nome de utilizador: joed  
  - Nome da conta no local SAM: consoante configuração do controlador de domínio no local

  ![Captura de ecrã de menu pendente de identidade de início de sessão delegada](./media/active-directory-application-proxy-sso-using-kcd/app_proxy_sso_diff_id_upn.png)  

### <a name="troubleshooting-sso-for-different-identities"></a>Resolução de problemas SSO para diferentes identidades
Se existir um erro no processo de SSO aparece no registo de eventos de máquina de conexão como é explicado em [resolução de problemas](active-directory-application-proxy-troubleshoot.md).
No entanto, em alguns casos, o pedido será com êxito enviado para a aplicação de back-end enquanto esta aplicação irá responder em várias outras respostas HTTP. Resolução de problemas nestes casos deverá começar por examinar o número de evento 24029 no computador conexão no registo de eventos de sessão do Proxy de aplicação. A identidade do utilizador que foi utilizada para delegação aparecerá no campo "utilizador" dentro os detalhes do evento. Para ativar o registo de sessão, selecione **Mostrar analítico registos e de depuração** no caso de Visualizador de ver o menu.


## <a name="see-also"></a>Consulte também

- [Publicar aplicações com o Proxy de aplicação](active-directory-application-proxy-publish.md)
- [Resolução de problemas que ocorram com Proxy de aplicação](active-directory-application-proxy-troubleshoot.md)
- [Trabalhar com aplicações deverá ter em consideração em afirmações](active-directory-application-proxy-claims-aware-apps.md)
- [Ativar o acesso condicional](active-directory-application-proxy-conditional-access.md)

Para obter as notícias mais recentes e um atualizações, consulte o [blogue do Proxy de aplicação](http://blogs.technet.com/b/applicationproxyblog/)


<!--Image references-->
[1]: ./media/active-directory-application-proxy-sso-using-kcd/AuthDiagram.png
[2]: ./media/active-directory-application-proxy-sso-using-kcd/Properties.jpg
