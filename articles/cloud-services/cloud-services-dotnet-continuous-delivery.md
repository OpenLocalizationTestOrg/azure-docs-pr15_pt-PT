<properties
    pageTitle="Serviços de entrega contínua para a nuvem com TFS no Azure | Microsoft Azure"
    description="Saiba como configurar o contínua entrega para as aplicações do Azure na nuvem. Exemplos de código para MSBuild declarações de linha de comandos e scripts de PowerShell."
    services="cloud-services"
    documentationCenter=""
    authors="TomArcher"
    manager="douge"
    editor=""/>

<tags
    ms.service="cloud-services"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="07/30/2016"
    ms.author="tarcher"/>

# <a name="continuous-delivery-for-cloud-services-in-azure"></a>Entrega contínua para serviços em nuvem no Azure

O processo descrito neste artigo mostra-lhe como configurar o contínua entrega para as aplicações do Azure na nuvem. Este processo permite-lhe criar automaticamente pacotes e implementar o pacote para Azure após dar entrada cada código. O processo de criação de pacote descrito neste artigo é equivalente ao comando do **pacote** no Visual Studio e os passos de publicação são equivalentes para o comando de **Publicar** no Visual Studio.
O artigo abrange os métodos que utilizaria para criar um servidor de compilação com MSBuild declarações de linha de comandos e scripts do Windows PowerShell e também demonstra como configurar opcionalmente Visual Studio Team Foundation Server - Criar equipa definições para utilizar os comandos de MSBuild e scripts de PowerShell. O processo é personalizável do seu ambiente de criação e ambientes de destino Azure.

Também pode utilizar o Visual Studio Team Services, uma versão do TFS que está alojado no Azure, para fazer isto mais facilmente. Para mais informações, consulte o artigo [Entrega contínua para Azure utilizando Visual Studio equipa os serviços][].

Antes de começar, deve publicar a aplicação a partir do Visual Studio.
Isto irá garantir que todos os recursos estão disponíveis e inicializado quando tenta automatizar o processo de publicação.

## <a name="1-configure-the-build-server"></a>1: configurar o servidor de compilação

Antes de poder criar um pacote de Azure utilizando MSBuild, tem de instalar o software e necessários ferramentas no servidor de compilação.

Visual Studio não é necessária para ser instalada no servidor de compilação. Se pretender utilizar Team Foundation construir Service para gerir o seu servidor de compilação, siga as instruções na documentação do [Serviço de construir Team Foundation][] .

