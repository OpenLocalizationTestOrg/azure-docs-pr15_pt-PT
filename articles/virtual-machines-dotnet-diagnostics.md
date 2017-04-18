<properties
    pageTitle="Como utilizar os diagnósticos do Azure em máquinas virtuais do | Microsoft Azure"
    description="Utilizar o Azure diagnósticos para recolher dados a partir do máquinas virtuais do Azure para depurar, medir o desempenho, monitorização, análise de tráfego e muito mais."
    services="virtual-machines"
    documentationCenter=".net"
    authors="rboucher"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="virtual-machines"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="02/20/2016"
    ms.author="robb"/>



# <a name="enabling-diagnostics-in-azure-virtual-machines"></a>Ativar diagnósticos em máquinas virtuais do Azure

Consulte o artigo [Descrição geral dos diagnósticos do Azure](azure-diagnostics.md) para um fundo no Azure diagnósticos.

## <a name="how-to-enable-diagnostics-in-a-virtual-machine"></a>Como ativar diagnósticos numa máquina Virtual

Este obter instruções descreve como instalar remotamente diagnósticos para uma máquina virtual Azure a partir de um computador de desenvolvimento. Também a Saiba como implementar uma aplicação que é executado em que a máquina virtual Azure e emite dados de telemetria utilizando a.NET [Origemdoevento escolares][]. Diagnósticos do Azure são utilizado para recolher de telemetria e armazená-lo numa conta de armazenamento Azure.

### <a name="pre-requisites"></a>Pré-requisitos
Este obter instruções assume que tenha uma subscrição do Azure e estiver a utilizar o Visual Studio 2013 com o SDK do Azure. Se não tiver uma subscrição do Azure, pode inscrever-se para a [Versão de avaliação gratuita][]. Certifique-se de que [instalar e configurar o Azure PowerShell versão 0.8.7 ou posterior][].

### <a name="step-1-create-a-virtual-machine"></a>Passo 1: Criar uma Máquina Virtual
1.  No seu computador de desenvolvimento, inicie o Visual Studio 2013.
2.  No Visual Studio **Server Explorer** expandir **Azure**, com o botão direito **máquinas virtuais** , em seguida, selecione **Criar Máquina Virtual**.
3.  Selecione a sua subscrição do Azure na caixa de diálogo **Escolher uma subscrição** e clique em **seguinte**.
4.  Selecione o **Windows Server 2012 R2 Centro de dados, de Novembro de 2014** na caixa de diálogo **Seleccionar uma imagem de Máquina Virtual** e clique em **seguinte**.
5.  Nas **Definições básicas de Máquina Virtual**, defina o nome de máquina virtual para "wadexample". Defina o seu nome de utilizador do administrador e a palavra-passe e clique em **seguinte**.
6.  Na caixa de diálogo **Definições de serviço de nuvem** crie um novo serviço de nuvem com o nome "wadexampleVM". Criar uma nova conta de armazenamento com o nome "wadexample" e clique em **seguinte**.
7.  Clique em **Criar**.

### <a name="step-2-create-your-application"></a>Passo 2: Criar uma aplicação
1.  No seu computador de desenvolvimento, inicie o Visual Studio 2013.
2.  Crie uma nova aplicação Visual c# consola que destinos .NET Framework 4,5. Nome do projeto "WadExampleVM".
    ![CloudServices_diag_new_project](./media/virtual-machines-dotnet-diagnostics/NewProject.png)
3.  Substitua os conteúdos de Program.cs o código seguinte. A classe **SampleEventSourceWriter** implementa quatro métodos de registo: **SendEnums**, **MessageMethod**, **SetOther** e **HighFreq**. O primeiro parâmetro para o método WriteEvent define o ID do evento respetivos. O método Run implementa ciclo infinito que liga para cada um dos métodos registo implementados a classe de **SampleEventSourceWriter** cada 10 segundos.

        using System;
        using System.Diagnostics;
        using System.Diagnostics.Tracing;
        using System.Threading;

        namespace WadExampleVM
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

        class Program
        {
        static void Main(string[] args)
        {
            Trace.TraceInformation("My application entry point called");

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
        }
        }


4.  Guardar o ficheiro e selecione **Criar solução** a partir do menu **Criar** para criar o seu código.


### <a name="step-3-deploy-your-application"></a>Passo 3: Implementar a aplicação
1.  Com o botão direito no projeto **WadExampleVM** no **Explorador de solução** e selecione **Abrir pasta no Explorador de ficheiros**.
2.  Navegue para a pasta *bin\Debug* e copie todos os ficheiros (WadExampleVM.*)
3.  No **Explorador de servidor** com o botão direito na máquina virtual e selecione **Ligar utilizando o ambiente de trabalho remoto**.
4.  Uma vez ligado para a VM crie uma pasta denominada WadExampleVM e colar a aplicação de ficheiros para a pasta.
5.  Inicie a aplicação WadExampleVM.exe. Deverá visualizar uma janela de consola em branco.

