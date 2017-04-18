<properties
   pageTitle="Estado de funcionamento de monitorização no serviço ferro | Microsoft Azure"
   description="Uma introdução para o estado de funcionamento do Azure Service ferro monitorização de modelo, que fornece o cluster e as aplicações e serviços de monitorização."
   services="service-fabric"
   documentationCenter=".net"
   authors="oanapl"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/28/2016"
   ms.author="oanapl"/>

# <a name="introduction-to-service-fabric-health-monitoring"></a>Introdução a monitorização de estado de funcionamento do serviço ferro
Azure Service ferro apresenta um modelo de estado de funcionamento que fornece o estado de funcionamento avançada, flexível e extensible avaliação e relatórios. O modelo permite perto em tempo real de monitorização do Estado de cluster e os serviços em execução no mesmo. Pode obter informações de estado de funcionamento e corrigir facilmente potenciais problemas antes de poderem em cascata e causam falhas grandes. No modelo normal, serviços enviar relatórios com base nas suas vistas locais e que informações são agregadas para fornecer uma global cluster nível vista.

Componentes de serviço ferro utilizam este modelo de estado de funcionamento formatado para comunicar o seu estado atual. Pode utilizar o mesmo mecanismo para o estado de funcionamento do relatório das suas aplicações. Se investir no relatório de estado de funcionamento de alta qualidade que captura as condições de personalizado, pode detetar e corrigir problemas para a sua aplicação em execução muito mais facilmente.

> [AZURE.NOTE] Vamos começar subsystem o estado de funcionamento para endereçar uma necessidade de monitorizada atualiza os. Serviço ferro fornece monitorizadas actualizações aplicação e cluster garantir a disponibilidade completa, sem tempo de inatividade e mínima para sem intervenção do utilizador. Para obter estes objetivos, a atualização verifica estado de funcionamento com base em configurado políticas de atualização e permite que uma atualização continuar a apenas quando o estado de funcionamento do respeita limiares pretendidos. Caso contrário, a atualização é automaticamente revertida ou em pausa para dar a oportunidade de corrigir os problemas de administradores. Para saber mais sobre actualizações de aplicação, consulte [Este artigo](service-fabric-application-upgrade.md).

## <a name="health-store"></a>Arquivo de estado de funcionamento
O arquivo de estado de funcionamento mantém as informações relacionadas com o estado de funcionamento do entidades no cluster de obtenção fácil e de avaliação. É implementada como um ferro serviço persistentes com estado serviço para se certificar de elevada disponibilidade e escalabilidade. Arquivo de estado de funcionamento faz parte do **ferro: / sistema** aplicação e está disponível quando o cluster estiver para cima e em execução.

## <a name="health-entities-and-hierarchy"></a>Entidades de estado de funcionamento e hierarquia
As entidades de estado de funcionamento estão organizadas numa hierarquia lógica que captura interações e dependências entre entidades diferentes. As entidades e hierarquia automaticamente foram criadas pelo arquivo de estado de funcionamento com base nos relatórios recebidos a partir de componentes de serviço ferro.

As entidades de estado de funcionamento aproximadamente as entidades de serviço ferro. (Por exemplo, **entidade de aplicação do Estado de funcionamento** corresponde a uma instância da aplicação implementada num cluster, enquanto a **entidade de nó do Estado de funcionamento** corresponde a um nó de cluster de serviço ferro.) Interações das entidades de sistema para capturar a hierarquia de estado de funcionamento e é a base para a avaliação de estado de funcionamento avançadas. Pode obter informações sobre os conceitos de serviço ferro chave na [Descrição geral técnica do serviço ferro](service-fabric-technical-overview.md). Para saber mais sobre a aplicação, consulte o [modelo de aplicação de serviço ferro](service-fabric-application-model.md).

As entidades de estado de funcionamento e hierarquia permitem a cluster e as aplicações ser eficazmente comunicado, depurado e controlado. O modelo de estado de funcionamento fornece uma representação exata, *granular* do Estado de funcionamento das várias partes mover no cluster.

![Entidades de estado de funcionamento.][1]
As entidades de estado de funcionamento, organizados numa hierarquia com base em relações de elemento principal-subordinado.

[1]: ./media/service-fabric-health-introduction/servicefabric-health-hierarchy.png

As entidades de estado de funcionamento são:

- **Cluster**. Representa o estado de funcionamento de um cluster de serviço ferro. Relatórios de estado de funcionamento de cluster descrevem condições que afetam a totalidade do cluster e não podem ser limitadas a um ou mais filhos danificados. Alguns exemplos incluem o cérebro do cluster dividir devido a partições ou comunicação problemas de rede.

