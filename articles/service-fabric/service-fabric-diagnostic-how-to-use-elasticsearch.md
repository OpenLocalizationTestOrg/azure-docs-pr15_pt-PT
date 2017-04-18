<properties
   pageTitle="Utilizar Elasticsearch como um arquivo de rastreio de aplicação de serviço ferro | Microsoft Azure"
   description="Descreve como as aplicações de serviço ferro podem utilizar Elasticsearch e Kibana para armazenar, índice e pesquisar através de rastreios de aplicação (registos)"
   services="service-fabric"
   documentationCenter=".net"
   authors="karolz-ms"
   manager="adegeo"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="08/09/2016"
   ms.author="karolz@microsoft.com"/>

# <a name="use-elasticsearch-as-a-service-fabric-application-trace-store"></a>Utilizar Elasticsearch como um rastreio de aplicação de serviço ferro armazenar
## <a name="introduction"></a>Introdução
Este artigo descreve como as aplicações do [Azure Service ferro](https://azure.microsoft.com/documentation/services/service-fabric/) podem utilizar **Elasticsearch** e **Kibana** para armazenamento de rastreio de aplicação, a indexação e pesquisa. [Elasticsearch](https://www.elastic.co/guide/index.html) é uma origem de abrir, distribuído e dimensionável em tempo real pesquisa e a análise de motor é bem adequado para esta tarefa. Pode ser instalado no Windows e Linux máquinas virtuais em execução no Microsoft Azure. Elasticsearch eficazmente pode processar rastreios *estruturadas* produzidos utilizando tecnologias, tais como **Eventos rastreio para Windows (ETW)**.

ETW é utilizado pelo serviço ferro runtime para informações de diagnóstico de origem (rastreios). É o método recomendado para aplicações de serviço ferro a origem as respetivas informações de diagnóstico, demasiado. Utilizando o mesmo mecanismo permite correlação entre rastreios fornecido runtime e fornecido pela aplicação e torna mais fácil de resolução de problemas. Modelos de projecto de serviço ferro no Visual Studio incluem uma API de registo (com base na classe .NET **Origemdoevento** ) emite rastreios ETW por predefinição. Para obter uma descrição geral de rastreio de aplicação de serviço ferro utilizando ETW, consulte o artigo [monitorização e diagnosticar serviços numa configuração de desenvolvimento do computador local](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md).

Para rastreios ser apresentada nos Elasticsearch, precisam de ser capturado nos nós de cluster de serviço ferro em tempo real (enquanto está a ser executado a aplicação) e enviadas para um ponto final Elasticsearch. Existem duas opções principais para captar o rastreio:

+ **Capturar rastreio no processo**  
A aplicação ou com mais precisão, processo de serviço, é responsável por envio de dados de diagnóstico para o arquivo de rastreio (Elasticsearch).

+ **Captura de rastreio de fora do processo**  
Um agente separado é capturar rastreios do processo de serviço ou processos e enviar-lhes para o arquivo de rastreio.

Abaixo, podemos descrevem como configurar Elasticsearch no Azure, discutir os profissionais e desvantagens para ambas as opções de captura e explicam como configurar um serviço de serviço ferro para enviar dados para Elasticsearch.


## <a name="set-up-elasticsearch-on-azure"></a>Configurar o Elasticsearch no Azure
A forma mais simples de configurar o serviço de Elasticsearch no Azure é através de [**modelos de Gestor de recursos do Azure**](../azure-resource-manager/resource-group-overview.md). Um [modelo de Gestor de recursos do Azure guia de introdução para Elasticsearch](https://github.com/Azure/azure-quickstart-templates/tree/master/elasticsearch) de abrangente está disponível a partir do repositório de modelos de guia de introdução do Azure. Este modelo utiliza contas de armazenamento em separado para unidades da escala (grupos de nós). -Lo também pode aprovisionar o cliente separado e nós de servidor com configurações diferentes e vários números de discos dados ligados.

Aqui, vamos utilizar outro modelo, chamado **ES MultiNode** a partir do [repositório de ferramentas de diagnóstico Azure](https://github.com/Azure/azure-diagnostics-tools). Este modelo é mais fácil de utilizar e que cria um cluster de Elasticsearch protegido por autenticação básica HTTP. Antes de continuar, transfira o repositório a partir de GitHub para o seu computador (ao clonar do repositório ou transferir um ficheiro zip). O modelo de ES MultiNode está localizado na pasta com o mesmo nome.

### <a name="prepare-a-machine-to-run-elasticsearch-installation-scripts"></a>Preparar uma máquina para executar scripts de instalação Elasticsearch
A forma mais fácil de utilizar o modelo de ES MultiNode é através de um script Azure PowerShell fornecido denominado `CreateElasticSearchCluster`. Para utilizar este script, tem de instalar o PowerShell módulos e uma ferramenta denominada **openssl**. O último é necessária para criar uma chave de SSH que pode ser utilizada para administrar o seu cluster Elasticsearch remotamente.

`CreateElasticSearchCluster`script foi concebido para fácil utilização com o modelo de ES MultiNode a partir de um computador Windows. É possível utilizar o modelo num computador que não sejam Windows, mas esse cenário está fora do âmbito deste artigo.

1. Se ainda não instalou-los já, instale o [**Azure PowerShell módulos**](http://aka.ms/webpi-azps). Quando lhe for pedido, clique em **Executar**, em seguida, **instalar**. É necessário o Azure PowerShell 1.3 ou mais recente.

2. A ferramenta de **openssl** é incluída na distribuição dos [**Git para Windows**](http://www.git-scm.com/downloads). Se ainda não o tiver o fez, instale agora [Git para Windows](http://www.git-scm.com/downloads) . (As opções de instalação predefinidas são OK).

3. Partindo do princípio que Git tenha sido instalado, mas não incluído no caminho do sistema, abra uma janela do Microsoft Azure PowerShell e execute os seguintes comandos:

    ```powershell
    $ENV:PATH += ";<Git installation folder>\usr\bin"
    $ENV:OPENSSL_CONF = "<Git installation folder>\usr\ssl\openssl.cnf"
    ```

    Substituir o `<Git installation folder>` com a localização de Git no seu computador; a predefinição é **"c:\Programas\Microsoft Files\Git"**. Tenha em atenção o caráter de ponto e vírgula no início do caminho da primeira.

4. Certifique-se que iniciou sessão Azure (através do [`Add-AzureRmAccount`](https://msdn.microsoft.com/library/mt619267.aspx) cmdlet) e que selecionou a subscrição que deve ser utilizada para criar o seu cluster de pesquisa flexível. Pode verificar que a subscrição correta está selecionada utilizando `Get-AzureRmContext` e `Get-AzureRmSubscription` cmdlets.

5. Se ainda não o tiver feito, altere o diretório atual para a pasta ES MultiNode.

### <a name="run-the-createelasticsearchcluster-script"></a>Executar o script CreateElasticSearchCluster
Antes de executar o script, abra o `azuredeploy-parameters.json` de ficheiros e verifique ou fornecer valores para os parâmetros de script. Os seguintes parâmetros são fornecidos:

|Nome do parâmetro           |Descrição|
|-----------------------  |--------------------------|
|dnsNameForLoadBalancerIP |O nome que é utilizado para criar o nome DNS publicamente visível para a pesquisa flexível cluster (por anexar o domínio de região Azure para o nome fornecido). Por exemplo, se este valor do parâmetro é "myBigCluster" e a região Azure que selecionou é ocidental dos EUA, o nome resultante de DNS para o cluster é myBigCluster.westus.cloudapp.azure.com. <br /><br />Este nome também serve um nome de raiz para muitos erros associados à cluster pesquisa flexível, tais como nomes de nó de dados.|
|adminUsername           |O nome da conta de administrador de gestão do cluster de pesquisa flexível (chaves SSH correspondentes são geradas automaticamente).|
|dataNodeCount           |O número de nós do cluster de pesquisa flexível. A versão atual do script não faz distinção entre nós de dados e consulta; todos os nós reproduzir ambas as funções. A predefinição é 3 nós.|
|dataDiskSize            |O tamanho dos dados discos (GB) que está atribuído para cada nó de dados. Cada nó recebe 4 discos de dados, exclusivamente dedicados ao serviço de pesquisa flexível.|
|região                  |O nome do Azure região onde o cluster flexível pesquisa deve ser localizado.|
|esUserName              |O nome de utilizador do utilizador que esteja configurado para ter acesso ES cluster (sujeito a autenticação básica de HTTP). A palavra-passe não faz parte do ficheiro de parâmetros e tem de ser fornecida quando `CreateElasticSearchCluster` script é chamado.|
|vmSizeDataNodes         |O tamanho de máquina virtual Azure para nós de cluster de pesquisa flexível. A predefinição é Standard_D2.|

Agora está pronto para executar o script. Emita o seguinte comando:

```powershell
CreateElasticSearchCluster -ResourceGroupName <es-group-name> -Region <azure-region> -EsPassword <es-password>
```

onde 

|Nome do parâmetro script    |Descrição|
|-----------------------  |--------------------------|
|`<es-group-name>`        |O nome do grupo de recursos Azure que conterá todos os recursos de cluster de pesquisa flexível.|
|`<azure-region>`         |O nome da região Azure onde o cluster de pesquisa flexível deve ser criado.|         
|`<es-password>`          |A palavra-passe para o utilizador pesquisa flexível.|

>[AZURE.NOTE] Se obtiver um NullReferenceException a partir do cmdlet AzureResourceGroup de teste, tenha esquecido iniciar sessão no Azure (`Add-AzureRmAccount`).

Se obtiver um erro de executar o script e determinar que o erro foi causado por um valor de parâmetro de modelo mal, corrigir o ficheiro de parâmetro e executar o script novamente com um nome de grupo de recursos diferentes. Também pode reutilizar o mesmo nome de grupo de recursos e obter o script limpar aquele antigo adicionando o `-RemoveExistingResourceGroup` parâmetro para a invocação de script.

### <a name="result-of-running-the-createelasticsearchcluster-script"></a>Resultado de executar o script CreateElasticSearchCluster
Depois de executar o `CreateElasticSearchCluster` script, seguintes erros principais serão criados. Neste exemplo estamos partem do princípio de que utilizou "myBigCluster" como o valor da `dnsNameForLoadBalancerIP` parâmetro e de que a região onde criou cluster EUA Ocidental.

|Artefacto|Nome, localização e comentários|
|----------------------------------|----------------------------------|
|Tecla SSH para administração remota |ficheiro de myBigCluster.key (no diretório a partir do qual foi executado o CreateElasticSearchCluster). <br /><br />Este ficheiro chave pode ser utilizado para ligar para o nó de administração e (através do nó de administração) para nós de dados no cluster.|
|Nó de administrador                        |myBigCluster admin.westus.cloudapp.azure.com <br /><br />Uma VM dedicada para a administração de cluster remota Elasticsearch – a única pessoa que lhe permite ligações SSH externas. É executado na mesma rede virtual como todos os nós de cluster de Elasticsearch, mas não são executadas quaisquer serviços Elasticsearch.|
|Nós de dados                        |myBigCluster1... myBigCluster*N* <br /><br />Nós de dados que estiver a executar o Elasticsearch e Kibana serviços. Pode ligar através do SSH para cada nó, mas apenas através do nó do administrador.|
|Elasticsearch cluster             |http://myBigCluster.westus.cloudapp.Azure.com/es/ <br /><br />O ponto final principal para o cluster Elasticsearch (tenha em atenção o sufixo de /es). Está protegido por autenticação básica de HTTP (as credenciais foram os parâmetros especificados de esUserName/esPassword do modelo ES MultiNode). Cluster também tem o chefe Plug-in instalado (http://myBigCluster.westus.cloudapp.azure.com/es/_plugin/head) para administração de cluster básica.|
|Serviço de Kibana                    |http://myBigCluster.westus.cloudapp.Azure.com <br /><br />O serviço de Kibana está configurado para mostrar dados de cluster Elasticsearch criado. Está protegido pelas mesmas credenciais de autenticação que o próprio cluster.|

## <a name="in-process-versus-out-of-process-trace-capturing"></a>No processo versus captar o rastreio de fora do processo
Na introdução mencionado duas formas fundamentais de recolha de dados de diagnóstico: no processo e fora do processo. Cada tem vantagens e fraquezas.

Vantagens do **captar o rastreio no processo** incluem:

1. *Fácil configuração e implementação*

    * A configuração de recolha de dados de diagnóstico é apenas para parte de configuração da aplicação. É fácil sempre mantê-lo "Sincronizar" com o resto da aplicação.

    * Configuração por aplicação ou por serviço é alcançáveis facilmente.

    * Normalmente captar o rastreio de fora do processo requer uma configuração do agente de diagnóstico, que é uma tarefa administrativa extra e uma origem de potencial dos erros e implementação em separado. A tecnologia de agente específica com frequência permite apenas uma instância do agente por máquina virtual (nó). Isto significa que a configuração para a coleção da configuração do diagnóstico é partilhada entre todas as aplicações e serviços em execução nesse nó.

2. *Flexibilidade*

    * A aplicação pode enviar os dados onde precisa de aceder, desde que existe uma biblioteca de cliente que suporta o sistema de armazenamento de dados de destino. Novos sumidouros podem ser adicionados como pretendido.

    * Regras complexas captura, filtragem e dados agregação podem ser implementadas.

    * Capturar um rastreio de fora do processo com frequência é limitado pelo sumidouros dados que o agente de suporte. Alguns agentes são expansíveis.

3. *Acesso a dados de interno da aplicação e contexto*

    * O subsystem diagnóstico a ser executado dentro o processo de serviço de aplicação/facilmente pode aumentar rastreios com informações contextuais.

    * Na abordagem de fora do processo, os dados devem ser enviados para um agente através de alguns mecanismos de comunicação entre processos, tais como o rastreio de evento para o Windows. Este mecanismo poderia impor limitações adicionais.

Vantagens de **captar o rastreio de fora do processo** incluem:

1. *Copia a possibilidade de controlar a aplicação e recolher falha de sistema*

    * Capturar rastreio no processo poderá não ter êxito se a aplicação não inicia ou falha. Um agente independente tem uma mais hipóteses de capturar informações de resolução de problemas cruciais.<br /><br />

2. *Vencimento, robustez e o desempenho provas dadas*

    * Um agente desenvolvido por um fornecedor de plataforma (por exemplo, um agente de diagnósticos do Microsoft Azure) foi sujeito estritas testes e batalha protecção.

    * Com o rastreio no processo capturar, deve ser cuidado para se certificar de que a atividade de envio de diagnóstico dados a partir de um processo de aplicação não interfira com tarefas principal da aplicação ou apresentar problemas de desempenho ou temporização. Um agente de forma independente em execução é menos sujeito a estas questões e especificamente foi concebido para limitar o impacto no sistema.

É possível combinar e beneficiem das vantagens das duas abordagens. Na verdade, poderá ser a melhor solução para muitas aplicações.

Aqui, utilizamos a **biblioteca de Microsoft.Diagnostic.Listeners** e o rastreio no processo capturar para enviar dados a partir de uma aplicação de serviço ferro a um cluster de Elasticsearch.

## <a name="use-the-listeners-library-to-send-diagnostic-data-to-elasticsearch"></a>Utilizar a biblioteca de Listeners para enviar dados de diagnóstico para Elasticsearch
A biblioteca Microsoft.Diagnostic.Listeners faz parte da aplicação de serviço ferro PartyCluster exemplo. Para utilizá-la:

1. Transfira [a amostra de PartyCluster](https://github.com/Azure-Samples/service-fabric-dotnet-management-party-cluster) da GitHub.

2. Copie os projectos Microsoft.Diagnostics.Listeners e Microsoft.Diagnostics.Listeners.Fabric (pastas inteiros) do diretório de exemplo PartyCluster para a pasta de solução da aplicação que é suposto para enviar os dados para Elasticsearch.

3. Abrir a solução de destino, com o botão direito no nó solução no Explorador de solução e selecione **Adicionar projeto existente**. Adicione o projecto Microsoft.Diagnostics.Listeners para a solução. Repita o mesmo para o projeto Microsoft.Diagnostics.Listeners.Fabric.

4. Adicione uma referência de projeto a partir do seu serviço de projectos para os dois projectos adicionados. (De cada serviço que é suposto para enviar dados para Elasticsearch deve referência Microsoft.Diagnostics.EventListeners e Microsoft.Diagnostics.EventListeners.Fabric).

    ![Referências de projecto para bibliotecas Microsoft.Diagnostics.EventListeners e Microsoft.Diagnostics.EventListeners.Fabric][1]

### <a name="service-fabric-general-availability-release-and-microsoftdiagnosticstracing-nuget-package"></a>Lançamento de disponibilidade geral do serviço ferro igual e pacote de Microsoft.Diagnostics.Tracing Nuget
Aplicações criadas com o lançamento de disponibilidade de serviço ferro geral (2.0.135, disponibilizada 31 de Março de 2016) **.NET Framework 4.5.2**de destino. Esta versão é a versão mais recente do .NET Framework suportados pelo Azure no momento da versão das versões DG. Infelizmente, esta versão do quadro não possui determinadas APIs EventListener que necessita de biblioteca de Microsoft.Diagnostics.Listeners. Porque Origemdoevento (o componente de que a base de início de sessão APIs nas aplicações de ferro de formulários) e EventListener são intimamente à forma, cada projeto que utiliza a biblioteca de Microsoft.Diagnostics.Listeners tem de utilizar uma implementação alternativa dos Origemdoevento. Esta implementação é fornecida pelo **Microsoft.Diagnostics.Tracing Nuget pacote**, criado pela Microsoft. O pacote está totalmente compatível com versões anteriores com Origemdoevento incluído no quadro, por isso sem alterações de código devem ser necessárias diferente de alterações de espaço de nomes referenciado.

Para começar a utilizar a aplicação de Microsoft.Diagnostics.Tracing a classe de Origemdoevento de, siga estes passos para cada projeto de serviço que necessita para enviar dados para Elasticsearch:

1. Com o botão direito no projeto serviço e selecione **Gerir pacotes de Nuget**.

2. Mudar para a origem de pacote nuget.org (se não estiver já selecionada) e pesquise por "**Microsoft.Diagnostics.Tracing**".

3. Instalar o `Microsoft.Diagnostics.Tracing.EventSource` pacote (e respectivas dependências).

4. Abra o ficheiro **ServiceEventSource.cs** ou **ActorEventSource.cs** no seu projeto do serviço e substituir o `using System.Diagnostics.Tracing` directiva na parte superior do ficheiro com o `using Microsoft.Diagnostics.Tracing` directiva.

Estes passos não será necessários assim que o **.NET Framework 4.6** é suportado pelo Microsoft Azure.

### <a name="elasticsearch-listener-instantiation-and-configuration"></a>Instâncias de escuta Elasticsearch e de configuração
O passo final para enviar dados de diagnóstico para Elasticsearch é criar uma instância do `ElasticSearchListener` e configurá-lo com dados de ligação Elasticsearch. A escuta captura automaticamente todos os eventos elevados através de classes Origemdoevento definidas no projeto serviço. Necessita de ser vivo durante a vida útil do serviço, pelo que é a melhor forma de criá-lo no código de inicialização do serviço. Eis o aspeto que o código de inicialização para um serviço sem estado poderia após as alterações necessárias (adições indicada nos comentários começando `****`):

```csharp
using System;
using System.Diagnostics;
using System.Fabric;
using System.Threading;
using System.Threading.Tasks;
using Microsoft.ServiceFabric.Services.Runtime;

// **** Add the following directives
using Microsoft.Diagnostics.EventListeners;
using Microsoft.Diagnostics.EventListeners.Fabric;

namespace Stateless1
{
    internal static class Program
    {
        /// <summary>
        /// This is the entry point of the service host process.
        /// </summary>        
        private static void Main()
        {
            try
            {
                // **** Instantiate ElasticSearchListener
                var configProvider = new FabricConfigurationProvider("ElasticSearchEventListener");
                ElasticSearchListener esListener = null;
                if (configProvider.HasConfiguration)
                {
                    esListener = new ElasticSearchListener(configProvider);
                }

                // The ServiceManifest.XML file defines one or more service type names.
                // Registering a service maps a service type name to a .NET type.
                // When Service Fabric creates an instance of this service type,
                // an instance of the class is created in this host process.

                ServiceRuntime.RegisterServiceAsync("Stateless1Type", 
                    context => new Stateless1(context)).GetAwaiter().GetResult();

                ServiceEventSource.Current.ServiceTypeRegistered(Process.GetCurrentProcess().Id, typeof(Stateless1).Name);

                // Prevents this host process from terminating so services keep running.
                Thread.Sleep(Timeout.Infinite);

                // **** Ensure that the ElasticSearchListner instance is not garbage-collected prematurely
                GC.KeepAlive(esListener);
            }
            catch (Exception e)
            {
                ServiceEventSource.Current.ServiceHostInitializationFailed(e);
                throw;
            }
        }
    }
}
```

Dados de ligação Elasticsearch devem ser colocados numa secção separada no ficheiro de configuração de serviço (**PackageRoot\Config\Settings.xml**). O nome da secção deve corresponder para o valor passado para o `FabricConfigurationProvider` construtor, por exemplo:

```xml
<Section Name="ElasticSearchEventListener">
  <Parameter Name="serviceUri" Value="http://myBigCluster.westus.cloudapp.azure.com/es/" />
  <Parameter Name="userName" Value="(ES user name)" />
  <Parameter Name="password" Value="(ES user password)" />
  <Parameter Name="indexNamePrefix" Value="myapp" />
</Section>
```
Os valores de `serviceUri`, `userName` e `password` parâmetros correspondem ao endereço de ponto final de cluster Elasticsearch, Elasticsearch nome de utilizador e palavra-passe, respetivamente. `indexNamePrefix`é o prefixo para Elasticsearch índices; a biblioteca de Microsoft.Diagnostics.Listeners cria um novo índice para os respetivos dados diariamente.

### <a name="verification"></a>Verificação
Já está! Agora, sempre que o serviço é executado, desde o início enviar rastreios para o serviço de Elasticsearch especificado na configuração. Pode verificar isto abrindo a IU Kibana associada a instância de Elasticsearch de destino. No nosso exemplo, o endereço da página é http://myBigCluster.westus.cloudapp.azure.com/. Verificação índices com o prefixo do nome escolhido para o `ElasticSearchListener` instância facto foram criadas e povoada com os dados.

![Eventos da aplicação mostrando Kibana PartyCluster][2]

## <a name="next-steps"></a>Próximos passos
- [Saiba mais sobre diagnosticar e monitorização de um serviço de ferro de serviço](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

<!--Image references-->
[1]: ./media/service-fabric-diagnostics-how-to-use-elasticsearch/listener-lib-references.png
[2]: ./media/service-fabric-diagnostics-how-to-use-elasticsearch/kibana.png
