<properties 
    pageTitle="Criar uma aplicação Web no serviço de aplicação do Azure utilizando o SDK do Azure para Java" 
    description="Saiba como criar uma aplicação Web no serviço de aplicação do Azure através de programação utilizando o SDK do Azure para Java." 
    tags="azure-classic-portal"
    services="app-service\web" 
    documentationCenter="Java" 
    authors="donntrenton" 
    manager="wpickett" 
    editor="jimbe"/>

<tags 
    ms.service="multiple" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="Java" 
    ms.topic="article" 
    ms.date="02/25/2016" 
    ms.author="v-donntr"/>


# <a name="create-a-web-app-in-azure-app-service-using-the-azure-sdk-for-java"></a>Criar uma aplicação Web no serviço de aplicação do Azure utilizando o SDK do Azure para Java

<!-- Azure Active Directory workflow is not yet available on the Azure Portal -->

## <a name="overview"></a>Descrição geral

Este tutorial mostra como criar um SDK Azure para aplicação Java que cria uma aplicação Web na [Aplicação de serviço de Azure][], em seguida, implementar uma aplicação para o mesmo. Consiste em duas partes:

- Parte 1 demonstra como criar uma aplicação Java que cria uma aplicação web.
- Parte 2 demonstra como criar uma simple JSP "Olá mundo" aplicação, em seguida, utilizar um FTP cliente para implementar o código para a aplicação de serviço.


## <a name="prerequisites"></a>Pré-requisitos

### <a name="software-installations"></a>Instalações do software

O código da aplicação AzureWebDemo neste artigo foi escrito utilizando Azure Java SDK 0.7.0, que pode instalar utilizando o [Web plataforma Installer][] (WebPI). Além disso, certifique-se utilizar a versão mais recente do [Azure Toolkit para Eclipse][]. Depois de instalar o SDK, actualizar dependências no projeto Eclipse executando **Atualizar índice remissivo** no **Maven repositórios**, em seguida, voltar a adicionar a versão mais recente de cada embalagem na janela do **dependências** . Pode verificar a versão do seu software instalado no Eclipse ao clicar em **Ajuda > detalhes da instalação**; que deve ter, pelo menos, as seguintes versões:

- Pacote do Microsoft Azure para bibliotecas de para Java 0.7.0.20150309
- Eclipse IDE para programadores de EE Java 4.4.2.20150219


### <a name="create-and-configure-cloud-resources-in-azure"></a>Criar e configurar os recursos da nuvem no Azure

Antes de começar este procedimento, tem de ter uma subscrição ativa do Azure e configurar o um Active Directory (AD) no Azure predefinido.


### <a name="create-an-active-directory-ad-in-azure"></a>Criar um Active Directory (AD) no Azure

Se ainda não tiver um Active Directory (AD) na sua subscrição do Azure, inicie sessão no [portal clássica Azure][] com a sua conta Microsoft. Se tiver múltiplas subscrições, clique em **subscrições** e selecione o directório predefinido para a subscrição que pretende utilizar para este projeto. Em seguida, clique em **Aplicar** a mudar para essa vista de subscrição.

1. Selecione **Do Active Directory** no menu à esquerda. **Clique em Novo > diretório > Criar personalizado**.

2. No **Directório de adicionar**, selecione **Criar novo directório**.

3. Em **nome**, introduza um nome de diretório.

4. No **domínio**, introduza um nome de domínio. Este é um nome de domínio básicas que vem incluído por predefinição com o seu diretório; tiver o formulário `<domain_name>.onmicrosoft.com`. Pode atribuir um nome-la com base no nome do directório ou outro nome do domínio que é o proprietário. Mais tarde, pode adicionar outro nome do domínio que utiliza a sua organização já.

5. **País ou região**, selecione a região.

Para mais informações sobre o AD, consulte o artigo [o que é um diretório do Azure AD][]?


### <a name="create-a-management-certificate-for-azure"></a>Criar um certificado de gestão do Azure

O SDK do Azure para Java utiliza certificados de gestão para autenticar com subscrições do Azure. Estes são x. 509 v3 certificados que utilizar para autenticar uma aplicação de cliente que utiliza a API de gestão do serviço para funcionar em nome do proprietário da subscrição para gerir os recursos de subscrição.

O código neste procedimento utiliza um certificado autoassinado para autenticar com Azure. Para este procedimento, tem de criar um certificado e carregue-o para o [portal clássica Azure][] prévia. Isso envolve os seguintes passos:

- Gerar um ficheiro PFX que representa o certificado de cliente e guardá-lo localmente.
- Gera um certificado de gestão (ficheiro CER) do ficheiro PFX.
- Carregue o ficheiro CER à sua subscrição do Azure.
- Converta o ficheiro PFX JKS, porque Java utiliza esse formato para autenticar utilizando certificados.
- Escreva código de autenticação a aplicação, que se refere ao ficheiro JKS local.

