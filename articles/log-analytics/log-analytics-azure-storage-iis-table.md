<properties
    pageTitle="Utilizar o armazenamento de BLOBs para o armazenamento do IIS e tabela para eventos | Microsoft Azure"
    description="Análise de registo pode ler os registos de serviços Azure que escrever diagnósticos ao armazenamento de tabelas ou registos do IIS escritos blob armazenamento."
    services="log-analytics"
    documentationCenter=""
    authors="bandersmsft"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="log-analytics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/25/2016"
    ms.author="banders"/>


# <a name="using-blob-storage-for-iis-and-table-storage-for-events"></a>Utilizar o armazenamento de Blobs do IIS e armazenamento de tabela para eventos

Análise de registo pode ler os registos para os seguintes serviços escrever diagnósticos ao armazenamento de tabelas ou registos do IIS escritos blob armazenamento:

+ Serviço ferro clusters (pré-visualização)
+ Máquinas virtuais
+ Funções da Web/trabalho

Antes da análise de registo podem recolher dados para estes recursos, tem de estar ativados diagnósticos do Azure.

Depois de diagnóstico está ativado, pode utilizar o portal do Azure ou PowerShell configurar análises de registo para recolher os registos.

Diagnósticos do Azure são uma extensão Azure que permite-lhe recolher dados de diagnóstico a partir de uma função de trabalho, função web ou máquina virtual em execução no Azure. Os dados são armazenados numa conta de armazenamento Azure e, em seguida, podem ser recolhidos pela análise de registo.

Para análise de registo recolher estes registos de diagnóstico Azure, tem de ser os registos nas seguintes localizações:

| Tipo de registo | Tipo de recurso | Localização |
| -------- | ------------- | -------- |
| Registos do IIS | Máquinas virtuais <br> Funções da Web <br> Funções de trabalho | WAD-iis-ficheiros de registo (armazenamento de BLOBs) |
| Syslog | Máquinas virtuais | LinuxsyslogVer2v0 (armazenamento de tabela) |
| Serviço ferro operacionais eventos | Nós ferro de serviço | WADServiceFabricSystemEventTable |
| Serviço ferro Ator fiável eventos | Nós ferro de serviço | WADServiceFabricReliableActorEventTable |
| Eventos de um serviço fiável ferro do serviço | Nós ferro de serviço | WADServiceFabricReliableServiceEventTable |
| Registos de eventos do Windows | Nós ferro de serviço <br> Máquinas virtuais <br> Funções da Web <br> Funções de trabalho | WADWindowsEventLogsTable (armazenamento de tabela) |
| Windows ETW registos | Nós ferro de serviço <br> Máquinas virtuais <br> Funções da Web <br> Funções de trabalho | WADETWEventTable (armazenamento de tabela) |

>[AZURE.NOTE] Registos do IIS de Web sites do Azure atualmente não são suportados.

Para máquinas virtuais, tem a opção de instalar o [agente de análise de registo](log-analytics-azure-vm-extension.md) para o seu máquina virtual para ativar a informações adicionais. Para além de ser capaz de analisar os registos do IIS e registos dos eventos, pode executar a análise adicional, incluindo o registo de alterações de configuração, avaliação de SQL e avaliação de atualização.

## <a name="enable-azure-diagnostics-in-a-virtual-machine-for-event-log-and-iis-log-collection"></a>Activar diagnósticos Azure numa máquina virtual para a coleção de registo de registo de eventos e IIS

Utilize o seguinte procedimento para ativar o Azure diagnósticos numa máquina virtual para colecção de registo de registo de eventos e IIS utilizando o portal do Microsoft Azure.

### <a name="to-enable-azure-diagnostics-in-a-virtual-machine-with-the-azure-portal"></a>Para ativar o Azure diagnósticos numa máquina virtual com o portal do Azure

1. Instale o agente de VM quando cria uma máquina virtual. Se a máquina virtual já existir, certifique-se de que o agente VM já está instalado.
    - No portal do Azure, navegue para a máquina virtual, selecione **Configuração opcional**e, em seguida **diagnóstico** e definir o **Estado** como **no**.

    Após a conclusão, a VM tem a extensão de diagnósticos do Azure instalada e em execução. Esta extensão é responsável por recolher os dados de diagnóstico.

