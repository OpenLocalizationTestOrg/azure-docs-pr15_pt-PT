<properties 
    pageTitle="Implementar o portal do utilizador para o servidor de autenticação Multifator do Azure"
    description="Esta é a página de autenticação multifator Azure que descreve como introdução ao Azure MFA e o portal de utilizador."
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
    ms.date="08/15/2016"
    ms.author="kgremban"/>

# <a name="deploying-the-user-portal-for-the-azure-multi-factor-authentication-server"></a>Implementar o portal do utilizador para o servidor de autenticação Multifator do Azure

Portal do utilizador permite ao administrador instalar e configurar o Portal de utilizador do Azure Multifator autenticação. O Portal de utilizador é um web site do IIS que permite aos utilizadores para se inscrever no Azure a autenticação Multifator e manter as respetivas contas. Um utilizador pode alterar o número de telefone, alterar o seu PIN ou ignorar Azure a autenticação Multifator durante as seguinte início de sessão no.

Os utilizadores irão iniciar sessão no Portal do utilizador com as respetivas normal nome de utilizador e palavra-passe e irá concluir uma chamada de autenticação Multifator de Azure ou responder a perguntas de segurança para concluir a sua autenticação. Se for permitida inscrição de utilizador, um utilizador irá configurar o número de telefone e o PIN a primeira vez que iniciarem sessão Portal do utilizador.

Administradores do Portal de utilizador podem ser configure e concedidos permissão para adicionar novos utilizadores e atualizar utilizadores existentes.

<center>![Programa de configuração](./media/multi-factor-authentication-get-started-portal/install.png)</center>

## <a name="deploying-the-user-portal-on-the-same-server-as-the-azure-multi-factor-authentication-server"></a>Implementar o portal de utilizador no mesmo servidor como o servidor de autenticação Multifator do Azure

As pré-requisitos seguintes são necessários para instalar o Portal de utilizadores no mesmo servidor que o servidor de autenticação Multifator Azure:

- IIS tem de estar instalado incluindo asp.net e compatibilidade de base meta IIS 6 (para o IIS 7 ou superior)
- Com sessão iniciada no utilizador tem de ter os direitos de administrador para o computador e o domínio se aplicável.  Isto acontece porque a conta tem permissões para criar grupos de segurança do Active Directory.

### <a name="to-deploy-the-user-portal-for-the-azure-multi-factor-authentication-server"></a>Para implementar o portal do utilizador para o servidor de autenticação Multifator do Azure

