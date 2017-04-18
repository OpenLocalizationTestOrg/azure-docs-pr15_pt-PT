<properties
    pageTitle="Azure aplicação de serviço do web app config e extensões avançadas"
    description="Utilize as declarações de Transformation(XDT) de documento XML para transformar o ficheiro config na aplicação web do serviço de aplicação do Azure e adicionar extensões privadas para ativar as ações personalizadas de administração."
    authors="cephalin"
    writer="cephalin"
    editor="mollybos"
    manager="wpickett"
    services="app-service"
    documentationCenter=""/>

<tags
    ms.service="app-service"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="02/25/2016"
    ms.author="cephalin"/>

# <a name="azure-app-service-web-app-advanced-config-and-extensions"></a>Azure aplicação de serviço do web app config e extensões avançadas

Ao utilizar as declarações de [Transformação de documento XML](http://msdn.microsoft.com/library/dd465326.aspx) (XDT), o utilizador pode transformar o ficheiro [config](http://www.iis.net/learn/get-started/planning-your-iis-architecture/introduction-to-applicationhostconfig) na sua aplicação web na aplicação de serviço de Azure. Também pode utilizar as declarações de XDT para adicionar extensões privadas para permitir ações de administração de aplicação web personalizada. Este artigo inclui uma extensão de aplicação de web de PHP Gestor de exemplo que permite a gestão das definições de PHP através de uma interface de web.

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

##<a id="transform"></a>Configuração avançada através de config
A plataforma de aplicação de serviço fornece flexibilidade e controlo para configuração da aplicação web. Apesar do ficheiro de configuração do IIS config padrão não está disponível para edição direta na aplicação de serviço, a plataforma suporta um modelo de transformação de config declarativa com base no transformação de documento XML (XDT).

Para tirar partido esta funcionalidade de transformação, criar um ficheiro de ApplicationHost.xdt com conteúdo XDT e colocar da raiz do site (d:\home\site) na [Consola Kudu](https://github.com/projectkudu/kudu/wiki/Kudu-console). Poderá ter de reiniciar a aplicação Web para as alterações sejam aplicadas.

O exemplo applicationHost.xdt seguinte mostra como adicionar uma nova variável de ambiente personalizado para uma aplicação web que utiliza PHP 5.4.

    <?xml version="1.0"?>
    <configuration xmlns:xdt="http://schemas.microsoft.com/XML-Document-Transform">
        <system.webServer>
                <fastCgi>
                    <application>
                        <environmentVariables>
                                <environmentVariable name="CONFIGTEST" value="TEST" xdt:Transform="Insert" xdt:Locator="XPath(/configuration/system.webServer/fastCgi/application[contains(@fullPath,'5.4')]/environmentVariables)" />
                        </environmentVariables>
                    </application>
                </fastCgi>
        </system.webServer>
    </configuration>


Um ficheiro de registo com o estado de transformação e detalhes está disponível a partir da raiz FTP em LogFiles\Transform.

Para obter exemplos adicionais, consulte o artigo [https://github.com/projectkudu/kudu/wiki/Xdt-transform-samples](https://github.com/projectkudu/kudu/wiki/Xdt-transform-samples).

**Nota**<br />
Elementos a partir da lista de módulos em `system.webServer` não podem ser removidos ou reordenados, mas são possíveis adições à lista.


##<a id="extend"></a>Expandir a sua aplicação web

###<a id="overview"></a>Descrição geral de extensões de aplicação web privadas

Aplicação de serviço suporta extensões de aplicação web como um ponto de expansão para ações administrativas. Na verdade, algumas funcionalidades de plataforma de aplicação de serviço são implementadas como extensões de pré-instaladas. Enquanto não podem ser modificadas as extensões de plataforma pré-instalado, pode criar e configurar as extensões de privado para o seu próprio web app. Esta funcionalidade também depende de declarações de XDT. Os passos principais para criar uma extensão de aplicação web privadas são as seguintes:

1. Aplicação extensão **conteúdo**da Web: criar uma aplicação web suportada pelo serviço de aplicação
2. Aplicação extensão **declaração**da Web: criar um ficheiro de ApplicationHost.xdt
3. Aplicação extensão **implementação**da Web: colocar conteúdo na pasta SiteExtensions em`root`

As hiperligações internas para a aplicação web devem apontar para um caminho relativamente ao caminho da aplicação especificado no ficheiro ApplicationHost.xdt. Qualquer alteração no ficheiro ApplicationHost.xdt necessita de uma reciclagem de aplicação web.

**Nota**: informações adicionais para estes elementos chaves estão disponíveis em [https://github.com/projectkudu/kudu/wiki/Azure-Site-Extensions](https://github.com/projectkudu/kudu/wiki/Azure-Site-Extensions).

Um exemplo detalhado é incluído para ilustrar os passos para criar e ativar uma extensão de aplicação web privada. O código de origem para o Gestor de PHP exemplo que se segue pode ser transferido das [https://github.com/projectkudu/PHPManager](https://github.com/projectkudu/PHPManager).

###<a id="SiteSample"></a>Exemplo de extensão de aplicação Web: PHP Gestor

Gestor de PHP é uma extensão de aplicação web que permite aos web administradores da aplicação facilmente ver e configurar as definições de PHP utilizando uma interface de web em vez de modificar os ficheiros. ini PHP diretamente. Ficheiros de configuração comum para PHP incluem o ficheiro de PHP. ini localizado em ficheiros de programas e o. user.ini ficheiro localizado na pasta raiz da sua aplicação web. Uma vez que o ficheiro PHP. ini não for editável diretamente a plataforma de aplicação de serviço, utiliza a extensão do Gestor de PHP o. user.ini ficheiro para aplicar alterações de definição.

####<a id="PHPwebapp"></a>A aplicação web do Gestor de PHP

A home page da implementação PHP gestor é o seguinte:

![TransformSitePHPUI][TransformSitePHPUI]

Como pode ver, é uma extensão de aplicação web tal como uma aplicação web normal, mas com um ficheiro ApplicationHost.xdt adicional colocado na pasta raiz da aplicação web (mais detalhes sobre o ficheiro ApplicationHost.xdt estão disponíveis na secção seguinte deste artigo).

A extensão do Gestor de PHP foi criado utilizando o modelo do Visual Studio ASP.NET MVC 4 aplicação Web. Vista a seguir a partir do Explorador de solução mostra a estrutura da extensão PHP gestor.

![TransformSiteSolEx][TransformSiteSolEx]

A lógica apenas especial conforme necessária para o ficheiro e/s é para indicar onde está localizado o diretório de wwwroot da aplicação web. Como exemplo de código que se segue mostra, ambiente variáveis "base" indica o caminho da raiz do web app e o caminho de wwwroot pode ser criado por acrescentá "site\wwwroot":

    /// <summary>
    /// Gives the location of the .user.ini file, even if one doesn't exist yet
    /// </summary>
    private static string GetUserSettingsFilePath()
    {
            var rootPath = Environment.GetEnvironmentVariable("HOME"); // For use on Azure Websites
            if (rootPath == null)
            {
                rootPath = System.IO.Path.GetTempPath(); // For testing purposes
            };
            var userSettingsFile = Path.Combine(rootPath, @"site\wwwroot\.user.ini");
            return userSettingsFile;
    }


Depois de ter o caminho do diretório, pode utilizar operações de e/s ficheiro normal para ler e escrever para ficheiros.

Um ponto de cuidado com as extensões de aplicação web no que diz respeito o processamento de hiperligações internas.  Se tiver as ligações nos seus ficheiros HTML que fornecem caminhos absolutos para ligações internas na sua aplicação web, certifique-se dessas que ligações são pré-concebido com o seu nome de extensão como a raiz. Isto é necessária porque a raiz para a sua extensão é agora "/`[your-extension-name]`/" em vez de serem apenas "/", por isso, qualquer internas ligações têm de ser uma atualizadas em conformidade. Por exemplo, suponha que o seu código inclui uma ligação para o seguinte procedimento:

`"<a href="/Home/Settings">PHP Settings</a>"`

Quando a ligação fizer parte de uma extensão de aplicação web, a ligação deve estar da seguinte forma:

`"<a href="/[your-site-name]/Home/Settings">Settings</a>"`

Pode contornar este requisito por um utilizar apenas os caminhos relativos dentro da sua aplicação web ou, no caso de aplicações do ASP.NET, utilizando o `@Html.ActionLink` método que cria nas ligações apropriadas por si.

####<a id="XDT"></a>O ficheiro applicationHost.xdt

O código para a sua extensão de aplicação web vai em %HOME%\SiteExtensions\[seu extensão de nome]. Irá chamamos isto na raiz da extensão.  

Para registar a sua extensão de aplicação web com o ficheiro Config, tem de colocar um ficheiro denominado ApplicationHost.xdt na raiz da extensão. O conteúdo do ficheiro ApplicationHost.xdt deve ser da seguinte forma:

    <?xml version="1.0"?>
    <configuration xmlns:xdt="http://schemas.microsoft.com/XML-Document-Transform">
        <system.applicationHost>
                <sites>
                    <site name="%XDT_SCMSITENAME%" xdt:Locator="Match(name)">
                        <!-- NOTE: Add your extension name in the application paths below -->
                        <application path="/[your-extension-name]" xdt:Locator="Match(path)" xdt:Transform="Remove" />
                        <application path="/[your-extension-name]" applicationPool="%XDT_APPPOOLNAME%" xdt:Transform="Insert">
                            <virtualDirectory path="/" physicalPath="%XDT_EXTENSIONPATH%" />
                        </application>
                    </site>
                </sites>
        </system.applicationHost>
    </configuration>

O nome que selecionar como o nome da extensão deverá ter o mesmo nome como a sua pasta de raiz da extensão.

Isto tem o efeito da ação de adicionar um novo caminho de aplicação para o `system.applicationHost` lista de sites sob o site SMS. O site SMS é um ponto de fim de administração do site com as credenciais de acesso específico. Tiver o URL `https://[your-site-name].scm.azurewebsites.net`.  

    <system.applicationHost>
        ...
        <site name="~1[your-website]" id="1716402716">
                <bindings>
                    <binding protocol="http" bindingInformation="*:80: [your-website].scm.azurewebsites.net" />
                    <binding protocol="https" bindingInformation="*:443: [your-website].scm.azurewebsites.net" />
                </bindings>
                <traceFailedRequestsLogging enabled="false" directory="C:\DWASFiles\Sites\[your-website]\VirtualDirectory0\LogFiles" />
                <detailedErrorLogging enabled="false" directory="C:\DWASFiles\Sites\[your-website]\VirtualDirectory0\LogFiles\DetailedErrors" />
                <logFile logSiteId="false" />
                <application path="/" applicationPool="[your-website]">
                    <virtualDirectory path="/" physicalPath="D:\Program Files (x86)\SiteExtensions\Kudu\1.24.20926.5" />
                </application>
                <!-- Note the custom changes that go here -->
                <application path="/[your-extension-name]" applicationPool="[your-website]">
                    <virtualDirectory path="/" physicalPath="C:\DWASFiles\Sites\[your-website]\VirtualDirectory0\SiteExtensions\[your-extension-name]" />
                </application>
            </site>
    </sites>
      ...
    </system.applicationHost>

###<a id="deploy"></a>Implementação de extensão de aplicação Web

Para instalar a sua extensão de aplicação web, pode utilizar FTP para copiar todos os ficheiros da sua aplicação web para a `\SiteExtensions\[your-extension-name]` pasta da aplicação web na qual pretende instalar a extensão.  Certifique-se de que copie o ficheiro ApplicationHost.xdt para desta localização também. Reinicie a aplicação web para ativar a extensão.

Deverá conseguir ver a sua extensão de aplicação web no:

`https://[your-site-name].scm.azurewebsites.net/[your-extension-name]`

Tenha em atenção que o URL parece tal como o URL para a aplicação web, exceto que utiliza o HTTPS e contém ".scm".

É possível desativar todas as extensões (não previamente instaladas) privadas para a sua aplicação web durante desenvolvimento e investigações adicionando um definições da aplicação com a tecla `WEBSITE_PRIVATE_EXTENSIONS` e um valor de `0`.

>[AZURE.NOTE] Se pretender começar a aplicação de serviço de Azure antes de inscrever-se para uma conta do Azure, aceda ao [Tentar aplicação de serviço](http://go.microsoft.com/fwlink/?LinkId=523751), onde imediatamente pode criar uma aplicação web do starter curto na aplicação de serviço. Sem cartões de crédito necessários; sem compromissos.

## <a name="whats-changed"></a>O que é alterado
* Para um guia para a alteração de Web sites para a aplicação de serviço Consulte o artigo: [aplicação de serviço de Azure e respectivo impacto na existente dos serviços do Azure](http://go.microsoft.com/fwlink/?LinkId=529714)

<!-- IMAGES -->
[TransformSitePHPUI]: ./media/web-sites-transform-extend/TransformSitePHPUI.png
[TransformSiteSolEx]: ./media/web-sites-transform-extend/TransformSiteSolEx.png
 
