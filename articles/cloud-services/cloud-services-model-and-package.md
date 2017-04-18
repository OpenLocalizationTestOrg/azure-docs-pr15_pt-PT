<properties
    pageTitle="O que é um modelo de serviço na nuvem e pacote | Microsoft Azure"
    description="Descreve o modelo de serviço de nuvem (.csdef, .cscfg) e o pacote (.cspkg) no Azure"
    services="cloud-services"
    documentationCenter=""
    authors="Thraka"
    manager="timlt"
    editor=""/>
<tags
    ms.service="cloud-services"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/06/2016"
    ms.author="adegeo"/>

# <a name="what-is-the-cloud-service-model-and-how-do-i-package-it"></a>O que é o modelo de serviço na nuvem e como compactá-lo?
Um serviço na nuvem é criado a partir de três componentes, a definição de serviço _(.csdef)_, o serviço config _(.cscfg)_e um pacote de serviço _(.cspkg)_. Ambos os **ServiceDefinition.csdef** **ServiceConfig.cscfg** ficheiros e são baseado em XML e descrevem a estrutura do serviço na nuvem e como está configurado; chamado constituem o modelo. O **ServicePackage.cspkg** é um ficheiro zip que é gerado a partir da **ServiceDefinition.csdef** e entre outras coisas, que contém todas as dependências necessárias baseado em binário. Azure cria um serviço na nuvem a partir de **ServicePackage.cspkg** e o **ServiceConfig.cscfg**.

Assim que o serviço de nuvem estiver em execução no Azure, pode reconfigurá-lo através do ficheiro **ServiceConfig.cscfg** , mas não é possível alterar a definição.

## <a name="what-would-you-like-to-know-more-about"></a>O que deseja saber mais sobre?

