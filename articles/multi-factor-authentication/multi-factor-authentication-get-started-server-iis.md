<properties 
    pageTitle="Autenticação do IIS e Azure servidor de autenticação Multifator"
    description="Esta é a página de autenticação multifator Azure que irá ajudar a implementar o IIS autenticação e de servidor de autenticação Multifator Azure."
    services="multi-factor-authentication"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor="curtand"/>

<tags
    ms.service="multi-factor-authentication"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="08/04/2016"
    ms.author="kgremban"/>

# <a name="iis-authentication"></a>Autenticação do IIS

A secção autenticação do IIS o servidor de autenticação Multifator Azure permite-lhe ativar e configurar a autenticação do IIS para a integração com as aplicações web IIS da Microsoft. O servidor de autenticação Multifator Azure instala um plug-in qual pode filtrar pedidos efectuados para o servidor web do IIS para poder adicionar Azure a autenticação Multifator. O IIS Plug-in fornece suporte para autenticação baseada em formulários e autenticação de HTTP integrada do Windows. IPs também pode ser configurado para excluir endereços IP internos de autenticação de dois fatores de confiança.


![Autenticação do IIS](./media/multi-factor-authentication-get-started-server-iis/iis.png)


## <a name="using-form-based-iis-authentication-with-azure-multi-factor-authentication-server"></a>Utilizando a autenticação do IIS baseadas em formulários com o servidor de autenticação Multifator Azure

Para proteger uma aplicação web do IIS que utilize autenticação baseada em formulários, instale o servidor de autenticação Multifator Azure no servidor web IIS e configure o servidor de pelo procedimento seguinte.

