<properties
    pageTitle="Utilizar o Azure MFA Server com o AD FS 2.0 | Microsoft Azure"
    description="Esta é a página de autenticação Multifator Azure que descreve como começar com o Azure MFA e AD FS 2.0."
    services="multi-factor-authentication"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor="yossib"/>

<tags
    ms.service="multi-factor-authentication"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/14/2016"
    ms.author="kgremban"/>

# <a name="secure-cloud-and-on-premises-resources-using-azure-multi-factor-authentication-server-with-ad-fs-20"></a>Proteger os recursos na nuvem e no local com o servidor de autenticação Multifator Azure AD FS 2.0

Este artigo é para as organizações que estão federadas com o Azure Active Directory e pretende proteger os recursos que estão no local ou na nuvem. Proteger os recursos utilizando o servidor de autenticação Multifator do Azure e configurá-lo para trabalhar com o AD FS, para que a verificação de dois passos é acionada para pontos finais de valor elevado.

Esta documentação abrange com o servidor de autenticação Multifator do Azure AD FS 2.0.  Obter mais informações sobre como [proteger o nuvem e no local recursos utilizando o servidor de autenticação Multifator Azure com o Windows Server 2012 R2 AD FS](multi-factor-authentication-get-started-adfs-w2k12.md).


## <a name="secure-ad-fs-20-with-a-proxy"></a>Seguro AD FS 2.0 com um proxy
Para proteger o AD FS 2.0 com um proxy, instale o servidor de autenticação Multifator Azure no servidor ADFS proxy e configure o servidor.

### <a name="configure-iis-authentication"></a>Configurar a autenticação IIS

