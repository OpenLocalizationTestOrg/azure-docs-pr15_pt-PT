<properties
    pageTitle="Como instalar e configurar PowerShell do Azure"
    description="Saiba como instalar e configurar o Azure PowerShell."
    editor="tysonn"
    manager="dongill"
    documentationCenter=""
    services=""
    authors="coreyp-at-msft"/>

<tags
    ms.service="multiple"
    ms.workload="multiple"
    ms.tgt_pltfrm="powershell"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/30/2016"
    ms.author="coreyp"/>

# <a name="how-to-install-and-configure-azure-powershell"></a>Como instalar e configurar PowerShell do Azure

<div class="dev-center-tutorial-selector sublanding"><a href="/manage/install-and-configure-windows-powershell/" title="PowerShell" class="current">PowerShell</a><a href="/manage/install-and-configure-cli/" title="Azure CLI">clip Azure</a></div>

##<a name="what-is-azure-powershell"></a>O que é o Azure PowerShell?
Azure PowerShell é um conjunto de módulos que fornecem cmdlets para gerir Azure com o Windows PowerShell. Pode utilizar os cmdlets para criar, teste, implementar e gerir soluções e serviços entregues através da plataforma Azure. Na maioria dos casos, os cmdlets pode ser utilizados para as mesmas tarefas como o Portal do Azure, tais como criar e configurar serviços em nuvem, máquinas virtuais, redes virtuais e aplicações web.

## <a name="how-versioning-works"></a>Como funciona o controlo de versões

Controlo de versões semântico, que significa que, se utiliza o Azure PowerShell versão respostas > versão B, em seguida, versão A tem as APIs da mais recentes. Além disso, significa que as alterações da média de versão principal uma quebra de alterar numa ou mais cmdlets.  Por isso, por exemplo, versão 1.7.0 é uma correcção para endereçar uma alteração quebra nas versões 1. x do Azure PowerShell.

Para mais informações sobre práticas de controlo de versões semântico no Azure PowerShell, consulte a especificação de controlo de versões semântico na: http://semver.org
 