- **Nó**. Representa o estado de funcionamento de um nó ferro de serviço. Relatórios de estado de funcionamento de nó descrevam as condições que afetam a funcionalidade de nó. Afetam normalmente todas as entidades implementadas em execução no mesmo. Exemplos de quando um nó estiver fora do espaço de disco (ou outra propriedade toda a máquina, tal como memória, ligações) e quando um nó é premida. A entidade de nó é identificada pelo nome do nó (cadeia).

- **Aplicação**. Representa o estado de funcionamento de uma instância de aplicação a ser executada em cluster. Relatórios de estado de funcionamento da aplicação descrevam as condições que afetam o estado de funcionamento geral da aplicação. Não pode ser limitadas aos filhos individuais (serviços ou aplicações implementadas). Alguns exemplos incluem a interação fim-a-ponto entre os diferentes serviços na aplicação. A entidade de aplicação está identificada pelo nome da aplicação (URI).

- **Serviço**. Representa o estado de funcionamento de um serviço em execução no cluster. Relatórios de estado de funcionamento do serviço descrevam as condições que afetam o estado de funcionamento geral do serviço e não podem ser limitadas a uma partição ou uma réplica. Alguns exemplos incluem uma configuração do serviço (como porta ou partilha de ficheiros externos) que está a causar problemas de todas as partições. A entidade serviço está identificada pelo nome do serviço (URI).

- **Partição**. Representa o estado de funcionamento de uma partição de serviço. Relatórios de estado de funcionamento partição descrevem condições que afetam o conjunto de réplicas inteira. Exemplos de quando o número de réplicas for inferior a contagem de destino e quando é uma partição na perda de quórum. A entidade de partição é identificada pela partição ID (GUID).

- **Réplica**. Representa o estado de funcionamento de uma réplica com estado do serviço ou uma instância do Estado de serviço. Podem comunicar a unidade que fiscais e componentes do sistema para uma aplicação. Para os serviços com estado, alguns exemplos incluem uma réplica primária relato quando-não é possível criar uma réplica operações secundários e quando a replicação não é continuar a esperada na ritmo. Além disso, pode comunicar uma instância sem estado quando está a ficar sem recursos ou tem problemas de conectividade. A entidade de réplica é identificada pela partição ID (GUID) e o ID de réplica ou instância (longo).

- **DeployedApplication**. Representa o estado de funcionamento de uma *aplicação em execução num nó*. Relatórios de estado de funcionamento da aplicação implementada descrevam as condições específicas para a aplicação no nó que não pode ser limitado a pacotes de serviço implementados no mesmo nó. Exemplos de quando não é possível transferir o pacote de aplicação nesse nó e quando existe uma definição de problema para cima principais de segurança de aplicação no nó. A aplicação implementada é identificada por nome da aplicação (URI) e o nome do nó (cadeia).

- **DeployedServicePackage**. Representa o estado de funcionamento de um pacote de serviço execução num nó no cluster. Descreve as condições específicas para um pacote de serviço que não afetam outros pacotes de serviço no mesmo nó para a aplicação do mesmo. Alguns exemplos incluem um pacote de código no pacote de serviço que não pode ser iniciado e um pacote de configuração que não pode ser lidos. O pacote de serviço implementado é identificado ao nome da aplicação (URI), o nome do nó (cadeia) e o nome manifesto do serviço (cadeia).

A granularidade do modelo do Estado de funcionamento de torna mais fácil detetar e corrigir problemas. Por exemplo, se não está a responder um serviço, é viável para comunicar que a instância da aplicação está danificada. Relatórios ao que nível não é ideal, no entanto, uma vez que o problema poderá não ser que afetam os todos os serviços dessa aplicação. O relatório deve ser aplicado ao serviço danificado ou para uma partição de subordinados específicos, se obter mais informações apontar para essa partição. Os dados automaticamente superfícies através da hierarquia e uma partição danificada é tornado visíveis em níveis de serviços e da aplicação. Este agregação ajuda a identificar e resolver o problema de raiz do problema mais rapidamente.

Hierarquia de estado de funcionamento é composta por relações de elemento principal-subordinado. Um cluster é composto por nós e aplicações. Aplicações de ter os serviços e implementado aplicações. Aplicações implementadas tem implementado pacotes de serviço. Serviços de tem a partições e cada partição tem um ou mais réplicas. Existe uma relação entre nós e entidades implementadas especial. Se um nó está danificado à medida que comunicado pelo seu componente de sistema de certificação (service Manager activação pós-falha), afeta as aplicações, pacotes de serviço e implementados réplicas implementadas no mesmo.

Hierarquia de estado de funcionamento representa o estado mais recente do sistema com base em relatórios de estado de funcionamento mais recentes, que é informações quase em tempo real.
Podem comunicar fiscais internas e externas nas mesmas entidades com base no lógica específicos da aplicação ou condições monitorizadas personalizadas. Relatórios de utilizador coexistem com os relatórios do sistema.

