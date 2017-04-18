<properties
    pageTitle="Como utilizar diagnósticos (.NET) do Azure com serviços em nuvem | Microsoft Azure"
    description="Utilizar o Azure diagnósticos para recolher dados dos serviços em nuvem Azure para depurar, medir o desempenho, monitorização, análise de tráfego e muito mais."
    services="cloud-services"
    documentationCenter=".net"
    authors="rboucher"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="cloud-services"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="01/25/2016"
    ms.author="robb"/>



# <a name="enabling-azure-diagnostics-in-azure-cloud-services"></a>Ativar o Azure diagnósticos nos serviços em nuvem Azure

Consulte o artigo [Descrição geral dos diagnósticos do Azure](../azure-diagnostics.md) para um fundo no Azure diagnósticos.


## <a name="how-to-enable-diagnostics-in-a-worker-role"></a>Como ativar diagnósticos numa função de trabalho

Este obter instruções descreve como implementar uma função de trabalho Azure emite dados de telemetria utilizando a classe de Origemdoevento .NET. Diagnósticos do Azure são utilizado para recolher dados de telemetria e guardá-la numa conta de armazenamento Azure. Quando criar uma função de trabalho Visual Studio ativa automaticamente diagnósticos 1.0 como parte da solução no Azure SDK para .NET 2.4 e versões anteriores. As instruções seguintes descrevem o processo para criar a função de trabalho, a desativação diagnósticos 1.0 da solução e implementar diagnósticos 1.2 ou 1.3 à função de trabalho.

### <a name="pre-requisites"></a>Pré-requisitos
Este artigo assume que tenha uma subscrição do Azure e estiver a utilizar o Visual Studio 2013 com o SDK do Azure. Se não tiver uma subscrição do Azure, pode inscrever-se para a [Versão de avaliação gratuita][]. Certifique-se de que [instalar e configurar o Azure PowerShell versão 0.8.7 ou posterior][].

### <a name="step-1-create-a-worker-role"></a>Passo 1: Criar uma função de trabalho
1.  Inicie o **Visual Studio 2013**.
2.  Crie um novo projeto de **Serviço em nuvem Azure** do modelo **na nuvem** que destinos .NET Framework 4,5.  Nome do projeto "WadExample" e clique em Ok.
3.  Selecione a **Função de trabalho** e clique em Ok. O projeto será criado.
4.  No **Explorador de soluções**, faça duplo clique no ficheiro **WorkerRole1** propriedades.
5.  Na **configuração** de tabulação desmarque **Ativar diagnósticos** para desativar diagnósticos 1.0 (Azure SDK 2.4 e eariler).
6.  Construa a sua solução para confirmar que tem sem erros.

### <a name="step-2-instrument-your-code"></a>Passo 2: Instrumento seu código
Substitua os conteúdos de WorkerRole.cs o código seguinte. A classe SampleEventSourceWriter, herdada de [Classe Origemdoevento][], implementa quatro métodos de registo: **SendEnums**, **MessageMethod**, **SetOther** e **HighFreq**. O primeiro parâmetro para o método **WriteEvent** define o ID do evento respectivo. O método Run implementa ciclo infinito que liga para cada um dos métodos registo implementados a classe de **SampleEventSourceWriter** cada 10 segundos.

    using Microsoft.WindowsAzure.ServiceRuntime;
    using System;
    using System.Diagnostics;
    using System.Diagnostics.Tracing;
    using System.Net;
    using System.Threading;

    namespace WorkerRole1
    {
    sealed class SampleEventSourceWriter : EventSource
    {
        public static SampleEventSourceWriter Log = new SampleEventSourceWriter();
        public void SendEnums(MyColor color, MyFlags flags) { if (IsEnabled())  WriteEvent(1, (int)color, (int)flags); }// Cast enums to int for efficient logging.
        public void MessageMethod(string Message) { if (IsEnabled())  WriteEvent(2, Message); }
        public void SetOther(bool flag, int myInt) { if (IsEnabled())  WriteEvent(3, flag, myInt); }
        public void HighFreq(int value) { if (IsEnabled()) WriteEvent(4, value); }

    }

    enum MyColor
    {
        Red,
        Blue,
        Green
    }

    [Flags]
    enum MyFlags
    {
        Flag1 = 1,
        Flag2 = 2,
        Flag3 = 4
    }

    public class WorkerRole : RoleEntryPoint
    {
        public override void Run()
        {
            // This is a sample worker implementation. Replace with your logic.
            Trace.TraceInformation("WorkerRole1 entry point called");

            int value = 0;

            while (true)
            {
                Thread.Sleep(10000);
                Trace.TraceInformation("Working");

                // Emit several events every time we go through the loop
                for (int i = 0; i < 6; i++)
                {
                    SampleEventSourceWriter.Log.SendEnums(MyColor.Blue, MyFlags.Flag2 | MyFlags.Flag3);
                }

                for (int i = 0; i < 3; i++)
                {
                    SampleEventSourceWriter.Log.MessageMethod("This is a message.");
                    SampleEventSourceWriter.Log.SetOther(true, 123456789);
                }

                if (value == int.MaxValue) value = 0;
                SampleEventSourceWriter.Log.HighFreq(value++);
            }
        }

        public override bool OnStart()
        {
            // Set the maximum number of concurrent connections
            ServicePointManager.DefaultConnectionLimit = 12;

            // For information on handling configuration changes
            // see the MSDN topic at http://go.microsoft.com/fwlink/?LinkId=166357.

            return base.OnStart();
        }
    }
    }