* Pretende saber mais sobre os ficheiros [ServiceDefinition.csdef](#csdef) e [ServiceConfig.cscfg](#cscfg) .
* Já conhecimento que, também me [alguns exemplos](#next-steps) sobre o que pode a configurar.
* Pretende criar a [ServicePackage.cspkg](#cspkg).
* Estou a utilizar o Visual Studio e que pretendo...
    * [Criar um novo serviço na nuvem][vs_create]
    * [Reconfigurar um serviço de nuvem existente][vs_reconfigure]
    * [Implementar um projeto de serviço na nuvem][vs_deploy]
    * [Ambiente de trabalho remoto para uma instância do serviço na nuvem][remotedesktop]

<a name="csdef"></a>
## <a name="servicedefinitioncsdef"></a>ServiceDefinition.csdef
O ficheiro **ServiceDefinition.csdef** Especifica as definições que são utilizadas pelo Azure para configurar um serviço na nuvem. O [Esquema de definição de serviço do Azure (.csdef ficheiro)](https://msdn.microsoft.com/library/azure/ee758711.aspx) fornece o formato permitido para um ficheiro de definição de serviço. O exemplo seguinte mostra as definições que podem ser definidas para as funções Web e trabalho:

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceDefinition name="MyServiceName" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
  <WebRole name="WebRole1" vmsize="Medium">
    <Sites>
      <Site name="Web">
        <Bindings>
          <Binding name="HttpIn" endpointName="HttpIn" />
        </Bindings>
      </Site>
    </Sites>
    <Endpoints>
      <InputEndpoint name="HttpIn" protocol="http" port="80" />
      <InternalEndpoint name="InternalHttpIn" protocol="http" />
    </Endpoints>
    <Certificates>
      <Certificate name="Certificate1" storeLocation="LocalMachine" storeName="My" />
    </Certificates>
    <Imports>
      <Import moduleName="Connect" />
      <Import moduleName="Diagnostics" />
      <Import moduleName="RemoteAccess" />
      <Import moduleName="RemoteForwarder" />
    </Imports>
    <LocalResources>
      <LocalStorage name="localStoreOne" sizeInMB="10" />
      <LocalStorage name="localStoreTwo" sizeInMB="10" cleanOnRoleRecycle="false" />
    </LocalResources>
    <Startup>
      <Task commandLine="Startup.cmd" executionContext="limited" taskType="simple" />
    </Startup>
  </WebRole>

  <WorkerRole name="WorkerRole1">
    <ConfigurationSettings>
      <Setting name="DiagnosticsConnectionString" />
    </ConfigurationSettings>
    <Imports>
      <Import moduleName="RemoteAccess" />
      <Import moduleName="RemoteForwarder" />
    </Imports>
    <Endpoints>
      <InputEndpoint name="Endpoint1" protocol="tcp" port="10000" />
      <InternalEndpoint name="Endpoint2" protocol="tcp" />
    </Endpoints>
  </WorkerRole>
</ServiceDefinition>
```

Pode referir-se para o [[esquema de definição de serviço]] para uma melhor compreensão do esquema XML utilizado aqui, no entanto, eis uma rápida explicação das alguns dos elementos:

**Sites**  
Contém as definições para as aplicações de Web sites ou na web que são alojadas no IIS7.

**InputEndpoints**  
Contém as definições para os pontos finais que são utilizadas para contactar o serviço de nuvem.

**InternalEndpoints**  
Contém as definições para os pontos finais que são utilizadas pelo instâncias de função para comunicar com os outros.

**ConfigurationSettings**  
Contém as definições de definição para funcionalidades de uma função específica.

**Certificados**  
Contém as definições para os certificados são necessários para uma função. O exemplo anterior do código mostra um certificado que é utilizado para a configuração do Azure ligar.

**LocalResources**  
Contém as definições para recursos de armazenamento local. Um recurso de armazenamento local é um diretório reservado no sistema de ficheiros da máquina virtual em que uma instância de uma função está a ser executado.

**Importar ficheiros**  
Contém as definições para módulos importados. No exemplo anterior do código mostra os módulos de ligação de ambiente de trabalho remoto e Azure ligar.

**Arranque**  
Contém as tarefas que são executadas quando a função é iniciado. As tarefas são definidas numa. cmd ou ficheiro executável.



<a name="cscfg"></a>
## <a name="serviceconfigurationcscfg"></a>ServiceConfiguration.cscfg
A configuração das definições do seu serviço de nuvem é determinada pelos valores no ficheiro **ServiceConfiguration.cscfg** . Especifique o número de instâncias que pretende implementar para cada função neste ficheiro. São adicionados os valores para as definições de configuração que definiu no ficheiro de definição do serviço para o ficheiro de configuração do serviço. Miniaturas para qualquer certificados de gestão da que estão associadas com o serviço de nuvem também são adicionadas ao ficheiro. O [Esquema de configuração do Azure Service (.cscfg ficheiro)](https://msdn.microsoft.com/library/azure/ee758710.aspx) fornece o formato permitido para um ficheiro de configuração do serviço.

O ficheiro de configuração de serviço não é fornecido com a aplicação, mas é carregado para Azure como um ficheiro separado e é utilizado para configurar o serviço de nuvem. Pode carregar um novo ficheiro de configuração do serviço sem Reimplementar seu serviço de nuvem. Os valores de configuração para o serviço de nuvem podem ser alterados enquanto o serviço de nuvem estiver em execução. O exemplo seguinte mostra as definições de configuração que podem ser definidas para as funções Web e trabalho:

```xml
<?xml version="1.0"?>
<ServiceConfiguration serviceName="MyServiceName" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration">
  <Role name="WebRole1">
    <Instances count="2" />
    <ConfigurationSettings>
      <Setting name="SettingName" value="SettingValue" />
    </ConfigurationSettings>

    <Certificates>
      <Certificate name="CertificateName" thumbprint="CertThumbprint" thumbprintAlgorithm="sha1" />
      <Certificate name="Microsoft.WindowsAzure.Plugins.RemoteAccess.PasswordEncryption"
         thumbprint="CertThumbprint" thumbprintAlgorithm="sha1" />
    </Certificates>
  </Role>
</ServiceConfiguration>
```

Pode referir-se para o [Esquema de configuração do serviço](https://msdn.microsoft.com/library/azure/ee758710.aspx) para compreender melhor o esquema XML utilizado aqui, no entanto, eis uma rápida explicação dos elementos:

**Instâncias**  
Configura o número de instâncias de para a função em execução. Para impedir que o seu serviço de nuvem potencialmente fique indisponível durante as actualizações, recomenda-se que implemente mais do que uma instância da sua funções web destinado. Ao fazê-lo, está a cumprir as diretrizes o [Azure calcular nível contrato serviço (SLA)](http://azure.microsoft.com/support/legal/sla/), que garante 99.95% conectividade externa para funções de acesso à Internet quando duas ou mais ocorrências de função são implementadas para um serviço.

**ConfigurationSettings**  
Configura as definições para as instâncias em execução de uma função. O nome da `<Setting>` elementos tem de corresponder as definições de definição no ficheiro de definição de serviço.

**Certificados**  
Configura os certificados utilizados pelo serviço. No exemplo anterior do código mostra como definir o certificado para o módulo de acesso remoto. O valor do atributo *impressão digital* tem de ser definido para a impressão digital do certificado para utilizar.

<p/>

 >[AZURE.NOTE] A impressão digital para o certificado pode ser adicionada ao ficheiro de configuração utilizando um editor de texto ou o valor pode ser adicionado no separador da página de **Propriedades** da função no Visual Studio **certificados** .



## <a name="defining-ports-for-role-instances"></a>Definir as portas de instâncias de uma função
Azure permite que o ponto de entrada apenas uma a uma função da web. Isto significa que todo o tráfego ocorre através de um endereço IP. Pode configurar os seus Web sites de partilhar uma porta ao configurar o cabeçalho de anfitrião para direcionar o pedido para a localização correta. Também pode configurar as aplicações para ouvir portas conhecidas do endereço IP.

O exemplo seguinte mostra a configuração para uma função de web com uma aplicação web e Web site. O Web site está configurado como a localização de entrada predefinida na porta 80 e as aplicações web estão configuradas para receber pedidos de um cabeçalho de sistema anfitrião alternativo denominado "mail.mysite.cloudapp.net".

```xml
<WebRole>
  <ConfigurationSettings>
    <Setting name="DiagnosticsConnectionString" />
  </ConfigurationSettings>
  <Endpoints>
    <InputEndpoint name="HttpIn" protocol="http" <mark>port="80"</mark> />
    <InputEndpoint name="Https" protocol="https" port="443" certificate="SSL"/>
    <InputEndpoint name="NetTcp" protocol="tcp" port="808" certificate="SSL"/>
  </Endpoints>
  <LocalResources>
    <LocalStorage name="Sites" cleanOnRoleRecycle="true" sizeInMB="100" />
  </LocalResources>
  <Site name="Mysite" packageDir="Sites\Mysite">
    <Bindings>
      <Binding name="http" endpointName="HttpIn" />
      <Binding name="https" endpointName="Https" />
      <Binding name="tcp" endpointName="NetTcp" />
    </Bindings>
  </Site>
  <Site name="MailSite" packageDir="MailSite">
    <Bindings>
      <Binding name="mail" endpointName="HttpIn" <mark>hostheader="mail.mysite.cloudapp.net"</mark> />
    </Bindings>
    <VirtualDirectory name="artifacts" />
    <VirtualApplication name="storageproxy">
      <VirtualDirectory name="packages" packageDir="Sites\storageProxy\packages"/>
    </VirtualApplication>
  </Site>
</WebRole>
```


## <a name="changing-the-configuration-of-a-role"></a>Alterar a configuração de uma função
Pode atualizar a configuração do seu serviço em nuvem enquanto estiver em execução no Azure, sem ser o serviço offline. Para alterar as informações de configuração, pode carregar um novo ficheiro de configuração, ou editar o ficheiro de configuração no local e aplicá-lo no seu serviço em execução. Podem ser efetuadas as seguintes alterações na configuração de um serviço:

- **Alterar os valores das definições de configuração**  
Quando uma configuração a definição das alterações, pode escolher uma instância de função aplicar a alteração enquanto a instância está online ou a Reciclagem correctamente a instância e aplicar a alteração enquanto a instância está offline.

- **Alterar a topologia de serviço de instâncias de função**  
Alterações da topologia não afetam instâncias em execução, exceto onde está a ser removida uma instância. Todas as instâncias restantes geralmente não precisam de ser reciclados; No entanto, pode optar por reciclagem instâncias de funções em resposta a uma alteração da topologia.

- **Alterar a impressão digital do certificado**  
Só pode atualizar um certificado quando uma instância de função está offline. Se um certificado é adicionado, eliminado ou alterado durante uma instância de função está online, Azure correctamente leva a instância offline para atualizar o certificado e colocá-lo novamente online após a alteração for concluída.

### <a name="handling-configuration-changes-with-service-runtime-events"></a>Alterações de configuração de processamento com eventos de tempo de execução do serviço
A [Biblioteca de Runtime Azure](https://msdn.microsoft.com/library/azure/mt419365.aspx) inclui o espaço de nomes [Microsoft.WindowsAzure.ServiceRuntime](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.aspx) , que fornece classes para interagir com o ambiente do Azure a partir de uma instância de uma função a execução de código. A classe de [RoleEnvironment](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.aspx) define os seguintes eventos estão elevados antes e depois de uma alteração à configuração:

- **[Alterar](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.changing.aspx) evento**  
Isto ocorre antes da alteração de configuração é aplicada a uma instância especificada de uma função que lhe dá a oportunidade de anotar as instâncias de função, se necessário.
- **Evento [alteradas](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.changed.aspx)**  
Ocorre após a alteração de configuração é aplicada a uma instância de uma função especificada.

> [AZURE.NOTE] Uma vez que as alterações de certificado colocar sempre as instâncias de uma função offline, não de aumentar os eventos RoleEnvironment.Changing ou RoleEnvironment.Changed.

<a name="cspkg"></a>
## <a name="servicepackagecspkg"></a>ServicePackage.cspkg
Para implementar uma aplicação como um serviço na nuvem no Azure, primeiro tem de compactar a aplicação no formato adequado. Pode utilizar a ferramenta de linha de comandos **CSPack** (instalada com o [Azure SDK](https://azure.microsoft.com/downloads/)) para criar o ficheiro de pacote como uma alternativa para Visual Studio.

**CSPack** utiliza os conteúdos do ficheiro de definição de serviço e ficheiro de configuração do serviço para definir os conteúdos do pacote. **CSPack** gera um ficheiro de pacote de aplicação (.cspkg) que pode carregar para o Azure utilizando o [Azure portal](cloud-services-how-to-create-deploy-portal.md#create-and-deploy). Por predefinição, o pacote é denominado `[ServiceDefinitionFileName].cspkg`, mas pode especificar um nome diferente, utilizando o `/out` opção de **CSPack**.

**CSPack** está geralmente localizada em  
`C:\Program Files\Microsoft SDKs\Azure\.NET SDK\[sdk-version]\bin\`

>[AZURE.NOTE]
CSPack.exe (no windows) está disponível ao executar o atalho de **linha de comandos do Microsoft Azure** que é instalado com o SDK.  
>  
>Execute o programa CSPack.exe por si só consulte a documentação sobre todos os comandos e parâmetros possíveis.

<p />

>[AZURE.TIP]
Executar o seu serviço de nuvem localmente no **Microsoft Azure calcular emulador**, utilize a opção de **/copyonly** que esta opção copia os ficheiros binários para a aplicação a um esquema de diretório a partir do qual pode executar no emulador de cluster.

### <a name="example-command-to-package-a-cloud-service"></a>Comando de exemplo para um serviço na nuvem do pacote
O exemplo seguinte cria um pacote de aplicações que contém as informações para uma função de web. O comando Especifica o ficheiro de definição de serviço para utilizar, o diretório onde podem ser encontrados ficheiros binários, e o nome do ficheiro do pacote.

    cspack [DirectoryName]\[ServiceDefinition]
           /role:[RoleName];[RoleBinariesDirectory]
           /sites:[RoleName];[VirtualPath];[PhysicalPath]
           /out:[OutputFileName]

Se a aplicação contém uma função de web e uma função de trabalho, é utilizado o seguinte comando:

    cspack [DirectoryName]\[ServiceDefinition]
           /out:[OutputFileName]
           /role:[RoleName];[RoleBinariesDirectory]
           /sites:[RoleName];[VirtualPath];[PhysicalPath]
           /role:[RoleName];[RoleBinariesDirectory];[RoleAssemblyName]

Onde as variáveis são definidas da seguinte forma:

| Variável                  | Valor |
| ------------------------- | ----- |
| \[Nome_do_directório\]         | Subdirectório sob o directório de projeto de raiz que contém o ficheiro .csdef do projeto Azure.|
| \[ServiceDefinition\]     | O nome do ficheiro de definição do serviço. Por predefinição, este ficheiro é denominado ServiceDefinition.csdef.  |
| \[Nomeficheirosaída\]        | O nome para o ficheiro de pacote gerado. Normalmente, isto está definido para nome da aplicação. Se não for especificado nenhum nome de ficheiro, o pacote de aplicação é criado como \[ApplicationName\].cspkg.|
| \[Nome da função\]              | O nome da função tal como foi definido no ficheiro de definição de serviço.|
| \[RoleBinariesDirectory] | A localização dos ficheiros binários para a função.|
| \[CaminhoVirtual\]           | Diretórios físicos para cada caminho virtual definidas na secção de Sites da definição do serviço.|
| \[CaminhoFísico\]          | Diretórios físicos do conteúdo para cada caminho virtual definida no nó do site da definição do serviço.|
| \[RoleAssemblyName\]      | O nome do ficheiro binário para a função.| 


## <a name="next-steps"></a>Próximos passos

Estou a criar um pacote de serviço de nuvem e que pretendo...

* [Configurar o ambiente de trabalho remoto para uma instância do serviço na nuvem][remotedesktop]
* [Implementar um projeto de serviço na nuvem][deploy]

Estou a utilizar o Visual Studio e que pretendo...

* [Criar um novo serviço na nuvem][vs_create]
* [Reconfigurar um serviço de nuvem existente][vs_reconfigure]
* [Implementar um projeto de serviço na nuvem][vs_deploy]
* [Configurar o ambiente de trabalho remoto para uma instância do serviço na nuvem][vs_remote]

[deploy]: cloud-services-how-to-create-deploy-portal.md
[remotedesktop]: cloud-services-role-enable-remote-desktop.md
[vs_remote]: ../vs-azure-tools-remote-desktop-roles.md
[vs_deploy]: ../vs-azure-tools-cloud-service-publish-set-up-required-services-in-visual-studio.md
[vs_reconfigure]: ../vs-azure-tools-configure-roles-for-cloud-service.md
[vs_create]: ../vs-azure-tools-azure-project-create.md