1. Dentro do servidor de autenticação Multifator Azure, clique no ícone de **Autenticação do IIS** no menu à esquerda.
2. Clique no separador **Baseadas em formulários** .
3. Clique na **Adicionar....** botão.
<center>![Programa de configuração](./media/multi-factor-authentication-get-started-adfs-adfs2/setup1.png)</center>
4. Detetar automaticamente o nome de utilizador, palavra-passe e variáveis de domínio, introduza o URL de início de sessão (por exemplo, https://sso.contoso.com/adfs/ls) dentro da caixa de diálogo Auto-Configure Form-Based site e clique em OK.
5. Selecione a caixa **exigir autenticação Multifator do Azure utilizador corresponder** se todos os utilizadores tenham sido ou serão importados no servidor e sujeito a verificação de dois passos. Se um número de utilizadores significativo ainda não foram importado no servidor e/ou será excluir da verificação de dois passos, deixe a caixa desmarcada. Para obter informações adicionais sobre esta funcionalidade, consulte o ficheiro de ajuda.
6. Se as variáveis de página não podem ser detectadas automaticamente, clique na **Especificar manualmente...** botão na caixa de diálogo Auto-Configure Form-Based Web site.
7. Na caixa de diálogo Add Form-Based Web site, introduza o URL para a página de início de sessão ADFS no campo URL submeter (como https://sso.contoso.com/adfs/ls) e introduza um nome da aplicação (opcional). O nome da aplicação aparece em relatórios de autenticação Multifator de Azure e pode ser apresentado dentro de mensagens de autenticação SMS ou a aplicação Mobile. Consulte o ficheiro de ajuda para obter mais informações sobre o URL submeter.
8. Definir o formato de pedido para "Publicar, ou obtenha."
9. Introduza o nome de utilizador variável (ctl00$ ContentPlaceHolder1$ UsernameTextBox) e a variável de palavra-passe (ctl00$ ContentPlaceHolder1$ PasswordTextBox). Se a sua página de início de sessão baseadas em formulários apresenta uma caixa de texto do domínio, introduza também a variável de domínio. Poderá ter navegar para a página de início de sessão num browser, com o botão direito na página e selecione **Ver origem** para encontrar os nomes das caixas de entrada dentro da página de início de sessão.
10. Selecione a caixa **exigir autenticação Multifator do Azure utilizador corresponder** se todos os utilizadores tenham sido ou serão importados no servidor e sujeito a verificação de dois passos. Se um número de utilizadores significativo ainda não foram importado no servidor e/ou será excluir da verificação de dois passos, deixe a caixa desmarcada.
<center>![Programa de configuração](./media/multi-factor-authentication-get-started-adfs-adfs2/manual.png)</center>
11. Clique em **Avançadas...** botão para rever as definições avançadas. Pode configurar definições, incluindo a capacidade para selecionar um ficheiro de página personalizado negação, para colocar em cache autenticação efetuada com êxito para o Web site utilizando cookies e para selecionar como autenticar as credenciais principais.
12. Uma vez que o servidor de proxy ADFS não é provável que aderiu para o domínio, pode utilizar LDAP para ligar à sua controlador de domínio para o utilizador a importação e pré-autenticação. Na caixa de diálogo Advanced Form-Based Web site, clique no separador **Principal de autenticação** e selecione **LDAP vincular** para o tipo de autenticação de pré-autenticação.
13. Quando tiver terminado, clique no botão **OK** para regressar à caixa de diálogo Add Form-Based Web site. Consulte o ficheiro de ajuda para obter mais informações sobre as definições avançadas.
14. Clique no botão **OK** para fechar a caixa de diálogo.
15. Assim que as variáveis de URL e página foram detetadas ou introduzidas, apresenta os dados de Web site no painel de baseadas em formulários.
16. Clique no separador **Módulo nativas** e selecione o servidor, o Web site que o proxy ADFS está a ser executado em (como "Web Site predefinido") ou a aplicação de proxy ADFS (como "ls" em "adfs") para ativar o IIS Plug-in no nível pretendido.
17. Clique na caixa de **autenticação do IIS ativar** na parte superior do ecrã.
18. A autenticação do IIS agora está ativada.

### <a name="configure-directory-integration"></a>Configurar integração de diretórios

Com permissão para autenticação do IIS, mas para efetuar a pré-autenticação para o Active Directory (AD) através do LDAP tem de configurar a ligação LDAP o controlador de domínio.

1. Clique no ícone de **Integração de diretórios** .
2. No separador Definições, selecione o botão de opção **configuração LDAP específica de utilização** .
<center>![Programa de configuração](./media/multi-factor-authentication-get-started-adfs-adfs2/ldap1.png)</center>
3. Clique na **Editar...** botão.
4. Na caixa de diálogo Editar LDAP configuração, preencha os campos com as informações necessárias para ligar para o controlador de domínio do AD. Descrições dos campos estão incluídas na tabela abaixo. Esta informação também é incluída no ficheiro de ajuda de servidor de autenticação Multifator Azure.
5. Teste a ligação LDAP ao clicar no botão de **teste** .
<center>![Programa de configuração](./media/multi-factor-authentication-get-started-adfs-adfs2/ldap2.png)</center>
6. Se o teste de ligação LDAP foi efetuada com êxito, clique no botão **OK** .

### <a name="configure-company-settings"></a>Configurar definições de empresa

1. Em seguida, clique no ícone **Definições da empresa** e selecione o separador **Resolução do nome de utilizador** .
2. Selecione o botão de opção **utilizar LDAP atributo de identificador exclusivo para liste correspondente** .
3. Se os utilizadores introduzirem o respetivo nome de utilizador no formato "domínio \ nomedeutilizador", o servidor tem de ser possível faixa o domínio desativar o nome de utilizador quando cria a consulta LDAP. Que pode ser feito através de uma definição de registo.
4. Abra o editor de registo e vá para HKEY_LOCAL_MACHINE/SOFTWARE/Wow6432Node/positivo redes/PhoneFactor num servidor de 64 bits. Se num servidor de 32 bits, demorar "Wow6432Node" fora do caminho. Criar uma chave de registo DWORD denominada "UsernameCxz_stripPrefixDomain" e defina o valor para 1. Autenticação Multifator Azure é agora proteger o proxy ADFS.

Certifique-se de que os utilizadores foram importados a partir do Active Directory no servidor. Consulte a [secção IPs de confiança](#trusted-ips) se gostaria de endereços IP internos lista branca para que a verificação de dois passos não é necessária ao iniciar sessão para o Web site a partir dessas localizações.

<center>![Programa de configuração](./media/multi-factor-authentication-get-started-adfs-adfs2/reg.png)</center>

## <a name="ad-fs-20-direct-without-a-proxy"></a>AD FS 2.0 direcionar sem um proxy

Pode proteger o AD FS quando não é utilizado o proxy do AD FS. Instale o servidor de autenticação Multifator Azure no servidor ADFS e configure o servidor pelos seguintes passos:

1. Dentro do servidor de autenticação Multifator Azure, clique no ícone de **Autenticação do IIS** no menu à esquerda.
2. Clique no separador **HTTP** .
3. Clique na **Adicionar....** botão.
4. Na caixa de diálogo Adicionar Base URL, introduza o URL para o Web site ADFS onde autenticação HTTP (como https://sso.domain.com/adfs/ls/auth/integrated) para o campo de URL da Base é executada. Em seguida, introduza um nome da aplicação (opcional). O nome da aplicação aparece em relatórios de autenticação Multifator de Azure e pode ser apresentado dentro de mensagens de autenticação SMS ou a aplicação Mobile.
5. Se pretender, ajuste o tempo limite Idle e máximo vezes sessão.
6. Selecione a caixa **exigir autenticação Multifator do Azure utilizador corresponder** se todos os utilizadores tenham sido ou serão importados no servidor e sujeito a verificação de dois passos. Se um número de utilizadores significativo ainda não foram importado no servidor e/ou será excluir da verificação de dois passos, deixe a caixa desmarcada. Para obter informações adicionais sobre esta funcionalidade, consulte o ficheiro de ajuda.
7. Selecione a caixa de cache de cookies, se pretender.
<center>![Programa de configuração](./media/multi-factor-authentication-get-started-adfs-adfs2/noproxy.png)</center>
8. Clique no botão **OK** .
9. Clique no separador **Módulo nativas** e selecione o servidor, o Web site (como "Web Site predefinido") ou a aplicação ADFS (por exemplo, "ls" em "adfs") para ativar o IIS Plug-in no nível pretendido.
10. Clique na caixa de **autenticação do IIS ativar** na parte superior do ecrã. Autenticação Multifator Azure é agora proteger ADFS.

Certifique-se de que os utilizadores foram importados a partir do Active Directory no servidor. Se gostaria de endereços IP internos lista branca para que a verificação de dois passos não é necessária ao iniciar sessão para o Web site a partir dessas localizações, consulte a secção de IPs de confiança.


## <a name="trusted-ips"></a>IPs de confiança
IPs de confiança permitem aos utilizadores ignorar o Azure a autenticação Multifator para pedidos de Web site com origem específico endereços IP ou sub-redes. Por exemplo, poderá aos utilizadores excluídos da verificação de dois passos quando iniciar sessão a partir do office. Para que isto, teria de especificar sub-rede office como uma entrada de IPs de confiança.

### <a name="to-configure-trusted-ips"></a>Para configurar IPs de confiança


1. Na secção autenticação do IIS, clique no separador **IPs de confiança** .
1. Clique na **Adicionar....** botão.
1. Quando for apresentada a caixa de diálogo Adicionar IPs de confiança, selecione um dos botões de opção **IP único**, **intervalo de IP**ou **sub-rede** .
1. Introduza o endereço IP, intervalo de endereços IP ou sub-rede que deve ser whitelisted. Se introduzir uma sub-rede, selecione a máscara de rede adequada e clique no botão **OK** . Endereços IP de confiança agora foi adicionado.


<center>![Programa de configuração](./media/multi-factor-authentication-get-started-adfs-adfs2/trusted.png)</center>