Quando concluir este procedimento, o certificado CER residirão na sua subscrição do Azure e o certificado JKS residirão na unidade local. Para mais informações sobre certificados de gestão, consulte o artigo [criar e carregar um certificado de gestão de Azure][].


#### <a name="create-a-certificate"></a>Criar um certificado

Para criar o seu próprio certificado autoassinado, abra uma consola de comando no seu sistema operativo e execute os seguintes comandos.

> **Nota:**  Computador no qual está a executar este comando tem de ter JDK instalado. Além disso, o caminho para o keytool depende a localização na qual instalou o JDK. Para mais informações, consulte o artigo [chave e ferramenta de gestão de certificado (keytool)][] na documentos online Java.

Para criar o ficheiro. pfx:

    <java-install-dir>/bin/keytool -genkey -alias <keystore-id>
     -keystore <cert-store-dir>/<cert-file-name>.pfx -storepass <password>
     -validity 3650 -keyalg RSA -keysize 2048 -storetype pkcs12
     -dname "CN=Self Signed Certificate 20141118170652"

Para criar o ficheiro. cer:

    <java-install-dir>/bin/keytool -export -alias <keystore-id>
     -storetype pkcs12 -keystore <cert-store-dir>/<cert-file-name>.pfx
     -storepass <password> -rfc -file <cert-store-dir>/<cert-file-name>.cer

em que:

- `<java-install-dir>`é o caminho para o directório no qual instalou o Java.
- `<keystore-id>`é o identificador de entrada keystore (por exemplo, `AzureRemoteAccess`).
- `<cert-store-dir>`é o caminho para o diretório na qual pretende armazenar certificados (por exemplo `C:/Certificates`).
- `<cert-file-name>`é o nome do ficheiro de certificado (por exemplo `AzureWebDemoCert`).
- `<password>`a palavra-passe, que selecione proteger o certificado; tem de ser, pelo menos, 6 carateres de comprimento. Não pode introduzir nenhuma palavra-passe, apesar de não é recomendado.
- `<dname>`é o nome distinto x. 500 de ser associadas a alias e é utilizada como os campos emissor e assunto no certificado autoassinado.

Para mais informações, consulte o artigo [criar e carregar um certificado de gestão de Azure][].


#### <a name="upload-the-certificate"></a>Carregar o certificado

Para carregar um certificado autoassinado para Azure, vá para a página de **Definições** no portal do clássica, em seguida, clique no separador **Gestão de certificados** . Clique em **carregar** na parte inferior da página e navegue para a localização do ficheiro CER que criou.


#### <a name="convert-the-pfx-file-into-jks"></a>Converter o ficheiro PFX JKS

No Windows linha de comandos (a executar como administrador), cd ao diretório que contém os certificados e execute o seguinte comando, onde `<java-install-dir>` é o directório no qual instalou o Java no seu computador:

    <java-install-dir>/bin/keytool.exe -importkeystore
     -srckeystore <cert-store-dir>/<cert-file-name>.pfx
     -destkeystore <cert-store-dir>/<cert-file-name>.jks
     -srcstoretype pkcs12 -deststoretype JKS

1. Quando lhe for pedido, introduza a palavra-passe de keystore de destino; Esta será a palavra-passe para o ficheiro JKS.

2. Quando lhe for pedido, introduza a palavra-passe de keystore de origem; Esta é a palavra-passe que especificou para o ficheiro PFX.

As duas palavras-passe não tem de ser a mesma. Não pode introduzir nenhuma palavra-passe, apesar de não é recomendado.


## <a name="build-a-web-app-creation-application"></a>Criar uma aplicação de criação de Web App

### <a name="create-the-eclipse-workspace-and-maven-project"></a>Criar a área de trabalho Eclipse e Maven projecto

Nesta secção criar uma área de trabalho e de um projeto Maven para a aplicação de criação da aplicação web, com o nome AzureWebDemo.

1. Crie um novo projeto de Maven. Clique em **ficheiro > novo > projeto Maven**. No **Novo projeto de Maven**, selecione **criar um projeto simple** e a **localização de área de trabalho predefinida de utilização**.

2. Na segunda página do **Novo projeto de Maven**, especifique o seguinte:

    - ID de grupo:`com.<username>.azure.webdemo`
    - ID de artefacto: AzureWebDemo
    - Versão: 0.0.1-SNAPSHOT
    - Embalagem: para caixa
    - Nome: AzureWebDemo

    Clique em **Concluir**.

3. Abra ficheiro de pom.xml o novo projeto no Project Explorer. Selecione o separador **dependências** . Como este é um novo projeto, não existem pacotes são apresentados ainda.

4. Abra a vista de Maven repositórios. **Clique em janela > Mostrar a vista > outros > Maven > Maven repositórios** e clique em **OK**. A vista de **Maven repositórios** irão aparecer na parte inferior do IDE.

5. Abrir **Repositórios Global**, com o botão direito do repositório **central** e selecione **Reconstruir Índice**.

    ![][1]
    
    Este passo pode demorar alguns minutos, dependendo da velocidade da sua ligação. Quando recria o índice remissivo, deverá ver os pacotes do Microsoft Azure no repositório **central** de Maven.

