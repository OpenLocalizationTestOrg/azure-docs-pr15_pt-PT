<properties 
    pageTitle="Introdução ao serviço de Web App Mobile do MFA servidor"
    description="A aplicação de autenticação Multifator Azure oferece uma opção de autenticação de fora da banda adicionais.  Permite que o servidor MFA para utilizar as notificações push para os utilizadores."
    services="multi-factor-authentication"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor="curtland"/>

<tags
    ms.service="multi-factor-authentication"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="08/04/2016"
    ms.author="kgremban"/>

# <a name="getting-started-the-mfa-server-mobile-app-web-service"></a>Introdução ao serviço de Web App Mobile do MFA servidor

A aplicação de autenticação Multifator Azure oferece uma opção de autenticação de fora da banda adicionais. Em vez de colocar uma chamada telefónica automatizada ou SMS para o utilizador durante o início de sessão, Azure a autenticação Multifator envia uma notificação à aplicação de autenticação Multifator do Azure no smartphone ou tablet do utilizador. O utilizador simplesmente toca em "Autenticar" (ou introduz um PIN e toca em "Autenticar") na aplicação para iniciar sessão.

Para poder utilizar a aplicação de autenticação Multifator Azure, seguem-se necessário, para que a aplicação com êxito possam comunicar com o serviço móvel do Web App:

- Consulte o artigo requisitos de Software de Hardware e para requisitos de hardware e software
- Tem de utilizar o v 6.0 ou superior do servidor de autenticação Multifator do Azure
- Serviço móvel do Web App tem de estar instalado num servidor web acesso à Internet a executar o Microsoft® informações de serviços Internet (IIS) IIS 7. x ou superior.  Para obter mais informações sobre IIS Consulte [IIS.NET](http://www.iis.net/).
- Certifique-se de ASP.NET v4.0.30319 está instalada, registados e configurada para permitido
- Serviços de função necessários incluem ASP.NET e IIS 6 Metabase compatibilidade
- Serviço móvel do Web App tem de ser acessível através de um URL público
- Serviço móvel do Web App tem de estar protegido com um certificado SSL.
- O Azure Multifator autenticação Web serviço SDK tem de estar instalado no IIS 7. x ou superior no servidor que o servidor de autenticação Multifator do Azure
- O Azure Multifator autenticação Web serviço SDK tem de estar protegido com um certificado SSL.
- Serviço móvel do Web App tem de conseguir ligar-se para o SDK de serviço Web de autenticação do Azure Multifator através de SSL
- Serviço móvel do Web App tem de ser possível autenticar o Azure Multifator autenticação Web serviço SDK do utilizando as credenciais de uma conta de serviço que é um membro de um grupo de segurança denominado "PhoneFactor administradores". Esta conta de serviço e grupo existirem no Active Directory que o servidor de autenticação Multifator Azure está a ser executado num servidor façam parte de um domínio. Esta conta de serviço e o grupo de existir localmente no servidor de autenticação Multifator do Azure se não estiver associado a um domínio.


Instalar o portal de utilizador num servidor que não seja o servidor de autenticação Multifator Azure requer os três passos seguintes:

1. Instalar o serviço web SDK
2. Instalar o serviço web de aplicação móvel
3. Configurar as definições de aplicação móvel do servidor de autenticação Multifator Azure
4. Ativar a aplicação de autenticação Multifator Azure para utilizadores finais

## <a name="install-the-web-service-sdk"></a>Instalar o serviço web SDK

Se o Azure Multifator autenticação Web serviço SDK já não estiver instalado no servidor de autenticação Multifator do Azure, aceda a esse servidor e abra o servidor de autenticação Multifator Azure. Clique no ícone de SDK de serviço Web, clique em instalar Web serviço SDK... botão e siga as instruções apresentadas. O SDK do serviço Web tem de estar protegido com um certificado SSL. Há um certificado autoassinado para esse efeito, mas tem de ser importado para o arquivo de "Raiz autoridades de certificação fidedigna" da conta de computador Local no servidor web Portal de utilizador para que-confie esse certificado quando iniciar a ligação SSL.

<center>![Programa de configuração](./media/multi-factor-authentication-get-started-server-webservice/sdk.png)</center>

## <a name="install-the-mobile-app-web-service"></a>Instalar o serviço web de aplicação móvel
Antes de instalar o serviço web de aplicação móvel, tenha em atenção o seguinte procedimento:

- Se o Portal de utilizador do Azure Multifator autenticação já estiver instalado no servidor de acesso à Internet, o nome de utilizador, a palavra-passe e o URL para o SDK do serviço Web podem ser copiadas a partir de ficheiro de Web. config o Portal de utilizador.
- É útil abrir um browser no servidor web acesso à Internet e navegue para o URL do SDK do serviço Web que foi introduzida para o ficheiro da Web. config. Se o browser pode aceder ao serviço web com êxito, deverá pedir-lhe para introduzir as credenciais. Introduza o nome de utilizador e palavra-passe que tenham sido introduzido no ficheiro Web. config exatamente conforme apresentado no ficheiro. Certifique-se de que são apresentados sem certificado avisos ou erros.
- Se uma firewall ou proxy inverso é sentada à frente do servidor de web do serviço Web App Mobile e realizar SSL descarregar, que pode editar o ficheiro de Web. config do serviço Web App Mobile ou adicione a seguinte chave para o <appSettings> secção para que o serviço móvel do Web App possam utilizar http em vez de https. No entanto SSL ainda é necessária a partir da aplicação móvel para o proxy de firewall/inversa. <add key="SSL_REQUIRED" value="false"/>

### <a name="to-install-the-mobile-app-web-service"></a>Para instalar o serviço web de aplicação móvel

<ol>
<li>Abra o Explorador do Windows no servidor de autenticação Multifator do Azure e navegue para a pasta onde está instalado o servidor de autenticação Multifator Azure (por exemplo, C:\Program Files\Azure autenticação Multifator). Escolha a versão de 32 bits ou 64 bits do Azure Multifator AuthenticationPhoneAppWebServiceSetup instalação ficheiro conforme adequado para o servidor que será instalado no serviço móvel do Web App. Copie o ficheiro de instalação para o servidor de acesso à Internet.</li>

<li>No servidor web acesso à Internet, o ficheiro de configuração tem de ser executado com direitos de administrador. A forma mais fácil para fazer isto é abrir uma linha de comandos como administrador e navegue para a localização onde o ficheiro de instalação foi copiado.</li>  

<li>Execute o ficheiro de instalação Multifator AuthenticationMobileAppWebServiceSetup, altere o Site se pretender e altere o diretório Virtual para um nome abreviado tal como "PA". É recomendado um nome curto directório virtual uma vez que os utilizadores têm de introduzir o URL do serviço móvel aplicação Web para o dispositivo móvel durante a ativação.</li>

<li>Depois de concluir a instalação de AuthenticationMobileAppWebServiceSetup de Multifator o Azure, navegue para C:\inetpub\wwwroot\PA (ou directório adequado com base no nome do directório virtual) e editar o ficheiro da Web. config.</li>  

<li>Localize as teclas WEB_SERVICE_SDK_AUTHENTICATION_USERNAME e WEB_SERVICE_SDK_AUTHENTICATION_PASSWORD e defina os valores para o nome de utilizador e palavra-passe da conta de serviço que é um membro da segurança PhoneFactor administradores de grupo (consulte a secção requisitos acima). Isto pode ser a mesma conta a ser utilizada como a identidade do Portal de utilizador do Azure Multifator autenticação se que tiver sido instalado anteriormente. Certifique-se introduzir o nome de utilizador e palavra-passe entre as aspas no final da linha, (valor = "" / >). Recomenda-se para utilizar um nome de utilizador qualificado (por exemplo, domínio \ nomedeutilizador ou machine\username).</li>  

<li>Localize a definição de aplicação Web Service_pfwssdk_PfWsSdk pfMobile e altere o valor de "http://localhost:4898/PfWsSdk.asmx" para o URL do SDK de serviço Web que está a ser executado no servidor de autenticação Multifator do Azure (por exemplo, https://computer1.domain.local/MultiFactorAuthWebServiceSdk/PfWsSdk.asmx). Uma vez SSL é utilizado para esta ligação, tem de referenciar o SDK do serviço Web ao nome do servidor e não o endereço IP uma vez que o certificado SSL vai ter sido emitido o nome do servidor e o URL utilizado deve corresponder ao nome do certificado. Se o nome do servidor não resolver para um endereço IP do servidor de acesso à Internet, adicione uma entrada ao ficheiro hosts nesse servidor para mapear o nome do servidor de autenticação Multifator Azure para o seu endereço IP. Guarde o ficheiro de Web. config depois foram efetuadas alterações.</li>  

<li>Se o Web site que o serviço móvel do Web App foi instalado em (por exemplo, Web Site predefinido) não já foi binded com um certificado assinado publicamente, instale o certificado no servidor se não estiver já instalada, abra o Gestor de IIS e vincular o certificado para o Web site.</li>  

<li>Abra um browser web a partir de qualquer computador e navegue para o URL onde foi instalado o serviço móvel do Web App (por exemplo, https://www.publicwebsite.com/PA). Certifique-se de que são apresentados sem certificado avisos ou erros.</li>

### <a name="configure-the-mobile-app-settings-in-the-azure-multi-factor-authentication-server"></a>Configurar as definições de aplicação móvel do servidor de autenticação Multifator Azure
Agora que o serviço web de aplicação móvel estiver instalado, tem de configurar o servidor de autenticação Multifator Azure para trabalhar com o portal.

#### <a name="to-configure-the-mobile-app-settings-in-the-azure-multi-factor-authentication-server"></a>Para configurar as definições de aplicação móvel no servidor de autenticação Multifator do Azure

1. Em servidor de autenticação Multifator o Azure, clique no ícone do Portal de utilizador. Se os utilizadores são permitidos para controlar os seus métodos de autenticação, no separador Definições, em permitir que os utilizadores para selecionar método, selecione aplicação Mobile. Sem esta funcionalidade ativada, os utilizadores finais serão exigidos que contactar o suporte para concluir a ativação para a aplicação Mobile.
2. Verifique os permitir que os utilizadores para ativar a caixa de aplicação Mobile.
3. Selecione a caixa permitir a inscrição do utilizador.
4. Clique no ícone de aplicação Mobile.
5. Introduza o URL a ser utilizado com o directório virtual que foi criado ao instalar o AuthenticationMobileAppWebServiceSetup Multifator Azure. Um nome de conta podem ser introduzido no espaço fornecido. Este nome de empresa será apresentada a aplicação móvel. Se deixado em branco, o nome do seu fornecedor de autenticação Multifator criado no Portal de gestão do Azure será apresentado.



<center>![Programa de configuração](./media/multi-factor-authentication-get-started-server-webservice/mobile.png)</center>