1.  No servidor de compilação, instale o [.NET Framework 4.5.2][], que inclui MSBuild.
2.  Instale as mais recentes [Ferramentas de criação do Azure para .NET](https://azure.microsoft.com/develop/net/).
3.  Instale as [bibliotecas Azure para .NET](http://go.microsoft.com/fwlink/?LinkId=623519).
4.  Copie o ficheiro de Microsoft.WebApplication.targets a partir de uma instalação do Visual Studio para o servidor de compilação.

    Num computador com o Visual Studio instalado, este ficheiro está localizado no diretório c:\\programa Files(x86)\\MSBuild\\Microsoft\\VisualStudio\\v14.0\\WebApplications. Deve o copiar para o mesmo directório no servidor de compilação.
5.  Instale as [Ferramentas Azure para Visual Studio](https://www.visualstudio.com/features/azure-tools-vs.aspx).

## <a name="2-build-a-package-using-msbuild-commands"></a>2: criar um pacote de utilizando os comandos de MSBuild

Esta secção descreve como construir um comando MSBuild que constrói um pacote de Azure. Execute este passo no servidor de compilação para confirmar que tudo está configurado corretamente e que é que o comando MSBuild o que pretende que seja fazer. Pode adicionar esta linha de comandos para scripts de compilação existentes no servidor de compilação ou pode utilizar a linha de comandos numa definição construir TFS, conforme descrito na secção seguinte. Para mais informações sobre parâmetros da linha de comandos e MSBuild, consulte o artigo [Referência de linha de comandos MSBuild](https://msdn.microsoft.com/library/ms164311%28v=vs.140%29.aspx).

1.  Se o Visual Studio estiver instalado no servidor de compilação, localize e selecione **Visual Studio comando linha de comandos** na pasta **Do Visual Studio Tools** no Windows.

    Se o Visual Studio não estiver instalado no servidor de compilação, abra uma linha de comandos e certifique-se de que MSBuild.exe está acessível no caminho. MSBuild está instalado com o .NET Framework no caminho % WINDIR %\\Microsoft.NET\\Framework\\*versão*. Por exemplo, para adicionar MSBuild.exe a variável de ambiente PATH quando tiver instalado o .NET Framework 4, escreva o seguinte comando na linha de comandos:

        set PATH=%PATH%;"C:\Windows\Microsoft.NET\Framework\v4.0.30319"

2.  Na linha de comandos, navegue para a pasta que contém o ficheiro de projeto Azure ao qual pretende criar.

3.  Executar MSBuild com o /target: publicar opção tal como no exemplo seguinte:

        MSBuild /target:Publish

    Esta opção pode ser abreviada como /t: publicar. A opção /t:Publish no MSBuild não deve ser confundida com os comandos de publicar disponíveis no Visual Studio, quando tiver instalado o SDK do Azure. O /t: publicar opção apenas compilações os Azure pacotes. Não implementar os pacotes tal como os comandos de publicar no Visual Studio.

    Opcionalmente, pode especificar o nome do projeto como um parâmetro MSBuild. Se não for especificado, é utilizado o diretório atual. Para mais informações sobre as opções de linha de comandos MSBuild, consulte o artigo [Referência de linha de comandos MSBuild](https://msdn.microsoft.com/library/ms164311%28v=vs.140%29.aspx).

4.  Localize o resultado. Por predefinição, este comando cria um diretório em relação a pasta de raiz para o projeto, tal como *ProjectDir*\\posição\\*configuração*\\app.publish\\. Quando constrói um projeto Azure, gerar dois ficheiros, o ficheiro do pacote e o ficheiro de configuração acompanhamento:

    -   Project.cspkg
    -   ServiceConfiguration. *TargetProfile*.cscfg

    Por predefinição, cada projeto Azure inclui um ficheiro de configuração de serviço (ficheiro .cscfg) para locais compilações (depuração) e outro para compilações da nuvem (transição ou produção), mas pode adicionar ou remover os ficheiros de configuração do serviço, conforme necessário. Quando constrói um pacote do Visual Studio, será pedido que ficheiro de configuração de serviço para incluir juntamente com o pacote.

5.  Especifique o ficheiro de configuração do serviço. Quando constrói um pacote utilizando MSBuild, o ficheiro de configuração do serviço local é incluído por predefinição. Para incluir um ficheiro de configuração de serviços diferente, defina a propriedade TargetProfile do comando MSBuild, tal como no exemplo seguinte:

        MSBuild /t:Publish /p:TargetProfile=Cloud

6.  Especifique a localização para o resultado. Definir o caminho utilizando o /p:PublishDir =*directório* \\ opção, incluindo o separador barra invertida à direita, tal como no exemplo seguinte:

        MSBuild /target:Publish /p:PublishDir=\\myserver\drops\

    Assim que construídos e testado uma linha de comandos MSBuild adequada para criar os seus projetos e combina-as para um pacote de Azure, pode adicionar esta linha de comandos para os scripts de compilação. Se utiliza o seu servidor de compilação scripts personalizados, este processo irá dependem os detalhes do processo de criação personalizado. Se estiver a utilizar TFS como um ambiente de criação, em seguida, pode seguir as instruções no próximo passo para adicionar a compilação de pacote Azure para o processo de compilação.

## <a name="3-build-a-package-using-tfs-team-build"></a>3: criar um pacote utilizando TFS construir de equipa

Se tiver Team Foundation Server (TFS) configurar como um controlador de compilação e o servidor de compilação configurar como uma máquina de compilação TFS, em seguida, opcionalmente, pode configurar uma compilação automatizada para o seu pacote Azure. Para obter informações sobre como configurar e utilizar o servidor de Team Foundation como um sistema de compilação, consulte o artigo [escala fora do seu sistema de compilação][]. Em particular, o seguinte procedimento pressupõe que tiver configurado o servidor de compilação conforme descrito no [Implementar e configurar um servidor de compilação][], e que criou um projeto de equipa, de criar um projecto de serviço de nuvem do projeto de equipa.

Para configurar TFS para construir pacotes Azure, execute os seguintes passos:

1.  No Visual Studio no seu computador de desenvolvimento, no menu Ver, selecionar **Explorer de equipa**ou Ctrl +\\, Ctrl + M. Na janela do Explorer de equipa, expanda o nó **constrói** ou escolha a página **constrói** e selecione **Nova definição de criar**.

    ![Nova opção de definição de compilação][0]

2.  Selecione o separador **acionador** e especifique as condições pretendidas para quando pretende que o pacote de ser criado. Por exemplo, especificar **Integração contínua** para construir o pacote sempre uma origem controlo dar entrada que ocorre.

3.  Selecione o separador **Definições da origem** e certifique-se a pasta de projeto é listada na coluna da **Pasta de origem do controlo** e o estado está **ativa**.

4.  Selecione o separador **Criar predefinições** e, em controlador de compilação, verifique se o nome do servidor de compilação.  Além disso, selecione a opção **Copiar construir saída na seguinte pasta de lista pendente** e especificar a localização da lista pendente pretendida.

5.  Selecione o separador de **processo** . No separador processo, selecione o modelo predefinido, em **Criar**, selecione o projeto se ainda não esteja selecionado e, expanda a secção **Avançadas** na secção **Criar** da grelha.

6.  Selecione **MSBuild argumentos**e defina os argumentos de linha de comandos MSBuild adequados, tal como descrito no passo 2 acima. Por exemplo, introduza **/t: publicar /p:PublishDir =\\\\omeuservidor\\descer\\ ** para criar um pacote e copiar os ficheiros de pacote para a localização \\ \\omeuservidor\\descer\\:

    ![MSBuild argumentos][2]

    **Nota:** Copiar os ficheiros para uma partilha de pública torna mais fácil manualmente implementar os pacotes do seu computador de desenvolvimento.

5.  Testar o sucesso no passo de compilação ao selecionar uma alteração ao projeto ou uma nova compilação de fila. Para uma nova compilação, no Explorador de equipa, de fila **Todas as definições de criar,** com o botão direito e, em seguida, selecione **Criar novo da fila de espera**.

## <a name="4-publish-a-package-using-a-powershell-script"></a>4: publicar um pacote utilizando um PowerShell Script

Esta secção descreve como construir um script do Windows PowerShell que irá publicar o resultado de pacote de aplicação da nuvem no Azure utilizando parâmetros opcionais. Este script pode ser chamado depois da compilação passo no seu automatização de compilação personalizado. Pode também chamado de atividades de fluxo de trabalho do modelo de processo no Visual Studio TFS equipa crie.

1.  Instalar os [cmdlets do Azure PowerShell][] (v0.6.1 ou superior).
    Durante a fase de configuração do cmdlet escolha instalar o como um snap-in. Tenha em atenção que esta versão beneficiam substitui a versão mais antiga oferecida através do CodePlex, apesar das versões anteriores foram numeradas 2.x.x.

2.  Iniciar o PowerShell Azure através do menu Iniciar ou comece a página. Se começar desta forma, os cmdlets do Azure PowerShell serão carregados.

3.  Na linha de comandos do PowerShell, certifique-se de que os cmdlets do PowerShell são carregados introduzindo o comando parcial `Get-Azure` e, em seguida, premir a tecla de tabulação para a conclusão da declaração.

    Se premir a tecla de tabulação repetidamente, deverá ver vários comandos do Azure PowerShell.

4.  Certifique-se de que consegue ligar à sua subscrição do Azure importando as informações de subscrição do ficheiro .publishsettings.

    `Import-AzurePublishSettingsFile c:\scripts\WindowsAzure\default.publishsettings`

    Em seguida, introduza o comando

    `Get-AzureSubscription`

    Isto mostra informações acerca da sua subscrição. Verifique se está tudo correto.

4.  Guardar o modelo de script fornecido no final deste artigo para a sua pasta de scripts como c:\\scripts\\WindowsAzure\\**PublishCloudService.ps1**.

5.  Reveja a secção de parâmetros do script. Adicionar ou modificar quaisquer valores predefinidos. Estes valores podem sempre ser substituídas por passagem no parâmetros explícitos.

6.  Garantir que não haja contas de serviço e de armazenamento na nuvem válidos são criadas na sua subscrição que pode ser atribuída pelo script publicar. A conta de armazenamento (armazenamento de BLOBs) será utilizada para carregar e armazenar temporariamente o ficheiro de pacote e configuração de implementação enquanto está a ser criada a implementação.

    -   Para criar um novo serviço de nuvem, pode ligar este script ou utilizar o [portal clássica Azure](http://go.microsoft.com/fwlink/?LinkID=213885). O nome do serviço de nuvem será utilizado como um prefixo num nome de domínio completamente qualificado e, consequentemente, tem de ser exclusivo.

            New-AzureService -ServiceName "mytestcloudservice" -Location "North Central US" -Label "mytestcloudservice"

    -   Para criar uma nova conta de armazenamento, pode ligar este script ou utilizar o [portal clássica Azure](http://go.microsoft.com/fwlink/?LinkID=213885). O nome da conta de armazenamento será utilizado como um prefixo num nome de domínio completamente qualificado e, consequentemente, tem de ser exclusivo. Pode tentar utilizar o mesmo nome que o serviço de nuvem.

            New-AzureStorageAccount -ServiceName "mytestcloudservice" -Location "North Central US" -Label "mytestcloudservice"

7.  Ligar para o script diretamente a partir do Azure PowerShell ou configurar este script do fio à sua automatização de compilação anfitrião a ocorrer após a compilação de pacote.

    >[AZURE.IMPORTANT] O script sempre irá eliminar ou substituir as implementações existentes por predefinição se estes estão detetados. Isto é necessário para ativar a entrega contínua de automatização onde é possível sem perguntar de utilizador.

    **Cenário de exemplo 1:** contínua implementação para o ambiente de teste de um serviço:

        PowerShell c:\scripts\windowsazure\PublishCloudService.ps1 -environment Staging -serviceName mycloudservice -storageAccountName mystoragesaccount -packageLocation c:\drops\app.publish\ContactManager.Azure.cspkg -cloudConfigLocation c:\drops\app.publish\ServiceConfiguration.Cloud.cscfg -subscriptionDataFile c:\scripts\default.publishsettings

    Isto é, normalmente, seguido teste de verificação e troque um VIP. Pode ser feito o trocar VIP através de do [portal clássica Azure](http://go.microsoft.com/fwlink/?LinkID=213885) ou ao utilizar o cmdlet de implementação de mover.

    **Cenário de exemplo 2:** contínua implementação para o ambiente de produção de um serviço de teste dedicada

        PowerShell c:\scripts\windowsazure\PublishCloudService.ps1 -environment Production -enableDeploymentUpgrade 1 -serviceName mycloudservice -storageAccountName mystorageaccount -packageLocation c:\drops\app.publish\ContactManager.Azure.cspkg -cloudConfigLocation c:\drops\app.publish\ServiceConfiguration.Cloud.cscfg -subscriptionDataFile c:\scripts\default.publishsettings

    **Ambiente de trabalho remoto:**

    Se o ambiente de trabalho remoto está ativado no projeto Azure que irá necessitar de efetuar passos adicionais únicos para garantir que o certificado de serviço de nuvem correcto é carregado para todos os serviços em nuvem orientados por este script.

    Localize os valores de impressão digital certificado esperados pelo seu funções. Os valores de impressão digital estão visíveis na secção de certificados do ficheiro de configuração da nuvem (ou seja, ServiceConfiguration.Cloud.cscfg). Também é visível na caixa de diálogo Configuração do ambiente de trabalho remoto no Visual Studio quando Mostrar opções e ver o certificado seleccionado.

        <Certificates>
              <Certificate name="Microsoft.WindowsAzure.Plugins.RemoteAccess.PasswordEncryption" thumbprint="C33B6C432C25581601B84C80F86EC2809DC224E8" thumbprintAlgorithm="sha1" />
        </Certificates>

    Carregar certificados de ambiente de trabalho remoto como uma etapa de configuração únicos utilizando o seguinte script do cmdlet:

        Add-AzureCertificate -serviceName <CLOUDSERVICENAME> -certToDeploy (get-item cert:\CurrentUser\MY\<THUMBPRINT>)

    Por exemplo:

        Add-AzureCertificate -serviceName 'mytestcloudservice' -certToDeploy (get-item cert:\CurrentUser\MY\C33B6C432C25581601B84C80F86EC2809DC224E8

    Em alternativa pode exportar o ficheiro de certificado PFX com a chave privada e carregar certificados para cada serviço em nuvem destino utilizando o [portal clássica Azure](http://go.microsoft.com/fwlink/?LinkID=213885). 
    Leia o seguinte artigo para saber mais: [[http://msdn.microsoft.com/library/windowsazure/gg443832.aspx]].

    **Atualizar a implementação de implementação vs. eliminar -\> nova implementação**

    O script irá, por predefinição, efetuar uma implementação atualizar ($enableDeploymentUpgrade = 1) quando nenhum parâmetro é transmitido no ou o valor 1 lhe é transmitido explicitamente. De instâncias de uma única isto tem a vantagem de demorar menos tempo do que uma implementação completo. Para instâncias que requerem elevada disponibilidade que isto também tem a vantagem de deixar algumas instâncias em execução enquanto outras pessoas são atualizadas (andar o seu domínio de atualização), assim como o seu VIP não será eliminado.

    Implementação de actualização pode ser desativada no script ($enableDeploymentUpgrade = 0) ou passando *- enableDeploymentUpgrade 0* como um parâmetro, que altera o comportamento de script pela primeira vez eliminar qualquer implementação existente e, em seguida, criar uma nova implementação.

    >[AZURE.IMPORTANT] O script sempre irá eliminar ou substituir as implementações existentes por predefinição se estes estão detetados. Isto é necessário para ativar a entrega contínua de automatização onde é possível sem perguntar do utilizador/operador.

## <a name="5-publish-a-package-using-tfs-team-build"></a>5: publicar um pacote utilizando TFS construir de equipa

Este passo opcional liga-se TFS Criar equipa para o script que criou no passo 4, que trata da publicação da compilação de pacote para Azure. Isto implica modificar o modelo de processo utilizado pela sua definição de compilação para que seja executada uma atividade de publicar no final do fluxo de trabalho. A atividade de publicar executará o comando do PowerShell prisma nos parâmetros da compilação. Resultado do MSBuild destina-se e publicar script será encaminhado para o resultado de compilação padrão.

1.  Editar a definição construir responsável pela contínua implementar.

2.  Selecione o separador de **processo** .

3.  Siga [estas instruções](http://msdn.microsoft.com/library/dd647551.aspx) para adicionar um projeto de atividade para o modelo de processo de criar, transferir o modelo predefinido, adicioná-lo ao projeto e dar entrada do mesmo. Dê ao modelo de processo de criar um novo nome, tal como AzureBuildProcessTemplate.

3.  Voltar para o separador **processo** e utilize **Mostrar detalhes** para mostrar uma lista de modelos de processo de compilação disponíveis. Selecione o botão de **novo...** e navegue para o projeto que adicionou e com entrada dada apenas. Localize o modelo que acabou de criar e clique **em OK**.

4.  Abra o modelo de processo selecionada para edição. Pode abrir diretamente no estruturador de fluxo de trabalho ou no editor de XML para trabalhar com o XAML.

5.  Adicione a seguinte lista de argumentos novos como itens em linhas separadas no separador argumentos de estruturador de fluxo de trabalho. Todos os argumentos devem ter direção = em e escreva = cadeia. Estes irão ser utilizados para parâmetros do fluxo da definição do compilação para o fluxo de trabalho, que, em seguida, obter utilizado para ligar o script publicar.

        SubscriptionName
        StorageAccountName
        CloudConfigLocation
        PackageLocation
        Environment
        SubscriptionDataFileLocation
        PublishScriptLocation
        ServiceName

    ![Lista de argumentos][3]

    O XAML correspondente tem o seguinte aspeto:

        <Activity  _ />
          <x:Members>
            <x:Property Name="BuildSettings" Type="InArgument(mtbwa:BuildSettings)" />
            <x:Property Name="TestSpecs" Type="InArgument(mtbwa:TestSpecList)" />
            <x:Property Name="BuildNumberFormat" Type="InArgument(x:String)" />
            <x:Property Name="CleanWorkspace" Type="InArgument(mtbwa:CleanWorkspaceOption)" />
            <x:Property Name="RunCodeAnalysis" Type="InArgument(mtbwa:CodeAnalysisOption)" />
            <x:Property Name="SourceAndSymbolServerSettings" Type="InArgument(mtbwa:SourceAndSymbolServerSettings)" />
            <x:Property Name="AgentSettings" Type="InArgument(mtbwa:AgentSettings)" />
            <x:Property Name="AssociateChangesetsAndWorkItems" Type="InArgument(x:Boolean)" />
            <x:Property Name="CreateWorkItem" Type="InArgument(x:Boolean)" />
            <x:Property Name="DropBuild" Type="InArgument(x:Boolean)" />
            <x:Property Name="MSBuildArguments" Type="InArgument(x:String)" />
            <x:Property Name="MSBuildPlatform" Type="InArgument(mtbwa:ToolPlatform)" />
            <x:Property Name="PerformTestImpactAnalysis" Type="InArgument(x:Boolean)" />
            <x:Property Name="CreateLabel" Type="InArgument(x:Boolean)" />
            <x:Property Name="DisableTests" Type="InArgument(x:Boolean)" />
            <x:Property Name="GetVersion" Type="InArgument(x:String)" />
            <x:Property Name="PrivateDropLocation" Type="InArgument(x:String)" />
            <x:Property Name="Verbosity" Type="InArgument(mtbw:BuildVerbosity)" />
            <x:Property Name="Metadata" Type="mtbw:ProcessParameterMetadataCollection" />
            <x:Property Name="SupportedReasons" Type="mtbc:BuildReason" />
            <x:Property Name="SubscriptionName" Type="InArgument(x:String)" />
            <x:Property Name="StorageAccountName" Type="InArgument(x:String)" />
            <x:Property Name="CloudConfigLocation" Type="InArgument(x:String)" />
            <x:Property Name="PackageLocation" Type="InArgument(x:String)" />
            <x:Property Name="Environment" Type="InArgument(x:String)" />
            <x:Property Name="SubscriptionDataFileLocation" Type="InArgument(x:String)" />
            <x:Property Name="PublishScriptLocation" Type="InArgument(x:String)" />
            <x:Property Name="ServiceName" Type="InArgument(x:String)" />
          </x:Members>

          <this:Process.MSBuildArguments>

6.  Adicione uma nova sequência no final do agente de executar no:

    1.  Comece por adicionar atividade uma instrução se verificar a existência de um ficheiro de script válida. Defina a condição com este valor:

            Not String.IsNullOrEmpty(PublishScriptLocation)

    2.  Em depois caso da instrução se adicionar uma nova actividade de sequência. Definir o nome a apresentar para 'Iniciar publicar'

    3.  Com o início publicar sequência ainda seleccionada, adicionar a seguinte lista de novas variáveis como itens em linhas separadas no separador variáveis de estruturador de fluxo de trabalho. Todas as variáveis devem ter o tipo de variável = cadeia e âmbito = início publicar. Estes irão ser utilizados para parâmetros do fluxo da definição do compilação para o fluxo de trabalho, que, em seguida, obter utilizado para ligar o script publicar.

        -   SubscriptionDataFilePath, do tipo cadeia

        -   PublishScriptFilePath, do tipo cadeia

            ![Novas variáveis][4]

    4.  Se estiver a utilizar TFS 2012 ou anterior, adicione uma atividade de ConvertWorkspaceItem no início da nova sequência. Se estiver a utilizar TFS 2013 ou posterior, adicione uma atividade de GetLocalPath no início da nova sequência. Para um ConvertWorkspaceItem, defina as propriedades da seguinte forma: direção = ServerToLocal, DisplayName = 'Converter publicar script nomedeficheiro', entrada = 'PublishScriptLocation' resultado = 'PublishScriptFilePath' Área de trabalho = 'Área de trabalho'. Para uma atividade de GetLocalPath, defina a propriedade IncomingPath para 'PublishScriptLocation' e o resultado para 'PublishScriptFilePath'. Esta actividade converte o caminho para o script de publicar a partir do TFS localizações do servidor (se aplicável) para um caminho de disco local padrão.

    5.  Se estiver a utilizar TFS 2012 ou anterior, adicione outra ConvertWorkspaceItem atividade no final da nova sequência. Direcção = ServerToLocal, DisplayName = 'Converter o nome de ficheiro de subscrição,' entrada = 'SubscriptionDataFileLocation' resultado = 'SubscriptionDataFilePath' Área de trabalho = 'Área de trabalho'. Se estiver a utilizar TFS 2013 ou posterior, adicione outro GetLocalPath. IncomingPath = 'SubscriptionDataFileLocation' e o resultado = 'SubscriptionDataFilePath'.

    6.  Adicione uma actividade InvokeProcess no final da nova sequência.
        Esta actividade chamadas PowerShell.exe com os argumentos transmitidos pela definição de criar.

        1.  Argumentos = String.Format ("-ficheiro""{0}" "- serviceName {1} - storageAccountName {2} - packageLocation""{3}" "- cloudConfigLocation""{4}" "- subscriptionDataFile""{5}" "- selectedSubscription {6}-ambiente""{7}" "", PublishScriptFilePath, ServiceName, StorageAccountName, PackageLocation, CloudConfigLocation, SubscriptionDataFilePath, SubscriptionName, ambiente)

        2.  DisplayName = executar publicar script

        3.  Nome de ficheiro = "PowerShell" (incluem as aspas)

        4.  OutputEncoding = System.Text.Encoding.GetEncoding(System.Globalization.CultureInfo.InstalledUICulture.TextInfo.OEMCodePage)

    7.  Na secção de **Saída padrão alça de** caixa de texto de InvokeProcess, defina o valor de caixa de texto para 'dados'. Esta é uma variável para armazenar os dados de saída padrão.

    8.  Adicione uma atividade de WriteBuildMessage imediatamente abaixo da secção **Processar saída padrão** . Definir a importância = 'Microsoft.TeamFoundation.Build.Client.BuildMessageImportance.High' e a mensagem = 'dados'. Este procedimento garante a saída padrão do script irá obter escrita para a saída de compilação.

    9.  Na secção **Processar erro de saída** , caixa de texto de InvokeProcess, defina o valor de caixa de texto para 'dados'. Esta é uma variável para armazenar os dados de erro-padrão.

    10. Adicione uma atividade de WriteBuildError imediatamente abaixo da secção **Processar erro de saída** . Definir a mensagem = 'dados'. Isto assegura que os erros de padrão do script irá obter escritos a saída de erro compilação.

    11. Corrigir erros, indicados por marcas de ponto de exclamação azuis. Paire o cursor sobre as marcas de ponto de exclamação para obter uma sugestão sobre o erro. Guarde o fluxo de trabalho para limpar os erros.

    O resultado final das atividades de fluxo de trabalho publicar terá o seguinte aspeto no estruturador de:

    ![Atividades de fluxo de trabalho][5]

    O resultado final das atividades de fluxo de trabalho publicar terá o seguinte aspeto na XAML:

        <If Condition="[Not String.IsNullOrEmpty(PublishScriptLocation)]" sap2010:WorkflowViewState.IdRef="If_1">
            <If.Then>
              <Sequence DisplayName="Start Publish" sap2010:WorkflowViewState.IdRef="Sequence_4">
                <Sequence.Variables>
                  <Variable x:TypeArguments="x:String" Name="SubscriptionDataFilePath" />
                  <Variable x:TypeArguments="x:String" Name="PublishScriptFilePath" />
                </Sequence.Variables>
                <mtbwa:ConvertWorkspaceItem DisplayName="Convert publish script filename" sap2010:WorkflowViewState.IdRef="ConvertWorkspaceItem_1" Input="[PublishScriptLocation]" Result="[PublishScriptFilePath]" Workspace="[Workspace]" />
                <mtbwa:ConvertWorkspaceItem DisplayName="Convert subscription filename" sap2010:WorkflowViewState.IdRef="ConvertWorkspaceItem_2" Input="[SubscriptionDataFileLocation]" Result="[SubscriptionDataFilePath]" Workspace="[Workspace]" />
                <mtbwa:InvokeProcess Arguments="[String.Format(&quot; -File &quot;&quot;{0}&quot;&quot; -serviceName {1}&#xD;&#xA;            -storageAccountName {2} -packageLocation &quot;&quot;{3}&quot;&quot;&#xD;&#xA;            -cloudConfigLocation &quot;&quot;{4}&quot;&quot; -subscriptionDataFile &quot;&quot;{5}&quot;&quot;&#xD;&#xA;            -selectedSubscription {6} -environment &quot;&quot;{7}&quot;&quot;&quot;,&#xD;&#xA;            PublishScriptFilePath, ServiceName, StorageAccountName,&#xD;&#xA;            PackageLocation, CloudConfigLocation,&#xD;&#xA;            SubscriptionDataFilePath, SubscriptionName, Environment)]" DisplayName="'Execute Publish Script'" FileName="[PowerShell]" sap2010:WorkflowViewState.IdRef="InvokeProcess_1">
                  <mtbwa:InvokeProcess.ErrorDataReceived>
                    <ActivityAction x:TypeArguments="x:String">
                      <ActivityAction.Argument>
                        <DelegateInArgument x:TypeArguments="x:String" Name="data" />
                      </ActivityAction.Argument>
                      <mtbwa:WriteBuildError Message="{x:Null}" sap2010:WorkflowViewState.IdRef="WriteBuildError_1" />
                    </ActivityAction>
                  </mtbwa:InvokeProcess.ErrorDataReceived>
                  <mtbwa:InvokeProcess.OutputDataReceived>
                    <ActivityAction x:TypeArguments="x:String">
                      <ActivityAction.Argument>
                        <DelegateInArgument x:TypeArguments="x:String" Name="data" />
                      </ActivityAction.Argument>
                      <mtbwa:WriteBuildMessage sap2010:WorkflowViewState.IdRef="WriteBuildMessage_2" Importance="[Microsoft.TeamFoundation.Build.Client.BuildMessageImportance.High]" Message="[data]" mva:VisualBasic.Settings="Assembly references and imported namespaces serialized as XML namespaces" />
                    </ActivityAction>
                  </mtbwa:InvokeProcess.OutputDataReceived>
                </mtbwa:InvokeProcess>
              </Sequence>
            </If.Then>
          </If>
        </Sequence>


7.  Guarde a dar entrada e fluxo de trabalho de modelo de processo de compilação este ficheiro.

8.  Editar a definição de compilação (fechá-lo se já estiver aberto) e selecione o botão de **Novo** , se ainda não vir o novo modelo na lista de modelos de processo.

9.  Defina o parâmetro de valores de propriedade na secção diversos da seguinte forma:

    1.  CloudConfigLocation =' c:\\descer\\app.publish\\ServiceConfiguration.Cloud.cscfg' *Este valor deriva da: ($PublishDir)ServiceConfiguration.Cloud.cscfg*

    2.  PackageLocation = ' c:\\descer\\app.publish\\ContactManager.Azure.cspkg' *Este valor deriva da: ($PublishDir)($ProjectName) .cspkg*

    3.  PublishScriptLocation = ' c:\\scripts\\WindowsAzure\\PublishCloudService.ps1'

    4.  ServiceName = 'mycloudservicename' *Utilize o nome do serviço de nuvem adequado aqui*

    5.  Ambiente = 'Transição'

    6.  StorageAccountName = 'mystorageaccountname' *Utilize o nome de conta do armazenamento adequado*

    7.  SubscriptionDataFileLocation = ' c:\\scripts\\WindowsAzure\\Subscription.xml'

    8.  SubscriptionName = 'predefinido'

    ![Valores de propriedade de parâmetros][6]

10. Guarde as alterações à definição de criar.

11. Fila de espera uma compilação para executar construir pacote e publicar. Se tiver um accionador definido para integração contínuo, será executado este comportamento em cada dar entrada.

### <a name="publishcloudserviceps1-script-template"></a>Modelo de script PublishCloudService.ps1

```
Param(  $serviceName = "",
        $storageAccountName = "",
        $packageLocation = "",
        $cloudConfigLocation = "",
        $environment = "Staging",
        $deploymentLabel = "ContinuousDeploy to $servicename",
        $timeStampFormat = "g",
        $alwaysDeleteExistingDeployments = 1,
        $enableDeploymentUpgrade = 1,
        $selectedsubscription = "default",
        $subscriptionDataFile = ""
     )


function Publish()
{
    $deployment = Get-AzureDeployment -ServiceName $serviceName -Slot $slot -ErrorVariable a -ErrorAction silentlycontinue
    if ($a[0] -ne $null)
    {
        Write-Output "$(Get-Date -f $timeStampFormat) - No deployment is detected. Creating a new deployment. "
    }
    #check for existing deployment and then either upgrade, delete + deploy, or cancel according to $alwaysDeleteExistingDeployments and $enableDeploymentUpgrade boolean variables
    if ($deployment.Name -ne $null)
    {
        switch ($alwaysDeleteExistingDeployments)
        {
            1
            {
                switch ($enableDeploymentUpgrade)
                {
                    1  #Update deployment inplace (usually faster, cheaper, won't destroy VIP)
                    {
                        Write-Output "$(Get-Date -f $timeStampFormat) - Deployment exists in $servicename.  Upgrading deployment."
                        UpgradeDeployment
                    }
                    0  #Delete then create new deployment
                    {
                        Write-Output "$(Get-Date -f $timeStampFormat) - Deployment exists in $servicename.  Deleting deployment."
                        DeleteDeployment
                        CreateNewDeployment

                    }
                } # switch ($enableDeploymentUpgrade)
            }
            0
            {
                Write-Output "$(Get-Date -f $timeStampFormat) - ERROR: Deployment exists in $servicename.  Script execution cancelled."
                exit
            }
        } #switch ($alwaysDeleteExistingDeployments)
    } else {
            CreateNewDeployment
    }
}

function CreateNewDeployment()
{
    write-progress -id 3 -activity "Creating New Deployment" -Status "In progress"
    Write-Output "$(Get-Date -f $timeStampFormat) - Creating New Deployment: In progress"

    $opstat = New-AzureDeployment -Slot $slot -Package $packageLocation -Configuration $cloudConfigLocation -label $deploymentLabel -ServiceName $serviceName

    $completeDeployment = Get-AzureDeployment -ServiceName $serviceName -Slot $slot
    $completeDeploymentID = $completeDeployment.deploymentid

    write-progress -id 3 -activity "Creating New Deployment" -completed -Status "Complete"
    Write-Output "$(Get-Date -f $timeStampFormat) - Creating New Deployment: Complete, Deployment ID: $completeDeploymentID"

    StartInstances
}

function UpgradeDeployment()
{
    write-progress -id 3 -activity "Upgrading Deployment" -Status "In progress"
    Write-Output "$(Get-Date -f $timeStampFormat) - Upgrading Deployment: In progress"

    # perform Update-Deployment
    $setdeployment = Set-AzureDeployment -Upgrade -Slot $slot -Package $packageLocation -Configuration $cloudConfigLocation -label $deploymentLabel -ServiceName $serviceName -Force

    $completeDeployment = Get-AzureDeployment -ServiceName $serviceName -Slot $slot
    $completeDeploymentID = $completeDeployment.deploymentid

    write-progress -id 3 -activity "Upgrading Deployment" -completed -Status "Complete"
    Write-Output "$(Get-Date -f $timeStampFormat) - Upgrading Deployment: Complete, Deployment ID: $completeDeploymentID"
}

function DeleteDeployment()
{

    write-progress -id 2 -activity "Deleting Deployment" -Status "In progress"
    Write-Output "$(Get-Date -f $timeStampFormat) - Deleting Deployment: In progress"

    #WARNING - always deletes with force
    $removeDeployment = Remove-AzureDeployment -Slot $slot -ServiceName $serviceName -Force

    write-progress -id 2 -activity "Deleting Deployment: Complete" -completed -Status $removeDeployment
    Write-Output "$(Get-Date -f $timeStampFormat) - Deleting Deployment: Complete"

}

function StartInstances()
{
    write-progress -id 4 -activity "Starting Instances" -status "In progress"
    Write-Output "$(Get-Date -f $timeStampFormat) - Starting Instances: In progress"

    $deployment = Get-AzureDeployment -ServiceName $serviceName -Slot $slot
    $runstatus = $deployment.Status

    if ($runstatus -ne 'Running')
    {
        $run = Set-AzureDeployment -Slot $slot -ServiceName $serviceName -Status Running
    }
    $deployment = Get-AzureDeployment -ServiceName $serviceName -Slot $slot
    $oldStatusStr = @("") * $deployment.RoleInstanceList.Count

    while (-not(AllInstancesRunning($deployment.RoleInstanceList)))
    {
        $i = 1
        foreach ($roleInstance in $deployment.RoleInstanceList)
        {
            $instanceName = $roleInstance.InstanceName
            $instanceStatus = $roleInstance.InstanceStatus

            if ($oldStatusStr[$i - 1] -ne $roleInstance.InstanceStatus)
            {
                $oldStatusStr[$i - 1] = $roleInstance.InstanceStatus
                Write-Output "$(Get-Date -f $timeStampFormat) - Starting Instance '$instanceName': $instanceStatus"
            }

            write-progress -id (4 + $i) -activity "Starting Instance '$instanceName'" -status "$instanceStatus"
            $i = $i + 1
        }

        sleep -Seconds 1

        $deployment = Get-AzureDeployment -ServiceName $serviceName -Slot $slot
    }

    $i = 1
    foreach ($roleInstance in $deployment.RoleInstanceList)
    {
        $instanceName = $roleInstance.InstanceName
        $instanceStatus = $roleInstance.InstanceStatus

        if ($oldStatusStr[$i - 1] -ne $roleInstance.InstanceStatus)
        {
            $oldStatusStr[$i - 1] = $roleInstance.InstanceStatus
            Write-Output "$(Get-Date -f $timeStampFormat) - Starting Instance '$instanceName': $instanceStatus"
        }

        $i = $i + 1
    }

    $deployment = Get-AzureDeployment -ServiceName $serviceName -Slot $slot
    $opstat = $deployment.Status

    write-progress -id 4 -activity "Starting Instances" -completed -status $opstat
    Write-Output "$(Get-Date -f $timeStampFormat) - Starting Instances: $opstat"
}

function AllInstancesRunning($roleInstanceList)
{
    foreach ($roleInstance in $roleInstanceList)
    {
        if ($roleInstance.InstanceStatus -ne "ReadyRole")
        {
            return $false
        }
    }

    return $true
}

#configure powershell with Azure 1.7 modules
Import-Module Azure

#configure powershell with publishsettings for your subscription
$pubsettings = $subscriptionDataFile
Import-AzurePublishSettingsFile $pubsettings
Set-AzureSubscription -CurrentStorageAccountName $storageAccountName -SubscriptionName $selectedsubscription
Select-AzureSubscription $selectedsubscription

#set remaining environment variables for Azure cmdlets
$subscription = Get-AzureSubscription $selectedsubscription
$subscriptionname = $subscription.subscriptionname
$subscriptionid = $subscription.subscriptionid
$slot = $environment

#main driver - publish & write progress to activity log
Write-Output "$(Get-Date -f $timeStampFormat) - Azure Cloud Service deploy script started."
Write-Output "$(Get-Date -f $timeStampFormat) - Preparing deployment of $deploymentLabel for $subscriptionname with Subscription ID $subscriptionid."

Publish

$deployment = Get-AzureDeployment -slot $slot -serviceName $servicename
$deploymentUrl = $deployment.Url

Write-Output "$(Get-Date -f $timeStampFormat) - Created Cloud Service with URL $deploymentUrl."
Write-Output "$(Get-Date -f $timeStampFormat) - Azure Cloud Service deploy script finished."
```

## <a name="next-steps"></a>Próximos passos

Para ativar a depuração remota ao utilizar a entrega contínua, consulte o artigo [Ativar depuração ao utilizar a entrega contínua para publicar o Azure remota](cloud-services-virtual-machines-dotnet-continuous-delivery-remote-debugging.md).

  [Entrega contínua para Azure, utilizando os serviços de equipa do Visual Studio]: cloud-services-continuous-delivery-use-vso.md  
  [Serviço de compilação do Team Foundation]: https://msdn.microsoft.com/library/ee259687.aspx
  [.NET Framework 4]: https://www.microsoft.com/download/details.aspx?id=17851
  [.NET Framework 4.5]: https://www.microsoft.com/download/details.aspx?id=30653
  [.NET framework 4.5.2]: https://www.microsoft.com/download/details.aspx?id=42643
  [Dimensionar fora do seu sistema de compilação]: https://msdn.microsoft.com/library/dd793166.aspx
  [Implementar e configurar um servidor de compilação]: https://msdn.microsoft.com/library/ms181712.aspx
  [Cmdlets do PowerShell Azure]: powershell-install-configure.md
  [the .publishsettings file]: https://manage.windowsazure.com/download/publishprofile.aspx?wa=wsignin1.0
  [0]: ./media/cloud-services-dotnet-continuous-delivery/tfs-01bc.png
  [2]: ./media/cloud-services-dotnet-continuous-delivery/tfs-02.png
  [3]: ./media/cloud-services-dotnet-continuous-delivery/common-task-tfs-03.png
  [4]: ./media/cloud-services-dotnet-continuous-delivery/common-task-tfs-04.png
  [5]: ./media/cloud-services-dotnet-continuous-delivery/common-task-tfs-05.png
  [6]: ./media/cloud-services-dotnet-continuous-delivery/common-task-tfs-06.png
