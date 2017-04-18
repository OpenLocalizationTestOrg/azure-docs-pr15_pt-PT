<properties
    pageTitle="Resolver problemas de Proxy de aplicação | Microsoft Azure"
    description="Aborda como resolver problemas de erros no Azure AD para o Proxy de aplicação."
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
    ms.date="08/19/2016"
    ms.author="kgremban"/>



# <a name="troubleshoot-application-proxy"></a>Resolver problemas de Proxy de aplicação

Se ocorrerem erros no aceder a uma aplicação publicada ou aplicações de publicação, consulte as seguintes opções para ver se Proxy de aplicação do Microsoft Azure AD está a funcionar corretamente:

- Abra a consola de serviços do Windows e certifique-se de que o serviço de **Proxy de aplicação do Microsoft AAD Connector** é activado e em execução. Também poderá querer observe a página de propriedades de serviço do Proxy de aplicação, conforme apresentado na seguinte imagem:  
  ![Captura de ecrã de janela de propriedades da aplicação Microsoft AAD Proxy conexão](./media/active-directory-application-proxy-troubleshoot/connectorproperties.png)

- Abra o Visualizador de eventos e procure eventos de conector de Proxy de aplicação nas **aplicações e serviços registos** > **Microsoft** > **AadApplicationProxy** > **conexão** > **Admin**.
- Se for necessário, os registos mais detalhados estão disponíveis ao ativar o registo a análise de depuração de registos e ativar o registo de sessão do conector de Proxy de aplicação.

## <a name="the-page-is-not-rendered-correctly"></a>A página não é composta corretamente

Se não estiver a receber uma mensagem de erro específico, ainda poderá ter problemas com a aplicação de composição ou está a funcionar incorretamente. Isto pode ocorrer se publicado o caminho de artigo, mas a aplicação necessita de conteúdo que existe fora esse caminho.

Por exemplo, se publicar o caminho https://yourapp/app, mas a aplicação chama imagens de https://yourapp/media, estes não sejam compostos. Certifique-se de que publica a aplicação utilizando o caminho de nível mais alto que precisar de incluir todo o conteúdo relevante. Neste exemplo, seria http://yourapp/.

