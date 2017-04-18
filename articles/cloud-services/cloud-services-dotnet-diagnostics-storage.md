<properties
    pageTitle="Armazenar e vista de dados de diagnóstico no Azure armazenamento | Microsoft Azure"
    description="Obter dados de diagnóstico Azure para o armazenamento do Windows Azure e vê-lo"
    services="cloud-services"
    documentationCenter=".net"
    authors="rboucher"
    manager="jwhit"
    editor="tysonn" />
<tags
    ms.service="cloud-services"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="08/01/2016"
    ms.author="robb" />

# <a name="store-and-view-diagnostic-data-in-azure-storage"></a>Armazenar e vista de dados de diagnóstico no armazenamento do Windows Azure

Dados de diagnóstico não são armazenados permanentemente, a menos que transferi-la para o emulador de armazenamento do Microsoft Azure ou ao armazenamento Azure. Uma vez no armazenamento, podem ser visualizada com uma das várias ferramentas disponíveis.

## <a name="specify-a-storage-account"></a>Especificar uma conta de armazenamento

Especificar a conta de armazenamento que pretende utilizar no ficheiro ServiceConfiguration.cscfg. As informações da conta são definidas como uma cadeia de ligação uma definição de configuração. O exemplo seguinte mostra a cadeia de ligação predefinida criada para um novo projeto de serviço na nuvem no Visual Studio:


```
    <ConfigurationSettings>
       <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="UseDevelopmentStorage=true" />
    </ConfigurationSettings>
```

Pode alterar esta cadeia de ligação para fornecer informações de conta para uma conta de armazenamento Azure.

Dependendo do tipo de dados de diagnóstico que estão a ser recolhidos, diagnósticos do Azure utiliza o serviço de BLOBs ou o serviço de tabela. A tabela seguinte mostra as origens de dados que são permanentes e o respetivo formato.

|Origem de dados|Formato de armazenamento|
|---|---|
|Registos Azure|Tabela|
|IIS 7.0 registos|Blob|
|Registos de infraestrutura de diagnósticos Azure|Tabela|
|Ocorreu uma falha de registos de pedir rastreio|Blob|
|Registos de eventos do Windows|Tabela|
|Contadores de desempenho|Tabela|
|Informações de estado da falha de sistema|Blob|
|Registos de erros personalizado|Blob|

## <a name="transfer-diagnostic-data"></a>Transferir dados diagnóstico

Para SDK 2,5 e posterior, o pedido para transferir dados diagnóstico pode ocorrer através do ficheiro de configuração. Pode transferir dados diagnóstico em intervalos agendados conforme especificado na configuração.

Para SDK 2.4 e anteriores pode pedir para transferir os dados diagnóstico através do ficheiro de configuração, assim como através de programação. A abordagem de programação também permite-lhe fazer a pedido transferências.


>[AZURE.IMPORTANT] Quando transfere dados diagnóstico para uma conta de armazenamento Azure, pode assumir os custos de recursos de armazenamento utilizado pelos dados diagnóstico.

## <a name="store-diagnostic-data"></a>Armazenar os dados de diagnóstico

Os dados do registo são armazenados no armazenamento de BLOBs ou tabela com os seguintes nomes:

**Tabelas**

- **WadLogsTable** - registos escritos código utilizando a escuta rastreio.

- Alterações **WADDiagnosticInfrastructureLogsTable** - monitor diagnóstico e de configuração.

- **WADDirectoriesTable** – directórios que está a acompanhar o monitor de diagnóstico.  Isto inclui registos do IIS, IIS falhou pedido registos e directórios personalizados.  A localização do ficheiro de registo blob é especificada no campo contentor e o nome do blob é no campo RelativePath.  O campo de CaminhoAbsoluto indica a localização e o nome do ficheiro, tal como se encontrava na máquina virtual Azure.

- **WADPerformanceCountersTable** – contadores de desempenho.

- **WADWindowsEventLogsTable** – registos dos eventos do Windows.

**BLOBs**

- **contentor de controlo wad** – (apenas para o SDK 2.4 e anterior) contém os ficheiros de configuração de XML que controla o Azure diagnóstico.

- **wad-iis-failedreqlogfiles** – inicia sessão contém informações a partir do IIS falhou pedido.

- **wad-iis-ficheiros de registo** – inicia sessão contém informações sobre o IIS.

- **"personalizada"** – um contentor personalizado com base em Configurar directórios são monitorizados pelo monitor de diagnóstico.  O nome deste contentor blob será especificado no WADDirectoriesTable.

## <a name="tools-to-view-diagnostic-data"></a>Ferramentas para ver os dados de diagnóstico
Estão disponíveis para ver os dados após é transferida para o armazenamento várias ferramentas. Por exemplo:

- Explorador de servidor no Visual Studio - se tiver instalado as ferramentas do Azure para o Microsoft Visual Studio, pode utilizar o nó de armazenamento do Windows Azure no Explorador do servidor para ver blob só de leitura e dados da tabela de contas armazenamento Azure. Pode apresentar dados da sua conta do emulador armazenamento local e também a partir de contas de armazenamento que criou para Azure. Para mais informações, consulte [a navegação e recursos de gestão de armazenamento com o Explorador de servidor](../vs-azure-tools-storage-resources-server-explorer-browse-manage.md).

- [Explorador de armazenamento do Microsoft Azure](../vs-azure-tools-storage-manage-with-storage-explorer.md) é uma aplicação autónoma que lhe permite trabalhar facilmente com dados de armazenamento do Windows Azure no Windows, os x e Linux.

- [Azure Management Studio](http://www.cerebrata.com/products/azure-management-studio/introduction) inclui o Gestor de diagnósticos do Azure que permite-lhe ver, transferir e gerir os dados de diagnóstico recolhidos por aplicações em execução no Azure.


## <a name="next-steps"></a>Próximos passos

[Analisar o fluxo de uma aplicação de serviços em nuvem nos diagnósticos do Azure](cloud-services-dotnet-diagnostics-trace-flow.md)
