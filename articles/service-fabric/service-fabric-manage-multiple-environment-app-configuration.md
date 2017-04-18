<properties
   pageTitle="Gerir vários ambientes no serviço ferro | Microsoft Azure"
   description="Aplicações de serviço ferro podem ser executadas em clusters de que o intervalo de tamanho de um computador a milhares de máquinas. Em alguns casos, que irá que pretende configurar a aplicação de forma diferente para esses ambientes variadas. Este artigo aborda como definir parâmetros de aplicação diferente por ambiente."
   services="service-fabric"
   documentationCenter=".net"
   authors="seanmck"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="07/19/2016"
   ms.author="seanmck"/>

# <a name="manage-application-parameters-for-multiple-environments"></a>Gerir os parâmetros de aplicação para vários ambientes

Pode criar Azure Service ferro clusters utilizando qualquer parte de uma para muitos milhares de máquinas. Enquanto binários da aplicação podem executar sem modificação em várias este espectro e uma altura de ambientes, que irá com frequência pretende configurar a aplicação de forma diferente, consoante o número de máquinas que está a implementar.

Como um exemplo simples, considere `InstanceCount` para um serviço de estado. Quando estiver a utilizar aplicações no Azure, que irá geralmente pretende definir este parâmetro para o valor de "-1" especial. Este procedimento assegura que o serviço está a ser executado em todos os nós no cluster. No entanto, esta configuração não é adequada para um cluster de máquina único uma vez que não pode ter vários processos escutar o mesmo ponto final num único computador. Em vez disso, normalmente, irá configurar `InstanceCount` para "1".

## <a name="specifying-environment-specific-parameters"></a>Especificação de específicos do ambiente de parâmetros

A solução para este problema de configuração é um conjunto de serviços parametrizadas predefinidos e ficheiros de parâmetro de aplicação preencher esses valores de parâmetro para um determinado ambiente. Parâmetros de aplicações e serviços predefinidos são configurados em manifestos de aplicações e serviços. A definição de esquema para os ficheiros ServiceManifest.xml e ApplicationManifest.xml é instalada com o serviço ferro SDK e ferramentas para *C:\Program Files\Microsoft SDKs\Service Fabric\schemas\ServiceFabricServiceModel.xsd*.

### <a name="default-services"></a>Serviços predefinidos

Aplicações de serviço ferro são constituídas por uma coleção de instâncias do serviço. Enquanto é possível que pode criar uma aplicação vazia e, em seguida, criar dinamicamente todas as instâncias de serviço, a maioria das aplicações ter um conjunto de core services que deve ser criada quando a aplicação é criada uma instância sempre. Estas são referidas como "serviços predefinidos". Estes são especificadas no manifesto da aplicação, com marcadores de posição para a configuração de per ambiente incluído entre parênteses Retos:

    <DefaultServices>
        <Service Name="Stateful1">
            <StatefulService
                ServiceTypeName="Stateful1Type"
                TargetReplicaSetSize="[Stateful1_TargetReplicaSetSize]"
                MinReplicaSetSize="[Stateful1_MinReplicaSetSize]">

                <UniformInt64Partition
                    PartitionCount="[Stateful1_PartitionCount]"
                    LowKey="-9223372036854775808"
                    HighKey="9223372036854775807"
                />
        </StatefulService>
    </Service>
  </DefaultServices>

Cada um dos parâmetros com nome tem de ser definida dentro do elemento de parâmetros de manifesto da aplicação:

    <Parameters>
        <Parameter Name="Stateful1_MinReplicaSetSize" DefaultValue="2" />
        <Parameter Name="Stateful1_PartitionCount" DefaultValue="1" />
        <Parameter Name="Stateful1_TargetReplicaSetSize" DefaultValue="3" />
    </Parameters>

O atributo ValorPredefinido Especifica o valor para ser utilizado na ausência de um parâmetro mais específicas para um determinado ambiente.

>[AZURE.NOTE] Nem todos os parâmetros de instância do serviço são adequados para a configuração de per ambiente. No exemplo acima, os valores LowKey e HighKey para esquema de partições o serviço são definidos explicitamente para todas as instâncias do serviço de uma vez que o intervalo de partição é uma função do domínio de dados, não o ambiente.


### <a name="per-environment-service-configuration-settings"></a>Definições de configuração do serviço por ambiente

O [modelo de aplicação de serviço ferro](service-fabric-application-model.md) ativa serviços incluir os pacotes de configuração que contêm pares chave-valor personalizados que são lidos em tempo de execução. Também podem ser diferenciar os valores destas definições ao ambiente ao especificar um `ConfigOverride` no manifesto da aplicação.

