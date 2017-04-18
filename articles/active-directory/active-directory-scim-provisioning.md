<properties
    pageTitle="Utilizar SCIM para activar automáticas de aprovisionamento de utilizadores e grupos a partir do Azure Active Directory para aplicações | Microsoft Azure"
    description="Automaticamente pode aprovisionar o Azure Active Directory utilizadores e grupos para qualquer loja de aplicações ou identidade de que é fronted por um serviço Web com a interface definida na especificação do protocolo SCIM"
    services="active-directory"
    documentationCenter=""
    authors="asmalser-msft"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="02/09/2016"
    ms.author="asmalser-msft"/>

#<a name="using-scim-to-enable-automatic-provisioning-of-users-and-groups-from-azure-active-directory-to-applications"></a>Utilizar SCIM para activar automáticas de aprovisionamento de utilizadores e grupos a partir do Azure Active Directory para aplicações

##<a name="overview"></a>Descrição geral

Automaticamente pode aprovisionar o Azure Active Directory utilizadores e grupos para qualquer loja de aplicações ou identidade de que é fronted por um serviço Web com a interface definida na [especificação do protocolo SCIM 2.0](https://tools.ietf.org/html/draft-ietf-scim-api-19). Azure Active Directory podem enviar pedidos para criar, modificar e eliminar atribuídos utilizadores e grupos para este serviço de Web, em seguida, pode traduzir esses pedidos para operações após o arquivo de identidade de destino. 

![][1]
*Figura: Aprovisionamento a partir do Azure Active Directory para um arquivo de identidade através de um serviço Web*

Esta funcionalidade pode ser utilizada em conjunto com a funcionalidade "[Trazer o seu próprio aplicação](http://blogs.technet.com/b/ad/archive/2015/06/17/bring-your-own-app-with-azure-ad-self-service-saml-configuration-gt-now-in-preview.aspx)" no Azure AD para permitir o início de sessão único e utilizador automática de aprovisionamento para as aplicações que fornecem ou são fronted por um serviço web SCIM.

Existem dois casos de utilização para SCIM no Azure Active Directory:

* **Aprovisionamento de utilizadores e grupos para aplicações que suportem SCIM** - aplicações que suportem SCIM 2.0 e utilizar tokens de tipo de ligação OAuth para autenticação irá funcionar com o Azure AD da caixa.

* **Construir a sua própria aprovisionamento solução para as aplicações que suportem outras aprovisionamento baseadas em API** - para as aplicações que não sejam SCIM, pode criar um ponto final de SCIM para traduzir entre o Azure AD ponto final SCIM e tudo o que suporta a API a aplicação para o aprovisionamento de utilizador.  Para ajudar a no desenvolvimento de um ponto final de SCIM, fornecemos-bibliotecas clip juntamente com exemplos de código que lhe mostram como para fornecer um ponto final de SCIM e traduzir mensagens SCIM.  

##<a name="provisioning-users-and-groups-to-applications-that-support-scim"></a>Aprovisionamento de utilizadores e grupos para aplicações que suportem SCIM

Azure Active Directory pode ser configurado para automaticamente o atribuído de aprovisionamento de utilizadores e grupos para as aplicações que implementam um [sistema de domínios para gestão de identidades 2 (SCIM)](https://tools.ietf.org/html/draft-ietf-scim-api-19) na Web de serviço e aceitar tokens de tipo de ligação OAuth para autenticação. Dentro da especificação de SCIM 2.0, aplicações tem de cumprir estes requisitos:

* Suporta a criação de utilizadores e/ou grupos, como por secção 3.3 do protocolo SCIM.  

* Suporta a modificar os utilizadores e/ou grupos com pedidos de patches de acordo com a secção 3.5.2 do protocolo SCIM.  

* Suporta a obter um recurso conhecido por secção 3.4.1 do protocolo SCIM.  

*  Suporta consultar os utilizadores e/ou grupos, como por secção 3.4.2 do protocolo SCIM.  Por predefinição, os utilizadores são avisados por externalId e grupos são consultados através de DisplayName em grupos.  

* Suporta consultar utilizador por ID e por Gestor de acordo com a secção 3.4.2 do protocolo SCIM.  

* Suporta consultar grupos por ID e por membro de acordo com a secção 3.4.2 do protocolo SCIM.  

* Aceita tokens de tipo de ligação OAuth para autorização de acordo com a secção 2.1 do protocolo SCIM.

Deve verificar com o seu fornecedor de aplicação ou a documentação do seu fornecedor de aplicação para declarações de compatibilidade com estes requisitos.
 
###<a name="getting-started"></a>Introdução

É possível ligar a aplicações que suportam o perfil SCIM descrito acima para o Azure Active Directory utilizando a funcionalidade de aplicação "personalizada" na Galeria de aplicação do Azure AD. Quando estiver ligado, o Azure AD executa um processo de sincronização em 5 minutos onde-consultas ponto final SCIM a aplicação para atribuídas utilizadores e grupos e cria ou modifica-los de acordo com os detalhes da atribuição.

**Para ligar uma aplicação que suporta SCIM:**

1.  Num browser, inicie o portal de gestão Azure em https://manage.windowsazure.com.
2.  Navegue até à **do Active Directory > diretório > [directório Your] > aplicações**e selecione **Adicionar > Adicionar uma aplicação a partir da Galeria**.
3.  Selecione o separador **personalizado** à esquerda, introduza um nome para a sua aplicação e clique no ícone de marca de verificação para criar um objeto de aplicação.

![][2]

4.  No ecrã resultante, selecione o segundo botão **Configurar aprovisionamento de conta** .
5.  No campo **URL do ponto final de aprovisionamento** , introduza o URL do ponto final SCIM a aplicação.
6.  Se o ponto final SCIM requer uma ligação OAuth OAuth a partir de um emissor que não seja o Azure AD, em seguida, copie o necessário token de ligação OAuth para o campo **Autenticação Token (opcional)** . Se este campo é deixado em branco, em seguida, Azure AD irá incluir um de ligação OAuth OAuth emitido a partir do Azure AD com cada pedido. As aplicações que utilizam o Azure AD como um fornecedor de idenity pode validar este Azure AD-emitido token.
7.  Clique em **seguinte**e clique no botão **Iniciar o teste** de ter o Azure Active Directory tentar ligar para o ponto final SCIM. Se as tentativas falharem, informações de diagnóstico serão apresentadas.  
8.  Se tentativas para ligar para a realizada com êxito de aplicação, em seguida, clique em **seguinte** no restante ecrãs e clique em **Concluir** para sair da caixa de diálogo.
9.  No ecrã resultante, selecione o botão de **Contas atribuir** terceiro. Na secção utilizadores e grupos resultante, atribua utilizadores ou grupos que pretende a disposição para a aplicação.
10. Quando são atribuídos a utilizadores e grupos, clique no separador **Configurar** na parte superior do ecrã.
11. Em o **Aprovisionamento de conta**, confirme que o estado está definido para no. 
12. Em **Ferramentas**, clique em **reiniciar o aprovisionamento de conta** para kick-start o processo de aprovisionamento.

Tenha em atenção que 5 a 10 minutos poderá ocorridos antes das vai começar o processo de aprovisionamento enviar pedidos para o ponto final SCIM.  É fornecido um resumo de tentativas de ligação no separador de Dashboard a aplicação e um relatório de aprovisionamento de atividade e os erros de aprovisionamento podem ser transferidos a partir do separador de relatórios do diretório.

##<a name="building-your-own-provisioning-solution-for-any-application"></a>Construir a sua própria solução de aprovisionamento de qualquer aplicação do

Através da criação de um serviço web SCIM que interfaces com o Azure Active Directory, pode ativar o único utilizador de início de sessão e automático de aprovisionamento de praticamente qualquer aplicação do que fornece um utilizador resto ou SOAP API de aprovisionamento.

Eis como funciona:

1.  Azure AD fornece uma biblioteca de infraestrutura de idioma comuns com o nome [Microsoft.SystemForCrossDomainIdentityManagement](https://www.nuget.org/packages/Microsoft.SystemForCrossDomainIdentityManagement/). Integradores tanto os programadores podem utilizar esta biblioteca para criar e implementar um web baseadas em SCIM ponto final de serviço compatível com a ligação Azure AD a loja de identidade de qualquer aplicação.
2.  Mapeamentos são implementados no serviço web para mapear o esquema de utilizador padronizado para o esquema de utilizador e o protocolo requerido pela aplicação.
3.  O URL de ponto final é registado no Azure AD como parte de uma aplicação personalizada na Galeria de aplicação.
4.  Utilizadores e grupos são atribuídos a esta aplicação no Azure AD. Relativamente a atribuição, o que são colocados numa fila para ser sincronizados com a aplicação de destino. O processo de sincronização processamento fila de espera é executado em 5 minutos.

###<a name="code-samples"></a>Exemplos de código

Para facilitar este processo, um conjunto de [código amostras](https://github.com/Azure/AzureAD-BYOA-Provisioning-Samples/tree/master) são fornecidos que criar um ponto final de serviço do SCIM web e demonstram aprovisionamento automático. É um exemplo de um fornecedor que mantém um ficheiro com linhas de valores separados por vírgulas que representa a utilizadores e grupos.  O outro é de um fornecedor que funciona o serviço de gestão de acesso e de identidade do Amazon Web Services.  

**Pré-requisitos**

* Visual Studio 2013 ou posterior
* [Azure SDK para .NET](https://azure.microsoft.com/downloads/)
* Windows máquina que suporte a arquitetura ASP.NET 4,5 para ser utilizado como o ponto final SCIM. Nesta máquina tem de ser acessível a partir da nuvem
* [Uma subscrição do Azure com uma versão de avaliação ou licenciada do Azure AD Premium](https://azure.microsoft.com/services/active-directory/)
* O exemplo Amazon AWS requer bibliotecas a partir do [Toolkit de AWS para Visual Studio](http://docs.aws.amazon.com/AWSToolkitVS/latest/UserGuide/tkv_setup.html). Consulte o ficheiro Leia-me incluído com o exemplo para detalhes adicionais

###<a name="getting-started"></a>Introdução

A forma mais fácil para implementar um ponto final de SCIM que pode aceitar pedidos de aprovisionamento a partir do Azure AD é criar e implementar o código de exemplo exporta os utilizadores aprovisionados para um ficheiro de valores separados por vírgulas (CSV).

**Para criar um ponto final SCIM de exemplo:**

1.  Transferir o pacote de exemplo de código em [https://github.com/Azure/AzureAD-BYOA-Provisioning-Samples/tree/master](https://github.com/Azure/AzureAD-BYOA-Provisioning-Samples/tree/master)
2.  Deszipar o pacote e coloque-o no seu computador Windows numa localização como C:\AzureAD-BYOA-Provisioning-Samples\.
3.  Nesta pasta, inicie a solução FileProvisioningAgent no Visual Studio.
4.  Selecione **Ferramentas > Gestor de pacotes de biblioteca > consola do Gestor de pacote**e execute os comandos abaixo do projeto FileProvisioningAgent para resolver as referências de solução:

    Pacote de instalação pacote de instalação Microsoft.SystemForCrossDomainIdentityManagement pacote de instalação Microsoft.IdentityModel.Clients.ActiveDirectory pacote de instalação Microsoft.Owin.Diagnostics Microsoft.Owin.Host.SystemWeb

5.  Construa o projeto FileProvisioningAgent.
6.  Inicie a aplicação de linha de comandos do Windows (como um administrador) e utilize o comando de **cd** para alterar o diretório para a sua pasta **\AzureAD-BYOA-Provisioning-Samples\ProvisioningAgent\bin\Debug** .
7.  Execute o comando abaixo, substituindo < endereço ip > com o nome de domínio ou IP do computador Windows.

    FileAgnt.exe http://<ip-address>:9000 TargetFile.csv

8.  No Windows em **definições do Windows > definições do Internet & rede**, selecione o **Firewall do Windows > Definições avançadas**e crie uma **Regra de entrada** que permita acesso a porta 9000 de entrada.
9.  Se o computador do Windows está atrás de um router, o router terão de ser configurado para executar a tradução de acesso de rede entre o seu porta 9000 está divulgada na internet e porta 9000 no computador Windows. Isto é necessário para Azure AD possam aceder a este ponto final na nuvem.


**Para registar o ponto final SCIM de exemplo no Azure AD:**

1.  Num browser, inicie o portal de gestão Azure em https://manage.windowsazure.com.
2.  Navegue até à **do Active Directory > diretório > [directório Your] > aplicações**e selecione **Adicionar > Adicionar uma aplicação a partir da Galeria**.
3.  Selecione o separador **personalizado** à esquerda, introduza um nome tal como "SCIM teste aplicação" e clique no ícone de marca de verificação para criar um objeto de aplicação. Tenha em atenção que o objeto de aplicação criado é a intenção para representar a aplicação de destino que seria do aprovisionamento e execução do serviço single sign-on para e não apenas o ponto final SCIM.

![][2]

4.  No ecrã resultante, selecione o segundo botão **Configurar aprovisionamento de conta** .
5.  Na caixa de diálogo, introduza o URL e porta do seu ponto final SCIM expostos à internet. Isto seria algo como http://testmachine.contoso.com:9000 ou http://<ip-address>:9000/, onde < endereço ip > é a internet exposta IP endereço.  
6.  Clique em **seguinte**e clique no botão **Iniciar o teste** de ter o Azure Active Directory tentar ligar para o ponto final SCIM. Se as tentativas falharem, informações de diagnóstico serão apresentadas.  
7.  Se tiver êxito tenta ligar ao serviço Web, em seguida, clique em **seguinte** nos ecrãs restantes e clique em **Concluir** para sair da caixa de diálogo.
8.  No ecrã resultante, selecione o botão de **Contas atribuir** terceiro. Na secção utilizadores e grupos resultante, atribua utilizadores ou grupos que pretende a disposição para a aplicação.
9.  Quando são atribuídos a utilizadores e grupos, clique no separador **Configurar** na parte superior do ecrã.
10. Em o **Aprovisionamento de conta**, confirme que o estado está definido para no. 
11. Em **Ferramentas**, clique em **reiniciar o aprovisionamento de conta** para kick-start o processo de aprovisionamento.

Tenha em atenção que 5 a 10 minutos poderá ocorridos antes das vai começar o processo de aprovisionamento enviar pedidos para o ponto final SCIM.  É fornecido um resumo de tentativas de ligação no separador de Dashboard a aplicação e um relatório de aprovisionamento de atividade e os erros de aprovisionamento podem ser transferidos a partir do separador de relatórios do diretório.

O passo final verificar a amostra é abrir o ficheiro de TargetFile.csv na pasta \AzureAD-BYOA-Provisioning-Samples\ProvisioningAgent\bin\Debug no seu computador Windows. Assim que o processo de aprovisionamento é executado, este ficheiro mostra os detalhes de todos os atribuídos e aprovisionado utilizadores e grupos.

###<a name="development-libraries"></a>Bibliotecas de desenvolvimento

Para desenvolver o seu próprio serviço Web que está em conformidade com a especificação de SCIM, pela primeira vez familiarizar-se com as seguintes bibliotecas fornecidas pela Microsoft para ajudar a acelerar o processo de desenvolvimento: 

**1:**  Bibliotecas de infraestrutura de idioma comuns são oferecidas para utilização com idiomas com base no que infraestrutura, tal como c#.  Uma nessas bibliotecas, [Microsoft.SystemForCrossDomainIdentityManagement.Service](https://www.nuget.org/packages/Microsoft.SystemForCrossDomainIdentityManagement/), declara uma interface, Microsoft.SystemForCrossDomainIdentityManagement.IProvider, apresentada na figura abaixo.  Um programador utilizando as bibliotecas seria implementar interface com uma classe que pode ser referida, gerais, como um fornecedor.  As bibliotecas para ativar o programador implementar facilmente um serviço Web que está em conformidade com a especificação de SCIM, quer alojado no prazo de serviços de informação Internet ou qualquer executável assemblagem de infraestrutura de idioma comuns.  Pedidos para esse serviço Web serão convertidos em chamadas para métodos do fornecedor, que seriam ser programados pelo programador para trabalhar com no algumas arquivo de identidade.    

![][3]

**2:** [Processadores de encaminhar Express](http://expressjs.com/guide/routing.html) estão disponíveis para analisar node.js pedido objectos que representa chamadas (conforme definido pela especificação SCIM), feita para um serviço Web node.js.     

###<a name="building-a-custom-scim-endpoint"></a>Criar um ponto final de SCIM personalizado

Utilizar bibliotecas descritas acima, os programadores que utilizam nessas bibliotecas podem alojar o dos seus serviços dentro qualquer executável assemblagem de infraestrutura de idioma comuns, ou dentro dos serviços de informação Internet.  Eis exemplos de código para um serviço numa assemblagem executável, no endereço http://localhost:9000 de alojamento: 

    private static void Main(string[] arguments)
    {
    // Microsoft.SystemForCrossDomainIdentityManagement.IMonitor, 
    // Microsoft.SystemForCrossDomainIdentityManagement.IProvider and 
    // Microsoft.SystemForCrossDomainIdentityManagement.Service are all defined in 
    // Microsoft.SystemForCrossDomainIdentityManagement.Service.dll.  
    
    Microsoft.SystemForCrossDomainIdentityManagement.IMonitor monitor = 
      new DevelopersMonitor();
    Microsoft.SystemForCrossDomainIdentityManagement.IProvider provider = 
      new DevelopersProvider(arguments[1]);
    Microsoft.SystemForCrossDomainIdentityManagement.Service webService = null;
    try
    {
        webService = new WebService(monitor, provider);
        webService.Start("http://localhost:9000");

        Console.ReadKey(true);
    }
    finally
    {
        if (webService != null)
        {
            webService.Dispose();
            webService = null;
        }
    }
    }

    public class WebService : Microsoft.SystemForCrossDomainIdentityManagement.Service
    {
    private Microsoft.SystemForCrossDomainIdentityManagement.IMonitor monitor;
    private Microsoft.SystemForCrossDomainIdentityManagement.IProvider provider;

    public WebService(
      Microsoft.SystemForCrossDomainIdentityManagement.IMonitor monitoringBehavior, 
      Microsoft.SystemForCrossDomainIdentityManagement.IProvider providerBehavior)
    {
        this.monitor = monitoringBehavior;
        this.provider = providerBehavior;
    }

    public override IMonitor MonitoringBehavior
    {
        get
        {
            return this.monitor;
        }

        set
        {
            this.monitor = value;
        }
    }

    public override IProvider ProviderBehavior
    {
        get
        {
            return this.provider;
        }

        set
        {
            this.provider = value;
        }
    }
    }

É importante ter em atenção que este serviço tem de ter um HTTP endereço e servidor de certificado de autenticação do qual é a autoridade de certificação de raiz um dos seguintes procedimentos: 

* CNNIC
* Comodo
* CyberTrust
* DigiCert
* GeoTrust
* GlobalSign
* Go Daddy
* VeriSign
* WoSign

Um certificado de autenticação do servidor pode estar ligado a uma porta num sistema anfitrião Windows utilizando o utilitário de shell de rede, desta forma: 

    netsh http add sslcert ipport=0.0.0.0:443 certhash=0000000000003ed9cd0c315bbb6dc1c08da5e6 appid={00112233-4455-6677-8899-AABBCCDDEEFF}  
 
Aqui, o valor fornecido para o argumento certhash é a impressão digital do certificado, enquanto o valor fornecido para o argumento appid é um identificador exclusivo globalmente arbitrário.  

Para alojar o serviço de serviços de informação Internet, um programador seria construir uma assemblagem de biblioteca de código de infraestrutura de idioma comuns com uma classe denominada arranque no espaço de nomes predefinido da assemblagem.  Eis um exemplo de uma classe tal: 

    public class Startup
    {
    // Microsoft.SystemForCrossDomainIdentityManagement.IWebApplicationStarter, 
    // Microsoft.SystemForCrossDomainIdentityManagement.IMonitor and  
    // Microsoft.SystemForCrossDomainIdentityManagement.Service are all defined in 
    // Microsoft.SystemForCrossDomainIdentityManagement.Service.dll.  

    Microsoft.SystemForCrossDomainIdentityManagement.IWebApplicationStarter starter;

    public Startup()
    {
        Microsoft.SystemForCrossDomainIdentityManagement.IMonitor monitor = 
          new DevelopersMonitor();
        Microsoft.SystemForCrossDomainIdentityManagement.IProvider provider = 
          new DevelopersProvider();
        this.starter = 
          new Microsoft.SystemForCrossDomainIdentityManagement.WebApplicationStarter(
            provider, 
            monitor);
    }

    public void Configuration(
      Owin.IAppBuilder builder) // Defined in in Owin.dll.  
    {
        this.starter.ConfigureApplication(builder);
    }
    }

###<a name="handling-endpoint-authentication"></a>Autenticação de ponto final de processamento

Os pedidos a partir do Azure Active Directory incluir um 2.0 token de ligação OAuth.   Qualquer receber o pedido de serviço, deverá autenticar o emissor como sendo o Azure Active Directory em nome de inquilino esperado do Azure Active Directory, para aceder ao serviço de Web grafo do Azure Active Directory.  Num token, o emissor está identificado por uma afirmação iss, como "iss": "https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/".  Neste exemplo, o endereço base do valor afirmação, https://sts.windows.net, identifica o Azure Active Directory como emissor, enquanto o segmento endereço relativo, cbb1a5ac-f33b-45fa-9bf5-f37db0fed422, é um identificador exclusivo do inquilino Azure Active Directory em nome do qual foi emitido o token.  Se o token foi emitido para aceder ao serviço da Azure Active Directory Graph Web, o identificador desse serviço, 00000002-0000-0000-c000-000000000000, deve ser o valor da afirmação de sociedade mista o token de.  

Os programadores que utilizam as bibliotecas de infraestrutura de idioma comuns fornecidas pela Microsoft para a criação de um serviço SCIM podem autenticar pedidos a partir do Azure Active Directory utilizando o pacote de Microsoft.Owin.Security.ActiveDirectory seguindo estes passos: 

**1:**  Num fornecedor, implementa a propriedade Microsoft.SystemForCrossDomainIdentityManagement.IProvider.StartupBehavior por-lo a devolver um método para ser chamado sempre que o serviço é iniciado: 

    public override Action\<Owin.IAppBuilder, System.Web.Http.HttpConfiguration.HttpConfiguration\> StartupBehavior
    {
      get
      {
        return this.OnServiceStartup;
      }
    }

    private void OnServiceStartup(
      Owin.IAppBuilder applicationBuilder,  // Defined in Owin.dll.  
      System.Web.Http.HttpConfiguration configuration)  // Defined in System.Web.Http.dll.  
    {
    }

**2:**  Adicione o seguinte código para esse método ter qualquer pedido para qualquer um dos pontos finais o serviço autenticados como contendo um token emitido por Azure Active Directory em nome de um inquilino especificado, para aceder ao serviço de publicação do Azure Active Directory Web de gráfico: 

    private void OnServiceStartup(
      Owin.IAppBuilder applicationBuilder IAppBuilder applicationBuilder, 
      System.Web.Http.HttpConfiguration HttpConfiguration configuration)
    {
      // IFilter is defined in System.Web.Http.dll.  
      System.Web.Http.Filters.IFilter authorizationFilter = 
        new System.Web.Http.AuthorizeAttribute(); // Defined in System.Web.Http.dll.configuration.Filters.Add(authorizationFilter);

      // SystemIdentityModel.Tokens.TokenValidationParameters is defined in    
      // System.IdentityModel.Token.Jwt.dll.
      SystemIdentityModel.Tokens.TokenValidationParameters tokenValidationParameters =     
        new TokenValidationParameters()
        {
          ValidAudience = "00000002-0000-0000-c000-000000000000"
        };

      // WindowsAzureActiveDirectoryBearerAuthenticationOptions is defined in 
      // Microsoft.Owin.Security.ActiveDirectory.dll
      Microsoft.Owin.Security.ActiveDirectory.
      WindowsAzureActiveDirectoryBearerAuthenticationOptions authenticationOptions =
        new WindowsAzureActiveDirectoryBearerAuthenticationOptions()    {
        TokenValidationParameters = tokenValidationParameters,
        Tenant = "03F9FCBC-EA7B-46C2-8466-F81917F3C15E" // Substitute the appropriate tenant’s 
                                                      // identifier for this one.  
      };

      applicationBuilder.UseWindowsAzureActiveDirectoryBearerAuthentication(authenticationOptions);
    }

##<a name="user-and-group-schema"></a>Utilizador e o esquema de grupo

Dois tipos de recursos para serviços de Web SCIM pode aprovisionar o Azure Active Directory.  Os tipos de recursos são utilizadores e grupos.  

Recursos do utilizador são identificados pelo identificador de esquema, urn: ietf:params:scim:schemas:extension:enterprise:2.0:User, que está incluído neste especificação do protocolo: http://tools.ietf.org/html/draft-ietf-scim-core-schema.  O mapeamento de predefinido dos atributos de utilizadores no Azure Active Directory para os atributos de recursos urn: ietf:params:scim:schemas:extension:enterprise:2.0:User é fornecido na tabela 1, abaixo.  

Agrupar recursos são identificados pelo identificador de esquema, http://schemas.microsoft.com/2006/11/ResourceManagement/ADSCIM/Group.  Tabela 2, abaixo mostra o mapeamento de predefinido dos atributos da grupos no Azure Active Directory para os atributos de http://schemas.microsoft.com/2006/11/ResourceManagement/ADSCIM/Group recursos.  

###<a name="table-1-default-user-attribute-mapping"></a>Tabela 1: Predefinido mapeamento de atributo de utilizador

| Utilizador do Azure Active Directory | urn: ietf:params:scim:schemas:extension:enterprise:2.0:User |
| ------------- | ------------- |
| IsSoftDeleted | Ativa |
| DisplayName em grupos | DisplayName em grupos |
| TelephoneNumber de fax | .value phoneNumbers [tipo eq "fax"] |
| givenName | name.givenName |
| Cargo | título |
| correio | .value e-mails [tipo eq "trabalho"] |
| mailNickname | externalId |
| Gestor de | Gestor de |
| dispositivos móveis | .value phoneNumbers [tipo eq "mobile"] |
| ID de objecto | ID |
| código postal | .postalCode endereços [tipo eq "trabalho"] |
| Endereços proxy | mensagens de correio eletrónico [Escreva eq, "outro"]. Valor |
| física-entrega-OfficeName | endereços [Escreva eq, "outro"]. Formatado |
| streetAddress | .streetAddress endereços [tipo eq "trabalho"] |
| Apelido | name.familyName |
| Número de telefone | .value phoneNumbers [tipo eq "trabalho"] |
| PrincipalName de utilizador | nome de utilizador |


###<a name="table-2-default-group-attribute-mapping"></a>Tabela 2: Predefinido mapeamento de atributo de grupo

| Grupo do Active Directory Azure | http://schemas.microsoft.com/2006/11/ResourceManagement/ADSCIM/Group |
| ------------- | ------------- |
| DisplayName em grupos | externalId |
| correio | .value e-mails [tipo eq "trabalho"] |
| mailNickname | DisplayName em grupos |
| Membros | Membros |
| ID de objecto | ID |
| proxyAddresses | mensagens de correio eletrónico [Escreva eq, "outro"]. Valor |


##<a name="user-provisioning-and-de-provisioning"></a>Aprovisionamento de utilizador e anular aprovisionamento

A figura seguinte mostra as mensagens que o Azure Active Directory irá enviar para um serviço SCIM para gerir o ciclo de vida de um utilizador no outro identidade armazenam.  O diagrama mostra também como um serviço SCIM implementado utilizando as bibliotecas de infraestrutura de idioma comuns fornecidas pela Microsoft para criar desses serviços irá traduzir os pedidos para chamadas para os métodos de um fornecedor.  

![][4]
*Figura: Aprovisionamento de utilizador e anular a sequência de aprovisionamento*

**1:**  Azure Active Directory irá consultar o serviço para um utilizador com um valor do atributo externalId correspondência o valor do atributo mailNickname de um utilizador no Azure Active Directory.  A consulta irá ser expresso como um pedido de Hypertext Transfer Protocol como esta, onde jyoung é um exemplo de um mailNickname de um utilizador no Azure Active Directory: 

    GET https://.../scim/Users?filter=externalId eq jyoung HTTP/1.1
    Authorization: Bearer ...

Se o serviço foi criado utilizando as bibliotecas de infraestrutura de idioma comuns fornecidas pela Microsoft para implementar SCIM serviços, o pedido de ser traduzido uma chamada para o método de consulta do fornecedor do serviço.  Eis a assinatura desse método: 

    // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
    // Microsoft.SystemForCrossDomainIdentityManagement.Resource is defined in 
    // Microsoft.SystemForCrossDomainIdentityManagement.Schemas.  
    // Microsoft.SystemForCrossDomainIdentityManagement.IQueryParameters is defined in 
    // Microsoft.SystemForCrossDomainIdentityManagement.Protocol.  

    System.Threading.Tasks.Task<Microsoft.SystemForCrossDomainIdentityManagement.Resource[]> Query(
      Microsoft.SystemForCrossDomainIdentityManagement.IQueryParameters parameters, 
      string correlationIdentifier);

Segue-se a definição da interface do Microsoft.SystemForCrossDomainIdentityManagement.IQueryParameters: 

    public interface IQueryParameters: 
      Microsoft.SystemForCrossDomainIdentityManagement.IRetrievalParameters
    {
        System.Collections.Generic.IReadOnlyCollection <Microsoft.SystemForCrossDomainIdentityManagement.IFilter> AlternateFilters 
        { get; }
    }

    public interface Microsoft.SystemForCrossDomainIdentityManagement.IRetrievalParameters
    {
      system.Collections.Generic.IReadOnlyCollection<string> ExcludedAttributePaths 
      { get; }
      System.Collections.Generic.IReadOnlyCollection<string> RequestedAttributePaths 
      { get; }
      string SchemaIdentifier 
      { get; }
    }

    public interface Microsoft.SystemForCrossDomainIdentityManagement.IFilter
    {
        Microsoft.SystemForCrossDomainIdentityManagement.IFilter AdditionalFilter 
          { get; set; }
        string AttributePath 
          { get; } 
        Microsoft.SystemForCrossDomainIdentityManagement.ComparisonOperator FilterOperator 
          { get; }
        string ComparisonValue 
          { get; }
    }
    
    public enum Microsoft.SystemForCrossDomainIdentityManagement.ComparisonOperator
    {
        Equals
    }

No caso de amostra precedente de uma consulta para um utilizador com um determinado valor para o atributo externalId, valores de argumentos passados para o método de consulta serão da seguinte forma: 

* parâmetros. AlternateFilters.Count: 1
* parâmetros. AlternateFilters.ElementAt(0). AttributePath: "externalId"
* parâmetros. AlternateFilters.ElementAt(0). OperadorDeComparação: ComparisonOperator.Equals
* parâmetros. AlternateFilter.ElementAt(0). ComparisonValue: "jyoung"
* correlationIdentifier: System.Net.Http.HttpRequestMessage.GetOwinEnvironment["owin. RequestId]" 

**2:**  Se a resposta a uma consulta para o serviço para que um utilizador com um valor do atributo externalId correspondência o valor do atributo mailNickname de um utilizador no Azure Active Directory não devolve nenhum dos utilizadores, em seguida, Azure Active Directory irá pedir que o serviço de aprovisionamento de um utilizador correspondente a um no Azure Active Directory.  Eis um exemplo de um pedido: 

    POST https://.../scim/Users HTTP/1.1
    Authorization: Bearer ...
    Content-type: application/json
    {
      "schemas":
      [
        "urn:ietf:params:scim:schemas:core:2.0:User",
        "urn:ietf:params:scim:schemas:extension:enterprise:2.0User"],
      "externalId":"jyoung",
      "userName":"jyoung",
      "active":true,
      "addresses":null,
      "displayName":"Joy Young",
      "emails": [
        {
          "type":"work",
          "value":"jyoung@Contoso.com",
          "primary":true}],
      "meta": {
        "resourceType":"User"},
       "name":{
        "familyName":"Young",
        "givenName":"Joy"},
      "phoneNumbers":null,
      "preferredLanguage":null,
      "title":null,
      "department":null,
      "manager":null}

As bibliotecas de infraestrutura de idioma comuns fornecidas pela Microsoft para implementar os serviços de SCIM seriam traduzir esse pedido para uma chamada para o método de criar o fornecedor do serviço.  O método Create tem esta assinatura: 

    // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
    // Microsoft.SystemForCrossDomainIdentityManagement.Resource is defined in 
    // Microsoft.SystemForCrossDomainIdentityManagement.Schemas.  

    System.Threading.Tasks.Task<Microsoft.SystemForCrossDomainIdentityManagement.Resource> Create(
      Microsoft.SystemForCrossDomainIdentityManagement.Resource resource, 
      string correlationIdentifier);

No caso de um pedido de aprovisionamento de um utilizador, o valor do argumento recurso será uma instância da Microsoft.SystemForCrossDomainIdentityManagement. Classe de Core2EnterpriseUser, definida na biblioteca de Microsoft.SystemForCrossDomainIdentityManagement.Schemas.  Se o pedido para aprovisionar o utilizador é concluída com êxito, em seguida, a implementação do método é esperada para devolver uma instância da a Microsoft.SystemForCrossDomainIdentityManagement. Classe Core2EnterpriseUser, com o valor da propriedade Identificador definido para o identificador exclusivo do utilizador recentemente aprovisionado.  

**3:**  Para atualizar um utilizador conhecido existir numa loja de identidade fronted por um SCIM, irá prosseguir Azure Active Directory solicitando o estado atual do que o utilizador de serviço com um pedido de como esta: 

    GET ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
    Authorization: Bearer ...

Num serviço construído utilizando as bibliotecas de infraestrutura de idioma comuns fornecidas pela Microsoft para implementar SCIM serviços, o pedido será convertido numa chamada para o método de obtenção do fornecedor do serviço.  Eis a assinatura do método recuperar: 

    // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
    // Microsoft.SystemForCrossDomainIdentityManagement.Resource and 
    // Microsoft.SystemForCrossDomainIdentityManagement.IResourceRetrievalParameters 
    // are defined in Microsoft.SystemForCrossDomainIdentityManagement.Schemas.  
    System.Threading.Tasks.Task<Microsoft.SystemForCrossDomainIdentityManagement.Resource> 
       Retrieve(
         Microsoft.SystemForCrossDomainIdentityManagement.IResourceRetrievalParameters 
           parameters, 
           string correlationIdentifier);
    
    public interface 
      Microsoft.SystemForCrossDomainIdentityManagement.IResourceRetrievalParameters:   
        IRetrievalParameters
        {
          Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier 
            ResourceIdentifier 
              { get; }
    }
    public interface Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier
    {
        string Identifier 
          { get; set; }
        string Microsoft.SystemForCrossDomainIdentityManagement.SchemaIdentifier 
          { get; set; }
    }

No caso de precedente exemplo de um pedido para obter o estado atual de um utilizador, os valores das propriedades do objeto fornecida como o valor do argumento parâmetros serão da seguinte forma: 

* Identificador de: "54D382A4-2050-4C03-94D1-E769F1D15682"
* SchemaIdentifier: "urn: ietf:params:scim:schemas:extension:enterprise:2.0:User"

**4:**  Se a um atributo referência estiver a ser atualizados, em seguida, Azure Active Directory irá consultar o serviço para determinar se é ou não o valor atual do atributo de referência no arquivo de identidade fronted pelo serviço já corresponde ao valor desse atributo no Azure Active Directory.  No caso dos utilizadores, o atributo só de que o valor atual irão ser consultado desta forma é o atributo gestor.  Eis um exemplo de um pedido para determinar se o atributo Gestor de um objeto de utilizador em particular tem atualmente um determinado valor: 

    GET ~/scim/Users?filter=id eq 54D382A4-2050-4C03-94D1-E769F1D15682 and manager eq 2819c223-7f76-453a-919d-413861904646&attributes=id HTTP/1.1
    Authorization: Bearer ...

O valor do parâmetro de consulta atributos, id, significa que se existir um objeto de utilizador que satisfaz a expressão fornecida como o valor do parâmetro de consulta de filtro, em seguida, o serviço é esperado para responder com um recurso urn: ietf:params:scim:schemas:core:2.0:User ou urn: ietf:params:scim:schemas:extension:enterprise:2.0:User, incluindo apenas o valor do atributo de id esse recurso.  Obviamente, o valor do atributo id é conhecido como o autor do pedido — está incluído no valor do parâmetro de consulta filtro; a finalidade de pedir a mesma é realmente pedir uma representação mínima de um recurso que que satisfaça a expressão de filtro como indicação de permissão ou não existe qualquer desse objeto.   

Se o serviço foi criado utilizando as bibliotecas de infraestrutura de idioma comuns fornecidas pela Microsoft para implementar SCIM serviços, o pedido de ser traduzido uma chamada para o método de consulta do fornecedor do serviço.  O valor das propriedades do objeto fornecida como o valor do argumento parâmetros será da seguinte forma: 

* parâmetros. AlternateFilters.Count: 2
* parâmetros. AlternateFilters.ElementAt(x). AttributePath: "id"
* parâmetros. AlternateFilters.ElementAt(x). OperadorDeComparação: ComparisonOperator.Equals
* parâmetros. AlternateFilter.ElementAt(x). ComparisonValue: "54D382A4-2050-4C03-94D1-E769F1D15682"
* parâmetros. AlternateFilters.ElementAt(y). AttributePath: "o Gestor de"
* parâmetros. AlternateFilters.ElementAt(y). OperadorDeComparação: ComparisonOperator.Equals
* parâmetros. AlternateFilter.ElementAt(y). ComparisonValue: "2819c223-7f76-453a-919d-413861904646"
* parâmetros. RequestedAttributePaths.ElementAt(0): "id"
* parâmetros. SchemaIdentifier: "urn: ietf:params:scim:schemas:extension:enterprise:2.0:User"

Aqui, o valor do índice de x pode ser 0 e o valor de y o índice poderá ser 1, ou o valor de x pode ser 1 e o valor de y pode ser 0, consoante a ordem das expressões do parâmetro de consulta de filtro.   

**5:**  Eis um exemplo de um pedido a partir do Azure Active Directory num serviço de SCIM para atualizar um utilizador: 

    PATCH ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
    Authorization: Bearer ...
    Content-type: application/json
    {
      "schemas": 
      [
        "urn:ietf:params:scim:api:messages:2.0:PatchOp"],
      "Operations":
      [
        {
          "op":"Add",
          "path":"manager",
          "value":
            [
              {
                "$ref":"http://.../scim/Users/2819c223-7f76-453a-919d-413861904646",
                "value":"2819c223-7f76-453a-919d-413861904646"}]}]}

As bibliotecas de infraestrutura de idioma comuns Microsoft para implementar os serviços de SCIM seriam traduzir o pedido para uma chamada para o método de atualização do fornecedor do serviço.  Eis a assinatura desse método: 

    // System.Threading.Tasks.Tasks and 
    // System.Collections.Generic.IReadOnlyCollection<T>
    // are defined in mscorlib.dll.  
    // Microsoft.SystemForCrossDomainIdentityManagement.IPatch, 
    // Microsoft.SystemForCrossDomainIdentityManagement.PatchRequestBase, 
    // Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier, 
    // Microsoft.SystemForCrossDomainIdentityManagement.PatchOperation, 
    // Microsoft.SystemForCrossDomainIdentityManagement.OperationName, 
    // Microsoft.SystemForCrossDomainIdentityManagement.IPath and 
    // Microsoft.SystemForCrossDomainIdentityManagement.OperationValue 
    // are all defined in Microsoft.SystemForCrossDomainIdentityManagement.Protocol. 

    System.Threading.Tasks.Task Update(
      Microsoft.SystemForCrossDomainIdentityManagement.IPatch patch, 
      string correlationIdentifier);

    public interface Microsoft.SystemForCrossDomainIdentityManagement.IPatch
    {
    Microsoft.SystemForCrossDomainIdentityManagement.PatchRequestBase 
      PatchRequest 
        { get; set; }
    Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier 
      ResourceIdentifier 
        { get; set; }        
    }

    public class PatchRequest2: 
      Microsoft.SystemForCrossDomainIdentityManagement.PatchRequestBase
    {
    public System.Collections.Generic.IReadOnlyCollection
      <Microsoft.SystemForCrossDomainIdentityManagement.PatchOperation> 
        Operations
        { get;}

    public void AddOperation(
      Microsoft.SystemForCrossDomainIdentityManagement.PatchOperation operation);
    }

    public class PatchOperation
    {
    public Microsoft.SystemForCrossDomainIdentityManagement.OperationName 
      Name
      { get; set; }
    
    public Microsoft.SystemForCrossDomainIdentityManagement.IPath 
      Path
      { get; set; }

    public System.Collections.Generic.IReadOnlyCollection
      <Microsoft.SystemForCrossDomainIdentityManagement.OperationValue> Value
      { get; }

    public void AddValue(
      Microsoft.SystemForCrossDomainIdentityManagement.OperationValue value);
    }

    public enum OperationName
    {
      Add,
      Remove,
      Replace
    }

    public interface IPath
    {
      string AttributePath { get; }
      System.Collections.Generic.IReadOnlyCollection<IFilter> SubAttributes { get; }
      Microsoft.SystemForCrossDomainIdentityManagement.IPath ValuePath { get; }
    }

    public class OperationValue
    {
      public string Reference
      { get; set; }
      
      public string Value
      { get; set; }
    }



No caso de precedente exemplo de um pedido para atualizar um utilizador, o objeto fornecido como o valor do argumento patches terão estes valores de propriedade: 

* ResourceIdentifier.Identifier: "54D382A4-2050-4C03-94D1-E769F1D15682"
* ResourceIdentifier.SchemaIdentifier: "urn: ietf:params:scim:schemas:extension:enterprise:2.0:User"
* (PatchRequest como PatchRequest2). Operations.Count: 1
* (PatchRequest como PatchRequest2). Operations.ElementAt(0). OperationName: OperationName.Add
* (PatchRequest como PatchRequest2). Operations.ElementAt(0). Path.AttributePath: "o Gestor de"
* (PatchRequest como PatchRequest2). Operations.ElementAt(0). Value.Count: 1
* (PatchRequest como PatchRequest2). Operations.ElementAt(0). Value.ElementAt(0). Referência: http://.../scim/Users/2819c223-7f76-453a-919d-413861904646
* (PatchRequest como PatchRequest2). Operations.ElementAt(0). Value.ElementAt(0). Valor: 2819c223-7f76-453a-919d-413861904646

**6:**  Para anular aprovisionar um utilizador a partir de uma loja de identidade fronted por um serviço SCIM, Azure Active Directory irá enviar um pedido de como esta: 

    DELETE ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
    Authorization: Bearer ...
    
Se o serviço foi criado utilizando as bibliotecas de infraestrutura de idioma comuns fornecidas pela Microsoft para implementar SCIM serviços, o pedido de ser traduzido uma chamada para o método de eliminar do fornecedor do serviço.   Que método tem esta assinatura: 

    // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
    // Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier, 
    // is defined in Microsoft.SystemForCrossDomainIdentityManagement.Protocol. 
    System.Threading.Tasks.Task Delete(
      Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier  
        resourceIdentifier, 
      string correlationIdentifier);
 
O objeto fornecido como o valor do argumento resourceIdentifier terão estes valores de propriedade no caso do precedente exemplo de um pedido de anular aprovisionamento de um utilizador: 

* ResourceIdentifier.Identifier: "54D382A4-2050-4C03-94D1-E769F1D15682"
* ResourceIdentifier.SchemaIdentifier: "urn: ietf:params:scim:schemas:extension:enterprise:2.0:User"

##<a name="group-provisioning-and-de-provisioning"></a>Aprovisionamento de grupo e anular aprovisionamento

A figura seguinte mostra as mensagens que o Azure Active Directory irá enviar para um serviço SCIM para gerir o ciclo de vida de um grupo de identidade outra armazenam.  As mensagens de ser diferentes das mensagens relativos a utilizadores de três formas: 

* O esquema de um recurso de grupo será identificado como http://schemas.microsoft.com/2006/11/ResourceManagement/ADSCIM/Group.  
* Pedidos para obter grupos irão prever que o atributo membros a serem excluídos qualquer recurso fornecido na resposta ao pedido de.  
* Pedidos para determinar se um atributo referência tem um determinado valor será pedidos sobre o atributo membros.  

![][5]
*Figura: Aprovisionamento de grupo e anular a sequência de aprovisionamento*

##<a name="related-articles"></a>Artigos relacionados

- [Índice de artigo para gestão de aplicações no Azure Active Directory](active-directory-apps-index.md)
- [Automatizar utilizador aprovisionamento/Deprovisioning às aplicações SaaS](active-directory-saas-app-provisioning.md)
- [Personalizar os mapeamentos de atributo para aprovisionamento de utilizador](active-directory-saas-customizing-attribute-mappings.md)
- [Escrever expressões para mapeamentos de atributo](active-directory-saas-writing-expressions-for-attribute-mappings.md)
- [Controlo do âmbito filtros para aprovisionamento de utilizador](active-directory-saas-scoping-filters.md)
- [Notificações de aprovisionamento de conta](active-directory-saas-account-provisioning-notifications.md)
- [Lista de tutoriais sobre como integrar SaaS aplicações](active-directory-saas-tutorial-list.md)


    
<!--Image references-->
[1]: ./media/active-directory-scim-provisioning/scim-figure-1.PNG
[2]: ./media/active-directory-scim-provisioning/scim-figure-2.PNG
[3]: ./media/active-directory-scim-provisioning/scim-figure-3.PNG
[4]: ./media/active-directory-scim-provisioning/scim-figure-4.PNG
[5]: ./media/active-directory-scim-provisioning/scim-figure-5.PNG
