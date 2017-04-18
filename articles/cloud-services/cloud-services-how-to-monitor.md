<properties 
    pageTitle="Como monitorizar a um serviço na nuvem | Microsoft Azure" 
    description="Saiba como monitorizar serviços em nuvem utilizando o portal do Azure clássico." 
    services="cloud-services" 
    documentationCenter="" 
    authors="rboucher" 
    manager="timlt" 
    editor=""/>

<tags 
    ms.service="cloud-services" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/04/2015" 
    ms.author="robb"/>


# <a name="how-to-monitor-cloud-services"></a>Como monitorizar a serviços em nuvem

[AZURE.INCLUDE [disclaimer](../../includes/disclaimer.md)]

Pode monitorizar `key` métricas de desempenho para os seus serviços na nuvem no portal do Azure clássico. Pode definir o nível de monitorização para mínimo e verboso para cada função de serviço e, pode personalizar a apresenta monitorização. Dados de monitorização verbosas estão armazenados numa conta de armazenamento, que pode aceder fora do portal. 

Monitorização apresenta no portal do Azure clássica é configuráveis altamente. Pode escolher as métricas que pretende monitorizar na lista de métricas na página **Monitor** e, pode escolher quais métricas representar em gráficos de métricas na página **Monitor** e o dashboard. 

## <a name="concepts"></a>Conceitos

Por predefinição, a monitorização mínimas é fornecido para um novo serviço de nuvem utilizando contadores desempenho reunidos do sistema operativo anfitrião para as instâncias de funções (máquinas virtuais). Métricas mínimas estão limitadas a percentagem de CPU, In de dados, dados de saída, débito de leitura do disco e débito escrever do disco. Ao configurar a monitorização verboso, poderá receber métricas adicionais com base em dados de desempenho máquinas virtuais (instâncias de função). Métricas verbosas ativar análise mais perto dos problemas que ocorram durante operações de aplicações.

Por predefinição, dados de contador de desempenho de instâncias de função são amostra e transferidos da instância de função a 3 minutos intervalos. Quando ativa a monitorização verboso, os dados de contador de desempenho em bruto são agregados em intervalos de 5 minutos, 1 hora e 12 horas para cada instância de função e em várias instâncias de função para cada função. Os dados agregados são eliminados depois de 10 dias.

Depois de ativar a monitorização verboso, os dados de monitorização agregados são armazenados em tabelas na sua conta de armazenamento. Para ativar a monitorização verboso para uma função, tem de configurar uma cadeia de ligação de diagnóstico com ligações para a conta de armazenamento. Pode utilizar contas de armazenamento diferentes para diferentes funções.

Tenha em atenção que activar a monitorização verboso irá aumentar os custos do armazenamento relacionadas com armazenamento de dados, transferência de dados e as transações de armazenamento. Monitorização mínimas não requer uma conta de armazenamento. Os dados para os métricas que são expostos ao nível de monitorização mínimo não são armazenados na sua conta de armazenamento, mesmo que defina o nível de monitorização para verboso.


## <a name="how-to-configure-monitoring-for-cloud-services"></a>Como: configurar a monitorização de serviços em nuvem

Utilize os seguintes procedimentos para configurar verboso ou mínimas monitorização no portal do Azure clássico. 

### <a name="before-you-begin"></a>Antes de começar

- Crie uma conta de armazenamento para armazenar os dados de monitorização. Pode utilizar contas de armazenamento diferentes para diferentes funções. Para obter mais informações, consulte o artigo ajuda para **Contas do armazenamento**ou veja [como criar uma conta de armazenamento](/manage/services/storage/how-to-create-a-storage-account/).