6. Em **dependências**, clique em **Adicionar**. No **Grupo Enter ID....** introduza `azure-management`. Selecione os pacotes de gestão de base e gestão de aplicação serviço Web Apps:

        com.microsoft.azure  azure-management
        com.microsoft.azure  azure-management-websites

    > **Nota:** Se estiver a atualizar as dependências após uma nova versão, tem de voltar a adicionar cada uma das dependências nesta lista.
    > Depois clique em **Adicionar** e selecione cada dependência, aparece com o novo número de versão na lista **dependências** .

Clique em **OK**. Os pacotes Azure, em seguida, são apresentados na lista **dependências** .


### <a name="writing-java-code-to-create-a-web-app-by-calling-the-azure-sdk"></a>Escrever o código de Java para criar uma aplicação Web ao contactar o SDK do Azure

Em seguida, escreva o código que chamadas APIs no SDK do Azure para Java para criar o serviço de aplicação web app.

1. Crie uma classe Java conter o código de ponto de entrada principal. No Project Explorer, o botão direito do rato sobre o nó do projeto e selecione **Novo > classe**.

2. No **Novo classe Java**, atribua um nome a classe `WebCreator` e marque a caixa de verificação **público principais void estático** . As seleções deverão aparecer da seguinte forma:

    ![][2]

3. Clique em **Concluir**. O ficheiro de WebCreator.java aparece no Project Explorer.


### <a name="calling-the-azure-api-to-create-an-app-service-web-app"></a>Chamar a API do Azure para criar um aplicação de serviço Web App


#### <a name="add-necessary-imports"></a>Adicionar importações necessárias

Na WebCreator.java, adicione as seguintes importações; Estas importações fornecem acesso a classes nas bibliotecas de gestão para consumir Azure APIs:

    // General imports
    import java.net.URI;
    import java.util.ArrayList;
    
    // Imports for Exceptions
    import java.io.IOException;
    import java.net.URISyntaxException;
    import javax.xml.parsers.ParserConfigurationException;
    import com.microsoft.windowsazure.exception.ServiceException;
    import org.xml.sax.SAXException;
    
    // Imports for Azure App Service management configuration
    import com.microsoft.windowsazure.Configuration;
    import com.microsoft.windowsazure.management.configuration.ManagementConfiguration;
    
    // Service management imports for App Service Web Apps creation
    import com.microsoft.windowsazure.management.websites.*;
    import com.microsoft.windowsazure.management.websites.models.*;
    
    // Imports for authentication
    import com.microsoft.windowsazure.core.utils.KeyStoreType;


#### <a name="define-the-main-entry-point-class"></a>Definir a classe de ponto de entrada principal

Uma vez que é o objetivo da aplicação AzureWebDemo criar uma aplicação do serviço Web App, atribua um nome a classe principal para esta aplicação `WebAppCreator`. Esta classe fornece o código de ponto de entrada principal que liga a API de gestão do serviço de Azure para criar a aplicação web.