Planear a investir em como um relatório e responder a Estado de funcionamento durante a estrutura de um serviço de nuvem grande, para facilitar depurar, monitorizar e trabalhar com o serviço.

## <a name="health-states"></a>Estados de funcionamento
Serviço ferro utiliza três Estados de estado de funcionamento para descrever se uma entidade está em bom estada ou não: OK, aviso e o erro. Qualquer relatório enviado para o arquivo de estado de funcionamento tem de especificar um dos seguintes Estados-membros. O resultado da avaliação de estado de funcionamento é um dos seguintes Estados-membros.

Os [Estados de funcionamento](https://msdn.microsoft.com/library/azure/system.fabric.health.healthstate) de possíveis são:

- **OK**. A entidade está em bom estada. Não existem problemas conhecidos comunicados em-lo ou secundários (quando aplicável).

- **Aviso**. A entidade experiências alguns problemas, mas ainda não está danificado (por exemplo, existem atrasos, mas não fazem com que quaisquer problemas funcionais ainda). Em alguns casos, poderá ser corrigir propriamente dito a condição de aviso sem qualquer intervenção especial e é útil para fornecer a visibilidade para o que é o problema?. Em outros casos, poderá prejudicar a condição de aviso um problema extremas sem intervenção do utilizador.

- **Erro**. A entidade está danificada. Ação deve ser redirecionada para corrigir o estado da entidade, uma vez que este não poderá funcionar corretamente.

- **Desconhecido**. Não existe a entidade na loja do Estado de funcionamento. Este resultado pode ser obtido a partir das consultas de distribuído resultados a partir de vários componentes de impressão em série. Por exemplo, obtenha consulta na lista nó vai para **FailoverManager** e **HealthManager**, obter aplicação consulta na lista acede à **ClusterManager** e **HealthManager**. Estas consultas intercalar resultados a partir de vários componentes do sistema. Se outro componente de sistema devolver uma entidade que não atingiu ou limpo a partir da loja do Estado de funcionamento, o resultado Unido tem desconhecido estado de funcionamento.

## <a name="health-policies"></a>Políticas de estado de funcionamento
Arquivo de estado de funcionamento aplica-se do Estado de funcionamento as políticas para determinar se uma entidade é Saudável com base no seus relatórios e sites secundários.

> [AZURE.NOTE] Políticas de estado de funcionamento podem ser especificadas no manifesto cluster (para avaliação de estado de funcionamento do cluster e nó) ou no manifesto da aplicação (para avaliação da aplicação e algum dos seus subordinados). Também podem passar pedidos de avaliação do Estado de funcionamento em políticas de avaliação do Estado de funcionamento personalizado, que é utilizado apenas para que a avaliação.

Por predefinição, o serviço ferro aplica regras estritamente (tudo o que tem de ser saudável) para a relação hierárquica de elemento principal-subordinado. Se o mesmo um dos subordinados tiver um evento danificado, é considerada danificado principal.

### <a name="cluster-health-policy"></a>Política de estado de funcionamento de cluster
A [política de estado de funcionamento de cluster](https://msdn.microsoft.com/library/azure/system.fabric.health.clusterhealthpolicy.aspx) é utilizada para avaliar o estado de funcionamento de cluster e o nó do Estado de funcionamento Estados-membros. A política pode ser definida no manifesto cluster. Se não estiver presente, é utilizada a política predefinida (igual a zero falhas toleradas).
A política de estado de funcionamento de cluster contém:

- [ConsiderWarningAsError](https://msdn.microsoft.com/library/azure/system.fabric.health.clusterhealthpolicy.considerwarningaserror.aspx). Especifica se tratar de estado de funcionamento do aviso relatórios como erros durante a avaliação de estado de funcionamento. Predefinido: FALSO.

- [MaxPercentUnhealthyApplications](https://msdn.microsoft.com/library/azure/system.fabric.health.clusterhealthpolicy.maxpercentunhealthyapplications.aspx). Especifica a percentagem máxima tolerada de aplicações que pode ser danificado antes do cluster considera erro.

- [MaxPercentUnhealthyNodes](https://msdn.microsoft.com/library/azure/system.fabric.health.clusterhealthpolicy.maxpercentunhealthynodes.aspx). Especifica a percentagem máxima tolerada de nós que podem ser danificado antes do cluster considera erro. Em grandes clusters, alguns nós são sempre para baixo ou para fora para reparações, para que esta percentagem deverá estar configurada para tolerar que.

- [ApplicationTypeHealthPolicyMap](https://msdn.microsoft.com/library/azure/system.fabric.health.clusterhealthpolicy.applicationtypehealthpolicymap.aspx). O mapa de política de estado de funcionamento de tipo de aplicação pode ser utilizado durante a avaliação de estado de funcionamento de cluster para descrever tipos de aplicação especial. Por predefinição, todas as aplicações estão colocar num conjunto de dados e avaliadas com MaxPercentUnhealthyApplications. Se alguns tipos de aplicação devem ser tratados de forma diferente, pode tomar partido do conjunto de global. Em vez disso, são avaliadas contra as percentagens associadas com o nome do tipo de aplicação no mapa. Por exemplo, num cluster existem milhares de aplicações de diferentes tipos e algumas instâncias da aplicação de controlo de um tipo de aplicação especial. As aplicações de controlo devem estar sempre preenchidos erro. Pode especificar MaxPercentUnhealthyApplications global para 20% tolerar algumas falhas, mas para o tipo de aplicação "ControlApplicationType" definir a MaxPercentUnhealthyApplications como 0. Desta forma, se alguns dos muitas aplicações estão danificados, mas abaixo a percentagem de danificado global, cluster seria avaliado para aviso. Um Estado de funcionamento de aviso não causam impacto na atualização de cluster ou outros monitorização acionado ao estado de funcionamento do erro. Mas a aplicação de controlo até mesmo um erro tornaria cluster danificado, que accionadores recuperar ou interrompe a atualização do cluster, consoante a configuração de atualização.
Para os tipos de aplicação definidos no mapa, todas as instâncias da aplicação sejam tomadas fora do conjunto de aplicações do global. São avaliadas com base no número total de aplicações do tipo de aplicação, utilizando o MaxPercentUnhealthyApplications específico a partir do mapa. Tudo o resto das aplicações do permanecem no conjunto global e são avaliados com MaxPercentUnhealthyApplications.

O exemplo seguinte é um excerto do manifesto cluster. Para definir o entradas no mapa do tipo de aplicação, prefixo o nome do parâmetro com "ApplicationTypeMaxPercentUnhealthyApplications-", seguido do nome do tipo de aplicação.

```xml
<FabricSettings>
  <Section Name="HealthManager/ClusterHealthPolicy">
    <Parameter Name="ConsiderWarningAsError" Value="False" />
    <Parameter Name="MaxPercentUnhealthyApplications" Value="20" />
    <Parameter Name="MaxPercentUnhealthyNodes" Value="20" />
    <Parameter Name="ApplicationTypeMaxPercentUnhealthyApplications-ControlApplicationType" Value="0" />
  </Section>
</FabricSettings>
```

### <a name="application-health-policy"></a>Política de estado de funcionamento da aplicação
A [política de estado de funcionamento da aplicação](https://msdn.microsoft.com/library/azure/system.fabric.health.applicationhealthpolicy.aspx) descreve como da avaliação de eventos e de agregação de Estados subordinado é feita para aplicações e respectivos subordinados. Pode ser definida no manifesto da aplicação, **ApplicationManifest.xml**, no pacote de aplicação. Se forem especificadas sem políticas, serviço ferro parte do princípio de que a entidade está danificada, se tiver um relatório de estado de funcionamento ou um elemento subordinado no estado de funcionamento da aviso ou um erro.
As políticas que foram configuráveis são:

- [ConsiderWarningAsError](https://msdn.microsoft.com/library/azure/system.fabric.health.applicationhealthpolicy.considerwarningaserror.aspx). Especifica se tratar de estado de funcionamento do aviso relatórios como erros durante a avaliação de estado de funcionamento. Predefinido: FALSO.

- [MaxPercentUnhealthyDeployedApplications](https://msdn.microsoft.com/library/azure/system.fabric.health.applicationhealthpolicy.maxpercentunhealthydeployedapplications.aspx). Especifica a percentagem máxima tolerada de implementado aplicações que pode ser danificado antes da aplicação que é considerada erro. Esta percentagem é calculada dividindo o número de aplicações implementados danificadas sobre o número de nós que as aplicações atualmente são implementadas no cluster. O cálculo Arredonda por excesso para tolerar uma falha em pequenos números de nós. Percentagem de predefinido: zero.

- [DefaultServiceTypeHealthPolicy](https://msdn.microsoft.com/library/azure/system.fabric.health.applicationhealthpolicy.defaultservicetypehealthpolicy.aspx). Especifica a serviço tipo do Estado de funcionamento política predefinida, que substitui a política de estado de funcionamento predefinida para todos os tipos de serviço na aplicação.

- [ServiceTypeHealthPolicyMap](https://msdn.microsoft.com/library/azure/system.fabric.health.applicationhealthpolicy.servicetypehealthpolicymap.aspx). Fornece um mapa de políticas de estado de funcionamento do serviço por tipo de serviço. Estas políticas substituem as políticas de estado de funcionamento do tipo de serviço predefinido para cada tipo de serviço especificado. Por exemplo, se tiver uma aplicação de um tipo de serviço de gateway sem estado e um tipo de serviço do motor de estado, pode de configurar as políticas de estado de funcionamento para a sua avaliação forma diferente. Quando especificar política por tipo de serviço, pode obter um controlo mais granular do Estado de funcionamento do serviço.

### <a name="service-type-health-policy"></a>Política de estado de funcionamento do serviço de tipo
A [política de estado de funcionamento do serviço de tipo](https://msdn.microsoft.com/library/azure/system.fabric.health.servicetypehealthpolicy.aspx) Especifica como avaliar e agregar os serviços e os elementos subordinados dos serviços. A política contém:

- [MaxPercentUnhealthyPartitionsPerService](https://msdn.microsoft.com/library/azure/system.fabric.health.servicetypehealthpolicy.maxpercentunhealthypartitionsperservice.aspx). Especifica a percentagem máxima tolerada das partições danificadas antes de um serviço considera danificado. Percentagem de predefinido: zero.

- [MaxPercentUnhealthyReplicasPerPartition](https://msdn.microsoft.com/library/azure/system.fabric.health.servicetypehealthpolicy.maxpercentunhealthyreplicasperpartition.aspx). Especifica a percentagem máxima tolerada de réplicas danificadas antes de uma partição considera danificada. Percentagem de predefinido: zero.

- [MaxPercentUnhealthyServices](https://msdn.microsoft.com/library/azure/system.fabric.health.servicetypehealthpolicy.maxpercentunhealthyservices.aspx). Especifica a percentagem máxima tolerada dos serviços danificados antes da aplicação é considerada danificada. Percentagem de predefinido: zero.

O exemplo seguinte é um excerto do manifesto de uma aplicação:

```xml
    <Policies>
        <HealthPolicy ConsiderWarningAsError="true" MaxPercentUnhealthyDeployedApplications="20">
            <DefaultServiceTypeHealthPolicy
                   MaxPercentUnhealthyServices="0"
                   MaxPercentUnhealthyPartitionsPerService="10"
                   MaxPercentUnhealthyReplicasPerPartition="0"/>
            <ServiceTypeHealthPolicy ServiceTypeName="FrontEndServiceType"
                   MaxPercentUnhealthyServices="0"
                   MaxPercentUnhealthyPartitionsPerService="20"
                   MaxPercentUnhealthyReplicasPerPartition="0"/>
            <ServiceTypeHealthPolicy ServiceTypeName="BackEndServiceType"
                   MaxPercentUnhealthyServices="20"
                   MaxPercentUnhealthyPartitionsPerService="0"
                   MaxPercentUnhealthyReplicasPerPartition="0">
            </ServiceTypeHealthPolicy>
        </HealthPolicy>
    </Policies>
```

## <a name="health-evaluation"></a>Avaliação de estado de funcionamento
Utilizadores e serviços automatizados podem avaliar o estado de funcionamento para qualquer entidade em qualquer altura. Para avaliar o estado de funcionamento de uma entidade, os agregados de arquivo de estado de funcionamento estado de funcionamento de todos os relatórios na entidade e avalia todos os seus subordinados (quando aplicável). O algoritmo de agregação de estado de funcionamento utiliza políticas de estado de funcionamento que especificar como avaliar relatórios de estado de funcionamento e como agregar Estados-membros subordinado do Estado de funcionamento (quando aplicável).

### <a name="health-report-aggregation"></a>Agregação de relatório de estado de funcionamento
Uma entidade pode ter vários relatórios de estado de funcionamento que foi enviados por sede diferente (componentes do sistema ou fiscais) nas propriedades diferentes. A agregação utiliza as políticas de estado de funcionamento associado em particular no membro ConsiderWarningAsError de aplicação ou cluster de política de estado de funcionamento. ConsiderWarningAsError Especifica como avaliar avisos.

O estado de funcionamento agregado é acionado pelos relatórios de estado de funcionamento *pior* na entidade. Se existir um relatório de estado de funcionamento de pelo menos um erro, o estado de funcionamento agregado é um erro.

![Agregação de relatório de estado de funcionamento com o relatório de erros.][2]

Uma entidade de estado de funcionamento que tem um ou mais relatórios de estado de funcionamento do erro é avaliada como um erro. O mesmo se VERDADEIRO para um relatório de estado de funcionamento expirado, independentemente do seu estado de funcionamento.

[2]: ./media/service-fabric-health-introduction/servicefabric-health-report-eval-error.png

Se existirem sem relatórios de erros e avisos de uma ou mais, o estado de funcionamento agregado é aviso ou erro, consoante o sinalizador de política ConsiderWarningAsError.

![Agregação de relatório de estado de funcionamento com o relatório de aviso e ConsiderWarningAsError FALSO.][3]

Agregação de relatório de estado de funcionamento com o relatório de aviso e ConsiderWarningAsError definido como FALSO (predefinição).

[3]: ./media/service-fabric-health-introduction/servicefabric-health-report-eval-warning.png

### <a name="child-health-aggregation"></a>Agregação de estado de funcionamento subordinado
O estado de funcionamento agregado de uma entidade reflete os Estados de estado de funcionamento subordinado (quando aplicável). O algoritmo para agregar subordinado do Estado de funcionamento Unidos utiliza as políticas de estado de funcionamento aplicáveis consoante o tipo de entidade.

![Agregação de estado de funcionamento de entidades subordinado.][4]

Agregação subordinado com base em políticas de estado de funcionamento.

[4]: ./media/service-fabric-health-introduction/servicefabric-health-hierarchy-eval.png

Depois do arquivo de estado de funcionamento tem avaliada todos os elementos subordinados, Tasks agrega os respetivos Estados de estado de funcionamento com base na percentagem máxima configurada das crianças danificadas. Esta percentagem é disponibilizada de política baseada no tipo de entidade e subordinado.

- Se todos os elementos subordinados têm Estados OK, o estado de funcionamento subordinado agregado é OK.

- Se tiverem crianças OK e Estados de aviso, o estado de funcionamento subordinado agregado é aviso.

- Se existirem filhos com Estados de erro que não cumpram o máximo permitido percentagem das crianças danificadas, o estado de funcionamento agregado é um erro.

- Se os filhos com erro Estados respeitam a percentagem máxima permitida das crianças danificadas, o estado de funcionamento agregado é aviso.

## <a name="health-reporting"></a>Relatórios de estado de funcionamento
Componentes do sistema, aplicações de sistema ferro e interna/externa fiscais podem comunicar contra entidades ferro de serviço. A sede certifique determinações *local* do Estado de funcionamento das entidades monitorizadas, com base em condições estiver a monitorizar. Não precisam de observar qualquer Estado global ou agregar dados. O comportamento pretendido é tenha sede simples e não complexos organismos que precisam de observar muitas coisas inferir que informações para enviar para.

Para enviar dados de estado de funcionamento para o arquivo de estado de funcionamento, um reporter tem de identificar a entidade afetada e criar um relatório de estado de funcionamento. O relatório, em seguida, pode ser enviado através da API utilizando [FabricClient.HealthClient.ReportHealth](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.healthclient_members.aspx), através do PowerShell ou resto.

### <a name="health-reports"></a>Relatórios de estado de funcionamento
Os [relatórios de estado de funcionamento](https://msdn.microsoft.com/library/azure/system.fabric.health.healthreport.aspx) de cada uma das entidades no cluster conter as seguintes informações:

- **SourceId**. Uma cadeia que identifica o autor do evento do Estado de funcionamento.

- **Identificador de entidade**. Identifica a entidade onde o relatório é aplicado. Difere na [entidade escreva](service-fabric-health-introduction.md#health-entities-and-hierarchy):

  - Cluster. Nenhum.

  - Nó. Nome do nó (cadeia).

  - Aplicação. Nome da aplicação (URI). Representa o nome da instância da aplicação implementado no cluster.

  - Serviço. Nome do serviço (URI). Representa o nome da instância serviço implementado no cluster.

  - Partição. ID da partição (GUID). Representa o identificador exclusivo partição.

  - Réplica. O ID de réplica do serviço de estado ou o ID da instância sem estado de serviço (INT64).

  - DeployedApplication. Nome da aplicação (URI) e o nome do nó (cadeia).

  - DeployedServicePackage. Nome da aplicação (URI), o nome do nó (cadeia) e o serviço de manifesto nome (cadeia).

- **Propriedade**. Uma *cadeia* (não uma enumeração fixa) que lhe permite autor categorizar o evento de estado de funcionamento de uma propriedade específica da entidade. Por exemplo, reporter A pode comunicar que o estado de funcionamento da propriedade Node01 "armazenamento" e reporter B pode comunicar o estado de funcionamento da propriedade Node01 "conectividade". Na loja do Estado de funcionamento, estes relatórios são tratados como eventos de estado de funcionamento separada para a entidade de Node01.

- **Descrição**. Uma cadeia que lhe permite uma reporter fornecer informações detalhadas sobre o evento de estado de funcionamento. **SourceId**, **propriedade**e **HealthState** totalmente deverão descrever o relatório. A descrição da adiciona legível informações sobre o relatório. O texto torna mais fácil para administradores e utilizadores compreender o relatório de estado de funcionamento.

- **HealthState**. Uma [enumeração](service-fabric-health-introduction.md#health-states) que descreva o estado de funcionamento do relatório. Os valores aceites são OK, o aviso e o erro.

- **TimeToLive**. Timespan que indica quanto tempo o relatório de estado de funcionamento do é válido. Associado à forma **RemoveWhenExpired**, permite o arquivo de estado de funcionamento saber como avaliar eventos expirados. Por predefinição, o valor é infinito e o relatório é indefinidamente válido.

- **RemoveWhenExpired**. Um valor booleano. Se definida como VERDADEIRO, o relatório de estado de funcionamento expirado é automaticamente removida o arquivo de estado de funcionamento e o relatório não ter um impacto avaliação de estado de funcionamento de entidade. Utilizado quando o relatório é válido para um determinado período de tempo apenas e autor não necessite de explicitamente desmarcá-la. Também é utilizado para eliminar relatórios a partir da loja do Estado de funcionamento (por exemplo, um watchdog é alterado e deixa de enviar relatórios com origem anterior e propriedade). Pode enviar um relatório com uma breve TimeToLive juntamente com RemoveWhenExpired para limpar o qualquer Estado a partir da loja do Estado de funcionamento anterior. Se o valor estiver definido como FALSO, o relatório expirado é tratado como um erro da avaliação de estado de funcionamento. O valor falso sinais para o arquivo de estado de funcionamento de que a origem deve periodicamente um relatório sobre esta propriedade. Se não tiver, em seguida, tem de ter um problema com o watchdog. Estado de funcionamento do watchdog é capturado ponderando o evento como um erro.

- **SequenceNumber**. Um número inteiro positivo que tem de estar crescente, que representa a ordem dos relatórios. É utilizado pelo arquivo de estado de funcionamento para detetar obsoletos relatórios que são recebidos atrasada devido a atrasos na rede ou outros problemas. Um relatório é rejeitado se o número de sequência for que menor ou igual a mais recentemente aplicado número para a mesma entidade, origem e propriedade. Se não for especificado, o número de sequência é gerado automaticamente. É necessário colocar o número de sequência apenas quando cria relatórios acerca transições de estado. Esta situação, a origem tem de se lembrar aos relatórios tiver enviado e manter as informações de recuperação activação pós-falha.

Estes quatro partes das informações – SourceId, identificador de entidade, propriedade e HealthState – são necessários para cada relatório de estado de funcionamento. SourceId cadeia não é permitida para começar com o prefixo "**do sistema.**", que é reservado para sistema relatórios. Para a mesma entidade, existe apenas um relatório para a mesma origem e a propriedade. Vários relatórios para a mesma origem e propriedade substituam entre si, seja no estado de funcionamento do lado do cliente (se são enviadas em batches) ou no estado de funcionamento armazenar lado. A substituição baseia-se os números de sequência; mais recentes relatórios (com números de sequência superiores) substituem relatórios mais antigos.

### <a name="health-events"></a>Eventos de estado de funcionamento
Internamente, o arquivo de estado de funcionamento mantém [eventos de estado de funcionamento](https://msdn.microsoft.com/library/azure/system.fabric.health.healthevent.aspx), que contêm todas as informações da relatórios e metadados adicionais. Os metadados incluem a hora que o relatório foi atribuído para o cliente de estado de funcionamento e a hora que foi modificado do lado do servidor. Os eventos de estado de funcionamento são devolvidos por [consultas de estado de funcionamento](service-fabric-view-entities-aggregated-health.md#health-queries).

Contém os metadados adicionados:

- **SourceUtcTimestamp**. A hora o relatório foi atribuído para o cliente de estado de funcionamento (a hora Universal Coordenada).

- **LastModifiedUtcTimestamp**. A hora em que o relatório da última modificação do lado do servidor (a hora Universal Coordenada).

- **IsExpired**. Um sinalizador para indicar se o relatório foi expirado quando a consulta tiver sido executada pelo arquivo de estado de funcionamento. Um evento pode ser expirou apenas se RemoveWhenExpired for falsa. Caso contrário, o evento não é devolvido pela consulta e é removido a partir da loja.

- **LastOkTransitionAt**, **LastWarningTransitionAt**, **LastErrorTransitionAt**. A hora da última para transições de aviso/OK/erro. Estes campos dar o histórico do Estado de funcionamento transições de estado para o evento.

Os campos de transição de estado podem ser utilizados para alertas de forma inteligentes ou informações de eventos do Estado de funcionamento "históricas". Que os mesmos ativam cenários tais como:

- Alerta quando uma propriedade tiver sido na/erro de aviso para mais de X minutos. Verificar a condição para um período de tempo evita alertas em condições temporárias. Por exemplo, pode ser convertido um alerta se o estado de funcionamento tenha sido aviso para mais de cinco minutos (HealthState = = aviso e agora - LastWarningTransitionTime > 5 minutos).

- Alerta apenas em condições que foram alterados na última X minutos. Se um relatório já estava no erro antes da hora especificada, pode ser ignorada porque já foi assinalado anteriormente.

- Determinar se uma propriedade está alternar entre o aviso e erro, quanto tempo tiver sido danificado (ou seja, não OK). Por exemplo, pode ser convertido um alerta se a propriedade não esteja há Saudável durante mais de cinco minutos (HealthState! = Ok e agora - LastOkTransitionTime > 5 minutos).

## <a name="example-report-and-evaluate-application-health"></a>Exemplo: Relatório e avaliar o estado de funcionamento da aplicação
O exemplo seguinte envia um relatório de estado de funcionamento através do PowerShell sobre a aplicação **ferro: / WordCount** da origem **MyWatchdog**. O relatório de estado de funcionamento contém informações sobre a propriedade de estado de funcionamento "disponibilidade" no estado de funcionamento de erro, com TimeToLive infinito. Em seguida, consulta para verificar o estado de funcionamento de aplicação, que devolve agregado erros de estado de estado de funcionamento e os eventos de estado de funcionamento denunciado na lista de eventos do Estado de funcionamento.

```powershell
PS C:\> Send-ServiceFabricApplicationHealthReport –ApplicationName fabric:/WordCount –SourceId "MyWatchdog" –HealthProperty "Availability" –HealthState Error

PS C:\> Get-ServiceFabricApplicationHealth fabric:/WordCount


ApplicationName                 : fabric:/WordCount
AggregatedHealthState           : Error
UnhealthyEvaluations            :
                                  Error event: SourceId='MyWatchdog', Property='Availability'.

ServiceHealthStates             :
                                  ServiceName           : fabric:/WordCount/WordCountService
                                  AggregatedHealthState : Error

                                  ServiceName           : fabric:/WordCount/WordCountWebService
                                  AggregatedHealthState : Ok

DeployedApplicationHealthStates :
                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_0
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_2
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_3
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_4
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_1
                                  AggregatedHealthState : Ok

HealthEvents                    :
                                  SourceId              : System.CM
                                  Property              : State
                                  HealthState           : Ok
                                  SequenceNumber        : 360
                                  SentAt                : 3/22/2016 7:56:53 PM
                                  ReceivedAt            : 3/22/2016 7:56:53 PM
                                  TTL                   : Infinite
                                  Description           : Application has been created.
                                  RemoveWhenExpired     : False
                                  IsExpired             : False
                                  Transitions           : Error->Ok = 3/22/2016 7:56:53 PM, LastWarning = 1/1/0001 12:00:00 AM

                                  SourceId              : MyWatchdog
                                  Property              : Availability
                                  HealthState           : Error
                                  SequenceNumber        : 131032204762818013
                                  SentAt                : 3/23/2016 3:27:56 PM
                                  ReceivedAt            : 3/23/2016 3:27:56 PM
                                  TTL                   : Infinite
                                  Description           :
                                  RemoveWhenExpired     : False
                                  IsExpired             : False
                                  Transitions           : Ok->Error = 3/23/2016 3:27:56 PM, LastWarning = 1/1/0001 12:00:00 AM
```

## <a name="health-model-usage"></a>Utilização de modelo de estado de funcionamento
O modelo de estado de funcionamento permite serviços em nuvem e a plataforma de serviço ferro subjacente para dimensionar, porque a monitorização e estado de funcionamento do determinações são distribuídas entre diferentes monitores dentro do cluster.
Outros sistemas de tem um serviço único centralizado ao nível do cluster que analisa todas as *potencialmente* informações úteis emitidas pelos serviços. Esta abordagem impede o respetiva escalabilidade. -Lo também não permite-los recolher informações muito específicas para ajudar a identificar problemas e potenciais problemas como perto a causa de raiz possível.

O modelo de estado de funcionamento é utilizado fortemente de monitorização e de diagnóstico, por avaliar o estado de funcionamento do cluster e de aplicações e para actualizações monitorizadas. Outros serviços utilizam dados de estado de funcionamento para executar reparações automáticas, criar histórico do Estado de funcionamento de cluster e emitir alertas em determinadas condições.

## <a name="next-steps"></a>Próximos passos
[Ver relatórios de estado de funcionamento do serviço ferro](service-fabric-view-entities-aggregated-health.md)

[Utilizar relatórios de estado de funcionamento do sistema para resolução de problemas](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)

[Como um relatório e verificar o estado de funcionamento do serviço](service-fabric-diagnostics-how-to-report-and-check-service-health.md)

[Adicionar relatórios de estado de funcionamento de serviço ferro personalizados](service-fabric-report-health.md)

[Monitorizar e diagnosticar serviços localmente](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[Atualização da aplicação de serviço ferro](service-fabric-application-upgrade.md)