Para obter as mais recentes APIs, deve utilizar versão 2. x. Mas se tiver scripts escritos contra versão 1. x e não pretender absorva as alterações recentes na versão 2. x descrito nas [Notas de lançamento](https://github.com/Azure/azure-powershell/blob/dev/documentation/release-notes/migration-guide.2.0.0.md), de 2. x, em seguida, deve instalar 1.7.0.

Um erro de correspondência de versão pode resultar se está instalada a versão mais recente do módulo perfil e uma versão anterior de um módulo que depende-la subsequentemente é carregada. A forma mais simples para resolver este problema é instalar a partir do. msi mais recente. O. msi limpa automaticamente versões mais antigas do módulos.
 
###<a name="installing-module-versions-side-by-side"></a>Instalar o módulo versões lado a lado

Versão 2.1.0 (e versão 1.2.6 para AzureStack) são as versões de módulo primeiros concebidas para ser instalado e utilizados lado a lado. Visto que Azure PowerShell utiliza módulos binários, tem de abrir numa nova janela do PowerShell e utilizar o **Módulo de importação** para importar uma versão específica dos cmdlets AzureRM:

    Import-Module AzureRM -RequiredVersion 2.1.0**

Versões antes de 2.1.0 (que não 1.2.6) não funcionam bem lado a lado com outras versões do módulo Azure PowerShell. Quando carrega uma versão anterior do Azure PowerShell módulos utilizando um comando, como o apresentado acima, versões incompatíveis do módulo **AzureRM.Profile** serão carregados, que resulta nos cmdlets pedir-lhe para iniciar sessão no sempre que executar um cmdlet, mesmo depois de iniciou sessão.

A forma mais fácil para resolver que este é para instalar o PowerShell mais recente do Azure a partir de WebPI feed ou. msi – esta ação remove as versões anteriores dos módulos instalados a partir da galeria. 

Tenha em atenção que módulos Azure e AzureRM têm dependências em comum, para que se utiliza o ambos os módulos, ao atualizar um, devem ser ambos. Versões anteriores do módulo Azure têm o mesmo problema com o lado a lado módulo que as versões anteriores do módulo AzureRM a ser carregados ter.

<a id="Install"></a>
## <a name="step-1-install"></a>Passo 1: instalar

Seguem-se os dois métodos pelo qual pode instalar o Azure PowerShell. Pode instalá-lo a partir da Galeria de PowerShell ou a WebPI.

###<a name="installing-azure-powershell-from-the-powershell-gallery"></a>Instalar o Azure PowerShell a partir da Galeria de PowerShell

A forma preferida é utilizar a Galeria de PowerShell. Precisa do módulo PowerShellGet para utilizar a Galeria de PowerShell. Esta funcionalidade está disponível aqui: [PowerShellGallery.com](https://www.powershellgallery.com/)

Instalar o Azure PowerShell 1.3.0 ou superior a partir da Galeria de PowerShell utilizando uma Windows PowerShell ou integrada Scripting ambiente (ISe do PowerShell) elevada utilizando os seguintes comandos:

    # Install the Azure Resource Manager modules from the PowerShell Gallery
    Install-Module AzureRM

    # Install the Azure Service Management module from the PowerShell Gallery
    Install-Module Azure

####<a name="more-about-these-commands"></a>Mais informações sobre estes comandos

- **Módulo de instalação AzureRM** instala um módulo de rollup para os cmdlets do Gestor de recursos do Azure. Depende do módulo AzureRM 
- um intervalo de uma versão específica para cada módulo Azure o Gestor de recursos. O intervalo de versão incluída assegura que podem ser incluídas sem alterações de módulo recentes ao instalar o AzureRM módulos com a mesma versão principal. Quando instalar o módulo AzureRM, qualquer módulo Azure o Gestor de recursos que anteriormente não foi instalado vai ser transferido e instalado a partir da Galeria de PowerShell. Para mais informações sobre o controlo de versões semântico utilizado pelo módulos Azure PowerShell, consulte o artigo [semver.org](http://semver.org). 
- **Instalar módulo Azure** instala o módulo Azure. Este módulo é o módulo de gestão do serviço a partir do Azure PowerShell 0.9.x. Isto deve ter não principais alterações e ser intermutáveis para a versão anterior do módulo Azure.

###<a name="installing-azure-powershell-from-webpi"></a>Instalar o Azure PowerShell a partir de WebPI

Instalar o Azure PowerShell 1.0 e superior a partir de WebPI é a mesma tal como estava para 0.9.x. Transferir o [PowerShell do Azure](http://aka.ms/webpi-azps) e iniciar a instalação. Se tiver Azure PowerShell 0.9.x instalado, versão 0.9.x será desinstalado como parte da atualização. Se instalou o Azure PowerShell módulos da galeria do PowerShell, o instalador do remove automaticamente os módulos antes da instalação para se certificar de um ambiente do Azure PowerShell consistente.

> [AZURE.NOTE] Se tiver instalado anteriormente módulos Azure a partir da galeria do PowerShell, o instalador do automaticamente removê-los. Isto impede que confusões sobre as versões de módulo tenha instalado e onde estão localizados. Módulos PowerShell Galeria normalmente instala no **%ProgramFiles%\WindowsPowerShell\Modules**. Em contrapartida, o instalador de WebPI irá instalar os módulos Azure * *% ProgramFiles (x86) %\Microsoft SDKs\Azure\PowerShell\**. Se ocorre um erro durante a instalação, pode remover manualmente o Azure* pastas na sua pasta de **%ProgramFiles%\WindowsPowerShell\Modules** e tente novamente a instalação.

Assim que a instalação for concluída, o ```$env:PSModulePath``` definição deve incluir os directórios que contém os cmdlets do Azure PowerShell.

> [AZURE.NOTE] Existe um problema conhecido com o PowerShell **$env: PSModulePath** que podem ocorrer ao instalar a partir de WebPI. Se o seu computador requer um reinício devido a actualizações do sistema ou outras instalações, poderá causar atualizações **$env: PSModulePath** não inclui o caminho onde está instalado o Azure PowerShell. Se tal acontecer, poderá ver uma mensagem de 'cmdlet não é reconhecido' ao tentar utilizar cmdlets do PowerShell do Azure após a instalação ou atualização. Se tal acontecer, reiniciar o computador deve corrigir o problema.

Se receber uma mensagem semelhante ao seguinte ao tentar carregar ou executar os cmdlets do:

```
    PS C:\> Get-AzureRmResource
    Get-AzureRmResource : The term 'Get-AzureRmResource' is not recognized as the name of a cmdlet, function,
    script file, or operable program. Check the spelling of the name, or if a path was included, verify that the path is
    correct and try again.
    At line:1 char:1
    + Get-AzureRmResource
    + ~~~~~~~~~~~~~~~~~~~~~~~
        + CategoryInfo          : ObjectNotFound: (get-azurermresourcefork:String) [], CommandNotFoundException
        + FullyQualifiedErrorId : CommandNotFoundException
```

Isto pode ser corrigido por reiniciar o computador ou importar os cmdlets do c:\Programas\Microsoft Files\WindowsPowerShell\Modules\Azure\XXXX\ como seguinte (onde XXXX é a versão do PowerShell instalado:

```
Import-Module "C:\Program Files\WindowsPowerShell\Modules\Azure\XXXX\azure.psd1"
Import-Module "C:\Program Files\WindowsPowerShell\Modules\Azure\XXXX\expressroute\expressroute.psd1"
```

## <a name="step-2-start"></a>Passo 2: Iniciar
Pode executar os cmdlets a partir da consola do Windows PowerShell padrão ou a partir do integrada Scripting ambiente (ISe do PowerShell).
O método que utiliza para abrir um dos consola depende da versão do Windows que está a executar:

- Num computador com, pelo menos, Windows 8 ou Windows Server 2012, pode utilizar a pesquisa incorporada. No ecrã **Iniciar** , comece a escrever power. Isto devolve uma lista âmbito de aplicações que inclui o Windows PowerShell. Para abrir a consola, clique em qualquer aplicação. (Para afixar a aplicação ao ecrã **Iniciar** , botão direito do rato no ícone.)

- Num computador a executar uma versão anterior ao Windows 8 ou Windows Server 2012, utilize o **menu Iniciar**. No menu **Iniciar** , clique em **Todos os programas**, clique em **Acessórios**, clique na pasta do **Windows PowerShell** e, em seguida, clique em **Windows PowerShell**.

Também pode executar o **Windows PowerShell ISE** utilizar itens de menu e atalhos de teclado para efetuar muitas das mesmas tarefas que pretende executar na consola do Windows PowerShell. Para utilizar ISE, na consola do Windows PowerShell, Cmd.exe, ou, na caixa **Executar** , escreva, **powershell_ise.exe**.

###<a name="commands-to-help-you-get-started"></a>Comandos para o ajudar a começar a trabalhar

    # To make sure the Azure PowerShell module is available after you install
    Get-Module –ListAvailable 
    
    # To log in to Azure Resource Manager
    Login-AzureRmAccount

    # You can also use a specific Tenant if you would like a faster log in experience
    # Login-AzureRmAccount -TenantId xxxx

    # To view all subscriptions for your account
    Get-AzureRmSubscription

    # To select a default subscription for your current session
    Get-AzureRmSubscription –SubscriptionName “your sub” | Select-AzureRmSubscription

    # View your current Azure PowerShell session context
    # This session state is only applicable to the current session and will not affect other sessions
    Get-AzureRmContext

    # To select the default storage context for your current session
    Set-AzureRmCurrentStorageAccount –ResourceGroupName “your resource group” –StorageAccountName “your storage account name”

    # View your current Azure PowerShell session context
    # Note: the CurrentStorageAccount is now set in your session context
    Get-AzureRmContext

    # To list all of the blobs in all of your containers in all of your accounts
    Get-AzureRmStorageAccount | Get-AzureStorageContainer | Get-AzureStorageBlob


## <a name="step-3-connect"></a>Passo 3: ligar
Os cmdlets precisa a sua subscrição de modo a que podem gerir os seus serviços. Pode comprar uma subscrição do Azure se ainda não tiver uma. Para obter instruções, consulte o artigo [como comprar Azure](http://go.microsoft.com/fwlink/p/?LinkId=320795).

1. Tipo **AzureRmAccount de início de sessão**

2. Escreva o endereço de e-mail e palavra-passe associada à sua conta. Azure autentica e guarda as informações de credenciais e, em seguida, fecha a janela.

– OU –

Inicie sessão no seu trabalho ou escola conta:

    $cred = Get-Credential
    Login-AzureRmAccount -Credential $cred
> [AZURE.NOTE] Se tiver mais do que um inquilino associado à sua conta organizacional, especifique o parâmetro TenantId:

    $loadersubscription = Get-AzureRmSubscription -SubscriptionName $YourSubscriptionName -TenantId $YourAssociatedSubscriptionTenantId


> [AZURE.NOTE] Este registo não interativa no método só funciona com uma conta escolar ou profissional. Uma conta escolar ou profissional é um utilizador que é gerido pelo seu trabalho ou escola e definido na instância do Azure Active Directory para o seu trabalho ou escola. Se não possui atualmente uma conta escolar ou profissional e estiver a utilizar uma conta Microsoft para iniciar sessão na sua subscrição do Azure, pode facilmente criar um utilizando os passos seguintes.

> 1. Inicie a sessão [portal clássica Azure](https://manage.windowsazure.com)e clique no **Active Directory**.

> 2. Se nenhum directório não existir, selecione **criar o seu diretório** e forneça as informações pedidas.

> 3. Selecione o seu diretório e adicionar um novo utilizador. Este novo utilizador pode inicie sessão com uma conta escolar ou profissional. Durante a criação do utilizador, serão fornecidas com ambos os um endereço de e-mail para o utilizador e uma palavra-passe temporária. Guarde esta informação, o que é utilizado no passo 5 abaixo.

> 4. A partir do portal do Azure clássico, selecione **Definições** e, em seguida, selecione **os administradores**. Selecione **Adicionar**e adicionar o novo utilizador como administrador de cocriação. Esta opção permite-a conta escolar ou profissional gerir a sua subscrição do Azure.

> 5. Por fim, termine a sessão no portal do Azure clássico e, em seguida, inicie novamente sessão com o trabalho conta ou escola. Se esta for a primeira vez iniciar sessão com esta conta, ser-lhe-á pedido para alterar a palavra-passe.

> Para mais informações sobre a inscrição no Microsoft Azure com uma conta escolar ou profissional, consulte o artigo [Inscrever-se no Microsoft Azure como uma organização](./active-directory/sign-up-organization.md).

> Para mais informações sobre a gestão de autenticação e de subscrição no Azure, consulte o artigo [Gerir contas, subscrições e funções administrativas](http://go.microsoft.com/fwlink/?LinkId=324796).

### <a name="view-account-and-subscription-details"></a>Ver detalhes de conta e subscrição

Pode ter várias contas e subscrições disponíveis para utilização ao PowerShell do Azure. Pode adicionar várias contas ao executar **AzureRmAccount adicionar** mais do que uma vez.

Para apresentar as contas do Azure disponíveis, escreva **Get-AzureAccount**.

Para apresentar as suas subscrições Azure, escreva **Get-AzureRmSubscription**.

##<a id="Help"></a>Obter ajuda##

Estes recursos fornecem ajuda cmdlets específicos:


-   Dentro da consola, pode utilizar o sistema de ajuda incorporado. O cmdlet **Get-Help** oferece um acesso para este sistema. 

- Para obter ajuda na Comunidade do, experimente estes fóruns populares:

 - [Fórum do Azure no MSDN]( http://go.microsoft.com/fwlink/p/?LinkId=320212)
 - [Stackoverflow](http://go.microsoft.com/fwlink/?LinkId=320213)

##<a name="learn-more"></a>Saiba mais


Consulte os seguintes recursos para saber mais sobre como utilizar os cmdlets:

Para obter instruções básicas sobre como utilizar o Windows PowerShell, consulte o artigo [Utilizar o Windows PowerShell](http://go.microsoft.com/fwlink/p/?LinkId=321939).

Para informações de referência sobre os cmdlets, consulte o artigo [Referência do Cmdlet Azure](https://msdn.microsoft.com/library/windowsazure/jj554330.aspx).

Para scripts de exemplo e instruções para o ajudar a aprender a utilizar o scripting para gerir Azure, consulte o artigo do [Centro de Script](http://go.microsoft.com/fwlink/p/?LinkId=321940).

