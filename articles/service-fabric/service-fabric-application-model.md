<properties
   pageTitle="Modelo de aplicação de serviço ferro | Microsoft Azure"
   description="Como modelo e descrevem aplicações e serviços no serviço ferro."
   services="service-fabric"
   documentationCenter=".net"
   authors="rwike77"
   manager="timlt"
   editor="mani-ramaswamy"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="08/10/2016"   
   ms.author="seanmck"/>

# <a name="model-an-application-in-service-fabric"></a>Modelar uma aplicação no ferro de serviço

Este artigo fornece uma descrição geral do modelo de aplicação Azure Service ferro. Também descreve como definir uma aplicação e o serviço através da ficheiros manifest e obter a aplicação compactado e pronto para implementação.

## <a name="understand-the-application-model"></a>Compreender o modelo de aplicação

Uma aplicação é uma coleção de serviços constituintes que executar uma determinada função ou funções. Um serviço executa uma função de conclusão e autónomo (pode iniciar e executar independentemente dos outros serviços) e é composto por código, configuração e dados. Para cada serviço consiste em código executáveis binários, configuração consiste em definições de serviço que podem ser carregadas em tempo de execução e dados consiste em arbitrários dados estáticos para consumidas pelo serviço. Cada componente neste modelo de aplicação hierárquica pode ser atualizado e versões aos forma independente.

![O modelo de aplicação de serviço ferro][appmodel-diagram]


Um tipo de aplicação é uma categorização de uma aplicação e é constituído por um conjunto de tipos de serviço. Um tipo de serviço é uma categorização de um serviço. A categorização pode ter configurações e definições diferentes, mas as funcionalidades principais se mantém o mesmo. As ocorrências de um serviço são as variações de configuração de serviços diferente do mesmo tipo de serviço.  

As classes (ou "tipos") de aplicações e serviços são descritos através de ficheiros XML (manifestos de aplicações e serviços manifestos) que estão os modelos de contra a qual aplicações é possível criar instâncias a partir do arquivo do cluster da imagem. A definição de esquema para o ficheiro ServiceManifest.xml e ApplicationManifest.xml é instalada com o serviço ferro SDK e ferramentas para *C:\Program Files\Microsoft SDKs\Service Fabric\schemas\ServiceFabricServiceModel.xsd*.

O código de instâncias de uma aplicação diferente será executado como processos separados mesmo quando alojado pelo mesmo nó ferro de serviço. Além disso, o ciclo de vida de cada instância da aplicação pode ser gerido (isto é actualizar o) separadamente. O diagrama seguinte mostra como tipos de aplicação são compostos pelos tipos de serviço, que por sua vez são compostos código, configuração e pacotes. Para simplificar o diagrama, apenas o código / / dados de configuração de pacotes para `ServiceType4` são apresentadas, apesar de cada tipo de serviço deverá incluir alguns ou todos os tipos de pacote.

![Tipos de aplicação de serviço ferro e tipos de serviço][cluster-imagestore-apptypes]

Dois ficheiros manifesto diferentes são utilizados para descrever as aplicações e serviços: a manifesto de serviço e manifesto da aplicação. Estas são abrangidas detalhadamente nas secções subsequentes.

Pode ser ativo no cluster um ou mais ocorrências de um tipo de serviço. Por exemplo, instâncias do serviço de estado ou réplicas, alcançar elevada fiabilidade através da replicação estado entre réplicas localizadas em nós diferentes no cluster. Esta replicação essencialmente redundância de para o serviço para estar disponível mesmo se um nó num cluster falhar. Um [serviço com partições](service-fabric-concepts-partitioning.md) ainda mais divide o respetivo estado (e padrões de acesso a esse Estado) em nós do cluster.

O diagrama seguinte mostra a relação entre as aplicações e instâncias do serviço, partições e réplicas.

![Partições e réplicas dentro de um serviço][cluster-application-instances]