1. Dentro do servidor de autenticação Multifator Azure: clique ícone do Portal de utilizador no menu à esquerda, clique em botão instalar Portal de utilizador.
1. Clique em seguinte.
1. Clique em seguinte.
1. Se o computador está associado a um domínio e a configuração do Active Directory para proteger a comunicação entre o Portal de utilizador e o serviço de autenticação Multifator de Azure é incompleta, o passo do Active Directory será apresentado. Clique no botão seguinte para concluir esta configuração automaticamente.
1. Clique em seguinte.
1. Clique em seguinte.
1. Clique em Fechar.
1. Abra um browser web a partir de qualquer computador e navegue para o URL onde foi instalado o Portal de utilizador (por exemplo, https://www.publicwebsite.com/MultiFactorAuth). Certifique-se de que são apresentados sem certificado avisos ou erros.

<center>![Programa de configuração](./media/multi-factor-authentication-get-started-portal/portal.png)</center>

## <a name="deploying-the-azure-multi-factor-authentication-server-user-portal-on-a-separate-server"></a>Implementar o Portal de utilizador do servidor de autenticação Multifator Azure num servidor separado

Para poder utilizar a aplicação de autenticação Multifator Azure, seguem-se necessário para que a aplicação com êxito possam comunicar com o Portal de utilizador:

Consulte o artigo requisitos de Software de Hardware e para requisitos de hardware e software:

- Tem de utilizar o v 6.0 ou superior do servidor de autenticação Multifator do Azure.
- Portal de utilizador tem de estar instalado num servidor web acesso à Internet a executar o Microsoft® informações de serviços Internet (IIS) 6. x, IIS 7. x ou superior.
- Quando utilizar o IIS 6. x, certifique-se de ASP.NET v2.0.50727 está instalada, registados e configurada para permitido.
- Serviços de função requeridos quando utilizar o IIS 7. x ou superior incluir ASP.NET e IIS 6 Metabase compatibilidade.
- Portal de utilizador deve ser protegido com um certificado SSL.
- O Azure Multifator autenticação Web serviço SDK tem de estar instalado no IIS 6. x, IIS 7. x ou superior no servidor que o servidor de autenticação Multifator Azure estiver instalado no.
- O Azure Multifator autenticação Web serviço SDK tem de estar protegido com um certificado SSL.
- Portal de utilizador tem de conseguir ligar-se para o SDK de serviço Web de autenticação do Azure Multifator através de SSL.
- Portal de utilizador tem de ser possível autenticar o Azure Multifator autenticação Web serviço SDK do utilizando as credenciais de uma conta de serviço que é um membro de um grupo de segurança denominado "PhoneFactor administradores". Esta conta de serviço e grupo existirem no Active Directory que o servidor de autenticação Multifator Azure está a ser executado num servidor façam parte de um domínio. Esta conta de serviço e o grupo de existir localmente no servidor de autenticação Multifator do Azure se não estiver associado a um domínio.

Instalar o portal de utilizador num servidor que não seja o servidor de autenticação Multifator Azure requer os três passos seguintes:

1. Instalar o serviço web SDK
2. Instalar o portal do utilizador
3. Configurar as definições de Portal de utilizador no servidor do Azure autenticação Multifator


### <a name="install-the-web-service-sdk"></a>Instalar o serviço web SDK

Se o Azure Multifator autenticação Web serviço SDK já não estiver instalado no servidor de autenticação Multifator do Azure, aceda a esse servidor e abra o servidor de autenticação Multifator Azure. Clique no ícone de SDK de serviço Web, clique em instalar Web serviço SDK... botão e siga as instruções apresentadas. O SDK do serviço Web tem de estar protegido com um certificado SSL. Há um certificado autoassinado para esse efeito, mas tem de ser importado para o arquivo de "Raiz autoridades de certificação fidedigna" da conta de computador Local no servidor web Portal de utilizador para que-confie esse certificado quando iniciar a ligação SSL.

<center>![Programa de configuração](./media/multi-factor-authentication-get-started-portal/sdk.png)</center>

### <a name="install-the-user-portal"></a>Instalar o portal do utilizador

Antes de instalar o portal de utilizador num servidor separado, tenha em atenção o seguinte procedimento:

- É útil abrir um browser no servidor web acesso à Internet e navegue para o URL do SDK do serviço Web que foi introduzida para o ficheiro da Web. config. Se o browser pode aceder ao serviço web com êxito, deverá pedir-lhe para introduzir as credenciais. Introduza o nome de utilizador e palavra-passe que tenham sido introduzido no ficheiro Web. config exatamente conforme apresentado no ficheiro. Certifique-se de que são apresentados sem certificado avisos ou erros.
- Se uma firewall ou proxy inverso é sentada à frente do servidor de web do Portal de utilizador e a execução de SSL descarregar, pode editar o ficheiro de Web. config Portal de utilizador e adicionar a seguinte chave para o <appSettings> secção para que o Portal do utilizador pode utilizar http em vez de https. <add key="SSL_REQUIRED" value="false"/>

#### <a name="to-install-the-user-portal"></a>Para instalar o portal de utilizador

1. Abra o Explorador do Windows no servidor do servidor de autenticação Multifator Azure e navegue para a pasta onde está instalado o servidor de autenticação Multifator Azure (por exemplo, C:\Program Files\Multi fator autenticação Server). Escolha a versão de 32 bits ou 64 bits do ficheiro de instalação MultiFactorAuthenticationUserPortalSetup conforme adequado para o servidor que será instalada no Portal de utilizador. Copie o ficheiro de instalação para o servidor de acesso à Internet.
2. No servidor web acesso à Internet, o ficheiro de configuração tem de ser executado com direitos de administrador. A forma mais fácil para fazer isto é abrir uma linha de comandos como administrador e navegue para a localização onde o ficheiro de instalação foi copiado.
3. Execute o ficheiro de instalação MultiFactorAuthenticationUserPortalSetup64, altere o nome do Site e directório Virtual se pretender.
4. Depois de concluir a instalação do Portal de utilizador, navegue para C:\inetpub\wwwroot\MultiFactorAuth (ou directório adequado com base no nome do directório virtual) e editar o ficheiro da Web. config.
5. Localize a chave USE_WEB_SERVICE_SDK e altere o valor de false para true. Localize as teclas WEB_SERVICE_SDK_AUTHENTICATION_USERNAME e WEB_SERVICE_SDK_AUTHENTICATION_PASSWORD e defina os valores para o nome de utilizador e palavra-passe da conta de serviço que é um membro da segurança PhoneFactor administradores de grupo (consulte a secção requisitos acima). Certifique-se introduzir o nome de utilizador e palavra-passe entre as aspas no final da linha, (valor = "" / >). Recomenda-se para utilizar um nome de utilizador qualificado (por exemplo, domínio \ nomedeutilizador ou machine\username)
6. Localize a definição de pfup_pfwssdk_PfWsSdk e altere o valor de "http://localhost:4898/PfWsSdk.asmx" para o URL do SDK de serviço Web que está a ser executado no servidor de autenticação Multifator do Azure (por exemplo, https://computer1.domain.local/MultiFactorAuthWebServiceSdk/PfWsSdk.asmx). Uma vez SSL é utilizado para esta ligação, tem de referenciar o SDK do serviço Web ao nome do servidor e não o endereço IP uma vez que o certificado SSL vai ter sido emitido o nome do servidor e o URL utilizado deve corresponder ao nome do certificado. Se o nome do servidor não resolver para um endereço IP do servidor de acesso à Internet, adicione uma entrada ao ficheiro hosts nesse servidor para mapear o nome do servidor de autenticação Multifator Azure para o seu endereço IP. Guarde o ficheiro de Web. config depois foram efetuadas alterações.
7. Se o Web site que o Portal de utilizador foi instalado em (por exemplo, Web Site predefinido) não já foi binded com um certificado assinado publicamente, instale o certificado no servidor se não estiver já instalada, abra o Gestor de IIS e vincular o certificado para o Web site.
8. Abra um browser web a partir de qualquer computador e navegue para o URL onde foi instalado o Portal de utilizador (por exemplo, https://www.publicwebsite.com/MultiFactorAuth). Certifique-se de que são apresentados sem certificado avisos ou erros.



## <a name="configure-the-user-portal-settings-in-the-azure-multi-factor-authentication-server"></a>Configurar as definições do portal de utilizador no servidor de autenticação Multifator Azure
Agora que o portal estiver instalado, tem de configurar o servidor de autenticação Multifator Azure para trabalhar com o portal.

Servidor de autenticação Multifator Azure fornece várias opções para o portal de utilizador.  A tabela seguinte fornece uma lista destas opções e explicações do que são utilizadas para.

Definições do Portal de utilizador|Descrição|
:------------- | :------------- |
URL do Portal de utilizador| Permite-lhe introduzir o URL do onde está a ser alojado o portal.
Autenticação principal| Permite-lhe especificar o tipo de autenticação para utilizar ao iniciar sessão no portal.  Autenticação do Windows, Radius ou LDAP.
Permitir que os utilizadores iniciar sessão|Permite aos utilizadores introduzir um nome de utilizador e palavra-passe na página Iniciar sessão para o portal de utilizador.  Se não estiver selecionada, as caixas de ser cinzento.
Permitir a inscrição do utilizador|Permite que o utilizador para se inscrever no autenticação multifator por levá-los ao ecrã de um programa de configuração que pede-lhes para obter informações adicionais tal como o número de telefone.  Pedir-lhe para cópia de segurança phone permite aos utilizadores especificar um número de telefone secundário.  Linha de comandos para terceiros token JURAMENTO permite aos utilizadores especificar um token de JURAMENTO festa 3º.
Permitir que os utilizadores iniciar o fluxo direto de One-Time| Isto permite aos utilizadores iniciar um fluxo direto único.  Se um conjuntos de utilizador isto o vai demorar afetam da próxima vez que o utilizador inicia sessão.  Linha de comandos para o fluxo direto segundos fornece o utilizador com uma caixa para que estes possam alterar a predefinição de 300 segundos.  Caso contrário, o fluxo direto único apenas é boa ideia para 300 segundos.
Permitir que os utilizadores selecionar método| Permite aos utilizadores especificar o seu método de contacto principal.  Isto pode ser chamada telefónica, mensagem de texto, aplicação móvel ou token JURAMENTO.
Permitir que os utilizadores selecionar o idioma|  Permite ao utilizador alterar o idioma que é utilizado para a chamada telefónica, mensagem de texto, aplicação móvel ou token JURAMENTO.
Permitir que os utilizadores ativar a aplicação móvel| Permite aos utilizadores gerar um código de ativação para concluir o processo de ativação da aplicação móvel que é utilizado com o servidor.  Também pode definir o número de dispositivos, pode ativar esta num.  Entre 1 e 10.
Utilizar perguntas de segurança para contingência|Permite-lhe utilizar perguntas de segurança no caso de falha de autenticação multifator.  Pode especificar o número de perguntas de segurança que têm de ser respondidas com êxito.
Permitir que os utilizadores associar token de JURAMENTO de terceiros| Permite aos utilizadores especificar um token de JURAMENTO de terceiros.
Utilizar o token de JURAMENTO para contingência|Permite a utilização de um token de JURAMENTO no caso de que a autenticação multifator não for bem sucedida.  Também pode especificar o limite de sessão em minutos.
Ativar o registo|Permite a iniciar sessão no portal do utilizador.  Os ficheiros de registo estão localizados na: c:\Programas\Microsoft Files\Multi fator autenticação Server\Logs.

A maioria destas definições são visíveis para o utilizador assim que estiverem ativadas e os sinais de utilizador para o portal de utilizador.

![Definições do portal de utilizador](./media/multi-factor-authentication-get-started-portal/portalsettings.png)



### <a name="to-configure-the-user-portal-settings-in-the-azure-multi-factor-authentication-server"></a>Para configurar as definições de portal de utilizador no servidor de autenticação Multifator do Azure




1. Em servidor de autenticação Multifator o Azure, clique no ícone do Portal de utilizador. No separador Definições, introduza o URL para o Portal de utilizador na caixa de texto URL do Portal de utilizador. Este URL será inserida para e-mails que são enviados para os utilizadores quando são importados para o servidor de autenticação Multifator Azure se a funcionalidade de correio eletrónico ter sido ativada.
2. Selecione as definições que pretende utilizar no Portal do utilizador. Por exemplo, se os utilizadores são permitidos para controlar os seus métodos de autenticação, certifique-se de que permitir que os utilizadores para selecionar método está selecionada juntamente com os métodos podem escolher a partir de.
3. Clique na ligação Ajuda no canto superior direito para obter ajuda Noções sobre qualquer uma das definições apresentadas.

<center>![Programa de configuração](./media/multi-factor-authentication-get-started-portal/config.png)</center>


## <a name="administrators-tab"></a>Separador de administradores
Este separador simplesmente permite-lhe adicionar utilizadores que terão privilégios administrativos.  Quando adicionar um administrador, pode ajustar as permissões que possam receber.  Desta forma, pode ter-se de que só conceder as permissões necessárias para o administrador.  Basta clicar no botão Adicionar e, em seguida, selecione e utilizador e as respetivas permissões e, em seguida, clique em Adicionar.

![Administradores de portais de utilizador](./media/multi-factor-authentication-get-started-portal/admin.png)


## <a name="security-questions"></a>Perguntas de segurança
Este separador permite-lhe especificar as perguntas de segurança que os utilizadores terão de fornecer respostas para se as questões de segurança de utilização para a opção de contingência está selecionada.  Azure Multifator Authenticaton servidor é fornecido com perguntas predefinidos que pode utilizar.  Também pode alterar a ordem ou adicionar as suas próprias perguntas.  Quando adicionar as suas próprias perguntas, pode especificar o idioma que pretende utilizar esses pergunta apareça na também.

![Perguntas de segurança do portal de utilizador](./media/multi-factor-authentication-get-started-portal/secquestion.png)


## <a name="passed-sessions"></a>Sessões passada

## <a name="saml"></a>SAML
Permite-lhe configurar o portal do utilizador para aceitar em afirmações a partir de um fornecedor de identidades utilizando SAML.  Pode especificar a tempo limite sessão, especificar o certificado de verificação e o registo de saída redirecionamento de URL.

![SAML](./media/multi-factor-authentication-get-started-portal/saml.png)

## <a name="trusted-ips"></a>IPs de confiança
Este separador permite-lhe especificar endereços IP únicos ou intervalos de endereços IP que podem ser adicionados para que se um utilizador é iniciar sessão a partir de uma estes endereços IP, em seguida, autenticação multifator é ignorada.

![IPs de confiança do portal de utilizador](./media/multi-factor-authentication-get-started-portal/trusted.png)

## <a name="self-service-user-enrollment"></a>Inscrição de utilizador personalizada
Se pretender que os utilizadores para iniciar sessão e inscrever-se tem de selecionar permitir que os utilizadores início de sessão no e permitir opções de inscrição do utilizador. Lembre-se de que as definições que selecionar serão afeta a experiência de utilizador de início de sessão.

Por exemplo, quando um utilizador inicia sessão Portal do utilizador e clica no botão Iniciar sessão, eles, em seguida, são encaminhados para a página de configuração de utilizador do Azure Multifator autenticação.  Dependendo de como tiver configurado Azure a autenticação Multifator, o utilizador poderá conseguir selecionar o seu método de autenticação.  

Se selecionar o método de autenticação chamadas de voz ou ter sido pré-configurada para utilizar esse método, a página será pedido ao utilizador que introduza o seu número de telefone principal e extensão se aplicável.  Pode igualmente permitidas para introduzir um número de telefone alternativo.  

![IPs de confiança do portal de utilizador](./media/multi-factor-authentication-get-started-portal/backupphone.png)

Se o utilizador for necessária para utilizar um PIN quando autenticam, a página será também pedir-lhes para introduzir um código PIN.  Depois de introduzir os seus números de telefone corretos e PIN (se aplicável), o utilizador clica a ligar-Me agora para autenticar botão.  Autenticação Multifator Azure irá efetuar uma chamada telefónica de autenticação para o número de telefone principal do utilizador.  O utilizador tem atender a chamada e introduza o seu PIN (se aplicável) e prima # para avançar para o próximo passo o processo de inscrição Self.   

Se o utilizador seleciona o método de autenticação de texto SMS ou tenha sido pré-configurada para utilizar esse método, a página será pedido ao utilizador para o seu número de telemóvel.  Se o utilizador for necessária para utilizar um PIN quando autenticam, a página será também pedir-lhes para introduzir um código PIN.  Depois de introduzir o seu número de telefone e o PIN (se aplicável), o utilizador clica o texto-Me agora para autenticar botão.  Autenticação Multifator Azure fará uma autenticação SMS para telemóvel do utilizador.  O utilizador tem de receber o serviço SMS que contém uma aquele-tempo-código de acesso (OTP) e a resposta à mensagem com essa OTP plus do seu PIN, se aplicável) para avançar para o próximo passo o processo de inscrição Self.

![Portal de utilizador SMS](./media/multi-factor-authentication-get-started-portal/text.png)   

Se o utilizador seleciona o método de autenticação da aplicação móvel ou tenha sido pré-configurada para utilizar esse método, a página será pedido ao utilizador para instalar a aplicação de autenticação Multifator de Azure no seu dispositivo e gerar um código de ativação.  Depois de instalar a aplicação de autenticação Multifator de Azure, o utilizador clica no botão gerar código de ativação.    

>[AZURE.NOTE]Para poder utilizar a aplicação de autenticação Multifator de Azure, o utilizador tem de ativar as notificações push para o seu dispositivo.

A página, em seguida, apresenta um código de ativação e um URL juntamente com uma imagem de código de barras.  Se o utilizador for necessária para utilizar um PIN quando autenticam, a página será também pedir-lhes para introduzir um código PIN.  O utilizador introduz o código de ativação e o URL para a aplicação de autenticação Multifator de Azure ou utiliza o código de barras scanner para digitalizar a imagem de código de barras e clica no botão Ativar.    

Após a ativação está concluída, o utilizador clica no botão autenticar-Me agora.  Autenticação Multifator Azure fará uma autenticação para a aplicação móvel do utilizador.  O utilizador deve introduzir o seu PIN (se aplicável) e prima o botão de autenticar na sua aplicação móvel para avançar para o próximo passo o processo de inscrição Self.  


Se os administradores de tem configurado o servidor de autenticação Multifator Azure para recolher segurança perguntas e respostas, o utilizador, em seguida, é encaminhado para a página de perguntas de segurança.  O utilizador tem de selecionar quatro perguntas de segurança e forneça respostas às suas perguntas selecionadas.    

![Perguntas de segurança do portal de utilizador](./media/multi-factor-authentication-get-started-portal/secq.png)  

A inscrição Self de utilizador agora é concluída e o utilizador tem sessão iniciado Portal do utilizador.  Os utilizadores podem iniciar sessão novamente no Portal de utilizador em qualquer altura no futuro para alterar os respetivos números de telefone, PIN, os métodos de autenticação e perguntas de segurança, se for permitido pela suas administradores.
