<properties
   pageTitle="Utilizar o Windows PowerShell Scripts para publicar para Dev Center e ambientes de teste | Microsoft Azure"
   description="Saiba como utilizar o Windows PowerShell scripts do Visual Studio para publicar para o desenvolvimento e testar ambientes."
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="08/15/2016"
   ms.author="tarcher" />

# <a name="using-windows-powershell-scripts-to-publish-to-dev-and-test-environments"></a>Utilizar scripts do Windows PowerShell para publicar Dev Center e testar ambientes

Quando cria uma aplicação web no Visual Studio, pode gerar um script do Windows PowerShell, que pode utilizar mais tarde para automatizar a publicação do seu Web site para Azure como uma aplicação Web na aplicação de serviço de Azure ou uma máquina virtual. Pode editar e expandir o script do Windows PowerShell no editor do Visual Studio para se adequar às suas necessidades ou integrar o script compilação existente, teste e scripts de publicação.

Utilizar estes scripts, pode aprovisionar o versões personalizadas (também conhecido como ambientes Dev Center e teste) do seu site para utilização temporária. Por exemplo, poderá configurar uma versão específica do seu Web site uma máquina virtual Azure ou a transição ranhura num Web site para executar um conjunto de aplicações de teste, reproduzir um erro, testar uma correcção de erros, versão de avaliação uma alteração proposta ou configurar um ambiente personalizado para uma demonstração ou apresentação. Após ter criado um script que publica o seu projeto, pode recriar ambientes idênticos executando novamente o script conforme necessário, ou executar o script com o seu próprio compilação da aplicação web para criar um ambiente personalizado para testar a ligação.

## <a name="what-you-need"></a>O que precisa