### <a name="step-3-deploy-your-worker-role"></a>Passo 3: Implementar a sua função de trabalho
1.  Implemente ao seu cargo trabalhador Azure a partir do Visual Studio selecionando o projeto **WadExample** a solução Explorer, em seguida, **Publicar** a partir do menu de **Criar** .
2.  Selecione a sua subscrição.
3.  Na caixa de diálogo **Definições de publicar do Microsoft Azure** selecione **Criar novo...**.
4.  Na caixa de diálogo **criar serviço em nuvem e conta de armazenamento** , introduza um **nome** (por exemplo, "WadExample") e selecione uma região ou afinidade do grupo.
5.  Configurar o **ambiente** para **transição**.
6.  Modificar quaisquer outras **Definições** conforme adequado e clique em **Publicar**.
7.  Depois de concluída a implementação Verifique no portal do Azure clássico que o seu serviço de nuvem está num estado **em execução** .

### <a name="step-4-create-your-diagnostics-configuration-file-and-install-the-extension"></a>Passo 4: Criar o seu ficheiro de configuração de diagnóstico e instale a extensão
1.  Transferir a definição de esquema do ficheiro de configuração público executando o seguinte comando PowerShell:
2.
        (Get AzureServiceAvailableExtension - ExtensionName 'PaaSDiagnostics' - ProviderNamespace 'Microsoft.Azure.Diagnostics'). PublicConfigurationSchema | Entrada de ficheiro-utf8 codificação - FilePath 'WadConfig.xsd'

2.  Adicionar um ficheiro XML ao projeto **WorkerRole1** clicando no projeto **WorkerRole1** e selecione **Adicionar** -> **Novo Item...**  ->  **Visual c# itens** -> **dados** -> **Ficheiro XML**. Nome ao ficheiro "WadExample.xml".

    ![CloudServices_diag_add_xml](./media/cloud-services-dotnet-diagnostics/AddXmlFile.png)

3.  Associe a WadConfig.xsd o ficheiro de configuração. Certifique-se a janela do editor WadExample.xml a janela ativa. Prima **F4** para abrir a janela de **Propriedades** . Clique na propriedade **esquemas** na janela de **Propriedades** . Clique em **…** na propriedade **esquemas** . Clique na **Adicionar....** botão e navegue para a localização onde o guardou o ficheiro XSD e selecione o ficheiro WadConfig.xsd. Clique em **OK**.
4.  Substituir o conteúdo do ficheiro de configuração WadExample.xml com o XML seguinte e guarde o ficheiro. Este ficheiro de configuração define algumas contadores de desempenho para recolher: uma para a utilização da CPU e outra para utilização de memória. Em seguida, a configuração define os quatro eventos correspondente aos métodos de aula SampleEventSourceWriter.

