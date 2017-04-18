<properties
    pageTitle="Introdução ao Azure chave Cofre | Microsoft Azure"
    description="Utilize este tutorial para ajudá-lo a introdução ao Azure chave cofre para criar um contentor puro no Azure, armazenar e gerir chaves de criptografia e segredos no Azure."
    services="key-vault"
    documentationCenter=""
    authors="cabailey"
    manager="mbaldwin"
    tags="azure-resource-manager"/>

<tags
    ms.service="key-vault"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="10/24/2016"
    ms.author="cabailey"/>

# <a name="get-started-with-azure-key-vault"></a>Introdução ao Azure chave Cofre #
Azure chave Cofre está disponível na maioria das regiões. Para mais informações, consulte o artigo [chave cofre preços página](https://azure.microsoft.com/pricing/details/key-vault/).

## <a name="introduction"></a>Introdução  
Utilize este tutorial para ajudá-lo a introdução ao Azure chave cofre para criar um contentor puro (cofre) no Azure, armazenar e gerir chaves de criptografia e segredos no Azure. Orienta-o através do processo de utilizar o PowerShell do Azure para criar um cofre que contém uma chave ou palavra-passe que, em seguida, pode utilizar com uma aplicação do Azure. -Em seguida, mostra-lhe como uma aplicação pode utilizar essa tecla ou palavra-passe.

**Estimativa tempo a concluir:** 20 minutos

>[AZURE.NOTE]  Neste tutorial não inclui instruções para como escrever a aplicação Azure que um destes passos inclui, nomeadamente como autorizar uma aplicação para utilizar uma chave ou palavra-passe no cofre chave.
>
>Neste tutorial, utiliza Azure PowerShell. Para obter instruções de Interface de comandos em diferentes plataformas, consulte o artigo [Este tutorial equivalente](key-vault-manage-with-cli.md).

Para obter informações gerais sobre Azure chave cofre, consulte o artigo [o que é o Azure chave cofre?](key-vault-whatis.md)

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, tem de ter o seguinte procedimento:

- Uma subscrição do Microsoft Azure. Se não tiver uma, pode inscrever-se para uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/).
- Azure PowerShell, **versão mínima do 1.1.0**. Para instalar o PowerShell do Azure e associá-la com a sua subscrição Azure, consulte o artigo [como instalar e configurar o Azure PowerShell](../powershell-install-configure.md). Se já tiver instalado o PowerShell do Azure e não souber a versão, a partir da consola do Azure PowerShell, escreva `(Get-Module azure -ListAvailable).Version`. Quando tiver Azure PowerShell versão 0.9.1 através de 0.9.8 instalado, ainda pode utilizar este tutorial com algumas alterações secundárias. Por exemplo, tem de utilizar o `Switch-AzureMode AzureResourceManager` comando e alguns dos comandos Azure chave cofre foram alterados. Para uma lista dos cmdlets do cofre chave para versões 0.9.1 através de 0.9.8, consulte [Cmdlets do Azure chave cofre](https://msdn.microsoft.com/library/azure/dn868052\(v=azure.98\).aspx). 
- Uma aplicação que irá estar configurada para utilizar a chave ou palavra-passe que criou neste tutorial. Uma aplicação de exemplo está disponível a partir do [Centro de transferências da Microsoft](http://www.microsoft.com/en-us/download/details.aspx?id=45343). Para obter instruções, consulte o artigo o ficheiro Leia-me acompanhamento.


Neste tutorial foi concebido para principiantes do Azure PowerShell, mas parte do princípio de que compreender os conceitos básicos, tais como módulos, cmdlets e sessões. Para mais informações, consulte o artigo [introdução com o Windows PowerShell](https://technet.microsoft.com/library/hh857337.aspx).

Para obter ajuda detalhada sobre qualquer cmdlet que vê neste tutorial, utilize o cmdlet **Get-Help** .

    Get-Help <cmdlet-name> -Detailed

Por exemplo, para obter ajuda para o cmdlet **AzureRmAccount de início de sessão** , escreva:

    Get-Help Login-AzureRmAccount -Detailed

Também pode ler os seguintes tutoriais para se familiarizar com o Gestor de recursos do Azure no Azure PowerShell:

- [Como instalar e configurar PowerShell do Azure](../powershell-install-configure.md)
- [Utilizar o Azure PowerShell com o Gestor de recursos](../powershell-azure-resource-manager.md)


## <a id="connect"></a>Ligar a suas subscrições ##

Iniciar uma sessão de PowerShell do Azure e inicie sessão na sua conta Azure com o seguinte comando:  

    Login-AzureRmAccount 

Tenha em atenção que, se estiver a utilizar uma instância específica de utilização do Azure, por exemplo, Azure Governo, o ambiente parâmetro - com este comando. Por exemplo:`Login-AzureRmAccount –Environment (Get-AzureRmEnvironment –Name AzureUSGovernment)`

Na janela do browser de pop-up, introduza o seu nome de utilizador da conta Azure e a palavra-passe. Azure PowerShell obtém todas as subscrições que estão associadas com esta conta e por predefinição, utiliza a primeira parte.

Se tem múltiplas subscrições e pretende especificar um específico a utilizar para Azure chave cofre, escreva o seguinte para ver as subscrições para a sua conta:

    Get-AzureRmSubscription

Em seguida, para especificar a subscrição para utilizar, escreva:

    Set-AzureRmContext -SubscriptionId <subscription ID>

Para mais informações sobre como configurar o Azure PowerShell, consulte o artigo [como instalar e configurar o Azure PowerShell](../powershell-install-configure.md).


## <a id="resource"></a>Criar um novo grupo de recursos ##

Quando utilizar o Gestor de recursos do Azure, todos os recursos relacionados são criados no interior de um grupo de recursos. Vamos irá criar um novo grupo de recursos denominado **ContosoResourceGroup** para este tutorial:

    New-AzureRmResourceGroup –Name 'ContosoResourceGroup' –Location 'East Asia'


## <a id="vault"></a>Criar uma chave Cofre ##

Utilize o cmdlet [AzureRmKeyVault novo](https://msdn.microsoft.com/library/azure/mt603736\(v=azure.300\).aspx) para criar uma chave cofre. Este cmdlet tem três parâmetros obrigatórios: um **nome de grupo de recursos**, um **nome de chave cofre**e a **localização geográfica**.

Por exemplo, se utilizar o nome do cofre do **ContosoKeyVault**, o nome do grupo de recursos de **ContosoResourceGroup**e a localização da **Ásia**, escreva:

    New-AzureRmKeyVault -VaultName 'ContosoKeyVault' -ResourceGroupName 'ContosoResourceGroup' -Location 'East Asia'

O resultado deste cmdlet mostra as propriedades do cofre chave que acabou de criar. As duas propriedades mais importantes são:

- **Nome do cofre**: no exemplo, esta é **ContosoKeyVault**. Irá utilizar este nome para outras cmdlets chave cofre.
- **URI cofre**: no exemplo, esta é https://contosokeyvault.vault.azure.net/. As aplicações que utilizam cofre através do seu REST API tem de utilizar este URI.

A conta Azure agora está autorizada a efetuar qualquer operações deste cofre chave. Ainda mais ninguém é.

>[AZURE.NOTE]  Se vir o erro **a subscrição não estiver registada para utilizar o espaço de nomes 'Microsoft.KeyVault'** quando tenta criar nova chave Cofre de palavras, executar `Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.KeyVault"` e, em seguida, volte a executar o comando novo AzureRmKeyVault. Para mais informações, consulte o artigo [Register AzureRmResourceProvider](https://msdn.microsoft.com/library/azure/mt759831\(v=azure.300\).aspx).
>

## <a id="add"></a>Adicionar uma chave ou palavra-passe ao Cofre de palavras chave ##

Se pretender Azure chave cofre para criar uma chave de software protegido por si, utilize o cmdlet [AzureKeyVaultKey adicionar](https://msdn.microsoft.com/library/azure/dn868048\(v=azure.300\).aspx) e escreva o seguinte:

    $key = Add-AzureKeyVaultKey -VaultName 'ContosoKeyVault' -Name 'ContosoFirstKey' -Destination 'Software'

No entanto, se tiver uma chave existente protegido por software num. Ficheiro PFX guardado na unidade C:\ num ficheiro denominado softkey.pfx que pretende carregar para o Azure Cofre de palavras chave, escreva o seguinte para definir a variável **securepfxpwd** uma palavra-passe de **123** para o. Ficheiro PFX:

    $securepfxpwd = ConvertTo-SecureString –String '123' –AsPlainText –Force

Em seguida, escreva o seguinte procedimento para importar a chave a partir de. Ficheiro PFX, protege a chave por software no serviço do Cofre de chave:

    $key = Add-AzureKeyVaultKey -VaultName 'ContosoKeyVault' -Name 'ContosoFirstKey' -KeyFilePath 'c:\softkey.pfx' -KeyFilePassword $securepfxpwd


Agora pode fazer referência a esta chave que criado ou carregado para Azure chave cofre, utilizando o respetivo URI. Utilizar **https://ContosoKeyVault.vault.azure.net/keys/ContosoFirstKey** para obter sempre a versão atual e utilize **https://ContosoKeyVault.vault.azure.net/keys/ContosoFirstKey/cgacf4f763ar42ffb0a1gca546aygd87** para obter esta versão específica.  

Para apresentar o URI para esta chave, escreva:

    $Key.key.kid

Para adicionar uma palavra-passe para o cofre, que é uma palavra-passe com o nome SQLPassword e tem o valor de Pa$ $w0rd Azure chave cofre, pela primeira vez converter o valor de Pa$ $w0rd para uma cadeia segura, escrevendo o seguinte:

    $secretvalue = ConvertTo-SecureString 'Pa$$w0rd' -AsPlainText -Force

Em seguida, escreva o seguinte:

    $secret = Set-AzureKeyVaultSecret -VaultName 'ContosoKeyVault' -Name 'SQLPassword' -SecretValue $secretvalue

Agora pode fazer referência a esta palavra-passe que adicionou a Azure chave cofre, utilizando o respetivo URI. Utilizar **https://ContosoVault.vault.azure.net/secrets/SQLPassword** para obter sempre a versão atual e utilize **https://ContosoVault.vault.azure.net/secrets/SQLPassword/90018dbb96a84117a0d2847ef8e7189d** para obter esta versão específica.

Para apresentar o URI para esta palavra-passe, escreva:

    $secret.Id

Vamos ver a chave ou palavra-passe que acabou de criar:

- Para ver a sua chave, escreva:`Get-AzureKeyVaultKey –VaultName 'ContosoKeyVault'`
- Para ver a sua palavra-passe, escreva:`Get-AzureKeyVaultSecret –VaultName 'ContosoKeyVault'`

Agora, está pronta para aplicações para utilizar a chave cofre e chave nem secreta. Tem de autorizar aplicações utilizá-las.  

## <a id="register"></a>Registe-se uma aplicação com o Azure Active Directory ##

Este passo faria normalmente ser feito por um programador, num computador diferente. Não é específico a Azure chave cofre, mas é aqui incluído para integridade.


>[AZURE.IMPORTANT] Para concluir o tutorial, sua conta, o Cofre e a aplicação que irá registar neste passo têm de ser todos no mesmo directório Azure.

As aplicações que utilizam um cofre chave tem de autenticar utilizando um token a partir do Azure Active Directory. Para fazer isto, o proprietário da aplicação primeiro tem de registar a aplicação no seu Azure Active Directory. No final do registo, o proprietário da aplicação obtém os seguintes valores:


- Um **ID da aplicação** (também conhecido como um ID de cliente) e a **chave de autenticação** (também conhecido como o segredo partilhado). A aplicação tem de apresentar ambos os seguintes valores para o Azure Active Directory, para obter um token. Como a aplicação está configurada para fazer isto depende da aplicação. Para a aplicação de exemplo do cofre chave, o proprietário da aplicação define estes valores no ficheiro App.

Para registar a aplicação no Azure Active Directory:

1. Inicie sessão no portal do Azure clássico.
2. À esquerda, clique em **Active Directory**e, em seguida, selecione a pasta na qual irá registar a aplicação. <br> <br> **Nota:** Tem de selecionar o diretório mesmo que contém a subscrição Azure com as quais criou cofre chave. Se não souber qual o directório isto é, clique em **Definições**, identificar a subscrição com a qual criou cofre chave e anote o nome do directório mostrado na última coluna.

3. Clique em **aplicações**. Se a aplicações que não foram adicionadas ao seu diretório, esta página mostra apenas a ligação **Adicionar uma aplicação** . Clique na ligação ou em alternativa, pode clicar em **Adicionar** à barra de comando.
4.  No assistente **Adicionar aplicação** , na **o que pretende fazer?** página, clique em **Adicionar uma aplicação que está a desenvolver a minha organização**.
5.  Na página **diga-nos sobre a aplicação** , especifique um nome para a sua aplicação e, em seguida, selecione **E/ou de aplicações WEB WEB API** (a predefinição). Clique no ícone **seguinte** .
6.  Na página **Propriedades da aplicação** , especifique o **Início de sessão no URL** e a **Aplicação ID URI** para a aplicação web. Se a aplicação não tiver estes valores, pode torná-las para este passo (por exemplo, pode especificar http://test1.contoso.com para ambas as caixas). Não importa se existirem estes sites. O que é importante é que a aplicação ID URI para cada aplicação é diferente para todas as aplicações no seu diretório. Diretório utiliza esta cadeia para identificar a sua aplicação.
7.  Clique no ícone de **concluído** para guardar as alterações no assistente.
8.  Na página de **Início rápido** , clique em **Configurar**.
9.  Desloque-se para a secção **chaves** , selecione a duração e, em seguida, clique em **Guardar**. A página atualiza e agora mostra um valor de chave. Tem de configurar a aplicação com este valor de chave e o valor de **ID de cliente** . (As instruções para esta configuração são específicos da aplicação).
10. Copie o valor de ID de cliente nesta página, que irá utilizar no próximo passo para definir permissões no seu cofre.

## <a id="authorize"></a>Autorize a aplicação para utilizar a chave ou palavra-passe ##

Para autorizar a aplicação para aceder à chave ou palavra-passe no cofre, utilize o cmdlet  [Set-AzureRmKeyVaultAccessPolicy](https://msdn.microsoft.com/library/azure/mt603625\(v=azure.300\).aspx) .

Por exemplo, se o nome do cofre for **ContosoKeyVault** e a aplicação que pretende autorizar tem um ID de cliente de 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed e pretende autorizar a aplicação para desencriptar e inicie sessão com as teclas no cofre, execute o seguinte:


    Set-AzureRmKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -ServicePrincipalName 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed -PermissionsToKeys decrypt,sign

Se pretender autorizar nesse mesmo pedido ler segredos no cofre, execute o seguinte:


    Set-AzureRmKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -ServicePrincipalName 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed -PermissionsToSecrets Get

## <a id="HSM"></a>Se pretender utilizar um módulo de segurança do hardware (HSM) ##

Para assurance adicionada, pode importar ou gerar chaves nos módulos de segurança de hardware (Os HSMs) que nunca deixe o limite da HSM. Os HSMs são 140-2 nível 2 validados FIPS. Se este requisito não se aplica a si, ignorar esta secção e aceda ao [Eliminar o Cofre chave e chaves e segredos associados](#delete).

Para criar estas teclas protegida HSM, tem de utilizar a [camada de serviços de Azure chave cofre Premium para suportar protegida HSM teclas](https://azure.microsoft.com/pricing/free-trial/). Além disso, tenha em atenção que esta funcionalidade não está disponível para a Azure China.


Quando cria o Cofre chave, adicione o parâmetro **- SKU** :


    New-AzureRmKeyVault -VaultName 'ContosoKeyVaultHSM' -ResourceGroupName 'ContosoResourceGroup' -Location 'East Asia' -SKU 'Premium'



Pode adicionar protegido por software chaves (como mostrado anteriormente) e em protegida HSM para esta chave cofre. Para criar uma tecla protegida HSM, defina o **-destino** parâmetro para 'HSM':

    $key = Add-AzureKeyVaultKey -VaultName 'ContosoKeyVaultHSM' -Name 'ContosoFirstHSMKey' -Destination 'HSM'

Pode utilizar o seguinte comando para importar uma chave de um. Ficheiro PFX no seu computador. Este comando importa a chave para os HSMs no serviço do Cofre de chave:

    $key = Add-AzureKeyVaultKey -VaultName 'ContosoKeyVaultHSM' -Name 'ContosoFirstHSMKey' -KeyFilePath 'c:\softkey.pfx' -KeyFilePassword $securepfxpwd -Destination 'HSM'


O comando seguinte importa "sua própria chave trazer" pacote (BYOK). Este cenário permite-lhe gerar a chave no seu HSM local e transferi-lo para os HSMs no serviço de chave cofre, sem a chave de sair o limite da HSM:

    $key = Add-AzureKeyVaultKey -VaultName 'ContosoKeyVaultHSM' -Name 'ContosoFirstHSMKey' -KeyFilePath 'c:\ITByok.byok' -Destination 'HSM'

Para obter instruções mais detalhadas sobre como gerar este pacote BYOK, consulte o artigo [como gerar e transferência protegida HSM teclas para Azure chave cofre](key-vault-hsm-protected-keys.md).

## <a id="delete"></a>Elimine a chave cofre e chaves associadas e segredos ##

Se já não precisar do cofre chave e a chave ou secreta nela contidos, pode eliminar o Cofre chave utilizando o cmdlet [AzureRmKeyVault remover](https://msdn.microsoft.com/library/azure/mt619485\(v=azure.300\).aspx) :

    Remove-AzureRmKeyVault -VaultName 'ContosoKeyVault'

Ou, pode eliminar um grupo de recursos de Azure inteira, que inclui o Cofre chave e outros recursos que incluídas nesse grupo:

    Remove-AzureRmResourceGroup -ResourceGroupName 'ContosoResourceGroup'


## <a id="other"></a>Outros Cmdlets do Azure PowerShell ##

Outros comandos que pode achar útil para gerir Azure Cofre de chave:

- `$Keys = Get-AzureKeyVaultKey -VaultName 'ContosoKeyVault'`: Este comando obtém uma visualização tabular de todas as teclas e propriedades selecionadas.
- `$Keys[0]`: Este comando apresenta uma lista completa das propriedades para a chave especificada
- `Get-AzureKeyVaultSecret`: Este comando apresenta uma apresentação em tabela de todos os nomes secretos e propriedades selecionadas.
- `Remove-AzureKeyVaultKey -VaultName 'ContosoKeyVault' -Name 'ContosoFirstKey'`: Exemplo como remover uma chave específica.
- `Remove-AzureKeyVaultSecret -VaultName 'ContosoKeyVault' -Name 'SQLPassword'`: Exemplo como remover uma palavra-passe específica.


## <a id="next"></a>Próximos passos ##

Para obter um tutorial de seguimento que utiliza o Azure chave cofre numa aplicação web, consulte o artigo [Utilizar Azure chave cofre a partir de uma aplicação Web](key-vault-use-from-web-application.md).

Para ver como a sua chave Cofre está a ser utilizado, consulte o artigo [Azure chave cofre registo](key-vault-logging.md).

Para uma lista do mais recente Azure os cmdlets do PowerShell para Azure chave cofre, consulte [Cmdlets do Azure chave cofre](https://msdn.microsoft.com/library/azure/dn868052\(v=azure.300\).aspx). 
 

Para programação referências, consulte o artigo [guia o programador Azure chave cofre](key-vault-developers-guide.md).
