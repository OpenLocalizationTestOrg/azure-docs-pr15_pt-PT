<properties
    pageTitle="Funções de web e trabalhador PHP | Microsoft Azure"
    description="Um guia para a criação de funções de web e trabalhador PHP num serviço na nuvem Azure e configurar o tempo de execução PHP."
    services=""
    documentationCenter="php"
    authors="rmcmurray"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="cloud-services"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="PHP"
    ms.topic="article"
    ms.date="08/11/2016"
    ms.author="robmcm"/>

#<a name="how-to-create-php-web-and-worker-roles"></a>Como criar funções de web e trabalhador PHP

## <a name="overview"></a>Descrição geral

Este guia mostrar-lhe como criar funções de web ou trabalhador PHP num ambiente de desenvolvimento do Windows, escolha uma versão específica da PHP da versão de "incorporado" disponível, altere a configuração de PHP, activar extensões e por fim, implementar Azure. Também descreve como configurar uma função web ou de trabalho para utilizar um runtime PHP (com configuração personalizado e extensões) que indicar.

## <a name="what-are-php-web-and-worker-roles"></a>O que são funções de web e trabalhador PHP?

Azure fornece três calcular modelos para executar aplicações: aplicação de serviço de Azure, máquinas virtuais do Azure e serviços em nuvem Azure. Todos os modelos de três suportam PHP. Serviços em nuvem, que inclui funções web e trabalhador, fornece *plataforma como um serviço (PaaS)*. Dentro de um serviço na nuvem, uma função de web fornece um servidor web de serviços de informação Internet (IIS) dedicado às aplicações de front-end web do anfitrião. Uma função de trabalho pode executar tarefas assíncronas, execução longa ou perpétuos independentemente da interação do utilizador ou de entrada.

Para mais informações sobre estas opções, consulte o artigo [Calcular opções alojamento fornecidas pelo Azure](./cloud-services/cloud-services-choose-me.md).

## <a name="download-the-azure-sdk-for-php"></a>Transferir o SDK Azure para PHP

O [SDK do Azure para PHP] é composta por vários componentes. Este artigo irá utilizar duas delas: PowerShell do Azure e os Azure emuladores. Estes dois componentes podem ser instalados através do Microsoft Web plataforma Installer. Para mais informações, consulte o artigo [como instalar e configurar o Azure PowerShell](powershell-install-configure.md).

## <a name="create-a-cloud-services-project"></a>Criar um projeto de serviços em nuvem

É o primeiro passo na criação de uma função PHP web ou de trabalho criar um projeto do Azure Service. um projeto do Azure Service funciona como um contentor lógico para funções de web e trabalhador e contém ficheiros de [definição de serviço (.csdef)] e [configuração do serviço (.cscfg)] do projeto.

Para criar um novo projeto de Azure Service, execute o Azure PowerShell como administrador e execute o seguinte comando:

    PS C:\>New-AzureServiceProject myProject

Este comando irá criar um novo directório (`myProject`) para o qual pode adicionar funções web e trabalhador.

## <a name="add-php-web-or-worker-roles"></a>Adicionar PHP web ou trabalhador funções

Para adicionar uma função de web PHP a um projeto, execute o seguinte comando a partir do diretório de raiz do projeto:

    PS C:\myProject> Add-AzurePHPWebRole roleName

Para uma função de trabalho, utilize este comando:

    PS C:\myProject> Add-AzurePHPWorkerRole roleName

> [AZURE.NOTE] O `roleName` parâmetro é opcional. Se este for omitido, o nome da função será gerado automaticamente. A função web primeira criada estará `WebRole1`, o segundo será `WebRole2`, e assim sucessivamente. A função trabalhador primeira criada estará `WorkerRole1`, o segundo será `WorkerRole2`, e assim sucessivamente.

## <a name="specify-the-built-in-php-version"></a>Especificar a versão PHP incorporada