Adicione as seguintes definições de parâmetro para a web app e webspace. Terá de fornecer as suas próprias informações de ID e o certificado de subscrição Azure.

    public class WebAppCreator {
    
        // Parameter definitions used for authentication.
        private static String uri = "https://management.core.windows.net/";
        private static String subscriptionId = "<subscription-id>";
        private static String keyStoreLocation = "<certificate-store-path>";
        private static String keyStorePassword = "<certificate-password>";
    
        // Define web app parameter values.
        private static String webAppName = "WebDemoWebApp";
        private static String domainName = ".azurewebsites.net";
        private static String webSpaceName = WebSpaceNames.WESTUSWEBSPACE;
        private static String appServicePlanName = "WebDemoAppServicePlan";

em que:

- `<subscription-id>`é o ID da subscrição Azure na qual pretende criar o recurso.
- `<certificate-store-path>`é o caminho e o nome de ficheiro para o ficheiro JKS no seu diretório de arquivo de certificados local. Por exemplo, `C:/Certificates/CertificateName.jks` para Linux e `C:\Certificates\CertificateName.jks` para Windows.
- `<certificate-password>`é a palavra-passe que especificou quando criou o seu certificado JKS.
- `webAppName`pode ser qualquer nome que escolher; Este procedimento utiliza o nome `WebDemoWebApp`. O nome completo do domínio é a `webAppName` com o `domainName` acrescentado, por isso, neste caso completo do domínio é `webdemowebapp.azurewebsites.net`.
- `domainName`deve ser especificado conforme mostrado acima.
- `webSpaceName`deve ser um dos valores definidos na classe [WebSpaceNames][] .
- `appServicePlanName`deve ser especificado conforme mostrado acima.

> **Nota:** Sempre que executar esta aplicação, tem de alterar o valor de `webAppName` e `appServicePlanName` (ou eliminar a aplicação web no Portal do Azure) antes de executar a aplicação novamente. Caso contrário, execução falhará porque o mesmo recurso já existe no Azure.


#### <a name="define-the-web-creation-method"></a>Definir o método de criação de web

Em seguida, defina um método para criar a aplicação web. Este método, `createWebApp`, especifica os parâmetros do web app e o webspace. Também cria e configura o cliente de gestão de aplicação serviço Web Apps, que é definido pelo objecto [WebSiteManagementClient][] . O cliente de gestão é fundamental para a criação de Web Apps. Fornece serviços RESTful web que permitem que as aplicações gerir aplicações web (efetuar operações, tais como criar, atualizar e eliminar) ao contactar o suporte a API de gestão de serviço.

    private static void createWebApp() throws Exception {

        // Specify configuration settings for the App Service management client.
        Configuration config = ManagementConfiguration.configure(
            new URI(uri),
            subscriptionId,
            keyStoreLocation,  // Path to the JKS file
            keyStorePassword,  // Password for the JKS file
            KeyStoreType.jks   // Flag that you are using a JKS keystore
        );

        // Create the App Service Web Apps management client to call Azure APIs
        // and pass it the App Service management configuration object.
        WebSiteManagementClient webAppManagementClient = WebSiteManagementService.create(config);

        // Create an App Service plan for the web app with the specified parameters.
        WebHostingPlanCreateParameters appServicePlanParams = new WebHostingPlanCreateParameters();
        appServicePlanParams.setName(appServicePlanName);
        appServicePlanParams.setSKU(SkuOptions.Free);
        webAppManagementClient.getWebHostingPlansOperations().create(webSpaceName, appServicePlanParams);

        // Set webspace parameters.
        WebSiteCreateParameters.WebSpaceDetails webSpaceDetails = new WebSiteCreateParameters.WebSpaceDetails();
        webSpaceDetails.setGeoRegion(GeoRegionNames.WESTUS);
        webSpaceDetails.setPlan(WebSpacePlanNames.VIRTUALDEDICATEDPLAN);
        webSpaceDetails.setName(webSpaceName);

        // Set web app parameters.
        // Note that the server farm name takes the Azure App Service plan name.
        WebSiteCreateParameters webAppCreateParameters = new WebSiteCreateParameters();
        webAppCreateParameters.setName(webAppName);
        webAppCreateParameters.setServerFarm(appServicePlanName);
        webAppCreateParameters.setWebSpace(webSpaceDetails);

        // Set usage metrics attributes.
        WebSiteGetUsageMetricsResponse.UsageMetric usageMetric = new WebSiteGetUsageMetricsResponse.UsageMetric();
        usageMetric.setSiteMode(WebSiteMode.Basic);
        usageMetric.setComputeMode(WebSiteComputeMode.Shared);

        // Define the web app object.
        ArrayList<String> fullWebAppName = new ArrayList<String>();
        fullWebAppName.add(webAppName + domainName);
        WebSite webApp = new WebSite();
        webApp.setHostNames(fullWebAppName);

        // Create the web app.
        WebSiteCreateResponse webAppCreateResponse = webAppManagementClient.getWebSitesOperations().create(webSpaceName, webAppCreateParameters);

        // Output the HTTP status code of the response; 200 indicates the request succeeded; 4xx indicates failure.
        System.out.println("----------");
        System.out.println("Web app created - HTTP response " + webAppCreateResponse.getStatusCode() + "\n");

        // Output the name of the web app that this application created.
        String shinyNewWebAppName = webAppCreateResponse.getWebSite().getName();
        System.out.println("----------\n");
        System.out.println("Name of web app created: " + shinyNewWebAppName + "\n");
        System.out.println("----------\n");
    }

O código será o estado HTTP da resposta que indica o sucesso ou o fracasso de saída e se tiver êxito, vai exportar, o nome da aplicação web criado.


#### <a name="define-the-main-method"></a>Definir o método de main()

Forneça o código do método de main() chamadas createWebApp() para criar a aplicação web.

Por fim, chamada `createWebApp` a partir do `main`:

        public static void main(String[] args)
            throws IOException, URISyntaxException, ServiceException,
            ParserConfigurationException, SAXException, Exception {

            // Create web app
            createWebApp();

        }  // end of main()

    }  // end of WebAppCreator class


#### <a name="run-the-application-and-verify-web-app-creation"></a>Execute a aplicação e verifique se a criação de aplicação web

Para verificar que é executada a sua aplicação, clique em **Executar > executar**. Quando a aplicação estiver concluída em execução, deverá ver o resultado seguinte na consola do Eclipse:

    ----------
    Web app created - HTTP response 200
    
    ----------
    
    Name of web app created: WebDemoWebApp
    
    ----------

Inicie sessão no portal clássico do Azure e clique em **Web Apps**. A nova aplicação web deverão aparecer na lista de aplicações Web dentro de alguns minutos.


## <a name="deploying-an-application-to-the-web-app"></a>Implementar uma aplicação para a aplicação Web