>[AZURE.TIP] Pode ver o esquema das aplicações num cluster de utilizar a ferramenta de serviço ferro Explorer disponível na http://&lt;yourclusteraddress&gt;: 19080/Explorer. Para obter mais detalhes, consulte o artigo [visualizar o seu cluster com o Explorador de ferro de serviço](service-fabric-visualizing-your-cluster.md).

## <a name="describe-a-service"></a>Descreva um serviço

O manifesto serviço forma declarativa define o tipo de serviço e a versão. Especifica os metadados de serviço como o tipo de serviço, propriedades do Estado de funcionamento, métricas de balanceamento de carga, binários do serviço e ficheiros de configuração.  Colocar outra forma, descreve os pacotes de código, configuração e dados que compõem um pacote de serviço para suportar uma ou mais tipos de serviço. Eis um manifesto de serviço de exemplo simples:

~~~
<?xml version="1.0" encoding="utf-8" ?>
<ServiceManifest Name="MyServiceManifest" Version="SvcManifestVersion1" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <Description>An example service manifest</Description>
  <ServiceTypes>
    <StatelessServiceType ServiceTypeName="MyServiceType" />
  </ServiceTypes>
  <CodePackage Name="MyCode" Version="CodeVersion1">
    <SetupEntryPoint>
      <ExeHost>
        <Program>MySetup.bat</Program>
      </ExeHost>
    </SetupEntryPoint>
    <EntryPoint>
      <ExeHost>
        <Program>MyServiceHost.exe</Program>
      </ExeHost>
    </EntryPoint>
  </CodePackage>
  <ConfigPackage Name="MyConfig" Version="ConfigVersion1" />
  <DataPackage Name="MyData" Version="DataVersion1" />
</ServiceManifest>
~~~

**Versão** atributos são cadeias não estruturadas e não analisado pelo sistema. Estas são utilizadas para a versão cada componente de atualização do.

**ServiceTypes** declara que tipos de serviço são suportados pelo **CodePackages** neste manifesto. Quando um serviço é criada uma instância contra um destes tipos de serviço, todos os pacotes de código declarados neste manifesto são ativados ao executar os respetivos pontos de entrada. Os processos resultantes são esperados para registar os tipos de serviço suportados em tempo de execução. Tenha em atenção que tipos de serviço sejam declarados ao nível de manifesto e não o nível de pacote código. Por isso, quando existem vários pacotes de código, estes são todas activados sempre que o sistema de procura qualquer um dos tipos de serviço declarados.

**SetupEntryPoint** é um ponto de entrada privilegiados que é executada com as mesmas credenciais como ferro de serviço (normalmente, a conta de *sistema local* ) antes de qualquer outro ponto de entrada. O ficheiro executável especificado por **ponto de entrada** é normalmente o anfitrião do serviço de execução longa. A presença de um ponto de entrada separadas configuração evita ter de executar o anfitrião do serviço com privilégios de alto para adicional períodos de tempo. O ficheiro executável especificado por **ponto de entrada** é executado depois **SetupEntryPoint** sai com êxito. O processo de resultante é monitorizado e reiniciado (começando novamente **SetupEntryPoint**) se alguma vez termina ou falha.

**DataPackage** declara uma pasta, com o nome o **nome do** atributo, que contém dados estáticos arbitrários para ser consumida pelo processo em tempo de execução.

**ConfigPackage** declara uma pasta, com o nome o **nome do** atributo, que contém um ficheiro de *Settings.xml* . Este ficheiro contém secções par definidos pelo utilizador, o valor de chave de definições de que o processo pode ler novamente em tempo de execução. Durante uma atualização, se apenas a **ConfigPackage** **versão** tiver sido alterada, em seguida, o processo em execução não é reiniciado. Em vez disso, uma chamada de retorno notifica o processo de que as definições de configuração foram alterados, para que possam ser carregadas dinamicamente. Eis um exemplo de ficheiro *Settings.xml* :

~~~
<Settings xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Section Name="MyConfigurationSecion">
    <Parameter Name="MySettingA" Value="Example1" />
    <Parameter Name="MySettingB" Value="Example2" />
  </Section>
</Settings>
~~~

> [AZURE.NOTE] Um manifesto serviço pode conter vários código, configuração e pacotes de dados. Cada um desses pode ser versões forma independente.