Quando adiciona uma função PHP web ou de trabalho a um projeto, ficheiros de configuração do projeto são modificados para que PHP será instalado em cada instância web ou de trabalho da sua aplicação quando é implementada. Para ver a versão do PHP será instalado por predefinição, execute o seguinte comando:

    PS C:\myProject> Get-AzureServiceProjectRoleRuntime

O resultado do comando acima terá uma aspeto semelhante ao que é mostrado abaixo. Neste exemplo, o `IsDefault` sinalizador está definido para `true` para PHP 5.3.17, indicando que irá estar a versão PHP predefinida instalada.

    Runtime Version     PackageUri                      IsDefault
    ------- -------     ----------                      ---------
    Node 0.6.17         http://nodertncu.blob.core...   False
    Node 0.6.20         http://nodertncu.blob.core...   True
    Node 0.8.4          http://nodertncu.blob.core...   False
    IISNode 0.1.21      http://nodertncu.blob.core...   True
    Cache 1.8.0         http://nodertncu.blob.core...   True
    PHP 5.3.17          http://nodertncu.blob.core...   True
    PHP 5.4.0           http://nodertncu.blob.core...   False

Pode configurar a versão de runtime PHP para qualquer uma das versões PHP que sejam encontram listadas. Por exemplo, para definir a versão PHP (para uma função com o nome `roleName`) para 5.4.0, utilize o seguinte comando:

    PS C:\myProject> Set-AzureServiceProjectRole roleName php 5.4.0

> [AZURE.NOTE] Versões PHP disponíveis podem mudar no futuro.

## <a name="customize-the-built-in-php-runtime"></a>Personalizar o runtime PHP incorporado

Vai ter controlo sobre a configuração do runtime PHP que está instalado ao seguir os passos acima, incluindo a modificação de `php.ini` definições e ativação da extensões.

Para personalizar o runtime PHP incorporado, siga estes passos:

1. Adicionar uma nova pasta denominada `php`, para o `bin` directório da sua função web. Para uma função de trabalho, adicione-a diretório de raiz a função.
2. No `php` pasta, criar outra pasta denominada `ext`. Colocar qualquer `.dll` ficheiros de extensão (por exemplo, `php_mongo.dll`) que pretende ativar nesta pasta.
3. Adicionar um `php.ini` do ficheiro para o `php` pasta. Ativar quaisquer extensões personalizadas e definir qualquer diretivas do PHP neste ficheiro. Por exemplo, se pretender ativar `display_errors` no e ativar o `php_mongo.dll` extensão, o conteúdo do seu `php.ini` ficheiro seria da seguinte forma:

        display_errors=On
        extension=php_mongo.dll

> [AZURE.NOTE] Quaisquer definições que não definidos explicitamente na `php.ini` ficheiro fornecer automaticamente vai ser definido como os respetivos valores predefinidos. No entanto, tenha em atenção que pode adicionar uma completa `php.ini` ficheiro.

## <a name="use-your-own-php-runtime"></a>Utilizar o seu próprio runtime PHP
Em alguns casos, em vez de selecionar um tempo de execução PHP incorporado e configurá-lo conforme descrito acima, poderá querer fornecer o seu próprio runtime PHP. Por exemplo, pode utilizar o mesmo runtime PHP numa função de web ou de trabalho que utilizar no seu ambiente de desenvolvimento. Isto torna mais fácil para se certificar de que a aplicação não será alterada comportamento no seu ambiente de produção.

### <a name="configure-a-web-role-to-use-your-own-php-runtime"></a>Configurar uma função de web para utilizar o seu próprio runtime PHP

Para configurar uma função de web para utilizar um tempo de execução PHP que fornece, siga estes passos:

1. Criar um projeto de serviço do Azure e adicionar uma função de web PHP, tal como descrito anteriormente neste tópico.
2. Criar um `php` pasta na `bin` pasta que está no diretório de raiz da sua função web e, em seguida, adicione o seu tempo de execução do PHP (todos os binários, ficheiros de configuração, subpastas, etc.) para o `php` pasta.
3. (OPCIONAL) Se a sua runtime PHP utiliza os [Controladores da Microsoft para PHP para SQL Server][sqlsrv drivers], terá de configurar a sua função web para instalar o [SQL Server 2012 cliente nativo] [ sql native client] quando está aprovisionado. Para executar esta tarefa, adicione o [instalador do sqlncli.msi x64] a `bin` pasta no diretório de raiz da sua função web. O script de arranque descrito no próximo passo será executar silenciosamente o instalador do quando a função está aprovisionada. Se o tempo de execução do PHP não utiliza o Drivers da Microsoft para PHP para SQL Server, pode remover a linha seguinte script apresentado no passo seguinte:

        msiexec /i sqlncli.msi /qn IACCEPTSQLNCLILICENSETERMS=YES

4. Definir uma tarefa de arranque que configura [Serviços de informação Internet (IIS)] [ iis.net] para utilizar o seu tempo de execução do PHP para processar pedidos de `.php` páginas. Para executar esta tarefa, abra o `setup_web.cmd` ficheiro (na `bin` ficheiro do diretório de raiz da sua função web) num editor de texto e substituir os seus conteúdos com o seguinte script:

        @ECHO ON
        cd "%~dp0"

        if "%EMULATED%"=="true" exit /b 0

        msiexec /i sqlncli.msi /qn IACCEPTSQLNCLILICENSETERMS=YES

        SET PHP_FULL_PATH=%~dp0php\php-cgi.exe
        SET NEW_PATH=%PATH%;%RoleRoot%\base\x86

        %WINDIR%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /+"[fullPath='%PHP_FULL_PATH%',maxInstances='12',idleTimeout='60000',activityTimeout='3600',requestTimeout='60000',instanceMaxRequests='10000',protocol='NamedPipe',flushNamedPipe='False']" /commit:apphost
        %WINDIR%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /+"[fullPath='%PHP_FULL_PATH%'].environmentVariables.[name='PATH',value='%NEW_PATH%']" /commit:apphost
        %WINDIR%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /+"[fullPath='%PHP_FULL_PATH%'].environmentVariables.[name='PHP_FCGI_MAX_REQUESTS',value='10000']" /commit:apphost
        %WINDIR%\system32\inetsrv\appcmd.exe set config -section:system.webServer/handlers /+"[name='PHP',path='*.php',verb='GET,HEAD,POST',modules='FastCgiModule',scriptProcessor='%PHP_FULL_PATH%',resourceType='Either',requireAccess='Script']" /commit:apphost
        %WINDIR%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /"[fullPath='%PHP_FULL_PATH%'].queueLength:50000"

5. Adicione os seus ficheiros de aplicação a diretório de raiz da sua função web. Este será diretório de raiz do servidor web.