Imaginemos que tem a seguinte definição no ficheiro Config\Settings.xml para o `Stateful1` serviço:


    <Section Name="MyConfigSection">
      <Parameter Name="MaxQueueSize" Value="25" />
    </Section>

Para substituir este valor para um par de aplicação/ambiente específico, crie uma `ConfigOverride` quando importa o manifesto de serviço no manifesto da aplicação.

    <ConfigOverrides>
     <ConfigOverride Name="Config">
        <Settings>
           <Section Name="MyConfigSection">
              <Parameter Name="MaxQueueSize" Value="[Stateful1_MaxQueueSize]" />
           </Section>
        </Settings>
     </ConfigOverride>
  </ConfigOverrides>

Este parâmetro, em seguida, pode ser configurado pelo ambiente, conforme mostrado acima. Pode fazê-lo ao declará-lo na secção de parâmetros da manifesto da aplicação e especificar valores específicos do ambiente nos ficheiros de parâmetro da aplicação.

>[AZURE.NOTE] No caso de definições de configuração do serviço, existem três locais onde pode ser definido o valor de uma chave: o pacote de configuração do serviço, manifesto da aplicação e o ficheiro de parâmetro de aplicação. Serviço ferro sempre irá escolher a partir do ficheiro de parâmetro da aplicação primeiro (se especificado), em seguida, manifesto da aplicação e, por fim, o pacote de configuração.


### <a name="application-parameter-files"></a>Ficheiros de parâmetro da aplicação

O projeto de aplicação de serviço ferro pode incluir um ou mais ficheiros de parâmetro de aplicação. Cada um deles define os valores específicos para os parâmetros que são definidos em manifesto da aplicação:

    <!-- ApplicationParameters\Local.xml -->

    <Application Name="fabric:/Application1" xmlns="http://schemas.microsoft.com/2011/01/fabric">
        <Parameters>
            <Parameter Name ="Stateful1_MinReplicaSetSize" Value="2" />
            <Parameter Name="Stateful1_PartitionCount" Value="1" />
            <Parameter Name="Stateful1_TargetReplicaSetSize" Value="3" />
        </Parameters>
    </Application>

Por predefinição, uma nova aplicação inclui dois ficheiros de parâmetro de aplicação, com o nome Local.xml e Cloud.xml:

![Ficheiros de parâmetro de aplicação no Explorador de soluções][app-parameters-solution-explorer]

Para criar um novo ficheiro de parâmetro, basta copie e cole uma existente e atribua um nome novo.

## <a name="identifying-environment-specific-parameters-during-deployment"></a>Identificar parâmetros específicos do ambiente durante a implementação

No momento da implementação, tem de escolher o ficheiro de parâmetro apropriado para aplicar com a aplicação. Pode fazê-lo através da caixa de diálogo Publicar no Visual Studio ou através do PowerShell.

### <a name="deploy-from-visual-studio"></a>Implementar a partir do Visual Studio

Pode também selecionar a partir da lista de ficheiros de parâmetro disponíveis ao publicar a sua aplicação no Visual Studio.

![Escolher um ficheiro de parâmetro na caixa de diálogo Publicar][publishdialog]

### <a name="deploy-from-powershell"></a>Implementar a partir do PowerShell

O `Deploy-FabricApplication.ps1` script PowerShell incluído no modelo de aplicação de projeto aceita um perfil de publicar como um parâmetro e o PublishProfile contém uma referência para o ficheiro de parâmetros de aplicação.

  ```PowerShell
    ./Deploy-FabricApplication -ApplicationPackagePath <app_package_path> -PublishProfileFile <publishprofile_path>
  ```

## <a name="next-steps"></a>Próximos passos

Para saber mais acerca de alguns dos conceitos core abordados neste tópico, consulte o artigo [Descrição geral técnica do serviço ferro](service-fabric-technical-overview.md). Para obter informações sobre outras capacidades de gestão de aplicação que estão disponíveis no Visual Studio, consulte o artigo [Gerir as suas aplicações de serviço ferro no Visual Studio](service-fabric-manage-application-in-visual-studio.md).

<!-- Image references -->

[publishdialog]: ./media/service-fabric-manage-multiple-environment-app-configuration/publish-dialog-choose-app-config.png
[app-parameters-solution-explorer]:./media/service-fabric-manage-multiple-environment-app-configuration/app-parameters-in-solution-explorer.png