2. Ativar a monitorização e configurar o registo de eventos numa VM existente. Pode ativar a diagnósticos ao nível do VM. Para ativar diagnósticos e, em seguida, configurar o registo de eventos, execute os seguintes passos:
    1. Selecione a VM.
    2. Clique em **monitorização**.
    3. Clique em **diagnóstico**.
    4. Defina o **Estado** para **Ativado**.
    5. Selecione cada registo de diagnóstico ao qual pretende recolher.
    7. Clique em **OK**.

Utilizar o Azure PowerShell com mais precisão pode especificar os eventos que são escritos armazenamento do Windows Azure. Referem-se para [recolher dados utilizando os diagnósticos Azure escritos armazenamento de tabela ou os registos do IIS escritos blob](log-analytics-azure-storage-json.md).


## <a name="enable-azure-diagnostics-in-a-web-role-for-iis-log-and-event-collection"></a>Activar diagnósticos Azure numa função de Web para colecção de registo e eventos do IIS

Consulte [Como para ativar diagnósticos num serviço de nuvem](../cloud-services/cloud-services-dotnet-diagnostics.md) para passos gerais sobre ativar diagnósticos do Azure. As instruções abaixo utilizam estas informações e personalizá-lo para utilização com o registo de análise.

Com o Azure diagnósticos ativada:

- Registos do IIS são armazenados por predefinição, com os dados do registo transferidos no intervalo de transferência scheduledTransferPeriod.
- Registos de eventos do Windows não são transferidos por predefinição.

### <a name="to-enable-diagnostics"></a>Para ativar diagnósticos

Para ativar os registos dos eventos do Windows ou para alterar o scheduledTransferPeriod, configurar diagnósticos do Azure utilizando o ficheiro de configuração de XML (diagnostics.wadcfg), conforme apresentado na [passo 4: criar o seu ficheiro de configuração de diagnóstico e instale a extensão](../cloud-services/cloud-services-dotnet-diagnostics.md)

O ficheiro de configuração de exemplo seguintes recolhe informações sobre registos do IIS e todos os eventos de registos de aplicação e sistema:

```
    <?xml version="1.0" encoding="utf-8" ?>
    <DiagnosticMonitorConfiguration xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration"
          configurationChangePollInterval="PT1M"
          overallQuotaInMB="4096">

      <Directories bufferQuotaInMB="0"
         scheduledTransferPeriod="PT10M">  
        <!-- IISLogs are only relevant to Web roles -->
        <IISLogs container="wad-iis" directoryQuotaInMB="0" />
      </Directories>

      <WindowsEventLog bufferQuotaInMB="0"
         scheduledTransferLogLevelFilter="Verbose"
         scheduledTransferPeriod="PT10M">
        <DataSource name="Application!*" />
        <DataSource name="System!*" />
      </WindowsEventLog>

    </DiagnosticMonitorConfiguration>
```

Certifique-se de que o seu ConfigurationSettings Especifica uma conta de armazenamento, tal como no exemplo seguinte:

```
    <ConfigurationSettings>
       <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="DefaultEndpointsProtocol=https;AccountName=<AccountName>;AccountKey=<AccountKey>"/>
    </ConfigurationSettings>
```

Os valores **AccountName** e **AccountKey** encontram-se no portal do Azure no dashboard de conta de armazenamento, em gerir as teclas de acesso. O protocolo para a cadeia de ligação tem de ser **https**.

Quando a configuração de diagnóstico actualizada está aplicada ao seu serviço de nuvem e está a escrever diagnósticos ao armazenamento do Azure, em seguida, está pronto para configurar a análise de registo.


## <a name="use-the-azure-portal-to-collect-logs-from-azure-storage"></a>Utilizar o portal do Azure recolher registos a partir do armazenamento do Windows Azure

Pode utilizar o portal do Azure para configurar a análise de registo para recolher registos para os seguintes serviços Azure:

+ Serviço ferro clusters
+ Máquinas virtuais
+ Funções da Web/trabalho

No portal do Azure, navegue para a área de trabalho de análise de registo e efetuar as seguintes tarefas:

1. Clique em *registos de contas de armazenamento*
2. Clique na tarefa *Adicionar*
3. Selecione a conta de armazenamento que contém os registos de diagnóstico
  - Esta conta pode ser uma conta de armazenamento clássico ou uma conta de armazenamento do Gestor de recursos do Azure