6. Publicar a sua aplicação, tal como descrito na secção de [publicar a aplicação](#how-to-publish-your-application) abaixo.

> [AZURE.NOTE] O `download.ps1` script (na `bin` pasta do diretório de raiz da função web) podem ser eliminados depois de seguir os passos descritos acima para utilizar o seu próprio runtime PHP.

### <a name="configure-a-worker-role-to-use-your-own-php-runtime"></a>Configurar uma função de trabalho para utilizar o seu próprio runtime PHP

Para configurar uma função de trabalho para utilizar um tempo de execução PHP que fornece, siga estes passos:

1. Criar um projeto de serviço do Azure e adicione uma função de trabalho PHP, tal como descrito anteriormente neste tópico.
2. Criar um `php` pasta no diretório de raiz a função de trabalho e, em seguida, adicionar o seu runtime PHP (todos os binários, ficheiros de configuração, subpastas, etc.) para o `php` pasta.
3. (OPCIONAL) Se o seu tempo de execução do PHP utiliza [Controladores da Microsoft para PHP para SQL Server][sqlsrv drivers], terá de configurar a sua função trabalhador para instalar o [SQL Server 2012 cliente nativo] [ sql native client] quando está aprovisionado. Para executar esta tarefa, adicione o [instalador do sqlncli.msi x64] diretório de raiz a função de trabalho. O script de arranque descrito no próximo passo será executar silenciosamente o instalador do quando a função está aprovisionada. Se o tempo de execução do PHP não utiliza o Drivers da Microsoft para PHP para SQL Server, pode remover a linha seguinte script apresentado no passo seguinte:

        msiexec /i sqlncli.msi /qn IACCEPTSQLNCLILICENSETERMS=YES

4. Definir uma tarefa de arranque que adiciona o `php.exe` executável a variável de ambiente de caminho a função de trabalho quando a função está aprovisionada. Para executar esta tarefa, abra o `setup_worker.cmd` de ficheiros (no diretório de raiz a função de trabalho) num editor de texto e substituir os seus conteúdos com o seguinte script:

        @echo on

        cd "%~dp0"

        echo Granting permissions for Network Service to the web root directory...
        icacls ..\ /grant "Network Service":(OI)(CI)W
        if %ERRORLEVEL% neq 0 goto error
        echo OK

        if "%EMULATED%"=="true" exit /b 0

        msiexec /i sqlncli.msi /qn IACCEPTSQLNCLILICENSETERMS=YES

        setx Path "%PATH%;%~dp0php" /M

        if %ERRORLEVEL% neq 0 goto error

        echo SUCCESS
        exit /b 0

        :error

        echo FAILED
        exit /b -1

5. Adicione os ficheiros de aplicação ao diretório de raiz do seu perfil de trabalho.

6. Publicar a sua aplicação, tal como descrito na secção de [publicar a aplicação](#how-to-publish-your-application) abaixo.

## <a name="run-your-application-in-the-compute-and-storage-emulators"></a>Executar a sua aplicação no emuladores de cluster e armazenamento

Os Azure emuladores fornecem um ambiente do local onde pode testar as aplicações do Azure antes de implementá-lo na nuvem. Existem algumas diferenças entre os emuladores e o ambiente do Azure. Para compreender melhor isto, consulte o artigo [utilizar o emulador armazenamento Azure desenvolvimento e teste](./storage/storage-use-emulator.md).

Tenha em atenção que tem de ter instalado localmente para utilizar o emulador cluster PHP. O emulador cluster irá utilizar a sua instalação PHP local para executar a sua aplicação.

Para executar o projeto nas emuladores, execute o seguinte comando a partir do diretório de raiz do seu projeto:

    PS C:\MyProject> Start-AzureEmulator

Irá ver o resultado semelhante ao seguinte:

    Creating local package...
    Starting Emulator...
    Role is running at http://127.0.0.1:81
    Started

Pode ver a aplicação em execução no emulador de abrir um browser e navegando para o endereço local apresentado no resultado (`http://127.0.0.1:81` no resultado do exemplo acima).

Para parar os emuladores, execute este comando:

    PS C:\MyProject> Stop-AzureEmulator

## <a name="publish-your-application"></a>Publicar a sua aplicação

Para publicar a sua aplicação, tem de importar primeiro as definições de publicação utilizando o cmdlet [AzurePublishSettingsFile importar](https://msdn.microsoft.com/library/azure/dn790370.aspx) . Em seguida, pode publicar a sua aplicação utilizando o cmdlet [AzureServiceProject publicar](https://msdn.microsoft.com/library/azure/dn495166.aspx) . Para obter informações sobre como iniciar sessão, consulte o artigo [como instalar e configurar o Azure PowerShell](powershell-install-configure.md).

## <a name="next-steps"></a>Próximos passos

Para mais informações, consulte o [Centro de programadores do PHP](/develop/php/).

[Azure SDK para PHP]: /develop/php/common-tasks/download-php-sdk/
[install ps and emulators]: http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409
[definição de serviço (.csdef)]: http://msdn.microsoft.com/library/windowsazure/ee758711.aspx
[configuração do serviço (.cscfg)]: http://msdn.microsoft.com/library/windowsazure/ee758710.aspx
[iis.net]: http://www.iis.net/
[sql native client]: http://msdn.microsoft.com/sqlserver/aa937733.aspx
[sqlsrv drivers]: http://php.net/sqlsrv
[SQLNCli.msi x64 installer]: http://go.microsoft.com/fwlink/?LinkID=239648
