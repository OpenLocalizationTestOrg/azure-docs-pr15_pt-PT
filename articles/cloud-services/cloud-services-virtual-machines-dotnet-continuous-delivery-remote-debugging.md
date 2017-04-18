<properties
    pageTitle="Activar depuração remota com entrega contínua | Microsoft Azure"
    description="Saiba como ativar depuração remota ao utilizar entrega contínua para implementar Azure"
    services="cloud-services"
    documentationCenter=".net"
    authors="TomArcher"
    manager="douge"
    editor=""/>

<tags
    ms.service="cloud-services"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-multiple"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="08/15/2016"
    ms.author="tarcher"/>

# <a name="enable-remote-debugging-when-using-continuous-delivery-to-publish-to-azure"></a>Ativar a depuração remoto ao utilizar entrega contínua para publicar o Azure

Pode ativar a depuração remota no Azure, para serviços em nuvem ou máquinas virtuais, ao utilizar [entrega contínua](cloud-services-dotnet-continuous-delivery.md) para publicar no Azure seguindo estes passos.

## <a name="enabling-remote-debugging-for-cloud-services"></a>Activar a depuração remoto para serviços em nuvem

1. No agente construir, configure o ambiente inicial para Azure conforme descrito no [Construir da linha de comandos para Azure](http://msdn.microsoft.com/library/hh535755.aspx).
2. Porque é necessário para o pacote de depuração remota runtime (msvsmon.exe), instale as [Ferramentas de remota para Visual Studio 2015](http://www.microsoft.com/en-us/download/details.aspx?id=48155) (ou as [Ferramentas remota para o Microsoft Visual Studio 2013 atualização 5](https://www.microsoft.com/en-us/download/details.aspx?id=48156) se estiver a utilizar o Visual Studio 2013). Como alternativa, pode copiar os binários depuração remota a partir de um sistema que tenha o Visual Studio instalada.
3. Crie um certificado conforme descrito na [Descrição geral de certificados para serviços em nuvem Azure](cloud-services-certs-create.md). Manter a. pfx e impressão digital do certificado RDP e carregar o certificado para o serviço de nuvem de destino.
4. Utilize as seguintes opções na linha de comandos MSBuild para criar e compactar com depuração remota ativada. (SUBST caminhos reais aos seus ficheiros do sistema e o project para os itens entre parênteses ângulo).

        msbuild /TARGET:PUBLISH /PROPERTY:Configuration=Debug;EnableRemoteDebugger=true;VSX64RemoteDebuggerPath="<remote tools path>";RemoteDebuggerConnectorCertificateThumbprint="<thumbprint of the certificate added to the cloud service>";RemoteDebuggerConnectorVersion="2.7" "<path to your VS solution file>"

    `VSX64RemoteDebuggerPath`é o caminho para a pasta que contém msvsmon.exe nas ferramentas de remoto, para o Visual Studio.
    `RemoteDebuggerConnectorVersion`é a versão do Azure SDK no seu serviço de nuvem. Também devem corresponder à versão instalada com o Visual Studio.

5. Publica o serviço de nuvem destino utilizando o ficheiro de pacote e .cscfg gerado no passo anterior.
6. Importe o certificado (ficheiro. pfx) ao computador que tem o Visual Studio com Azure SDK para .NET instalado. Certifique-se importar para o `CurrentUser\My` arquivo de certificados, caso contrário, como anexar a depurador no Visual Studio irá falhar.

## <a name="enabling-remote-debugging-for-virtual-machines"></a>Activar a depuração remoto para máquinas virtuais

1. Crie uma máquina de virtual Azure. Consulte o artigo [criar uma máquina de Virtual com o Windows Server](../virtual-machines/virtual-machines-windows-hero-tutorial.md) ou [criar e gerir Azure máquinas virtuais no Visual Studio](../virtual-machines/virtual-machines-windows-classic-manage-visual-studio.md).
2. Na [página do portal clássica Azure](http://go.microsoft.com/fwlink/p/?LinkID=269851), ver dashboard da máquina virtual para ver a máquina virtual **Impressão digital do certificado RDP**. Este valor é utilizado para o `ServerThumbprint` valor na configuração da extensão.
3. Criar um certificado de cliente, conforme descrito na [Descrição geral de certificados para serviços em nuvem Azure](cloud-services-certs-create.md) (manter a. pfx e impressão digital do certificado RDP).
4. Instalar o Azure Powershell (versão 0.7.4 ou posterior) conforme descrito no [artigo como instalar e configurar o Azure PowerShell](../powershell-install-configure.md).
5. Execute o seguinte script para ativar a extensão de RemoteDebug. Substitua os caminhos e dados pessoais com o seu próprio, como o seu nome da subscrição, nome do serviço e impressão digital.

    >[AZURE.NOTE] Este script está configurado para Visual Studio 2015. Se estiver a utilizar o Visual Studio 2013, modifique o `$referenceName` e `$extensionName` atribuições abaixo para utilizar `RemoteDebugVS2013` (em vez de `RemoteDebugVS2015`).

    <pre>
   AzureAccount adicionar

    Selecione-AzureSubscription "Minha subscrição do Microsoft"

    $vm = get AzureVM - ServiceName "mytestvm1"-mytestvm1"nome"

    $endpoints = @( ,@{Name="RDConnVS2013"; PublicPort = 30400; PrivatePort = 30398} ,@{Name="RDFwdrVS2013"; PublicPort = 31400; PrivatePort = 31398})  

    foreach ($endpoint no $endpoints) {adicionar AzureEndpoint - VM $vm-nome $endpoint. Name - protocolo tcp - PublicPort $endpoint. PublicPort - PortaLocal $endpoint. PrivatePort}

    $referenceName = "Microsoft.VisualStudio.WindowsAzure.RemoteDebug.RemoteDebugVS2015" $publisher = "Microsoft.VisualStudio.WindowsAzure.RemoteDebug" $extensionName = "RemoteDebugVS2015" $version = "1.*" $publicConfiguration = "<PublicConfig>< Connector.Enabled > Verdadeiro < /Connector.Enabled ><ClientThumbprint>56D7D1B25B472268E332F7FC0C87286458BFB6B2</ClientThumbprint><ServerThumbprint>E7DCB00CB916C468CC3228261D6E4EE45C8ED3C6</ServerThumbprint><ConnectorPort>30398</ConnectorPort><ForwarderPort>31398</ForwarderPort></PublicConfig>"

    $vm | Conjunto AzureVMExtension `
    -ReferenceName $referenceName ` 
    -Publisher $publisher `
    -ExtensionName $extensionName ` 
    -versão $version ' - PublicConfiguration $publicConfiguration

    foreach ($extension no $vm. VM. ResourceExtensionReferences) {se (($extension. Nome_da_referência - eq $referenceName) `
    -and ($extension.Publisher -eq $publisher) ` 
    - e ($extension. Atribuir um nome - eq $extensionName) '- e ($extension. Versão - eq $version)) {$extension. ResourceExtensionParameterValues [0]. Chave = 'txt' quebra}}

    $vm | AzureVM de actualização </pre>

6. Importe o certificado (. pfx) ao computador que tem o Visual Studio com Azure SDK para .NET instalado.
