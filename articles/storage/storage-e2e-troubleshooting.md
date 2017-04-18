<properties
    pageTitle="Resolução de problemas utilizando métricas de armazenamento do Azure e registo, AzCopy e analisador de mensagem de ponto a ponto | Microsoft Azure"
    description="Um tutorial demonstrar ponto a ponto resolução de problemas com a análise de armazenamento do Azure, AzCopy e Microsoft mensagem Analyzer"
    services="storage"
    documentationCenter="dotnet"
    authors="robinsh"
    manager="carmonm"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="08/03/2016"
    ms.author="robinsh"/>


# <a name="end-to-end-troubleshooting-using-azure-storage-metrics-and-logging-azcopy-and-message-analyzer"></a>Resolução de problemas de ponto a ponto com métricas de armazenamento do Azure e registo, AzCopy e analisador de mensagem

[AZURE.INCLUDE [storage-selector-portal-e2e-troubleshooting](../../includes/storage-selector-portal-e2e-troubleshooting.md)]

## <a name="overview"></a>Descrição geral

Diagnosticar e resolução de problemas são uma chave competências para a criação e de suporte aplicações cliente com o armazenamento do Windows Azure. Devido à natureza distribuída de uma aplicação do Azure, diagnóstico e resolução de problemas de erros e problemas de desempenho podem ser mais complexas do que nos ambientes tradicionais.

Neste tutorial, vamos vai demonstrar como identificar cliente determinados erros que podem afetar o desempenho e resolver problemas esses erros a partir do fim para fim utilizando ferramentas fornecidas pela Microsoft e o armazenamento do Windows Azure, para otimizar a aplicação de cliente.

Este tutorial fornece uma práticas informações detalhadas de um cenário de resolução de problemas de fim para fim. Para um guia aprofundado conceptual para a resolução de problemas armazenamento Azure aplicações, consulte o artigo [Monitor, diagnosticar e resolver problemas de armazenamento do Windows Azure](storage-monitoring-diagnosing-troubleshooting.md).

## <a name="tools-for-troubleshooting-azure-storage-applications"></a>Ferramentas para resolução de problemas aplicações de armazenamento do Windows Azure

Resolver problemas relacionados com aplicações de cliente utilizando o armazenamento do Windows Azure, pode utilizar uma combinação de ferramentas para determinar quando ocorreu um problema e o que pode ser a causa do problema. Estas ferramentas incluem:

- **Análise de armazenamento azure**. [Análise de armazenamento do Azure](http://msdn.microsoft.com/library/azure/hh343270.aspx) fornece métricas e registo para o armazenamento do Windows Azure.
    - **Métricas de armazenamento** controla métricas da transação e métricas de capacidade para a sua conta de armazenamento. Utilizar métricas, pode determinar como a aplicação está a executar de acordo com uma variedade de diferentes medidas. Consulte o artigo [Esquema de tabela de métricas de análise de armazenamento](http://msdn.microsoft.com/library/azure/hh343264.aspx) para obter mais informações sobre os tipos de métricas controladas pela análise de armazenamento.

    - **Armazenamento de registo** regista cada pedido para os serviços de armazenamento do Windows Azure a um registo do lado do servidor. O registo controla as dados detalhados para cada pedido, incluindo a operação executada, o estado da operação e informações de latência. Consulte o artigo [Formato de registo de análise de armazenamento](http://msdn.microsoft.com/library/azure/hh343259.aspx) para obter mais informações sobre os dados de pedidos e respostas escrito aos registos de análise de armazenamento.

> [AZURE.NOTE] Contas de armazenamento com um tipo de replicação de zona redundantes armazenamento (ZRS) não possui a métricas ou a funcionalidade de registo ativado neste momento. 

- **Azure Portal**. Pode configurar registo de métricas de e para a sua conta de armazenamento no [Portal do Azure](https://portal.azure.com). Também pode ver gráficos e gráficos que mostram como a aplicação está a executar ao longo do tempo e configurar alertas para notificá-lo se a sua aplicação executa forma diferente do que o esperado de uma métrica especificada.

    Consulte [Monitor de uma conta de armazenamento no Portal do Azure](storage-monitor-storage-account.md) para obter informações sobre como configurar a monitorização no Portal do Azure.

- **AzCopy**. Registos do servidor para o armazenamento do Windows Azure são armazenados como blobs, para que possa utilizar AzCopy para copiar os blobs do registo para um diretório local para uma análise utilizando o Microsoft mensagem Analyzer. Consulte o artigo [Transferir dados com o utilitário AzCopy da linha de comandos](storage-use-azcopy.md) para mais informações sobre AzCopy.

- **Análise de mensagens da Microsoft**. Análise de mensagens é uma ferramenta que consome ficheiros de registo e apresenta os dados do registo num formato visual que torna mais fácil para filtrar, procurar e dados de registo de grupo para conjuntos útil que pode utilizar para analisar erros e problemas de desempenho. Consulte o artigo [Guia de funcionamento do Microsoft mensagem Analyzer](http://technet.microsoft.com/library/jj649776.aspx) para obter mais informações sobre o analisador de mensagem.

## <a name="about-the-sample-scenario"></a>Sobre o cenário de exemplo

Para este tutorial, vamos examinar um cenário onde métricas de armazenamento do Windows Azure indicam uma taxa de sucesso percentagem baixa para uma aplicação que chamadas armazenamento Azure. A métrica do taxa sucesso percentagem baixo (mostrada como **PercentSuccess** no [Portal do Azure](https://portal.azure.com) e nas tabelas métricas) controla as operações que teve êxito, mas que devolvem um código de estado HTTP for maior do que 299. Os ficheiros de registo do lado do servidor de armazenamento, estas operações são registadas com o estado da transação **ClientOtherErrors**. Para obter mais detalhes sobre a métrica do baixa sucesso percentagem, consulte o artigo [métricas mostram PercentSuccess baixa ou entradas de registo de análise tem operações com o estado da transação da ClientOtherErrors](storage-monitoring-diagnosing-troubleshooting.md#metrics-show-low-percent-success).

Operações de armazenamento Azure podem devolver códigos de estado HTTP maior 299 como parte da respetiva funcionalidade normal. Mas estes erros em alguns casos indicam que poderá conseguir otimizar a sua aplicação de cliente de desempenho melhorado.

Neste cenário, vamos considerar uma taxa de sucesso percentagem baixa para ser nada abaixo de 100%. No entanto, pode escolher um nível de métrico diferente, de acordo com as suas necessidades. Recomendamos que durante o ensaio da sua aplicação, estabelecer uma tolerância do plano base para sua métricas de chave de desempenho. Por exemplo, poderá determinar, com base em testes, que a sua aplicação deve ter uma taxa de sucesso percentagem consistentes de 90% ou 85%. Se os dados de métricas mostram que a aplicação é afaste a partir desse número, em seguida, possa investigar problemas que podem estar a causar o aumentar.

Para cenário nosso exemplo, assim que tiver Estabelecemos que a métrica do taxa sucesso percentagem for inferior a 100%, iremos examinar os registos de início para encontrar os erros que se relacionam com as métricas e utilizá-los para descobrir o que está a causar a taxa de sucesso percentagem inferior. Abordaremos especificamente erros no intervalo 400. Em seguida, podemos irá investigar mais atentamente 404 erros (não encontrado).

### <a name="some-causes-of-400-range-errors"></a>Algumas causas de erros de intervalo 400

Os exemplos abaixo mostra uma amostra dos alguns erros 400 intervalo para pedidos de contra armazenamento de Blobs do Azure e os respetivos possíveis causas. Qualquer um dos seguintes erros, bem como erros no intervalo de 300 e o intervalo 500, podem contribuir para uma taxa de sucesso percentagem baixa.

Tenha em atenção que as listas abaixo estão longe de ser concluída. Consulte o artigo [Estado e códigos de erro](http://msdn.microsoft.com/library/azure/dd179382.aspx) no MSDN para obter mais detalhes sobre erros gerais de armazenamento do Windows Azure e sobre erros específicos para cada um dos serviços de armazenamento.

**Exemplos de código 404 (não encontrado) de estado**

Ocorre quando uma operação de leitura contra um contentor ou blob falha porque o blob ou contentor não se encontra.

- Ocorre se um contentor ou BLOBs foi eliminada por outro cliente antes deste pedido.
- Ocorre se estiver a utilizar uma chamada de API que cria o contentor ou blob depois de verificar se existe. API do CreateIfNotExists efetuar uma chamada de cabeça pela primeira vez para verificar a existência do contentor ou blob; Se não existir, é devolvido um erro 404 e, em seguida, uma segunda colocar chamada for feita para escrever o contentor ou BLOBs.

**Exemplos de (conflito) 409 o código de estado**

- Ocorre se utilizar uma API criar para criar um novo contentor ou blob, sem a verificar a existência pela primeira vez e já existe um contentor ou blob com esse nome.
- Ocorre se está a ser eliminado um contentor e tentar criar um novo contentor com o mesmo nome antes da operação de eliminação estiver concluída.
- Ocorre se especificar uma locação num contentor ou blob e já existe uma locação apresentar.

**Código de estado 412 (condição prévia Ocorreu uma falha) Exemplos**

- Ocorre quando a condição especificada por um cabeçalho condicional não tenha sido respeitada.
- Ocorre quando o ID de locação financeira especificado não corresponder o ID de locação financeira no contentor ou blob.

## <a name="generate-log-files-for-analysis"></a>Gerar ficheiros de registo para uma análise

Neste tutorial, vamos utilizar analisador de mensagem para trabalhar com três tipos diferentes de ficheiros de registo, embora poderá optar por trabalhar com qualquer um dos seguintes procedimentos:

- O **registo de servidor**, que é criado quando ativar o registo de armazenamento do Windows Azure. O registo do servidor contém dados sobre cada operação denominado relativamente a um dos serviços do armazenamento do Windows Azure - blob, fila de espera, tabela e ficheiro. O registo do servidor indica qual operação foi chamada e que o código de estado foi devolvidos, bem como outras detalhes sobre os pedidos e respostas.
- O **registo do cliente .NET**, que é criado quando ativar o registo do lado do cliente a partir de dentro da sua aplicação do .NET. O registo do cliente inclui informações detalhadas sobre como o cliente prepara o pedido e recebe e processa a resposta.
- O **registo de rastreio de rede HTTP**, que recolhe dados no HTTP/HTTPS pedidos e respostas dados, incluindo para operações de armazenamento do Windows Azure. Neste tutorial, vamos irá gerar o rastreio de rede através de análise de mensagem.

### <a name="configure-server-side-logging-and-metrics"></a>Configurar o registo do lado do servidor e métricas

Em primeiro lugar, irá precisamos de configurar o registo de armazenamento do Windows Azure e métricas, para que temos dados da aplicação do cliente para analisar. Pode configurar registo e métricas de várias formas - através de do [Portal do Azure](https://portal.azure.com), utilizando o PowerShell, ou através de programação. Consulte o artigo [Ativar métricas de armazenamento e visualizar dados de métricas](http://msdn.microsoft.com/library/azure/dn782843.aspx) e [Ativando log de armazenamento e aceder a dados do registo](http://msdn.microsoft.com/library/azure/dn782840.aspx) no MSDN para obter detalhes sobre como configurar a funcionalidade de registo e métricas.

**Através do Portal do Azure**

Para configurar a funcionalidade de registo e métricas para a sua conta de armazenamento utilizando o [Portal do Azure](https://portal.azure.com), siga as instruções apresentadas no [Monitor de uma conta de armazenamento no Portal do Azure](storage-monitor-storage-account.md).

> [AZURE.NOTE] Não é possível definir métricas minutos utilizando o Portal do Azure. No entanto, recomendamos que o utilizador defina-las para fins de neste tutorial e para investigar problemas de desempenho com a aplicação. Pode definir métricas minutos através do PowerShell, conforme apresentado abaixo ou através de programação a utilizar a biblioteca do cliente de armazenamento.
>
> Note que o Portal do Azure não é possível apresentar métricas minutos, apenas a hora a hora métricas.

**Através do PowerShell**

Para começar a utilizar com o PowerShell para Azure, consulte o artigo [como instalar e configurar o Azure PowerShell](../powershell-install-configure.md).

1. Utilize o cmdlet [AzureAccount adicionar](http://msdn.microsoft.com/library/azure/dn722528.aspx) para adicionar a sua conta de utilizador Azure à janela do PowerShell:

    ```
    Add-AzureAccount
    ```

2. Na janela de **Iniciar sessão no Microsoft Azure** , escreva o endereço de e-mail e palavra-passe associada à sua conta. Azure autentica e guarda as informações de credenciais e, em seguida, fecha a janela.
3. Configurar a conta de armazenamento predefinida para a conta de armazenamento que estiver a utilizar para o tutorial através da execução estes comandos na janela do PowerShell:

    ```
    $SubscriptionName = 'Your subscription name'
    $StorageAccountName = 'yourstorageaccount'
    Set-AzureSubscription -CurrentStorageAccountName $StorageAccountName -SubscriptionName $SubscriptionName
    ```

4. Ativar o registo de armazenamento para o serviço de BLOBs:

    ```
    Set-AzureStorageServiceLoggingProperty -ServiceType Blob -LoggingOperations Read,Write,Delete -PassThru -RetentionDays 7 -Version 1.0
    ```
5. Ativar métricas de armazenamento para o serviço de BLOBs, certificando-se definir **- MetricsType** para `Minute`:

    ```
    Set-AzureStorageServiceMetricsProperty -ServiceType Blob -MetricsType Minute -MetricsLevel ServiceAndApi -PassThru -RetentionDays 7 -Version 1.0
    ```

### <a name="configure-net-client-side-logging"></a>Configurar o registo do lado do cliente .NET

Para configurar o registo do lado do cliente para uma aplicação do .NET, ativar diagnósticos .NET no ficheiro de configuração da aplicação (Web. config ou App). Consulte o [registo do lado do cliente com a biblioteca do .NET armazenamento cliente](http://msdn.microsoft.com/library/azure/dn782839.aspx) e do [lado do cliente registo com o Microsoft Azure armazenamento SDK para Java](http://msdn.microsoft.com/library/azure/dn782844.aspx) no MSDN para obter detalhes.

O registo do lado do cliente inclui informações detalhadas sobre como o cliente prepara o pedido e recebe e processa a resposta.

A biblioteca do cliente armazenamento armazena os dados do registo do lado do cliente na localização especificada no ficheiro de configuração da aplicação (Web. config ou App).

### <a name="collect-a-network-trace"></a>Recolher um rastreio de rede

Pode utilizar o analisador de mensagem para recolher um rastreio de rede HTTP/HTTPS enquanto a aplicação de cliente estiver em execução. Mensagem Analyzer utiliza [Fiddler](http://www.telerik.com/fiddler) no back-end. Antes de recolher o rastreio de rede, recomendamos que configure Fiddler para gravar o tráfego HTTPS não encriptado:

1. Instale [Fiddler](http://www.telerik.com/download/fiddler).
2. Inicie o Fiddler.
2. Selecione ferramentas **| Opções de fiddler**.
3. Na caixa de diálogo Opções, certifique-se que **Liga de HTTPS capturar** e **Desencriptar tráfego HTTPS** são ambos selecionada, conforme apresentado abaixo.

![Configurar as opções de Fiddler](./media/storage-e2e-troubleshooting/fiddler-options-1.png)

Para o tutorial, recolher e guardar um rastreio de rede pela primeira vez no analisador de mensagem, em seguida, criar uma sessão de análise para analisar o rastreio e os registos. Para recolher um rastreio de rede no Analyzer mensagem:

1. Na mensagem Analyzer, selecione **ficheiro | Rastreio rápido | Não encriptado HTTPS**.
2. O rastreio será iniciado imediatamente. Selecione **Parar** para parar o rastreio, para que recomendamos pode configurá-la para apenas o tráfego de armazenamento rastreio.
3. Selecione **Editar** para editar a sessão de rastreio.
4. Selecione a ligação **Configurar** à direita do fornecedor ETW **Microsoft-Pef-WebProxy** .
5. Na caixa de diálogo **Definições avançadas** , clique no separador **fornecedor** .
6. No campo de **Filtro Hostname** , especifique os pontos finais de armazenamento, separados por espaços. Por exemplo, pode especificar os pontos finais da seguinte forma; alterar `storagesample` para o nome da sua conta de armazenamento:

    ```
    storagesample.blob.core.windows.net storagesample.queue.core.windows.net storagesample.table.core.windows.net
    ```

7. Sair da caixa de diálogo e clique em **reiniciar** para começar a recolher o rastreio com o filtro do nome do anfitrião no local, para que apenas tráfego de rede de armazenamento do Windows Azure está incluído no rastreio.

>[AZURE.NOTE] Depois de terminar de recolher seu rastreio de rede, é fortemente recomendado que reverter as definições que poderá ter alterado no Fiddler para o tráfego de HTTPS desencriptar. Na caixa de diálogo Opções de Fiddler, desmarque as caixas de verificação **Capturar liga HTTPS** e **Desencriptar tráfego HTTPS** .

Consulte o artigo [utilizar as funcionalidades de rastreio de rede](http://technet.microsoft.com/library/jj674819.aspx) no Technet para obter mais detalhes.

## <a name="review-metrics-data-in-the-azure-portal"></a>Rever dados de métricas no Portal do Azure

Assim que a sua aplicação ter sido executada durante um período de tempo, pode rever os gráficos de métricas que aparecem no [Portal do Azure](https://portal.azure.com) para observar sobre o seu serviço tenha sido desempenho. Em primeiro lugar, navegue até à sua conta de armazenamento no Portal do Azure e adicionar um gráfico para a **Percentagem de sucesso** métrica.

No Portal do Azure, verá agora a **Percentagem de sucesso** no gráfico de monitorização, juntamente com quaisquer outras métricas que pode ter adicionado. No o cenário podemos irá investigar junto ao analisar os registos no analisador de mensagem, a taxa de sucesso percentagem é ligeiramente abaixo de 100%.

Para obter mais detalhes sobre como adicionar métricas para a página de monitorização, consulte o artigo [como: Adicionar métricas à tabela métricas](storage-monitor-storage-account.md#how-to-add-metrics-to-the-metrics-table).

> [AZURE.NOTE] Poderá demorar algum tempo para os seus dados de métricas apareça no Portal do Azure após ativar métricas de armazenamento. Isto acontece porque métricas de hora a hora para a hora anterior não são apresentadas no Portal do Azure até ter decorrido a hora atual. Além disso, métricas minutos atualmente não são apresentadas no Portal do Azure. Por isso, consoante quando ativar o métricas, poderá demorar até duas horas para ver dados de métricas.

## <a name="use-azcopy-to-copy-server-logs-to-a-local-directory"></a>Utilizar AzCopy para copiar os registos do servidor para um diretório local

Armazenamento Azure escreve dados do registo de servidor blobs, enquanto métricas estão escritas às tabelas. Registo blobs estão disponíveis no mais conhecidos `$logs` contentor para a sua conta de armazenamento. Registo blobs são com nome hierarquicamente por ano, mês, dia e hora, para que pode localizar facilmente o intervalo de tempo que pretende investigar. Por exemplo, na `storagesample` conta, o contentor para blobs o registo de 01/02/2015, a partir de 9 de 8 am, é `https://storagesample.blob.core.windows.net/$logs/blob/2015/01/08/0800`. Os blobs individuais neste contentor são com nome sequencialmente, começando `000000.log`.

Pode utilizar a ferramenta de linha de comandos AzCopy para transferir estes ficheiros de registo do lado do servidor para uma localização da sua escolha no seu computador local. Por exemplo, pode utilizar o seguinte comando para transferir os ficheiros de registo para operações de BLOBs que substituiu no 2 de Janeiro de 2015 para a pasta `C:\Temp\Logs\Server`; substituir `<storageaccountname>` com o nome da sua conta de armazenamento, e `<storageaccountkey>` com a sua chave de acesso de conta:

    AzCopy.exe /Source:http://<storageaccountname>.blob.core.windows.net/$logs /Dest:C:\Temp\Logs\Server /Pattern:"blob/2015/01/02" /SourceKey:<storageaccountkey> /S /V

AzCopy está disponível para transferência na página de [Transferências do Azure](https://azure.microsoft.com/downloads/) . Para obter detalhes sobre como utilizar AzCopy, consulte o artigo [Transferir dados com o utilitário AzCopy da linha de comandos](storage-use-azcopy.md).

Para obter informações adicionais sobre transferir registos do lado do servidor, consulte o artigo [Transferir o registo de armazenamento de dados do registo](http://msdn.microsoft.com/library/azure/dn782840.aspx#DownloadingStorageLogginglogdata).

## <a name="use-microsoft-message-analyzer-to-analyze-log-data"></a>Utilizar o Microsoft mensagem Analyzer para analisar os dados do registo

Microsoft mensagem Analyzer é uma ferramenta para capturar, apresentar e analisar protocolo tráfego, eventos e outras mensagens do sistema ou a aplicação em cenários de diagnóstico e resolução de problemas de mensagens. Mensagem Analyzer também permite-lhe carregar, Agregar e analisar dados a partir do registo e rastreio ficheiros guardados. Para mais informações sobre o analisador de mensagem, consulte o artigo [Guia de funcionamento do Microsoft mensagem Analyzer](http://technet.microsoft.com/library/jj649776.aspx).

Mensagem Analyzer inclui elementos para o armazenamento do Windows Azure que ajudá-lo para analisar o servidor, cliente e registos de rede. Nesta secção, vamos abordar como utilizar as ferramentas para resolver o problema de sucesso percentagem baixo nos registos do armazenamento.

### <a name="download-and-install-message-analyzer-and-the-azure-storage-assets"></a>Transferir e instalar o analisador de mensagem e os ativos de armazenamento do Azure

1. Transfira o [Analisador de mensagem](http://www.microsoft.com/download/details.aspx?id=44226) a partir do Microsoft Download Center e executar o instalador.
2. Inicie o analisador de mensagem.
3. A partir do menu **Ferramentas** , selecione **O Gestor de elementos**. Na caixa de diálogo **Gestor de elementos** , selecione **transferências**, em seguida, filtrar **Armazenamento do Windows Azure**. Verá os ativos de armazenamento do Azure, conforme mostrado na imagem abaixo.
4. Clique em **Sincronizar todos os itens apresentados** para instalar os ativos de armazenamento do Azure. Os ativos disponíveis incluem:
    - **Regras de cor de armazenamento azure:** Regras de cor de armazenamento Azure permitem-lhe definir filtros especiais que utilizam cores, texto e estilos de tipo de letra para a realçar as mensagens que contêm informações específicas num rastreio.
    - **Armazenamento azure gráficos:** Gráficos de armazenamento Azure são gráficos predefinidos que gráficos de dados de registo de servidor. Tenha em atenção que para utilizar gráficos de armazenamento do Windows Azure neste momento, poderá apenas carregue o registo do servidor na grelha da análise.
    - **Armazenamento azure analisadores:** Os analisadores de armazenamento do Windows Azure analisar o cliente de armazenamento do Windows Azure, servidor e registos de HTTP para apresentá-los na grelha de análise.
    - **Armazenamento azure filtros:** Os filtros de armazenamento Azure são critérios predefinidos que pode utilizar para os dados na grelha de análise de consulta.
    - **Armazenamento azure vista esquemas:** Esquemas de vista de armazenamento Azure são esquemas de coluna predefinidos e agrupamentos na grelha de análise.
4. Reinicie o analisador de mensagem após instalar os ativos.

![Gestor de elementos de análise de mensagem](./media/storage-e2e-troubleshooting/mma-start-page-1.png)

> [AZURE.NOTE] Instale todos os ativos de armazenamento do Windows Azure apresentados para fins deste tutorial.

### <a name="import-your-log-files-into-message-analyzer"></a>Importar os seus ficheiros de registo para o analisador de mensagem

Pode importar todos os seus ficheiros de registo guardadas (do lado do servidor, ao lado do cliente e rede) para uma única sessão no Microsoft mensagem Analyzer para uma análise.

1. No menu **ficheiro** no Microsoft analisador de mensagem, clique em **Nova sessão**e, em seguida, clique em **Sessão em branco**. Na caixa de diálogo **Nova sessão** , introduza um nome para a sua sessão de análise. No painel de **Detalhes da sessão** , clique no botão **ficheiros** .
1. Para carregar os dados de rastreio de rede gerados pela análise de mensagem, clique em **Adicionar ficheiros**, navegue para a localização onde o guardou o ficheiro .matp partir da sua sessão de rastreio web, selecione o ficheiro .matp e clique em **Abrir**.
1. Para carregar os dados de registo do lado do servidor, clique em **Adicionar ficheiros**, navegue para a localização onde que transferiu os registos do lado do servidor, selecione os ficheiros de registo para o intervalo de tempo que pretende analisar e clique em **Abrir**. Em seguida, no painel de **Detalhes da sessão** , defina a **Configuração do registo de texto** pendente para cada ficheiro de registo do lado do servidor para **AzureStorageLog** para se certificar de que Microsoft mensagem Analyzer pode analisar o ficheiro de registo corretamente.
1. Para carregar os dados de registo do lado do cliente, clique em **Adicionar ficheiros**, navegue para a localização onde o guardou os registos do lado do cliente, selecione os ficheiros de registo que pretende analisar e clique em **Abrir**. Em seguida, no painel de **Detalhes da sessão** , defina a **Configuração do registo de texto** pendente para cada ficheiro de registo do lado do cliente para **AzureStorageClientDotNetV4** para se certificar de que Microsoft mensagem Analyzer pode analisar o ficheiro de registo corretamente.
1. Clique em **Iniciar** , na caixa de diálogo **Nova sessão** para carregar e analisar os dados do registo. Apresenta os dados do registo na grelha de análise de análise da mensagem.

A imagem abaixo mostra uma sessão de exemplo configurada com o servidor, cliente e ficheiros de registo de rastreio de rede.

![Configurar a sessão de análise de mensagens](./media/storage-e2e-troubleshooting/configure-mma-session-1.png)

Tenha em atenção que a mensagem Analyzer carrega ficheiros de registo na memória. Se tiver um grande conjunto de dados do registo, que irá que pretende filtrá-lo para obter o melhor desempenho de mensagem Analyzer.

Em primeiro lugar, determine o período de tempo que está interessado em rever e manter este período de tempo o menor possível. Em muitos casos pretenderá rever um período de minutos ou horas na maior parte. Importe o conjunto mais pequeno de registos que podem corresponder às suas necessidades.

Se ainda tiver uma grande quantidade de dados do registo, poderá querer especificar uma sessão de filtro para filtrar os dados do registo antes de carregá-lo. Na caixa de **Filtro de sessão** , selecione o botão de **biblioteca** para escolher um filtro predefinido; Por exemplo, escolha **Global tempo filtro I** os filtros de armazenamento do Windows Azure ao filtro num intervalo de tempo. Em seguida, pode editar os critérios de filtro para especificar as inicial e final carimbo para o intervalo que pretende ver. Também pode filtrar um código de estado específico. Por exemplo, pode optar por carregar apenas as entradas de registo onde o código de estado está 404.

Para mais informações sobre como importar dados do registo para o Microsoft mensagem Analyzer, consulte o artigo [Obter dados de mensagens](http://technet.microsoft.com/library/dn772437.aspx) no TechNet.

### <a name="use-the-client-request-id-to-correlate-log-file-data"></a>Utilizar o ID de cliente do pedido para correlacionar dados do ficheiro de registo

A biblioteca de cliente de armazenamento do Azure gera automaticamente um ID de pedido de cliente exclusivo para cada pedido. Este valor é escrito do registo do cliente, o registo do servidor e o rastreio de rede, para utilizá-lo para correlacionar dados ao longo de todos os três registos que dentro de análise de mensagem. Ver [ID de pedido de cliente](storage-monitoring-diagnosing-troubleshooting.md#client-request-id) para obter informações adicionais sobre o pedido do cliente ID.

As secções abaixo descrevem como utilizar as vistas de esquema personalizado e pré-configurada para correlacionar e agrupar dados com base no ID de pedido de cliente.

### <a name="select-a-view-layout-to-display-in-the-analysis-grid"></a>Selecione um esquema de vista para apresentar na grelha de análise

Os ativos de armazenamento para Analisador de mensagem incluem Azure armazenamento vista esquemas, que são as vistas pré-configurada que pode utilizar para apresentar os seus dados com agrupamentos úteis e colunas para cenários diferentes. Também pode criar esquemas de vista personalizada e guardá-las para reutilização.

A imagem abaixo mostra o menu de **Vista de esquema** disponível ao selecionar a **Vista de esquema** no Friso da barra de ferramentas. Os esquemas de vista para o armazenamento do Azure são agrupados sob o nó de **Armazenamento do Windows Azure** no menu de lista. Pode procurar `Azure Storage` na caixa de pesquisa para filtrar em armazenamento do Windows Azure ver apenas esquemas. Também pode selecionar a estrela junto a um esquema de vista para o tornar um favorito e apresentá-lo na parte superior do menu.

![Menu de vista de esquema](./media/storage-e2e-troubleshooting/view-layout-menu.png)

Para começar, selecione **agrupado por ClientRequestID e módulo**. Grupos de esquema esta vista inicie sessão dados a partir de todos os três registos pela primeira vez por ID de pedido de cliente, em seguida, ao ficheiro de registo de origem (ou **módulo** no analisador de mensagem). Com esta vista, pode desagregar um ID de pedido de cliente em particular e ver os dados de todos os ficheiros de registo de três para esse pedido de cliente ID.

A imagem abaixo mostra esta vista de esquema aplicada aos dados do registo de exemplo, com um subconjunto de colunas apresentadas. Pode ver que um ID de pedido de cliente em particular, grelha da análise apresenta dados a partir do registo do cliente, o registo de servidor e o rastreio de rede.

![Armazenamento Azure Vista esquema](./media/storage-e2e-troubleshooting/view-layout-client-request-id-module.png)

>[AZURE.NOTE] Ficheiros de registo diferentes tem colunas diferentes, para quando dados a partir de vários ficheiros de registo são apresentados na grelha de análise, algumas colunas não podem conter quaisquer dados para uma determinada linha. Por exemplo, na imagem acima, as linhas de registo do cliente não mostrar todos os dados de **data/hora**, **TimeElapsed**, **origem**e **destino** colunas, uma vez que estas colunas não existam do registo do cliente, mas existe no rastreio de rede. Da mesma forma, a coluna de **data/hora** apresenta dados de data/hora a partir do registo do servidor, mas não serão apresentados dados para as colunas **TimeElapsed**, de **origem**e de **destino** , não fazem parte do registo do servidor.

Além de utilizar os esquemas de vista de armazenamento do Windows Azure, também pode definir e guardar o seus próprio esquemas de vista. Pode selecionar outros campos pretendidos para agrupar dados e guardar o agrupamento como parte do seu esquema personalizado também.

### <a name="apply-color-rules-to-the-analysis-grid"></a>Aplicar regras de cor a grelha de análise

Os ativos de armazenamento também incluir regras de cor, o que significa que um visual identificar os diferentes tipos de erros na grelha de análise da oferta. As regras de cores predefinidas aplicáveis a erros HTTP, para que apareçam apenas para o rastreio de rede e registo de servidor.

Para aplicar regras de cor, selecione **Regras de cor** a partir do Friso da barra de ferramentas. Verá as regras de cor de armazenamento do Windows Azure no menu de lista. Para o tutorial, selecione **Erros de cliente (StatusCode entre 400 e 499)**, conforme mostrado na imagem abaixo.

![Armazenamento Azure Vista esquema](./media/storage-e2e-troubleshooting/color-rules-menu.png)

Além de utilizar as regras de cor de armazenamento do Windows Azure, também pode definir e guardar as suas próprias regras de cor.

### <a name="group-and-filter-log-data-to-find-400-range-errors"></a>Agrupar e filtrar os dados do registo para encontrar erros de intervalo 400

Em seguida, vamos agrupar e filtrar os dados do registo para encontrar todos os erros no intervalo 400.

1. Localize a coluna **StatusCode** na grelha da análise, botão direito do rato no cabeçalho de coluna e selecione o **grupo**.
2. Grupo seguinte, na coluna **ClientRequestId** . Verá que os dados na grelha de análise está agora organizados por código de estado e por ID de pedido de cliente.
1. Apresenta a janela de ferramenta de filtro de vista, se ainda não estiver visível. No Friso barra de ferramentas, selecione **Windows ferramenta**, em seguida, o **Filtro de vista**.
2. Para filtrar os dados do registo para apresentar apenas os erros de intervalo 400, adicione os seguintes critérios de filtro para a janela de **Filtro de vista** e clique em **Aplicar**:

        (AzureStorageLog.StatusCode >= 400 && AzureStorageLog.StatusCode <=499) || (HTTP.StatusCode >= 400 && HTTP.StatusCode <= 499)

A imagem abaixo mostra os resultados deste agrupar e filtrar. Por exemplo, expandir o campo **ClientRequestID** sob o agrupamento para o código de estado 409, mostra uma operação que resultou nesse código de estado.

![Armazenamento Azure Vista esquema](./media/storage-e2e-troubleshooting/400-range-errors1.png)

Depois de aplicar este filtro, verá que as linhas de registo do cliente são excluídas, como o cliente de registo não inclui uma coluna de **StatusCode** . Para começar, podemos irá reveja o servidor e registos de rastreio de rede para localizar 404 erros e, em seguida, podemos irá regressar do registo do cliente para examinar as operações de cliente por um instrutor às mesmas.

>[AZURE.NOTE] Pode filtrar a coluna **StatusCode** e continuar a apresentar dados a partir de todos os três registos que, incluindo o registo do cliente, se adicionar uma expressão para o filtro que inclui entradas de registo onde o código de estado é nulo. Para construir esta expressão de filtro, utilize:
>
> <code>&#42;StatusCode >= 400 or !&#42;StatusCode</code>
>
> Este filtro devolve todas as linhas de cliente do registo e apenas as linhas do registo de servidor e registo HTTP onde for maior que 400 o código de estado. Se aplicar ao esquema de vista agrupado por ID de pedido de cliente e o módulo, pode procurar ou deslocar-se através das entradas de registo para localizar as onde todos os três registos que são representados.   

### <a name="filter-log-data-to-find-404-errors"></a>Filtrar os dados do registo para encontrar 404 erros

Os ativos de armazenamento incluir filtros predefinidos que pode utilizar para limitar os dados do registo para encontrar erros ou que está à procura de tendências. Em seguida, podemos vai aplicar dois filtros predefinidos: que filtra o servidor e registos de rastreio de rede para 404 erros e que filtra os dados num intervalo de tempo especificado.

1. Apresenta a janela de ferramenta de filtro de vista, se ainda não estiver visível. No Friso barra de ferramentas, selecione **Windows ferramenta**, em seguida, o **Filtro de vista**.
2. Na janela do filtro de vista, selecione **biblioteca**e, procure no `Azure Storage` para localizar o armazenamento do Windows Azure filtros. Seleccione o filtro para **404 mensagens (não encontrado) em todos os registos**.
3. Apresentar o menu de **biblioteca** novamente e localize e selecione o **Filtro de tempo Global**.
4. Edite as marcas de hora apresentadas no filtro de intervalo que pretende ver. Isto irá ajudar a reduzir o intervalo de dados para analisar.
5. O filtro deverá aparecer semelhante ao exemplo abaixo. Clique em **Aplicar** para aplicar o filtro à grelha de análise.

        ((AzureStorageLog.StatusCode == 404 || HTTP.StatusCode == 404)) And
        (#Timestamp >= 2014-10-20T16:36:38 and #Timestamp <= 2014-10-20T16:36:39)

![Armazenamento Azure Vista esquema](./media/storage-e2e-troubleshooting/404-filtered-errors1.png)

### <a name="analyze-your-log-data"></a>Analisar os seus dados de registo

Agora que tem agrupados e filtrados os seus dados, pode examinar os detalhes de pedidos individuais que gerou 404 erros. Esquema de vista atual, os dados são agrupados por ID de pedido de cliente, em seguida, pela origem de registo. Uma vez que recomendamos estiver a filtrar sobre os pedidos de onde o campo de StatusCode contém 404, vamos ver apenas o servidor e dados de rastreio de rede, não os dados de registo do cliente.

A imagem abaixo mostra um pedido específico onde uma operação de obter Blob produzidos um 404 porque o blob não existe. Tenha em atenção que algumas colunas foram removidas a partir da vista padrão para apresentar os dados relevantes.

![Servidor filtrado e registos de rastreio de rede](./media/storage-e2e-troubleshooting/server-filtered-404-error.png)

Em seguida, podemos irá haver uma correlação este ID de pedido de cliente com os dados de registo do cliente para ver o quê o cliente foi demorar quando o erro aconteceu. Pode apresentar uma nova vista de grelha de análise para esta sessão ver os dados de registo do cliente, que abre num separador segundo:

1. Em primeiro lugar, copie o valor do campo **ClientRequestId** para a área de transferência. Pode fazê-lo ao selecionar qualquer linha, localizar o campo **ClientRequestId** , clicando com o botão direito no valor de dados e seleccionando **Copiar 'ClientRequestId'**.
1. No Friso barra de ferramentas, selecione **Novo Viewer**, em seguida, selecione uma **Análise de grelha** para abrir um novo separador. O separador novo mostra todos os dados nos seus ficheiros de registo, sem agrupar, filtrar ou regras de cor.
2. No Friso barra de ferramentas, selecione **Vista de esquema**, em seguida, selecione **Todas as colunas de cliente do .NET** na secção de **Armazenamento do Windows Azure** . Este esquema vista mostra os dados do cliente do registo, bem como os registos de rastreio de servidor e de rede. Por predefinição é efetuada na coluna **MessageNumber** .
3. Em seguida, procure o registo de cliente o ID de pedido de cliente. No Friso barra de ferramentas, selecione **Localizar mensagens**, em seguida, especifique um filtro personalizado no ID do pedido de cliente no campo **Procurar** . Utilize esta sintaxe para o filtro, especificando o seu próprio ID de pedido de cliente:

        *ClientRequestId == "398bac41-7725-484b-8a69-2a9e48fc669a"

Mensagem Analyzer localiza e seleciona a primeira entrada de registo que os critérios de pesquisa corresponde o ID de pedido de cliente. No registo do cliente, existem várias entradas para cada ID do pedido de cliente, pelo que poderá querer agrupá-los no campo **ClientRequestId** para facilitar vê-los a todos os em conjunto. A imagem abaixo mostra todas as mensagens no registo do cliente para o cliente especificado ID do pedido.

![Cliente registo com os erros de 404](./media/storage-e2e-troubleshooting/client-log-analysis-grid1.png)

Utilizar os dados apresentados nos esquemas de vista nestes dois separadores, pode analisar os dados de pedido para determinar o que pode ter causado o erro. Também pode observe pedidos precedidos este um para ver se um evento anterior pode ter por um instrutor para o erro 404. Por exemplo, pode rever as entradas de registo de cliente anterior este ID de pedido de cliente para determinar se o blob terem sido eliminado, ou se o erro foi devido a aplicação de cliente chamar uma API CreateIfNotExists num contentor ou blob. No registo do cliente, pode encontrar endereço o blob no campo **Descrição** ; no servidor e registos de rastreio de rede, esta informação aparece no campo **Resumo** .

Quando já souber o endereço do blob que produzidos o erro 404, pode investigar ainda mais. Se procura as entradas de registo para outras mensagens associadas a operações no blob da mesma, pode verificar se o cliente eliminado anteriormente a entidade.

## <a name="analyze-other-types-of-storage-errors"></a>Analisar os outros tipos de erros de armazenamento

Agora que estiver familiarizado com a utilizar o analisador de mensagem para analisar os dados do registo, pode analisar outros tipos de erros utilizando a vista de esquemas, regras de cor e procurar/filtragem. As tabelas abaixo apresenta alguns problemas de que poderá encontrar e os critérios de filtro, pode utilizar para localizá-las. Para mais informações sobre como construir filtros e a análise de mensagem de filtragem de idioma, consulte o artigo [Filtrar dados da mensagem](http://technet.microsoft.com/library/jj819365.aspx).

|    Para investigar...                                                                                               |    Utilize a expressão de filtro...                                                                                                                                                                                                                                        |    Expressão aplica-se para o registo (cliente, servidor, rede, todos os)    |
|------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------|
|    Atrasos inesperados na entrega de mensagens numa fila                                                              |    AzureStorageClientDotNetV4.Description contém "A tentar novamente falhou operação".                                                                                                                                                                                |    Cliente                                                        |
|    Aumento HTTP PercentThrottlingError                                                                       |    HTTP. Response.StatusCode = = 500 & #124; & #124; HTTP. Response.StatusCode = = 503                                                                                                                                                                                          |    Rede                                                       |
|    Aumentar em PercentTimeoutError                                                                               |    HTTP. Response.StatusCode = = 500                                                                                                                                                                                                                             |    Rede                                                       |
|    Aumentar em PercentTimeoutError (todas)                                                                         |    * StatusCode = = 500                                                                                                                                                                                                                                          |    Todos os                                                           |
|    Aumentar em PercentNetworkError                                                                               |    AzureStorageClientDotNetV4.EventLogEntry.Level < 2                                                                                                                                                                                                          |    Cliente                                                        |
|    Mensagens HTTP 403 (proibido)                                                                                 |    HTTP. Response.StatusCode = = 403                                                                                                                                                                                                                             |    Rede                                                       |
|    HTTP 404 (não encontrado) mensagens                                                                                 |    HTTP. Response.StatusCode = = 404                                                                                                                                                                                                                             |    Rede                                                       |
|    404 (todas)                                                                                                     |    * StatusCode = = 404                                                                                                                                                                                                                                          |    Todos os                                                           |
|    Partilhado problema de autorização de acesso assinatura (SA)                                                             |    AzureStorageLog.RequestStatus = = "SASAuthorizationError"                                                                                                                                                                                                     |    Rede                                                       |
|    HTTP 409 (conflito) mensagens                                                                                  |    HTTP. Response.StatusCode = = 409                                                                                                                                                                                                                             |    Rede                                                       |
|    409 (todas)                                                                                                     |    * StatusCode = = 409                                                                                                                                                                                                                                          |    Todos os                                                           |
|    Entradas de registo de baixa PercentSuccess ou analytics tem operações com o estado da transação da ClientOtherErrors    |    AzureStorageLog.RequestStatus = = "ClientOtherError"                                                                                                                                                                                                         |    Servidor                                                        |
|    Aviso de Nagle                                                                                               |    ((AzureStorageLog.EndToEndLatencyMS-AzureStorageLog.ServerLatencyMS) > (AzureStorageLog.ServerLatencyMS * 1,5)) e (AzureStorageLog.RequestPacketSize < 1460) e (AzureStorageLog.EndToEndLatencyMS - AzureStorageLog.ServerLatencyMS > = 200)        |    Servidor                                                        |
|    Intervalo de tempo nos registos do servidor e de rede                                                                    |    #Data/hora > = 2014-10-20T16:36:38 e #Timestamp < = 2014-10-20T16:36:39                                                                                                                                                                                     |    Servidor de rede                                               |
|    Intervalo de tempo nos registos do servidor                                                                                |    AzureStorageLog.Timestamp > = 2014-10-20T16:36:38 e AzureStorageLog.Timestamp < = 2014-10-20T16:36:39                                                                                                                                                     |    Servidor                                                        |


## <a name="next-steps"></a>Próximos passos

Para mais informações sobre resolução de problemas cenários de ponto a ponto no armazenamento do Windows Azure, consulte estes recursos:

- [Monitorizar, diagnosticar e resolver problemas de armazenamento do Windows Azure](storage-monitoring-diagnosing-troubleshooting.md)
- [Análise de armazenamento](http://msdn.microsoft.com/library/azure/hh343270.aspx)
- [Monitorizar a uma conta de armazenamento no Portal do Azure](storage-monitor-storage-account.md)
- [Transferir dados com o utilitário AzCopy da linha de comandos](storage-use-azcopy.md)
- [Guia de operativo analisador de mensagens da Microsoft](http://technet.microsoft.com/library/jj649776.aspx)