- Azure SDK 2.3 ou posterior. Consulte o artigo [Visual Studio transferências](http://go.microsoft.com/fwlink/?LinkID=624384) para obter mais informações.

Não é necessário o SDK do Azure para gerar os scripts para projetos web. Esta funcionalidade está para projetos web, não web funções serviços em nuvem.

- Azure PowerShell 0.7.4 ou posterior. Veja [como instalar e configurar o Azure PowerShell](powershell-install-configure.md) para obter mais informações.

- [O Windows PowerShell 3.0](http://go.microsoft.com/?linkid=9811175) ou posterior.

## <a name="additional-tools"></a>Ferramentas adicionais

Ferramentas e recursos para trabalhar com o PowerShell no Visual Studio para o desenvolvimento Azure adicionais estão disponíveis. Consulte o artigo [PowerShell ferramentas para o Visual Studio](http://go.microsoft.com/fwlink/?LinkId=404012).

## <a name="generating-the-publish-scripts"></a>Gerar os scripts de publicar

Pode gerar os scripts de publicar para uma máquina de virtual que aloja o seu Web site quando cria um novo projeto pelo que se seguem [estas instruções](./virtual-machines/virtual-machines-windows-classic-web-app-visual-studio.md). Também pode [Gerar publicar scripts para aplicações web na aplicação de serviço de Azure](./app-service-web/web-sites-dotnet-get-started.md).

## <a name="scripts-that-visual-studio-generates"></a>Scripts que gera Visual Studio

O Visual Studio gera uma pasta de nível de solução denominada **PublishScripts** que contém dois ficheiros do Windows PowerShell, um script de publicar para a sua máquina virtual ou Web site e um módulo que contém as funções que pode utilizar os scripts. Também o Visual Studio gera um ficheiro no formato JSON que especifica os detalhes do projeto que está a implementar.

### <a name="windows-powershell-publish-script"></a>Windows PowerShell publicar script

O script publicar contém passos de publicar específico para implementar a um Web site ou máquina virtual. O Visual Studio fornece sintaxe colorir para o desenvolvimento do Windows PowerShell. Ajuda para as funções está disponível e livre pode editar as funções no script de acordo com os seus requisitos de alteração.

### <a name="windows-powershell-module"></a>Módulo Windows PowerShell

O módulo Windows PowerShell, que o Visual Studio gera contém funções que utiliza o script publicar. Estas são as funções de PowerShell do Azure e não se destina a ser modificada. Veja [como instalar e configurar o Azure PowerShell](powershell-install-configure.md) para obter mais informações.

### <a name="json-configuration-file"></a>Ficheiro de configuração de JSON

O ficheiro JSON é criado na pasta **configurações** e contém os dados de configuração que especifica exatamente quais os recursos para implementar Azure. O nome do ficheiro que o Visual Studio gera é projeto-nome-WAWS-dev.json se tiver criado um Web site ou projeto nome-VM-dev.json se tiver criado uma máquina virtual. Eis um exemplo de um ficheiro de configuração de JSON é gerado quando criar um Web site. A maioria dos valores são facilmente compreensíveis. O nome do Web site é gerado pelo Azure, para que não poderá correspondam ao nome do seu projeto.

```
{
"environmentSettings": {
"webSite": {
"name": "WebApplication26632",
"location": "West US"
},
"databases": [
{
"connectionStringName": "DefaultConnection",
"databaseName": "WebApplication26632_db",
"serverName": "YourDatabaseServerName",
"user": "sqluser2",
"password": "",
"edition": "",
"size": "",
"collation": "",
"location": "West US"
}
]
}
}
```
Quando cria uma máquina virtual, o ficheiro de configuração de JSON aspeto semelhante ao seguinte. Tenha em atenção que é criado um serviço na nuvem como um contentor para a máquina virtual. A máquina virtual contém o habituais pontos finais para o web access através de HTTP e HTTPS, bem como os pontos finais para implementar Web, que permite-lhe publicar para o Web site a partir do computador local, o ambiente de trabalho remoto e o Windows PowerShell.

```
{
"environmentSettings": {
"cloudService": {
"name": "myusernamevm1",
"affinityGroup": "",
"location": "West US",
"virtualNetwork": "",
"subnet": "",
"availabilitySet": "",
"virtualMachine": {
"name": "myusernamevm1",
"vhdImage": "a699494373c04fc0bc8f2bb1389d6106__Win2K8R2SP1-Datacenter-201403.01-en.us-127GB.vhd",
"size": "Small",
"user": "vmuser1",
"password": "",
"enableWebDeployExtension": true,
"endpoints": [
{
"name": "Http",
"protocol": "TCP",
"publicPort": "80",
"privatePort": "80"
},
{
"name": "Https",
"protocol": "TCP",
"publicPort": "443",
"privatePort": "443"
},
{
"name": "WebDeploy",
"protocol": "TCP",
"publicPort": "8172",
"privatePort": "8172"
},
{
"name": "Remote Desktop",
"protocol": "TCP",
"publicPort": "3389",
"privatePort": "3389"
},
{
"name": "Powershell",
"protocol": "TCP",
"publicPort": "5986",
"privatePort": "5986"
}
]
}
},
"databases": [
{
"connectionStringName": "",
"databaseName": "",
"serverName": "",
"user": "",
"password": ""
}
],
"webDeployParameters": {
"iisWebApplicationName": "Default Web Site"
}
}
}
```

Pode editar a configuração de JSON para alterar o que acontece ao executar os scripts de publicar. O `cloudService` e `virtualMachine` secções são necessárias, mas pode eliminar o `databases` secção caso não precise da mesma. As propriedades que estiverem em branco no ficheiro de configuração predefinido que o Visual Studio gera são opcionais; aqueles que têm valores no ficheiro de configuração de predefinido são necessários.

Se tiver um Web site que tem vários ambientes de implementação (conhecidos como faixas) em vez de um site de produção única no Azure, pode incluir o nome de ranhura na nome do Web site no ficheiro de configuração de JSON. Por exemplo, se tiver um Web site que tem com o **meu site** nome e uma ranhura para o mesmo com o nome de **teste** , em seguida, o URI é test.cloudapp.net do meu site, mas o nome correto para utilizar no ficheiro de configuração é mysite(test). Só o pode fazer isto se o Web site e faixas já existem na sua subscrição. Se não existirem, crie o Web site ao executar o script sem especificar a ranhura, em seguida, criar a ranhura no [portal clássica Azure](http://go.microsoft.com/fwlink/?LinkID=213885)e, posteriormente, execute o script com o nome do Web site modificados. Para mais informações sobre faixas de implementação para web apps, consulte o artigo [Configurar ambientes para web apps no Azure aplicação de serviço de teste](./app-service-web/web-sites-staged-publishing.md).

## <a name="how-to-run-the-publish-scripts"></a>Como executar os scripts de publicar

Se nunca tiver executado um script do Windows PowerShell antes, primeiro tem de definir a política de execução para ativar scripts sejam executados. Esta é uma funcionalidade de segurança para impedir que os utilizadores de executarem scripts do Windows PowerShell se estiverem vulneráveis a software maligno ou vírus que envolvam executar scripts.

### <a name="run-the-script"></a>Executar o script

1. Crie o pacote Web implementar para o seu projeto. Um pacote Web implementar é num arquivo comprimido (ficheiro. zip) que contêm os ficheiros que pretende copiar para o seu Web site ou máquina virtual. Pode criar Web implementar pacotes no Visual Studio para qualquer aplicação web.

![Criar Web implementar o pacote](./media/vs-azure-tools-publishing-using-powershell-scripts/IC767885.png)

Para obter mais informações, consulte o artigo [como: criar um pacote de implementação de Web no Visual Studio](https://msdn.microsoft.com/library/dd465323.aspx). Também pode automatizar a criação do seu pacote Web implementar, conforme descrito na secção **Personalizar e expandir os scripts de publicar** mais adiante.

1. No **Explorador de soluções**, abra o menu de contexto para o script e, em seguida, escolha **Abrir com o ISE do PowerShell**.

1. Se esta for a primeira vez que tenha executar scripts do Windows PowerShell neste computador, abra uma janela de linha de comandos com privilégios de administrador e escreva o seguinte comando:

`Set-ExecutionPolicy RemoteSigned`

1. Inicie sessão no Azure utilizando o comando seguinte.

`Add-AzureAccount`

Quando lhe for pedido, fornece o seu nome de utilizador e palavra-passe.

Tenha em atenção que, quando automatizar o script, este método de fornecimento credenciais Azure não irá funcionar. Em vez disso, deve utilizar o ficheiro .publishsettings para fornecer credenciais. Uma vez só, que utilize o comando **Get-AzurePublishSettingsFile** para transferir o ficheiro a partir do Azure e, posteriormente, utilizar **AzurePublishSettingsFile importação** para importar o ficheiro. Para obter instruções detalhadas, consulte o artigo [como instalar e configurar o Azure PowerShell](powershell-install-configure.md).

1. (Opcional) Se pretende criar recursos Azure como a máquina virtual, a base de dados e o Web site sem a publicar a sua aplicação web, utilize o comando de **Publicar WebApplication.ps1** com o **-configuração** argumento definido para o ficheiro de configuração de JSON. Esta linha de comandos utiliza o ficheiro de configuração de JSON para determinar quais os recursos para criar. Uma vez que utiliza as definições predefinidas para outros argumentos da linha de comandos, cria os recursos, mas não publicar a sua aplicação web. O – verboso opção fornece-lhe mais informações sobre o que se passa.

`Publish-WebApplication.ps1 -Verbose –Configuration C:\Path\WebProject-WAWS-dev.json`

1. Utilize o comando de **Publicar WebApplication.ps1** conforme apresentado dos exemplos seguintes para invocar o script e publicar a sua aplicação web. Se precisar de substituir as predefinições para qualquer um dos outros argumentos, tal como o nome da subscrição, publicar o nome do pacote, credenciais de máquina virtual ou as credenciais do servidor de base de dados, pode especificar os parâmetros. Utilizar o **– verboso** opção para ver mais informações sobre o progresso do processo de publicação.

```
Publish-WebApplication.ps1 –Configuration C:\Path\WebProject-WAWS-dev-json `
–SubscriptionName Contoso `
-WebDeployPackage C:\Documents\Azure\ADWebApp.zip `
-DatabaseServerPassword @{Name="dbServerName";Password="adminPassword"} `
-Verbose
```

Se estiver a criar uma máquina virtual, o comando aspeto semelhante ao seguinte. Este exemplo mostra também como especificar as credenciais para várias bases de dados. Para as máquinas virtuais que estes scripts criar, o certificado SSL não está a partir de uma autoridade raiz fidedigna. Por isso, tem de utilizar a opção **– AllowUntrusted** .

```
Publish-WebApplication.ps1 `
-Configuration C:\Path\ADVM-VM-test.json `
-SubscriptionName Contoso `
-WebDeployPackage C:\Path\ADVM.zip `
-AllowUntrusted `
-VMPassword @{name = "vmUserName"; password = "YourPasswordHere"} `
-DatabaseServerPassword @{Name="server1";Password="adminPassword1"}, @{Name="server2";Password="adminPassword2"} `
-Verbose
```

O script pode criar bases de dados, mas não será criada os servidores de base de dados. Se pretender criar um servidor de base de dados, pode utilizar a função **AzureSqlDatabaseServer de novo** no módulo Azure.

## <a name="customizing-and-extending-the-publish-scripts"></a>Personalizar e expandir os scripts de publicar

Pode personalizar o script de publicar e o ficheiro de configuração de JSON. As funções no módulo Windows PowerShell **AzureWebAppPublishModule.psm1** destinam-se não ser alterada. Se apenas quiser especificar uma base de dados diferente ou alterar algumas das propriedades da máquina virtual, edite o ficheiro de configuração de JSON. Se pretender aumentar a funcionalidade de script para automatizar a criação e testar o seu projeto, pode implementar fragmentos da função no **WebApplication.ps1 publicar**.

Para automatizar a criação do seu projeto, adicione o código que chamadas MSBuild para `New-WebDeployPackage` conforme mostrado no exemplo código. O caminho para o comando MSBuild é diferente consoante a versão do Visual Studio tiver instalado. Para obter o caminho correto, pode utilizar a função **Get-MSBuildCmd**, tal como é mostrado neste exemplo.

### <a name="to-automate-building-your-project"></a>Para automatizar a criação do seu projeto

1. Adicionar o `$ProjectFile` parâmetro na secção global param.

```
[Parameter(Mandatory = $false)]
  [ValidateScript({Test-Path $_ -PathType Leaf})]
  [String]
  $ProjectFile,
```

1. Copiar a função `Get-MSBuildCmd` para o seu ficheiro de script.

```
function Get-MSBuildCmd
{
        process
{

             $path =  Get-ChildItem "HKLM:\SOFTWARE\Microsoft\MSBuild\ToolsVersions\" |
                                   Sort-Object {[double]$_.PSChildName} -Descending |
                                   Select-Object -First 1 |
                                   Get-ItemProperty -Name MSBuildToolsPath |
                                   Select -ExpandProperty MSBuildToolsPath
       
            $path = (Join-Path -Path $path -ChildPath 'msbuild.exe')

        return Get-Item $path
    }
}
```

1. Substituir `New-WebDeployPackage` com o seguinte código e substitua os marcadores de posição de linha construir `$msbuildCmd`. Este código é para Visual Studio 2015. Se estiver a utilizar o Visual Studio 2013, altere a propriedade de **VisualStudioVersion** abaixo para `12.0`.

```
function New-WebDeployPackage
{
    #Write a function to build and package your web application
      
#To build your web application, use MsBuild.exe. For help, see MSBuild Command-Line Reference at: http://go.microsoft.com/fwlink/?LinkId=391339
      
Write-VerboseWithTime 'Build-WebDeployPackage: Start'
      
$msbuildCmd = '"{0}" "{1}" /T:Rebuild;Package /P:VisualStudioVersion=14.0 /p:OutputPath="{2}\MSBuildOutputPath" /flp:logfile=msbuild.log,v=d' -f (Get-MSBuildCmd), $ProjectFile, $scriptDirectory
      
Write-VerboseWithTime ('Build-WebDeployPackage: ' + $msbuildCmd)
      
#Start execution of the build command
$job = Start-Process cmd.exe -ArgumentList('/C "' + $msbuildCmd + '"') -WindowStyle Normal -Wait -PassThru
      
if ($job.ExitCode -ne 0)
{
throw('MsBuild exited with an error. ExitCode:' + $job.ExitCode)
}

#Obtain the project name
$projectName = (Get-Item $ProjectFile).BaseName
      
#Construct the path to web deploy zip package
$DeployPackageDir =  '.\MSBuildOutputPath\_PublishedWebsites\{0}_Package\{0}.zip' -f $projectName
      
      
#Get the full path for the web deploy zip package. This is required for MSDeploy to work
$WebDeployPackage = Resolve-Path –LiteralPath $DeployPackageDir
      
Write-VerboseWithTime 'Build-WebDeployPackage: End'
      
return $WebDeployPackage
}
```

1. Ligar a `New-WebDeployPackage` função antes deste linha: `$Config = Read-ConfigFile $Configuration` para web apps ou `$Config = Read-ConfigFile $Configuration -HasWebDeployPackage:([Bool]$WebDeployPackage)` para máquinas virtuais.

```
if($ProjectFile)
{
$WebDeployPackage = New-WebDeployPackage
}
```

1. Invocar o script personalizado a partir da linha de comandos utilizando passagem a `$Project` argumento, tal como no exemplo seguinte linha de comandos.

```
.\Publish-WebApplicationVM.ps1 -Configuration .\Configurations\WebApplication5-VM-dev.json `
-ProjectFile ..\WebApplication5\WebApplication5.csproj `
-VMPassword @{Name="VMUser";Password="Test.123"} `
-AllowUntrusted `
-Verbose
```

Para automatizar testes da sua aplicação, adicione o código para `Test-WebApplication`. Certifique-se de que as linhas de **Publicar WebApplication.ps1** onde estas funções chamam-se de remova os comentários. Se não fornecer uma implementação, pode criar manualmente o seu projeto com o Visual Studio e, em seguida, execute o script de publicar para publicar o Azure.

## <a name="publishing-function-summary"></a>Publicação função de resumo

Para obter ajuda para funções que pode utilizar os comandos do Windows PowerShell, utilize o comando `Get-Help function-name`. A ajuda inclui o parâmetro ajuda e exemplos. Também é o mesmo texto de ajuda nos ficheiros de origem script, **AzureWebAppPublishModule.psm1** e **WebApplication.ps1 publicar**. O script e ajuda está localizada no idioma Visual Studio.

**AzureWebAppPublishModule**

|Nome de função|Descrição|
|---|---|
|AzureSQLDatabase adicionar|Cria uma nova base de dados do Azure SQL.|
|AzureSQLDatabases adicionar|Cria bases de dados Azure SQL a partir de valores no ficheiro de configuração JSON que o Visual Studio gera.|
|AzureVM adicionar|Cria uma máquina virtual Azure e devolve o URL da VM implementada. A função configura os pré-requisitos e, em seguida, liga para a função de **Novo AzureVM** (módulo Azure) para criar uma nova máquina virtual.|
|AzureVMEndpoints adicionar|Adiciona os pontos finais de entrada novos para uma máquina virtual e devolve a máquina virtual com o novo ponto final.|
|AzureVMStorage adicionar|Cria uma nova conta de armazenamento Azure na subscrição atual. O nome da conta começa com "devtest" seguido de uma cadeia de alfanumérica exclusiva. A função devolve o nome da nova conta de armazenamento. Tem de especificar uma localização ou um grupo de afinidade para a nova conta de armazenamento.|
|AzureWebsite adicionar|Cria um Web site com o nome especificado e a localização. Esta função chamadas a função **AzureWebsite de novo** no módulo Azure. Se a subscrição já não inclui um Web site com o nome especificado, esta função cria o Web site e devolve um objeto de Web site. Caso contrário, devolve `$null`.|
|Cópia de segurança da subscrição|Guarda a subscrição Azure atual na `$Script:originalSubscription` variável no âmbito de script. Esta função guarda a subscrição Azure atual (como obtidos por `Get-AzureSubscription -Current`) e sua conta de armazenamento e a subscrição da qual é alterada por este script (armazenado na variável `$UserSpecifiedSubscription`) e a sua conta de armazenamento, no âmbito de script. Ao guardar os valores, pode utilizar uma função, tais como `Restore-Subscription`, para restaurar a conta de armazenamento e de subscrição atual original para o estado atual, se o estado atual tiver sido alterada.|
|Localizar AzureVM|Obtém a máquina de virtual Azure especificada.|
|Formato DevTestMessageWithTime|Anexa a data e hora a uma mensagem. Esta função foi concebida para mensagens escritas para as sequências de erro e verboso.|
|Get-AzureSQLDatabaseConnectionString|Reúne uma cadeia de ligação para ligar a uma base de dados do Azure SQL.|
|Get-AzureVMStorage|Devolve o nome da conta de armazenamento primeiro com o padrão de nome "devtest*" (maiúsculas e minúsculas) na localização especificada ou afinidade do grupo. Se o "devtest*" a conta de armazenamento não corresponde a localização ou grupo de afinidade, a função ignora-lo. Tem de especificar uma localização ou um grupo de afinidade.|
|Get-MSDeployCmd|Devolve um comando para executar a ferramenta de MsDeploy.exe.|
|Novo AzureVMEnvironment|Localiza ou cria uma máquina virtual na subscrição que corresponde aos valores no ficheiro de configuração de JSON.|
|WebPackage publicar|Utilizações MsDeploy.exe e um web publicar o pacote. Ficheiro zip para implementar recursos para um Web site. Esta função não gera nenhuma saída. Se a chamada para MSDeploy.exe falhar, a função inicia uma exceção. Para obter mais detalhada saída, utilize o **-verboso** opção.|
|WebPackageToVM publicar|Verifica se os valores de parâmetros e, em seguida, liga para a função **WebPackage publicar** .|
|ConfigFile de leitura|Validar o ficheiro de configuração de JSON e devolve uma tabela de hash dos valores selecionados.|
|Restaurar a subscrição|Repõe a subscrição atual para a subscrição original.|
|Teste AzureModule|Devolve `$true` se a versão do módulo Azure instalado é 0.7.4 ou posterior. Devolve `$false` se o módulo não estiver instalado ou é uma versão anterior. Esta função não tem parâmetros.|
|Teste AzureModuleVersion|Devolve `$true` se a versão do módulo Azure é 0.7.4 ou posterior. Devolve `$false` se o módulo não estiver instalado ou é uma versão anterior. Esta função não tem parâmetros.|
|Teste HttpsUrl|Converte o URL de entrada para um objeto de System.Uri. Devolve `$True` se o URL é absoluto e a esquema é https. Devolve `$false` se o URL for relativo, a esquema de cores não está HTTPS ou a cadeia de entrada, não pode ser convertida para um URL.|
|Membro de teste|Devolve `$true` se uma propriedade ou método é um membro do objeto. Caso contrário, devolve `$false`.|
|ErrorWithTime de escrita|Escreve uma mensagem de erro o prefixo com a hora atual. Esta função chamadas a função de **Formato DevTestMessageWithTime** para anexar como prefixo o tempo antes de escrever a mensagem para a sequência de erro.|
|HostWithTime de escrita|Escreve uma mensagem para o programa de anfitrião (**Anfitrião de escrita**) o prefixo com a hora atual. O efeito de escrita para o programa anfitrião varia. A maioria dos programas que alojem o Windows PowerShell escrita estas mensagens de saída padrão.|
|VerboseWithTime de escrita|Escreve uma mensagem verbosa o prefixo com a hora atual. Uma vez que as chamadas de **Escrita verboso**, a mensagem apresenta apenas quando o script é executado com o parâmetro **verboso** ou quando a preferência **VerbosePreference** está definida para **continuar**.|

**WebApplication publicar**

|Nome de função|Descrição|
|---|---|
|Novo AzureWebApplicationEnvironment|Cria Azure recursos, como um Web site ou máquina virtual.|
|Novo WebDeployPackage|Esta função não está implementada. Pode adicionar comandos nesta função para criar o seu projeto.|
|AzureWebApplication publicar|Publica uma aplicação web para Azure.|
|WebApplication publicar|Cria e implementa Web Apps, máquinas virtuais, bases de dados SQL e contas de armazenamento para um projeto do Visual Studio web.|
|Teste WebApplication|Esta função não está implementada. Pode adicionar comandos nesta função para testar a sua aplicação.|

## <a name="next-steps"></a>Próximos passos

Obter mais informações sobre a criação de scripts PowerShell ao ler o artigo [Scripting com o Windows PowerShell](https://technet.microsoft.com/library/bb978526.aspx) e ver outros scripts de Azure PowerShell no [Centro de Script](https://azure.microsoft.com/documentation/scripts/).