Depois de ter executado AzureWebDemo e criado a nova aplicação web, inicie sessão no portal do clássico, clique em **Aplicações Web**e selecione **WebDemoWebApp** na lista de **Aplicações Web** . Na página de dashboard do web app, clique em **Procurar** (ou clique em URL, `webdemowebapp.azurewebsites.net`) para navegar para o mesmo. Irá ver uma página de marcador de posição em branco, porque não existe conteúdo foi publicado do web app ainda.

Seguinte irá criar uma aplicação de "Olá mundo" e implementá-lo para a aplicação web.


### <a name="create-a-jsp-hello-world-application"></a>Criar uma aplicação de JSP Olá mundo

#### <a name="create-the-application"></a>Criar a aplicação

Para demonstrar como implementar uma aplicação para o web, o seguinte procedimento mostra-lhe como criar uma aplicação "Olá mundo" Java simples e carregue-o para a aplicação do serviço Web App que criou a sua aplicação.

1. Clique em **ficheiro > novo > projeto Web dinâmico**. -Lhe o nome `JSPHello`. Não é necessário alterar qualquer das definições nesta caixa de diálogo. Clique em **Concluir**.

    ![][3]

2. No Explorador de projeto, expanda o projecto **JSPHello** , com o botão direito **ConteúdoWeb**, em seguida, clique em **Novo > ficheiro JSP**. Na caixa de diálogo novo ficheiro JSP, o nome do novo ficheiro `index.jsp`. Clique em **seguinte**.

3. Na caixa de diálogo **Seleccionar JSP modelo** , selecione **Novo ficheiro JSP (html)** e clique em **Concluir**.

4. Na index.jsp, adicione o código seguinte na `<head>` e `<body>` marcar secções:

        <head>
          ...
          java.util.Date date = new java.util.Date();
        </head>
    
        <body>
          Hello, the time is <%= date %> 
        </body>


#### <a name="run-the-hello-world-application-in-localhost"></a>Executar a aplicação Olá mundo no localhost

Antes de executar esta aplicação, tem de configurar algumas propriedades.

1. Com o botão direito do projecto **JSPHello** e selecione **Propriedades**.

2. Na caixa de diálogo **Propriedades** : selecione **Java construir caminho**, selecione o separador **ordem e exportar** , verifique a **Biblioteca de sistema JRE**, em seguida, clique em **para cima** para movê-lo para a parte superior da lista.

    ![][4]

3. Também na caixa de diálogo **Propriedades** : selecione **Direccionado Runtimes** e clique em **Novo**.

4. Na caixa de diálogo **Novo ambiente de tempo de execução do servidor** , selecione um servidor como **v 7.0 de Apache Tomcat** e clique em **seguinte**. Na caixa de diálogo **Tomcat Server** , defina o **nome** para `Apache Tomcat v7.0`e configurar o **Directório de instalação Tomcat** ao diretório no qual instalou a versão do servidor de Tomcat que pretende utilizar.

    ![][5]

    Clique em **Concluir**.

5. Em seguida, regresse à página **Direccionado Runtimes** da caixa de diálogo **Propriedades** . Selecione **v 7.0 de Apache Tomcat**, em seguida, clique em **OK**.

    ![][6]

6. No menu Eclipse **Executar** , clique em **Executar**. Na **Executar como** caixa de diálogo, selecione **executar no servidor**. Na caixa de diálogo **executar no servidor** , selecione **Tomcat v 7.0 Server**:

    ![][7]

    Clique em **Concluir**.

7. Quando a aplicação é executada, deverá ver a página de **JSPHello** são apresentados numa janela de localhost no Eclipse (`http://localhost:8080/JSPHello/`), apresentar a seguinte mensagem:

    `Hello World, the time is Tue Mar 24 23:21:10 GMT 2015`


#### <a name="export-the-application-as-a-war"></a>Exportar a aplicação como uma guerra

Exporte os ficheiros do project web como um ficheiro de arquivo (guerra) web para que pode implementá-lo para a aplicação web. Os seguintes ficheiros do project web residem na pasta ConteúdoWeb:

    META-INF
    WEB-INF
    index.jsp

1. Botão direito do rato na pasta ConteúdoWeb e selecione **Exportar**.

2. Na caixa de diálogo **Selecione Exportar** , clique em **Web > guerra** de ficheiro, em seguida, clique em **seguinte**.

3. Na caixa de diálogo **Exportar guerra** , selecione o diretório de src no projeto atual e incluir o nome do ficheiro guerra no final. Por exemplo:

    `<project-path>/JSPHello/src/JSPHello.war`

Para mais informações sobre como implementar ficheiros guerra, consulte o artigo [Adicionar uma aplicação de Java ao Azure aplicação de serviço Web Apps](web-sites-java-add-app.md).


### <a name="deploying-the-hello-world-application-using-ftp"></a>Implementar a aplicação do mundo Olá utilizando FTP