Se alterar o caminho para incluir conteúdo referenciado, mas ainda precisa de utilizadores a pedra uma ligação mais aprofundada no caminho, consulte a mensagem [na ligação à direita para o Proxy de aplicação de aplicações no iniciador de aplicações do Office 365 e painel de acesso do Azure AD a definição](https://blogs.technet.microsoft.com/applicationproxyblog/2016/04/06/setting-the-right-link-for-application-proxy-applications-in-the-azure-ad-access-panel-and-office-365-app-launcher/)do blogue.

## <a name="general-errors"></a>Erros gerais

Erro | Descrição | Resolução
--- | --- | ---
Não pode ser acedida esta aplicação empresarial. Não está autorizado para aceder a esta aplicação. Falha de autorização. Certifique-se atribuir ao utilizador com acesso a esta aplicação. | Poderá não ter atribuído o utilizador para esta aplicação. | Aceda ao separador da **aplicação** e, em **utilizadores e grupos**, atribuir este utilizador ou grupo de utilizador para esta aplicação.
Não pode ser acedida esta aplicação empresarial. Não está autorizado para aceder a esta aplicação. Falha de autorização. Certifique-se de que o utilizador tem uma licença para o Azure Active Directory Premium ou Basic. | Os utilizadores poderão obter este erro quando tentar aceder à aplicação publicados se de que não foram atribuídos explicitamente com uma licença de Premium/Basic pelo administrador do assinante. | Vá para do Active Directory **licenças** separador o subscritor e certifique-se de que este utilizador ou grupo de utilizadores está atribuído um Premium ou licença básica.


## <a name="connector-troubleshooting"></a>Resolução de problemas de conexão
Se o registo falhar durante a instalação do Assistente de conexão, existem duas formas de ver o motivo da falha. Quer procure no registo de eventos em **aplicações e serviços Logs\Microsoft\AadApplicationProxy\Connector\Admin**ou execute o seguinte comando Windows PowerShell.

    Get-EventLog application –source “Microsoft AAD Application Proxy Connector” –EntryType “Error” –Newest 1

| Erro | Descrição | Resolução |
| --- | --- | --- |
| Registo de conexão falhou: Certifique-se que ativa o Proxy de aplicação no Portal de gestão do Azure e que introduziu o nome de utilizador do Active Directory e a palavra-passe corretamente. Erro: 'um ou mais ocorreram erros.' | Na janela registo tiver fechado sem iniciar sessão no Azure AD de efetuar. | Execute novamente o Assistente de conexão e registar o Connector. |
| Registo de conexão falhou: Certifique-se que ativa o Proxy de aplicação no Portal de gestão do Azure e que introduziu o nome de utilizador do Active Directory e a palavra-passe corretamente. Erro: ' AADSTS50001: recurso `https://proxy.cloudwebappproxy.net /registerapp` está desativado.' | Proxy de aplicação é desativado.  | Certifique-se de que ativar o Proxy de aplicação no portal do Azure clássico antes de tentar registar a conexão. Para mais informações sobre como ativar o Proxy de aplicação, consulte o artigo [Ativar o Proxy de aplicação serviços](active-directory-application-proxy-enable.md). |
| Registo de conexão falhou: Certifique-se que ativa o Proxy de aplicação no Portal de gestão do Azure e que introduziu o nome de utilizador do Active Directory e a palavra-passe corretamente. Erro: 'um ou mais ocorreram erros.' | Se a janela registo é aberto e, em seguida, fecha de imediato sem permitir que inicie sessão no, provavelmente obterá este erro. Este erro ocorre quando existe um erro de rede no seu sistema. | Certifique-se de que é possível ligar a partir de um browser a um Web site público e que as portas estão abertas conforme especificado na [Pré-requisitos de Proxy de aplicação](active-directory-application-proxy-enable.md). |
| Registo de conexão falhou: Certifique-se de que o computador está ligado à Internet. Erro: "não tiver nenhum ponto final listening na `https://connector.msappproxy.net :9090/register/RegisterConnector` que pudesse aceitar a mensagem. Isto é frequentemente causado por um endereço incorreto ou acção SOAP. Consulte InnerException, se existirem, para obter mais detalhes.' | Se iniciar sessão com o seu nome de utilizador do Azure AD e a palavra-passe, mas, em seguida, receber este erro, pode ser que todas as portas acima 8081 estão bloqueadas. | Certifique-se de que as portas necessárias estão abertas. Para mais informações, consulte o artigo [Pré-requisitos de Proxy de aplicação](active-directory-application-proxy-enable.md). |
| Limpar erro é apresentado na janela do registo. Não é possível continuar – apenas para fechar a janela. | Introduziu o nome de utilizador mal ou palavra-passe. | Tente novamente. |
| Registo de conexão falhou: Certifique-se que ativa o Proxy de aplicação no Portal de gestão do Azure e que introduziu o nome de utilizador do Active Directory e a palavra-passe corretamente. Erro: ' AADSTS50059: nenhuma informação que identifica o inquilino encontrado em qualquer um pedido de ou implícitas por qualquer credenciais fornecidas e procurar por princípio serviço URI falhou. | Está a tentar iniciar sessão utilizando uma Account Microsoft e não um domínio que está a parte do ID de organização do directório que está a tentar aceder. | Certifique-se de que o administrador faz parte do mesmo nome de domínio que o domínio de inquilino, por exemplo, se o domínio do Azure AD for contoso.com, o administrador deverá ser admin@contoso.com. |
| Falha ao obter a política de execução atual para executar scripts de PowerShell. | Se a instalação de conexão falhar, verifique para se certificar de que a política de execução do PowerShell não está desactivada. | Abra o Editor de políticas de grupo. Aceda a **Configuração do computador** > **Modelos administrativos** > **Componentes do Windows** > **Windows PowerShell** e faça duplo clique em **Ativar a execução de Script**. Isto pode ser definido como **Não configurado** ou **ativada**. Se definir para **ativado**, certifique-se de que em Opções, a política de execução está definida para um dos **scripts locais de permitir e remote com a sessão iniciada** ou para **Permitir que todos os scripts**. |
| Não foi possível transferir a configuração do conector. | Certificado de cliente a conexão, que é utilizado para autenticação, expirou. Também poderá ocorrer se tiver o conector instalado atrás de um proxy. Neste caso, a conexão não é possível aceder à Internet e não poderão fornecer aplicações aos utilizadores remotos. | Renovar fidedignidade manualmente utilizando o `Register-AppProxyConnector` cmdlet no Windows PowerShell. Se estiver a conexão atrás de um proxy, é necessário conceder acesso à Internet às contas de conexão "serviços de rede" e "sistema local". Isto pode ser feito através de lhes conceder acesso para o Proxy ou definindo-los para ignorar o proxy. |
| Registo de conexão falhou: Certifique-se de que for um Administrador Global do seu Active Directory para registar a conexão. Erro: "o pedido de registo foi negado.' | O alias que está a tentar iniciar sessão com não é um administrador neste domínio. A conexão é sempre instalada para o directório proprietária domínio do utilizador. | Certifique-se de que o administrador está a tentar iniciar sessão no como tem permissões globais para o inquilino do Azure AD.|


## <a name="kerberos-errors"></a>Erros de Kerberos

| Erro | Descrição | Resolução |
| --- | --- | --- |
| Falha ao obter a política de execução atual para executar scripts de PowerShell. | Se a instalação de conexão falhar, verifique para se certificar de que a política de execução do PowerShell não está desactivada. | Abra o Editor de políticas de grupo. Aceda a **Configuração do computador** > **Modelos administrativos** > **Componentes do Windows** > **Windows PowerShell** e faça duplo clique em **Ativar a execução de Script**. Isto pode ser definido como **Não configurado** ou **ativada**. Se definir para **ativado**, certifique-se de que em Opções, a política de execução está definida para um dos **scripts locais de permitir e remote com a sessão iniciada** ou para **Permitir que todos os scripts**. |
| 12008 - azure AD excedido o número máximo de tentativas de autenticação Kerberos permitidos para o servidor de back-end. | Este evento pode indicar configuração incorreta entre Azure AD e o servidor da aplicação back-end ou um problema na configuração de data e hora em ambas as máquinas. | O servidor de back-end recusado a permissão de Kerberos criado por Azure AD. Certifique-se de que Azure AD e o servidor da aplicação back-end estão corretamente configurados. Certifique-se de que a configuração de data e hora no Azure AD e o servidor da aplicação back-end são sincronizados. |
| 13016 - azure AD não consegue obter um bilhetes Kerberos em nome de utilizador, porque não existe nenhuma UPN no token de limite ou no cookie acesso. | Existe um problema com a configuração de STS. | Corrigir a configuração de afirmação UPN no STS. |
| 13019 - azure AD não consegue obter um bilhetes Kerberos em nome de utilizador devido ao seguinte erro API geral. | Este evento pode indicar configuração incorreta entre Azure AD e servidor controlador de domínio ou um problema na configuração de data e hora em ambas as máquinas. | O controlador de domínio recusado a permissão de Kerberos criado por Azure AD. Certifique-se de que Azure AD e o servidor da aplicação back-end estão configurados corretamente, especialmente a configuração de SPN. Certifique-se Azure AD domínio associado ao mesmo domínio como o controlador de domínio para se certificar de que o controlador de domínio estabelece fidedignidade com Azure AD. Certifique-se de que a configuração de data e hora no Azure AD e são sincronizados o controlador de domínio. |
| 13020 - azure AD não consegue obter um bilhetes Kerberos em nome de utilizador, porque o servidor de back-end SPN não está definido. | Este evento pode indicar configuração incorreta entre Azure AD e servidor controlador de domínio ou um problema na configuração de data e hora em ambas as máquinas. | O controlador de domínio recusado a permissão de Kerberos criado por Azure AD. Certifique-se de que Azure AD e o servidor da aplicação back-end estão configurados corretamente, especialmente a configuração de SPN. Certifique-se Azure AD domínio associado ao mesmo domínio como o controlador de domínio para se certificar de que o controlador de domínio estabelece fidedignidade com Azure AD. Certifique-se de que a configuração de data e hora no Azure AD e são sincronizados o controlador de domínio. |
| 13022 - azure AD não é possível autenticar o utilizador porque o servidor de back-end responde à tentativas de autenticação Kerberos com um erro de HTTP 401. | Este evento pode indicar configuração incorreta entre Azure AD e o servidor da aplicação back-end ou um problema na configuração de data e hora em ambas as máquinas. | O servidor de back-end recusado a permissão de Kerberos criado por Azure AD. Certifique-se de que Azure AD e o servidor da aplicação back-end estão corretamente configurados. Certifique-se de que a configuração de data e hora no Azure AD e o servidor da aplicação back-end são sincronizados. |
| O Web site não consegue apresentar a página. | O utilizador poderá obter este erro quando tentar aceder à aplicação publicados se a aplicação é uma aplicação de IWA. O SPN definido para esta aplicação poderá estar incorreto. | Para as aplicações IWA: Certifique-se de que o SPN configurado para esta aplicação está correto. |
| O Web site não consegue apresentar a página. | O utilizador poderá obter este erro quando tentar aceder à aplicação publicados se a aplicação é uma aplicação OWA. Isto pode ser causado por um dos seguintes procedimentos: <br> -O SPN definido para esta aplicação está incorreto. <br> -O utilizador que tentou aceder à aplicação está a utilizar uma conta Microsoft em vez da conta da empresa adequada para iniciar sessão ou o utilizador é um utilizador convidado. <br> -O utilizador que tentou aceder à aplicação não está corretamente definido para esta aplicação no lado no local. | Os passos para mitigar em conformidade: <br> -Certifique-se de que o SPN configurado para esta aplicação está correto. <br> -Certifique-se de que o utilizador inicia sessão com a sua conta empresarial que corresponde ao domínio da aplicação publicado. Os utilizadores da Microsoft Account e como convidado não é possível aceder a IWA aplicações. <br> -Certifique-se de que este utilizador tem as permissões adequadas, tal como foi definido para esta aplicação back-end no computador no local. |
| Não pode ser acedida esta aplicação empresarial. Não está autorizado para aceder a esta aplicação. Falha de autorização. Certifique-se atribuir ao utilizador com acesso a esta aplicação. | Os utilizadores poderão obter este erro quando tentar aceder à aplicação publicados se utilizar contas Microsoft lugar da sua conta da empresa para iniciar sessão. Utilizadores convidados também poderão obter este erro. | Utilizadores do Microsoft Account e convidados não é possível aceder a IWA aplicações. Certifique-se de que o utilizador inicia sessão com a sua conta empresarial que corresponde ao domínio da aplicação publicado. |
| Esta aplicação empresarial não pode ser acedida neste momento. Tente novamente mais tarde... O conector excedido. | Os utilizadores poderão obter este erro quando tentar aceder à aplicação publicados se não estiver corretamente definidos para esta aplicação no lado no local. | Certifique-se de que os utilizadores têm as permissões adequadas, tal como foi definido para esta aplicação back-end no computador no local. |


## <a name="see-also"></a>Consulte também

- [Ativar o Proxy de aplicação para o Azure Active Directory](active-directory-application-proxy-enable.md)
- [Publicar aplicações com o Proxy de aplicação](active-directory-application-proxy-publish.md)
- [Ativar o início de sessão único](active-directory-application-proxy-sso-using-kcd.md)
- [Ativar o acesso condicional](active-directory-application-proxy-conditional-access.md)

Para obter as notícias mais recentes e um atualizações, consulte o [blogue do Proxy de aplicação](http://blogs.technet.com/b/applicationproxyblog/)


<!--Image references-->
[1]: ./media/active-directory-application-proxy-troubleshoot/connectorproperties.png
[2]: ./media/active-directory-application-proxy-troubleshoot/sessionlog.png