<!--
For more information about other features supported by service manifests, refer to the following articles:

*TODO: LoadMetrics, PlacementConstraints, ServicePlacementPolicies
*TODO: Resources
*TODO: Health properties
*TODO: Trace filters
*TODO: Configuration overrides
-->

## <a name="describe-an-application"></a>Descrever uma aplicação


Manifesto da aplicação forma declarativa descreve o tipo de aplicação e versão. Especifica metadados de composição de serviço como os nomes estáveis, criar a partições esquema, fator de contagem/replicação instância, política de segurança/isolamento, as restrições de colocação, substituições de configuração e tipos de serviço constituintes. Os domínios de balanceamento de carga no qual a aplicação é colocada também são descritos.

Portanto, manifesto de uma aplicação descreve elementos ao nível da aplicação e faz referência a uma ou mais manifestos de serviço para redigir um tipo de aplicação. Eis um manifesto da aplicação de exemplo simples:

~~~
<?xml version="1.0" encoding="utf-8" ?>
<ApplicationManifest
      ApplicationTypeName="MyApplicationType"
      ApplicationTypeVersion="AppManifestVersion1"
      xmlns="http://schemas.microsoft.com/2011/01/fabric"
      xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <Description>An example application manifest</Description>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="MyServiceManifest" ServiceManifestVersion="SvcManifestVersion1"/>
  </ServiceManifestImport>
  <DefaultServices>
     <Service Name="MyService">
         <StatelessService ServiceTypeName="MyServiceType" InstanceCount="1">
             <SingletonPartition/>
         </StatelessService>
     </Service>
  </DefaultServices>
</ApplicationManifest>
~~~

Como manifestos de serviço, **versão** atributos são cadeias não estruturadas e não são analisados pelo sistema. Estes são também utilizada para a versão cada componente de atualização do.

**ServiceManifestImport** contém referências a manifestos de serviço que compõem este tipo de aplicação. Serviço importados manifestos determinam que tipos de serviço são válidos dentro deste tipo de aplicação.

**DefaultServices** declara instâncias do serviço que são criadas automaticamente sempre que uma aplicação é criada uma instância contra este tipo de aplicação. Serviços predefinidos são apenas uma questão de conveniência e comportam-se como serviços normais em perfeita depois de ter criados. Estes são atualizados juntamente com quaisquer outros serviços na instância da aplicação e podem ser removidas também.

> [AZURE.NOTE] Manifesto de uma aplicação pode conter várias importações manifesto do serviço e serviços predefinidos. Cada importação manifesto serviço pode ser versões forma independente.

Para saber como manter aplicação diferente e os parâmetros do serviço para individuais ambientes, consulte o artigo [Gerir parâmetros de aplicação para vários ambientes](service-fabric-manage-multiple-environment-app-configuration.md).

<!--
For more information about other features supported by application manifests, refer to the following articles:

*TODO: Application parameters
*TODO: Security, Principals, RunAs, SecurityAccessPolicy
*TODO: Service Templates
-->

## <a name="package-an-application"></a>Compactar uma aplicação

### <a name="package-layout"></a>Esquema de pacote

Manifesto da aplicação, manifest(s) de serviço e outros ficheiros de pacote necessárias devem ser organizados de um esquema específico para implementação para um cluster de serviço ferro. Manifestos exemplo neste artigo seriam tem de ser organizados na estrutura de diretório do seguinte:

~~~
PS D:\temp> tree /f .\MyApplicationType

D:\TEMP\MYAPPLICATIONTYPE
│   ApplicationManifest.xml
│
└───MyServiceManifest
    │   ServiceManifest.xml
    │
    ├───MyCode
    │       MyServiceHost.exe
    │
    ├───MyConfig
    │       Settings.xml
    │
    └───MyData
            init.dat
~~~

As pastas são com nome para corresponder aos atributos do **nome** de cada elemento correspondente. Por exemplo, se o manifesto serviço contido dois pacotes de código com os nomes **MyCodeA** e **MyCodeB**, em seguida, duas pastas com os mesmos nomes iria conter os binários necessários para cada pacote de código.