Selecione um cliente de FTP de terceiros para publicar a aplicação. Este procedimento descreve duas opções: consola do Kudu incorporada no Azure; e FileZilla, uma ferramenta popular com uma IU conveniente, gráfica.

> **Nota:** O Toolkit de Azure para Eclipse suporta implementação para contas de armazenamento e serviços em nuvem, mas actualmente não suporta implementação às aplicações web. Pode implementar a contas de armazenamento e serviços em nuvem utilizando um projecto de implementação do Azure, tal como descrito na [criação de uma aplicação de Olá mundo para Azure no Eclipse](http://msdn.microsoft.com/library/azure/hh690944.aspx), mas não a aplicações web. Utilize outros métodos como FTP ou GitHub a transferência de ficheiros para a sua aplicação web.

> **Nota:** Recomendamos que não utilizar FTP de linha de comandos do Windows (da linha de comandos FTP.EXE utilitário que vem incluído com o Windows). Os clientes de FTP que utilizam FTP ativo, tal como FTP.EXE, muitas vezes falharem trabalhar com firewalls. FTP ativo Especifica um endereço interno com base em LAN à qual um servidor de FTP provavelmente falhará ligar.

Para obter mais informações sobre a implementação para uma aplicação de serviço web app utilizando o FTP, consulte os seguintes tópicos:

- [Implementar a utilizar um utilitário FTP](web-sites-deploy.md)


#### <a name="set-up-deployment-credentials"></a>Defina credenciais de implementação

Certifique-se de que tem de executar a aplicação de **AzureWebDemo** para criar uma aplicação web. Irá transferir ficheiros para esta localização.

1. Inicie sessão no portal do clássico e clique em **Web Apps**. Certifique-se **que webdemowebapp** aparece na lista de aplicações web e certifique-se de que está a funcionar. Clique em **WebDemoWebApp** para abrir a sua página do **Dashboard** .

2. Na página do **Dashboard** , em **Rapidamente rápida**, clique em **configurar as suas credenciais de implementação** (se já tiver credenciais de implementação, isto lê **Repor as suas credenciais de implementação**).

    Credenciais de implementação estão associadas uma conta Microsoft. Tem de especificar um nome de utilizador e palavra-passe que pode utilizar para implementar utilizando Git e FTP. Pode utilizar estas credenciais para implementar a qualquer aplicação do web em todas as subscrições Azure associada à sua conta Microsoft. Fornecer credenciais de implementação Git e FTP na caixa de diálogo e gravar o nome de utilizador e palavra-passe para utilização futura.


#### <a name="get-ftp-connection-information"></a>Obter informações de ligação de FTP

Para utilizar o FTP para implementar a aplicação web recentemente criado ficheiros de aplicação, é necessário obter informações de ligação. Existem duas formas para obter informações de ligação. Uma forma é visite a página do **Dashboard** do web app; a forma como é para transferir web aplicação publicar perfil. O perfil de publicar é um ficheiro XML que fornece informações como FTP anfitrião nome e de início de sessão as credenciais para as suas aplicações web na aplicação de serviço de Azure. Pode utilizar este nome de utilizador e palavra-passe para implementar a qualquer aplicação do web em todas as subscrições associado à conta Azure, não apenas este um.

Para obter informações sobre a ligação FTP a partir do pá do web app no [Portal do Azure][]:

1. Em **Essentials**, localize e copie o **FTP hostname**. Este é um URI semelhante ao `ftp://waws-prod-bay-NNN.ftp.azurewebsites.windows.net`.

2. Em **Essentials**, localize e copie **FTP/implementação nome de utilizador**. Isto não terá de formulário *nome de utilizador webappname\deployment*; Por exemplo `WebDemoWebApp\deployer77`.

Para obter informações sobre a ligação FTP publicar perfil:

1. No pá do web app, clique em **obter publicar perfil**. Isto irá transferir um ficheiro de .publishsettings para a sua unidade local.

2. Abra o ficheiro .publishsettings num editor de XML ou editor de texto e localize o `<publishProfile>` elemento que contém `publishMethod="FTP"`. Deverá ter com aspeto semelhante ao seguinte:

        <publishProfile
            profileName="WebDemoWebApp - FTP"
            publishMethod="FTP"
            publishUrl="ftp://waws-prod-bay-NNN.ftp.azurewebsites.windows.net/site/wwwroot"
            ftpPassiveMode="True"
            userName="WebDemoWebApp\$WebDemoWebApp"
            userPWD="<deployment-password>"
            ...
        </publishProfile>

3. Tenha em atenção que a aplicação web `publishProfile` mapa de definições para as definições do Gestor de Site de FileZilla da seguinte forma:

- `publishUrl`é o mesmo **nome de anfitrião FTP**, o valor que definiu no **anfitrião**.
- `publishMethod="FTP"`significa que o utilizador defina **protocolo** **FTP - File Transfer Protocol**e **encriptação** para **utilizar o FTP simples**.
- `userName`e `userPWD` são chaves para os valores de palavra-passe que especificou quando repor as credenciais de implementação e o nome de utilizador real. `userName`é igual **implementação / FTP utilizador**. São mapeados para o **utilizador** e **palavra-passe** no FileZilla.
- `ftpPassiveMode="True"`significa que o local de FTP utiliza a transferência de FTP passiva; Selecione **passivas** no separador **Definições de transferência** .


#### <a name="configure-the-web-app-to-host-a-java-application"></a>Configurar a aplicação Web para alojar uma aplicação Java

Antes de publicar a aplicação, pode ter de alterar algumas definições de configuração para que a aplicação web pode alojar uma aplicação Java.

1. No portal do clássico, aceda à página do **Dashboard** do web app e clique em **Configurar**. Na página **Configurar** , especifique as seguintes definições.

2. Na **versão de Java** a predefinição é **desativar**; Selecione a versão de Java alvos sua aplicação; Por exemplo 1.7.0_51. Também após fazer isto, certifique-se de que o **contentor Web** está definido para uma versão do servidor de Tomcat.

3. Em **Documentos predefinidos**, adicione index.jsp e mova-o até ao topo da lista. (O ficheiro predefinido para aplicações web é hostingstart.html.)

4. Clique em **Guardar**.


#### <a name="publish-your-application-using-kudu"></a>Publicar a sua aplicação utilizando Kudu

Uma forma para publicar a aplicação é utilizar a consola de depuração de Kudu incorporada no Azure. Kudu é conhecido sejam estável e consistentes com a aplicação de serviço Web Apps e Tomcat servidor. Aceder a consola para a aplicação web mediante navegação ao URL da seguinte forma:

`https://<webappname>.scm.azurewebsites.net/DebugConsole`

1. Para este procedimento, consola do Kudu está localizada no seguinte URL; Navegue para esta localização:

    `https://webdemowebapp.scm.azurewebsites.net/DebugConsole`

2. No menu superior, selecione **Depurar consola > CMD**.

3. Na linha de comandos da consola, navegue para `/site/wwwroot` (ou clique em `site`, em seguida, `wwwroot` na vista de diretório na parte superior da página):

    `cd /site/wwwroot`

4. Depois de especificar **versão Java**, servidor Tomcat deve criar um diretório de webapps. Na linha de comandos da consola, navegue para o directório webapps:

    `mkdir webapps`

    `cd webapps`

5. Arraste JSPHello.war a partir do `<project-path>/JSPHello/src/` e largue-a para a vista de diretório Kudu em `/site/wwwroot/webapps`. Não arraste-a para a área "Arrastar para aqui para carregar e código postal", uma vez que irá deszipar Tomcat-lo.

  ![][8]

Na primeira JSPHello.war será apresentado na área de diretório por si:

  ![][9]

Um curto período de tempo (provavelmente menos de 5 minutos) Tomcat servidor irá descomprimir o ficheiro de guerra para num diretório JSPHello descompactado. Clique no diretório de raiz para ver se index.jsp foi descompactados e copiados para lá. Se Sim, navegar de volta para o directório de webapps para verificar se o diretório JSPHello descompactado foi criado. Se não vir estes itens, aguarde e repita.

  ![][10]


#### <a name="publish-your-application-using-filezilla-optional"></a>Publicar a sua aplicação utilizando FileZilla (opcional)

Outra ferramenta que pode utilizar para publicar a aplicação é FileZilla, um cliente de FTP terceiros popular com uma IU conveniente, gráfico. Pode transferir e instalar FileZilla a partir de [http://filezilla-project.org/](http://filezilla-project.org/) se que ainda não tivê-lo. Para mais informações sobre como utilizar o cliente, consulte a [documentação de FileZilla](https://wiki.filezilla-project.org/Documentation) e esta entrada de blogue no [os clientes de FTP - parte 4: FileZilla](http://blogs.msdn.com/b/robert_mcmurray/archive/2008/12/17/ftp-clients-part-4-filezilla.aspx).

1. No FileZilla, clique em **ficheiro > Gestor do Site**.
2. Na caixa de diálogo **Gestor do Site** , clique em **Novo Site**. Um novo local de FTP em branco serão apresentados na **Entrada selecione** solicitar para fornecer um nome. Para este procedimento,-lhe o nome `AzureWebDemo-FTP`.

    No separador **Geral** , especifique as seguintes definições:
    - **Anfitrião:** Introduza o **Nome de anfitrião FTP** que copiou do dashboard.
    - **Porta:** (Deixe este estiver em branco, tal como esta é uma transferência passivo e o servidor vai determinar a porta a utilizar.)
    - **Protocolo:** Protocolo de transferência de ficheiro de FTP
    - **Encriptação:** Utilizar o FTP simples
    - **Tipo de início de sessão:** Normal
    - **Utilizador:** Introduza a implementação / FTP utilizador que copiou do dashboard. Este é o completo nome de utilizador FTP, que tem o formulário *webappname\username*.
    - **Palavra-passe:** Introduza a palavra-passe que especificou quando define as credenciais de implementação.

    No separador **Definições de transferência** , selecione **passiva**.

3. Clique em **Ligar**. Se tiver êxito, consola do FileZilla irá apresentar uma `Status: Connected` mensagem e problema um `LIST` comando para listar os conteúdos de diretório.

4. No painel de site **Local** , selecione o diretório de origem no qual o ficheiro JSPHello.war reside; o caminho será semelhante ao seguinte:

    `<project-path>/JSPHello/src/`

5. No painel de site **remoto** , selecione a pasta de destino. Irá implementar o ficheiro de guerra para o `webapps` directório em raiz do web app. Navegue para `/site/wwwroot`, com o botão direito no `wwwroot`e selecione **criar diretório**. Atribua um nome de directório `webapps` e introduza esse diretório.

6. Transferir JSPHello.war para `/site/wwwroot/webapps`. Selecione JSPHello.war na lista de ficheiros **Local** , com o botão direito no mesmo e selecione **carregar**. Deverá visualizar esta apareça no `/site/wwwroot/webapps`.

7. Depois de copiar JSPHello.war ao diretório webapps, servidor Tomcat serão automaticamente descompactar (deszipar) os ficheiros no ficheiro guerra. Apesar do servidor de Tomcat começa descompactar quase imediatamente, pode demorar um valor longo tempo (possivelmente horas) para os ficheiros que seja apresentado no cliente do FTP.


#### <a name="run-the-hello-world-application-on-the-web-app"></a>Executar a aplicação Olá mundo num Web App

1. Após ter carregado o ficheiro de guerra e verificado que o servidor de Tomcat criou uma descompactados `JSPHello` diretório, procurar para `http://webdemowebapp.azurewebsites.net/JSPHello` para executar a aplicação.

    > **Nota:** Se clicar em **Procurar** a partir do portal clássico, poderá receber a página Web predefinido, dizer "Esta aplicação web do Java com base foi criada com êxito." Poderá ter de atualizar a página Web para poder ver o resultado da aplicação em vez da página Web predefinido.

2. Quando a aplicação é executada, deverá ver uma página web com o seguinte resultado:

    `Hello World, the time is Tue Mar 24 23:21:10 GMT 2015`


#### <a name="clean-up-azure-resources"></a>Limpar o Azure recursos

Este procedimento cria uma aplicação web do serviço de aplicação. Vai ser faturada para o recurso como existir. A menos que planeia continuar a utilizar a aplicação web para testar ou desenvolvimento, deverá tomar em consideração parar ou eliminá-la. Uma aplicação web que foi parada irá ainda impliquem pequeno, mas pode reiniciar o-lo em qualquer altura. Eliminar uma aplicação web apaga todos os dados que carregou à mesma.

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

  [1]: ./media/java-create-azure-website-using-java-sdk/eclipse-maven-repositories-rebuild-index.png
  [2]: ./media/java-create-azure-website-using-java-sdk/eclipse-new-java-class.png
  [3]: ./media/java-create-azure-website-using-java-sdk/eclipse-new-dynamic-web-project.png
  [4]: ./media/java-create-azure-website-using-java-sdk/eclipse-java-build-path.png
  [5]: ./media/java-create-azure-website-using-java-sdk/eclipse-targeted-runtimes-tomcat-server.png
  [6]: ./media/java-create-azure-website-using-java-sdk/eclipse-targeted-runtimes-properties-page.png
  [7]: ./media/java-create-azure-website-using-java-sdk/eclipse-run-on-server.png
  [8]: ./media/java-create-azure-website-using-java-sdk/kudu-console-drag-drop.png
  [9]: ./media/java-create-azure-website-using-java-sdk/kudu-console-jsphello-war-1.png
  [10]: ./media/java-create-azure-website-using-java-sdk/kudu-console-jsphello-war-2.png
 

[Azure de aplicação de serviço]: http://go.microsoft.com/fwlink/?LinkId=529714
[Web plataforma Installer]: http://go.microsoft.com/fwlink/?LinkID=252838
[Toolkit Azure para Eclipse]: https://msdn.microsoft.com/library/azure/hh690946.aspx
[Azure portal clássico]: https://manage.windowsazure.com
[O que é um diretório do Azure AD]: http://technet.microsoft.com/library/jj573650.aspx
[Criar e carregar um certificado de gestão do Azure]: ../cloud-services/cloud-services-certs-create.md
[Ferramenta de gestão de certificado (keytool) e chave]: http://docs.oracle.com/javase/6/docs/technotes/tools/windows/keytool.html
[WebSiteManagementClient]: http://azure.github.io/azure-sdk-for-java/com/microsoft/azure/management/websites/WebSiteManagementClient.html
[WebSpaceNames]: http://dl.windowsazure.com/javadoc/com/microsoft/windowsazure/management/websites/models/WebSpaceNames.html
[Portal do Azure]: https://portal.azure.com