```
        <?xml version="1.0" encoding="utf-8"?>
        <PublicConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
            <WadCfg>
                <DiagnosticMonitorConfiguration overallQuotaInMB="25000">
                <PerformanceCounters scheduledTransferPeriod="PT1M">
                    <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT1M" unit="percent" />
                    <PerformanceCounterConfiguration counterSpecifier="\Memory\Committed Bytes" sampleRate="PT1M" unit="bytes"/>
                    </PerformanceCounters>
                    <EtwProviders>
                        <EtwEventSourceProviderConfiguration provider="SampleEventSourceWriter" scheduledTransferPeriod="PT5M">
                            <Event id="1" eventDestination="EnumsTable"/>
                            <Event id="2" eventDestination="MessageTable"/>
                            <Event id="3" eventDestination="SetOtherTable"/>
                            <Event id="4" eventDestination="HighFreqTable"/>
                            <DefaultEvents eventDestination="DefaultTable" />
                        </EtwEventSourceProviderConfiguration>
                    </EtwProviders>
                </DiagnosticMonitorConfiguration>
            </WadCfg>
        </PublicConfig>
```

### <a name="step-5-install-diagnostics-on-your-worker-role"></a>Passo 5: Instalar diagnósticos no seu perfil de trabalho
Os cmdlets do PowerShell para gerir diagnósticos numa função web ou de trabalho são: conjunto AzureServiceDiagnosticsExtension, Get-AzureServiceDiagnosticsExtension e remover AzureServiceDiagnosticsExtension.

1.  Abra o Azure PowerShell.
2.  Execute o script para instalar diagnósticos no seu perfil do trabalho (substitua *StorageAccountKey* com a chave de conta de armazenamento da sua conta de armazenamento wadexample):

```
    $storage_name = "wadexample"
    $key = "<StorageAccountKey>"
    $config_path="c:\users\<user>\documents\visual studio 2013\Projects\WadExample\WorkerRole1\WadExample.xml"
    $service_name="wadexample"
    $storageContext = New-AzureStorageContext -StorageAccountName $storage_name -StorageAccountKey $key
    Set-AzureServiceDiagnosticsExtension -StorageContext $storageContext -DiagnosticsConfigurationPath $config_path -ServiceName $service_name -Slot Staging -Role WorkerRole1
```

### <a name="step-6-look-at-your-telemetry-data"></a>Passo 6: Observe os dados de telemetria
No Visual Studio **Server Explorer** navegue para a conta de armazenamento wadexample. Após a nuvem serviço está a ser executado cerca de 5 minutos deverá ver as tabelas **WADEnumsTable**, **WADHighFreqTable**, **WADMessageTable**, **WADPerformanceCountersTable** e **WADSetOtherTable**. Faça duplo clique das tabelas para ver a telemetria que tenha sido recolhida.
    ![CloudServices_diag_tables](./media/cloud-services-dotnet-diagnostics/WadExampleTables.png)


## <a name="configuration-file-schema"></a>Esquema de ficheiro de configuração

O ficheiro de configuração de diagnósticos define valores que são utilizadas para iniciar as definições de configuração de diagnóstico quando inicia o agente de diagnóstico. Consulte a [referência do esquema do mais recente](https://msdn.microsoft.com/library/azure/mt634524.aspx) para os valores válidos e exemplos.

## <a name="troubleshooting"></a>Resolução de problemas

Se tiver problemas, consulte o artigo [Resolução de problemas dos diagnósticos do Azure](../azure-diagnostics-troubleshooting.md) para obter ajuda com problemas comuns.

## <a name="next-steps"></a>Próximos passos
[Ver uma lista de máquina virtual relacionados com artigos Azure diagnósticos](azure-diagnostics.md#cloud-services) para alterar os dados que está a recolher, resolução de problemas ou mais informações sobre diagnósticos em geral.


[Origemdoevento classe]: http://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource(v=vs.110).aspx

[Debugging an Azure Application]: http://msdn.microsoft.com/library/windowsazure/ee405479.aspx   
[Collect Logging Data by Using Azure Diagnostics]: http://msdn.microsoft.com/library/windowsazure/gg433048.aspx
[Versão de avaliação gratuita]: http://azure.microsoft.com/pricing/free-trial/
[Instalar e configurar o Azure PowerShell versão 0.8.7 ou posterior]: http://azure.microsoft.com/documentation/articles/install-configure-powershell/