4. Selecione o tipo de dados que pretende recolher registos para
  - As opções são registos do IIS; Eventos; Syslog (Linux); ETW registos; Serviço ferro eventos
5. O valor para origem é preenchido automaticamente com base no tipo de dados e não pode ser alterado
6. Clique em OK para guardar a configuração

Repita os passos 2 a 6 para tipos de dados que pretende que a análise de registo para recolher e contas de armazenamento adicional.

Em cerca de 30 minutos, se possível ver dados a partir da conta de armazenamento no registo de análise. Apenas verá dados destina-se ao armazenamento após a configuração é aplicada. Análise de registo não lê os dados pré-existentes da conta de armazenamento.

>[AZURE.NOTE] O portal não validar a origem existe na conta de armazenamento ou se está a ser escritos novos dados.

## <a name="enable-azure-diagnostics-in-a-virtual-machine-for-event-log-and-iis-log-collection-using-powershell"></a>Activar diagnósticos Azure numa máquina virtual para o registo de eventos e IIS registo coleção através do PowerShell

Siga os passos na [Configuração de análises de registo para indexar diagnósticos do Azure](log-analytics-powershell-workspace-configuration.md#configuring-log-analytics-to-index-azure-diagnostics) para utilizar o PowerShell para ler a partir do Azure diagnósticos escritas ao armazenamento de tabelas.

Utilizar o Azure PowerShell com mais precisão pode especificar os eventos que são escritos armazenamento do Windows Azure.
Para mais informações, consulte o artigo [Ativar diagnósticos em máquinas virtuais do Azure](../virtual-machines-dotnet-diagnostics.md).

Pode ativar e atualizar diagnósticos Azure utilizando o seguinte script do PowerShell.
Também pode utilizar este script com uma configuração de registo personalizados.
Modificar o script para configurar a conta de armazenamento, o nome do serviço e o nome de máquina virtual.
O script utiliza cmdlets para máquinas virtuais clássicas.

Reveja o seguinte exemplo de script, copiá-la, modificá-lo conforme necessário, guardar a amostra como um ficheiro de script do PowerShell e, em seguida, execute o script.

```
    #Connect to Azure
    Add-AzureAccount

    # settings to change:
    $wad_storage_account_name = "myStorageAccount"
    $service_name = "myService"
    $vm_name = "myVM"

    #Construct Azure Diagnostics public config and convert to config format

    # Collect just system error events:
    $wad_xml_config = "<WadCfg><DiagnosticMonitorConfiguration><WindowsEventLog scheduledTransferPeriod=""PT1M""><DataSource name=""System!* "" /></WindowsEventLog></DiagnosticMonitorConfiguration></WadCfg>"

    $wad_b64_config = [System.Convert]::ToBase64String([System.Text.Encoding]::UTF8.GetBytes($wad_xml_config))
    $wad_public_config = [string]::Format("{{""xmlCfg"":""{0}""}}",$wad_b64_config)

    #Construct Azure diagnostics private config

    $wad_storage_account_key = (Get-AzureStorageKey $wad_storage_account_name).Primary
    $wad_private_config = [string]::Format("{{""storageAccountName"":""{0}"",""storageAccountKey"":""{1}""}}",$wad_storage_account_name,$wad_storage_account_key)

    #Enable Diagnostics Extension for Virtual Machine

    $wad_extension_name = "IaaSDiagnostics"
    $wad_publisher = "Microsoft.Azure.Diagnostics"
    $wad_version = (Get-AzureVMAvailableExtension -Publisher $wad_publisher -ExtensionName $wad_extension_name).Version # Gets latest version of the extension

    (Get-AzureVM -ServiceName $service_name -Name $vm_name) | Set-AzureVMExtension -ExtensionName $wad_extension_name -Publisher $wad_publisher -PublicConfiguration $wad_public_config -PrivateConfiguration $wad_private_config -Version $wad_version | Update-AzureVM
```


## <a name="next-steps"></a>Próximos passos

- [Ficheiros de utilização JSON no armazenamento de BLOBs](log-analytics-azure-storage-json.md) para ler os registos do Azure dos serviços que diagnósticos de escrita ao armazenamento blob do formato JSON.
- [Ativar soluções](log-analytics-add-solutions.md) para fornecer visão os dados.
- [Utilizar consultas de pesquisa](log-analytics-log-searches.md) para analisar os dados.