### <a name="step-4-create-your-diagnostics-configuration-and-install-the-extension"></a>Passo 4: Criar a sua configuração de diagnóstico e instale a extensão
1.  Transfira a definição de esquema do ficheiro de configuração público para o computador de desenvolvimento executando o seguinte comando PowerShell:

        (Get-AzureServiceAvailableExtension -ExtensionName 'PaaSDiagnostics' -ProviderNamespace 'Microsoft.Azure.Diagnostics').PublicConfigurationSchema | Out-File -Encoding utf8 -FilePath 'WadConfig.xsd'

2.  Abra um novo ficheiro XML no Visual Studio, um projeto que já tenha aberto ou num Visual Studio instância com sem projectos abertos. No Visual Studio, selecione **Adicionar** -> **Novo Item...**  ->  **Visual c# itens** -> **dados** -> **Ficheiro XML**. O nome do ficheiro "WadExample.xml"
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

### <a name="step-5-remotely-install-diagnostics-on-your-azure-virtual-machine"></a>Passo 5: Instalar remotamente diagnósticos no seu máquina de Virtual do Azure
Os cmdlets do PowerShell para gerir diagnóstico sobre um VM são: conjunto AzureVMDiagnosticsExtension, Get-AzureVMDiagnosticsExtension e remover AzureVMDiagnosticsExtension.

1.  No seu computador de programador, abra o PowerShell Azure.
2.  Execute o script para instalar remotamente diagnósticos no seu VM (substituir *StorageAccountKey* com a chave de conta de armazenamento da sua conta de armazenamento wadexamplevm):

        $storage_name = "wadexamplevm"
        $key = "<StorageAccountKey>"
        $config_path="c:\users\<user>\documents\visual studio 2013\Projects\WadExampleVM\WadExampleVM\WadExample.xml"
        $service_name="wadexamplevm"
        $vm_name="WadExample"
        $storageContext = New-AzureStorageContext -StorageAccountName $storage_name -StorageAccountKey $key
        $VM1 = Get-AzureVM -ServiceName $service_name -Name $vm_name
        $VM2 = Set-AzureVMDiagnosticsExtension -DiagnosticsConfigurationPath $config_path -Version "1.*" -VM $VM1 -StorageContext $storageContext
        $VM3 = Update-AzureVM -ServiceName $service_name -Name $vm_name -VM $VM2.VM


### <a name="step-6-look-at-your-telemetry-data"></a>Passo 6: Observe os dados de telemetria
No Visual Studio **Server Explorer** navegue para a conta de armazenamento wadexample. Depois da VM tenha sido em execução cerca de 5 minutos deverá ver as tabelas **WADEnumsTable**, **WADHighFreqTable**, **WADMessageTable**, **WADPerformanceCountersTable** e **WADSetOtherTable**. Faça duplo clique das tabelas para ver a telemetria que tenha sido recolhida.

![CloudServices_diag_wadexamplevm_tables](./media/virtual-machines-dotnet-diagnostics/WadExampleVMTables.png)

## <a name="configuration-file-schema"></a>Esquema de ficheiro de configuração

O ficheiro de configuração de diagnósticos define valores que são utilizadas para iniciar as definições de configuração de diagnóstico quando inicia o agente de diagnóstico. Consulte a [referência do esquema do mais recente](https://msdn.microsoft.com/library/azure/mt634524.aspx) para os valores válidos e exemplos.

## <a name="troubleshooting"></a>Resolução de problemas

Para mais informações, consulte [Resolução de problemas dos diagnósticos do Azure](azure-diagnostics-troubleshooting.md) .


## <a name="next-steps"></a>Próximos passos
[Ver uma lista de máquina virtual relacionados com artigos Azure diagnósticos](azure-diagnostics.md#virtual-machines-using-azure-diagnostics) para alterar os dados que está a recolher, resolução de problemas ou mais informações sobre diagnósticos em geral.


[Origemdoevento classe]: http://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource(v=vs.110).aspx

[Debugging an Azure Application]: http://msdn.microsoft.com/library/windowsazure/ee405479.aspx   
[Collect Logging Data by Using Azure Diagnostics]: http://msdn.microsoft.com/library/windowsazure/gg433048.aspx
[Versão de avaliação gratuita]: http://azure.microsoft.com/pricing/free-trial/
[Instalar e configurar o Azure PowerShell versão 0.8.7 ou posterior]: http://azure.microsoft.com/documentation/articles/install-configure-powershell/