1. Dentro do servidor de autenticação Multifator Azure clique no ícone de autenticação do IIS no menu à esquerda.
2. Clique no separador baseadas em formulários.
3. Clique em Adicionar... botão.
4. Para detectar o nome de utilizador, palavra-passe e domínio variáveis automaticamente, introduza o URL de início de sessão (por exemplo, https://localhost/contoso/auth/login.aspx) dentro da caixa de diálogo Auto-Configure Form-Based site e clique em OK.
5. Selecione a caixa de correspondência de utilizador do exigir autenticação Multifator se todos os utilizadores tenham sido ou serão importados no servidor e sujeito a autenticação multifator. Se um número de utilizadores significativo ainda não foram importado no servidor e/ou será isentos de autenticação multifator, deixe a caixa desmarcada.
6. Se as variáveis de página não podem ser detectadas automaticamente, clique em especificar manualmente... botão na caixa de diálogo Auto-Configure Form-Based Web site.
7. Na caixa de diálogo Add Form-Based Web site, introduza o URL para a página de início de sessão no campo URL submeter e introduza um nome da aplicação (opcional). O nome da aplicação aparece em relatórios de autenticação Multifator de Azure e pode ser apresentado dentro de mensagens de autenticação SMS ou a aplicação Mobile. Consulte o ficheiro de ajuda para obter mais informações sobre o URL submeter.
8. Selecione o formato correto do pedido. Este é definido para "Mensagem ou obter" para a maioria das aplicações web.
9. Introduza o nome de utilizador variável, a variável de palavra-passe e a variável de domínio (se for apresentada na página de início de sessão). Poderá ter navegar para a página de início de sessão num browser, com o botão direito na página e selecione "Ver origem" para encontrar os nomes das caixas de entrada dentro de uma página.
10. Selecione a caixa de correspondência de utilizador do exigir autenticação Multifator do Azure se todos os utilizadores tenham sido ou serão importados no servidor e sujeito a autenticação multifator. Se um número de utilizadores significativo ainda não foram importado no servidor e/ou será isentos de autenticação multifator, deixe a caixa desmarcada. Consulte o artigo o ficheiro de ajuda para obter informações adicionais sobre esta funcionalidade.
11.  Clique em Avançadas... botão para rever as definições avançadas, incluindo a capacidade para selecionar um ficheiro de página personalizado negação, para autenticação efetuada com êxito para o Web site em cache durante um período de tempo utilizando os cookies e para selecionar se pretende autenticar as credenciais primárias contra o domínio do Windows, um directório LDAP ou um servidor RADIUS. Quando estiver concluído, clique no botão OK para regressar à caixa de diálogo Add Form-Based Web site. Consulte o ficheiro de ajuda para obter mais informações sobre as definições avançadas.
12. Clique em OK.
13. Assim que foram detetadas ou introduzidas as variáveis de URL e de página, irão apresentar os dados de Web site no painel de baseadas em formulários.
14. Consulte os ativar IIS Plug-ins para a secção de servidor de autenticação Multifator Azure diretamente abaixo para concluir a configuração de autenticação do IIS.

## <a name="using-integrated-windows-authentication-with-azure-multi-factor-authentication-server"></a>Utilizando a autenticação do Windows integrado com o servidor de autenticação Multifator Azure

Para proteger uma aplicação web do IIS que utilize autenticação integrada HTTP do Windows, instale o servidor de autenticação Multifator Azure no servidor web IIS e configure o servidor de pelo procedimento seguinte.

1. Dentro do servidor de autenticação Multifator Azure clique no ícone de autenticação do IIS no menu à esquerda.
2. Clique no separador HTTP. Clique no separador baseadas em formulários.
3. Clique em Adicionar... botão.
4. Na caixa de diálogo Adicionar Base URL, introduza o URL para o Web site onde a autenticação de HTTP é executado (por exemplo, http://localhost/owa) para o campo da Base de URL e introduza um nome da aplicação (opcional). O nome da aplicação aparece em relatórios de autenticação Multifator de Azure e pode ser apresentado dentro de mensagens de autenticação SMS ou a aplicação Mobile.
5. Ajuste o tempo limite Idle e máximo vezes sessão se a predefinição não é suficiente.
6. Selecione a caixa de correspondência de utilizador do exigir autenticação Multifator se todos os utilizadores tenham sido ou serão importados no servidor e sujeito a autenticação multifator. Se um número de utilizadores significativo ainda não foram importado no servidor e/ou será isentos de autenticação multifator, deixe a caixa desmarcada. Consulte o artigo o ficheiro de ajuda para obter informações adicionais sobre esta funcionalidade.
7. Selecione a caixa de cache de cookies, se pretender.
8. Clique em OK.
9. Consulte a secção [Ativar IIS Plug-ins para servidor de autenticação Multifator Azure](#enable-iis-plug-ins-for-azure-multi-factor-authentication-server) diretamente abaixo para concluir a configuração de autenticação do IIS.


## <a name="enable-iis-plug-ins-for-azure-multi-factor-authentication-server"></a>Ativar o Plug-ins do IIS para servidor de autenticação Multifator Azure

Depois de ter configurado o baseadas em formulários ou URLs de autenticação de HTTP e definições, tem de selecionar as localizações onde os plug-ins do IIS de autenticação Multifator de Azure deve ser carregados e ativados no IIS. Utilize o seguinte procedimento:

1. Se executar no IIS 6, clique no separador ISAPI e selecione o Web site que execute a aplicação web em (por exemplo, Web Site predefinido) para ativar o filtro de ISAPI de autenticação Multifator de Azure Plug-in para esse site.
2. Se executar no IIS 7 ou superior, clique no separador de módulo nativas e selecione o servidor, sítios Web ou aplicações para ativar o IIS Plug-in no nível pretendido (is).
3. Clique na caixa de autenticação de ativar IIS na parte superior do ecrã. Autenticação Multifator Azure é agora a proteger a aplicação do IIS seleccionada. Certifique-se de que os utilizadores tem sido importados para o servidor. Consulte a secção de IPs de confiança abaixo se gostaria de lista branca que endereços de IP interno, de modo que a autenticação fator duplo não é necessária ao iniciar sessão para o Web site a partir dessas localizações.


## <a name="trusted-ips"></a>IPs de confiança

IPs de confiança permite aos utilizadores ignorar o Azure a autenticação Multifator para pedidos de Web site proveniente de endereços IP específicos ou sub-redes. Por exemplo, poderá aos utilizadores de isenção fiscal a partir do Azure a autenticação Multifator enquanto o registo na partir do escritório. Para tal, teria especifique a sub-rede do office como uma entrada de IPs de confiança. Para configurar IPs de confiança, utilize o seguinte procedimento:

1. Na secção autenticação do IIS, clique no separador IPs de confiança.
2. Clique em Adicionar... botão.
3. Quando for apresentada a caixa de diálogo Adicionar IPs de confiança, selecione IP único, o intervalo de IP ou botão de opção sub-rede.
4. ntral o endereço IP, intervalo de endereços IP ou sub-rede que deve ser whitelisted. Se introduzir uma sub-rede, selecione a máscara de rede adequada e clique em OK. Agora foi adicionada à lista branca.