### <a name="use-setupentrypoint"></a>Utilizar SetupEntryPoint

Cenários típicos para utilizar **SetupEntryPoint** são quando que precisa para executar um ficheiro executável antes do serviço é iniciado ou precisa executar uma operação com privilégios elevados. Por exemplo:

- Configurar e a inicialização variáveis de que necessita do executável do serviço. Não está limitado a apenas os executáveis escritos através de modelos de programação do serviço ferro. Por exemplo, npm.exe precisa de algumas variáveis de ambiente configurados para implementar uma aplicação de node.js.

- Configurar o controlo de acesso instalando certificados de segurança.

### <a name="build-a-package-by-using-visual-studio"></a>Criar um pacote de utilizando o Visual Studio

Se utilizar o Visual Studio 2015 para criar uma aplicação, pode utilizar o comando Compactar para criar automaticamente um pacote que corresponde ao esquema descrito acima.

Para criar um pacote, com o botão direito do projecto de aplicação no Explorador de soluções e escolha o comando de pacote, conforme apresentado abaixo:

![Embalagem de uma aplicação com o Visual Studio][vs-package-command]

Quando embalagem estiver concluída, irá encontrar a localização do pacote na janela de **saída** . Repare que o passo embalagem ocorre automaticamente quando implementar ou depurar sua aplicação no Visual Studio.

### <a name="test-the-package"></a>Teste o pacote

Pode verificar a estrutura de pacote localmente através do PowerShell com o comando de **Teste ServiceFabricApplicationPackage** . Este comando irá verificar a existência de problemas de análise de manifesto e verifique todas as referências. Tenha em atenção que este comando só verifica a regularidade estrutura de diretórios e ficheiros no pacote. Não irá verificar qualquer um dos conteúdos do pacote de código ou dados para além de verificação que todos os ficheiros necessários estão presentes.

~~~
PS D:\temp> Test-ServiceFabricApplicationPackage .\MyApplicationType
False
Test-ServiceFabricApplicationPackage : The EntryPoint MySetup.bat is not found.
FileName: C:\Users\servicefabric\AppData\Local\Temp\TestApplicationPackage_7195781181\nrri205a.e2h\MyApplicationType\MyServiceManifest\ServiceManifest.xml
~~~

Este erro mostra que o ficheiro de *MySetup.bat* referenciado no manifesto do serviço **SetupEntryPoint** está em falta do pacote código. Depois de adicionar o ficheiro em falta, transmite a verificação de aplicação:

~~~
PS D:\temp> tree /f .\MyApplicationType

D:\TEMP\MYAPPLICATIONTYPE
│   ApplicationManifest.xml
│
└───MyServiceManifest
    │   ServiceManifest.xml
    │
    ├───MyCode
    │       MyServiceHost.exe
    │       MySetup.bat
    │
    ├───MyConfig
    │       Settings.xml
    │
    └───MyData
            init.dat

PS D:\temp> Test-ServiceFabricApplicationPackage .\MyApplicationType
True
PS D:\temp>
~~~

Assim que a aplicação está corretamente compactada e transmite verificação, em seguida, está pronto para implementação.

## <a name="next-steps"></a>Próximos passos

[Implementar e remover aplicações][10]

[Gestão de parâmetros de aplicação para vários ambientes][11]

[RunAs: Executar uma aplicação de serviço ferro com permissões de segurança diferente][12]

<!--Image references-->
[appmodel-diagram]: ./media/service-fabric-application-model/application-model.png
[cluster-imagestore-apptypes]: ./media/service-fabric-application-model/cluster-imagestore-apptypes.png
[cluster-application-instances]: media/service-fabric-application-model/cluster-application-instances.png
[vs-package-command]: ./media/service-fabric-application-model/vs-package-command.png

<!--Link references--In actual articles, you only need a single period before the slash-->
[10]: service-fabric-deploy-remove-applications.md
[11]: service-fabric-manage-multiple-environment-app-configuration.md
[12]: service-fabric-application-runas-security.md