- Ative o Azure diagnósticos para funções de serviço de nuvem. Consulte [Configurar diagnósticos para serviços em nuvem](https://msdn.microsoft.com/library/azure/dn186185.aspx#BK_EnableBefore).

Certifique-se de que a cadeia de ligação de diagnóstico está presente na configuração do papel. Não pode ativar a monitorização verboso até activar diagnósticos do Azure e incluir uma cadeia de ligação de diagnóstico na configuração de função.   

> [AZURE.NOTE] Projetos de filtragem de Azure SDK 2,5 não incluiu automaticamente a cadeia de ligação de diagnóstico no modelo de projeto. Estes projectos terá de adicionar manualmente a cadeia de ligação de diagnóstico para a configuração de função.

**Para adicionar manualmente a cadeia de ligação de diagnóstico a configuração de funções**

1. Abra o projeto de serviço na nuvem no Visual Studio
2. Faça duplo clique na **função** para abrir o estruturador de função e selecione o separador **Definições**
3. Procure uma definição de denominada **Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString**. 
4. Se não existir esta definição, em seguida, clique no botão **Adicionar definição** para adicioná-lo para a configuração e alterar o tipo de para a nova definição para **ConnectionString**
5. Defina o valor de cadeia de ligação ao clicar no botão **…** . Isto vai abrir uma caixa de diálogo permitindo-lhe selecionar uma conta de armazenamento.

    ![Definições do Visual Studio](./media/cloud-services-how-to-monitor/CloudServices_Monitor_VisualStudioDiagnosticsConnectionString.png)

### <a name="to-change-the-monitoring-level-to-verbose-or-minimal"></a>Para alterar o nível de monitorização para verboso ou mínimas

1. No [portal clássica Azure](https://manage.windowsazure.com/), abra a página de **Configurar** a implementação de serviço de nuvem.

2. Em **nível**, clique em **verboso** ou **mínimas**. 

3. Clique em **Guardar**.

Depois de ativar a monitorização verboso, deverá começar a ver os dados no portal do Azure clássico monitorização dentro a hora.

Os dados de contador de desempenho em bruto e dados agregados de monitorização são armazenados na conta de armazenamento de tabelas qualificados pelo ID da implementação para as funções. 

## <a name="how-to-receive-alerts-for-cloud-service-metrics"></a>Como: receber alertas para métricas de serviço de nuvem

Pode receber alertas com base no seu serviço de nuvem métricas de monitorização. Na página **Serviços de gestão** do portal clássica Azure, pode criar uma regra para acionar um alerta quando a métrica do que escolher atinja um valor que especificar. Também pode optar por correio enviado quando o alerta é acionado. Para obter mais informações, consulte o artigo [como: receber notificações de alerta e gerir um alerta de regras no Azure](http://go.microsoft.com/fwlink/?LinkId=309356).

## <a name="how-to-add-metrics-to-the-metrics-table"></a>Como: Adicionar métricas à tabela de métricas

1. No [portal clássica Azure](http://manage.windowsazure.com/), abra a página **Monitor** para o serviço de nuvem.

    Por predefinição, a tabela de métricas apresenta um subconjunto de métricas disponíveis. A ilustração mostra as métricas verboso predefinido para um serviço na nuvem, que é limitado a contador de desempenho Memory\Available MB, com dados agregados ao nível de função. Utilize **Adicionar métricas** para selecionar métricas de agregação e ao nível da função adicionais para monitorizar no portal do Azure clássico.

    ![Apresentar verboso](./media/cloud-services-how-to-monitor/CloudServices_DefaultVerboseDisplay.png)
 
2. Para adicionar métricas à tabela métricas:

    1. Clique em **Adicionar métricas** para abrir **Métricas escolher**, apresentado abaixo.

        A primeira métrica disponível é expandida para mostrar as opções que estão disponíveis. Para cada métrica, a opção superior apresenta dados agregados de monitorização para todas as funções. Além disso, pode escolher funções individuais para apresentar dados.

        ![Adicionar métricas](./media/cloud-services-how-to-monitor/CloudServices_AddMetrics.png)

    2. Para selecionar métricas para apresentar

        - Clique na seta para baixo por métrica para expandir as opções de monitorização.
        - Selecione a caixa de verificação para cada opção de monitorização que pretende apresentar.

        Pode apresentar até 50 métricas na tabela métricas.

        > [AZURE.TIP] No verboso monitorização, a lista de métricas pode conter dezenas de métricas. Para apresentar uma barra de deslocamento, Paire o cursor sobre o lado direito da caixa de diálogo. Para filtrar a lista, clique no ícone de pesquisa e introduza o texto na caixa de pesquisa, conforme apresentado abaixo.
    
        ![Adicionar métricas de pesquisa](./media/cloud-services-how-to-monitor/CloudServices_AddMetrics_Search.png)


3. Depois de terminar de selecionar métricas, clique em OK (marca de verificação).

    Métricas selecionadas são adicionadas à tabela de métricas, conforme apresentado abaixo.

    ![métricas do monitor](./media/cloud-services-how-to-monitor/CloudServices_Monitor_UpdatedMetrics.png)

 
4. Para eliminar uma métrica a partir da tabela de métricas, clique em métrica para selecioná-la e, em seguida, clique em **Eliminar métrica**. (Só verá **Eliminar métrica** quando tiver uma métrica selecionada.)

### <a name="to-add-custom-metrics-to-the-metrics-table"></a>Para adicionar métricas personalizadas à tabela de métricas

**Verboso** nível de monitorização fornece uma lista de métricas de predefinido que pode monitorizar no portal. Para além de estas pode monitorizar qualquer métricas personalizadas ou contadores de desempenho definidos pela sua aplicação através do portal.

Os seguintes passos partem do pressuposto de que ativou **verboso** nível de monitorização e que tem de configurar a sua aplicação para recolher e transferir contadores de desempenho personalizado. 

Para apresentar os contadores de desempenho personalizado no portal do terá de atualizar a configuração no contentor de controlo wad:
 
1. Abra o contentor wad-controlo blob na sua conta de armazenamento do diagnóstico. Pode utilizar o Visual Studio ou qualquer outro Explorador de armazenamento para o fazer.

    ![Explorador de servidor do Visual Studio](./media/cloud-services-how-to-monitor/CloudServices_Monitor_VisualStudioBlobExplorer.png)

2. Navegue até o caminho de BLOBs utilizando o padrão de **Nome da função/DeploymentId/RoleInstance** para localizar a configuração para o seu instância de função. 

    ![Explorador de armazenamento do Visual Studio](./media/cloud-services-how-to-monitor/CloudServices_Monitor_VisualStudioStorage.png)
3. Transferir o ficheiro de configuração para a instância de função e atualize-o para incluir qualquer contadores de desempenho personalizado. Por exemplo monitorizar a *Bytes/seg escrita disco* na *unidade C* , adicione o seguinte em **PerformanceCounters\Subscriptions** nó

    ```xml
    <PerformanceCounterConfiguration>
    <CounterSpecifier>\LogicalDisk(C:)\Disk Write Bytes/sec</CounterSpecifier>
    <SampleRateInSeconds>180</SampleRateInSeconds>
    </PerformanceCounterConfiguration>
    ```
4. Guardar as alterações e carregue o ficheiro de configuração novamente para a mesma localização substituir o ficheiro existente no blob.
5. Alternar para modo verboso na configuração portal clássica do Azure. Se fosse no modo verboso já tiver de alternar para mínimo e voltar a verboso.
6. Contador de desempenho personalizado agora estarão disponível na caixa de diálogo **Adicionar métricas** . 

## <a name="how-to-customize-the-metrics-chart"></a>Como: personalizar o gráfico de métricas

1. Na tabela métricas, selecione métricas até 6 representar no gráfico métricas. Para selecionar uma métrica, clique na caixa de verificação o lado esquerdo. Para remover uma métrica do gráfico métricas, desmarque a caixa de verificação na tabela métricas.

    À medida que seleciona métricas na tabela métricas, as métricas são adicionadas ao gráfico métricas. Num ecrã estreito, uma lista pendente de **n mais** contém os cabeçalhos métricos que não se ajustam a apresentação.

 
2. Para alternar entre apresentar valores relativas (valor final apenas para cada métrica) e os valores de absoluto (eixo do Y apresentado), selecione relativas ou absoluto na parte superior do gráfico.

    ![Referências absolutas ou relativas](./media/cloud-services-how-to-monitor/CloudServices_Monitor_RelativeAbsolute.png)

3. Para alterar o intervalo de tempo a gráfico apresenta métricas, selecione 1 hora, 24 horas ou dias 7 na parte superior do gráfico.

    ![Período de visualização do monitor](./media/cloud-services-how-to-monitor/CloudServices_Monitor_DisplayPeriod.png)

    No gráfico de métricas de dashboard, o método para desenho de métricas é diferente. Um conjunto de métricas padrão está disponível e métricas são adicionadas ou removidas ao selecionar o cabeçalho métrico.

### <a name="to-customize-the-metrics-chart-on-the-dashboard"></a>Para personalizar o gráfico de métricas no dashboard

1. Abra o dashboard para o serviço de nuvem.

2. Adicionar ou remover métricas do gráfico:

    - Para desenhar uma nova métrica, selecione a caixa de verificação para a métrica nos cabeçalhos de gráfico. Num ecrã estreito, clique na seta para baixo por ** *n*??metrics** para desenhar uma métrica que na área de cabeçalho de gráfico não é possível apresentar.

    - Para eliminar uma métrica que está desenhada no gráfico, limpe a caixa de verificação ao seu cabeçalho.

3. Alternar entre **referências relativas** e **absolutas** apresenta.

4. Selecione 1 hora, 24 horas ou dias 7 de dados a apresentar.

## <a name="how-to-access-verbose-monitoring-data-outside-the-azure-classic-portal"></a>Como: acesso verboso monitorizar os dados fora do Azure portal clássico

Dados de monitorização verbosas são armazenados em tabelas na contas de armazenamento que especificar para cada função. Para cada implementação de serviço de nuvem, tabelas de seis são criadas para a função. Duas tabelas são criadas para cada (5 minutos, 1 hora e 12 horas). Uma das seguintes tabelas armazena agregações de nível da função; outra tabela armazena agregações de instâncias de uma função. 

Os nomes das tabelas têm o seguinte formato:

```
WAD*deploymentID*PT*aggregation_interval*[R|RI]Table
```

em que:

- *deploymentID* é o GUID atribuído a implementação de serviço de nuvem

- *aggregation_interval* = 5 M, 1H ou 12 H

- função nível agregações = R

- agregações de instâncias de uma função = RI

Por exemplo, as tabelas seguintes seriam armazenar dados de monitorização verbosas agregados em intervalos de 1 hora:

```
WAD8b7c4233802442b494d0cc9eb9d8dd9fPT1HRTable (hourly aggregations for the role)

WAD8b7c4233802442b494d0cc9eb9d8dd9fPT1HRITable (hourly aggregations for role instances)
```
