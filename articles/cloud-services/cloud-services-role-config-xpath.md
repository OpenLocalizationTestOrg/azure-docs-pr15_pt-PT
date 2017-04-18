<properties 
pageTitle="Folha de batota nuvem função Serviços config XPath | Microsoft Azure" 
description="As diferentes definições de XPath pode utilizar na configuração de função de serviço de nuvem para expor definições como uma variável de ambiente." 
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
ms.date="08/10/2016" 
ms.author="adegeo"/>

# <a name="expose-role-configuration-settings-as-an-environment-variable-with-xpath"></a>Expor definições de configuração de função como uma variável de ambiente com XPath

No ficheiro de definição de serviço de função web ou trabalhador de serviço de nuvem, pode expor os valores de configuração de runtime como variáveis de ambiente. Os seguintes valores XPath são suportados (que correspondem aos valores de API).

Estes valores XPath também estão disponíveis através da biblioteca de [Microsoft.WindowsAzure.ServiceRuntime](https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleenvironment.aspx) . 

## <a name="app-running-in-emulator"></a>Aplicação em execução no emulador

Indica que a aplicação está em execução no emulador.

| Tipo  | Exemplo |
| ----- | ------- |
| XPath | xpath="/RoleEnvironment/Deployment/@emulated" |
| Código  | var x = RoleEnvironment.IsEmulated; |


## <a name="deployment-id"></a>ID de implementação

Obtém o ID da implementação para a instância.

| Tipo  | Exemplo |
| ----- | ------- |
| XPath | xpath="/RoleEnvironment/Deployment/@id" |
| Código  | var deploymentId = RoleEnvironment.DeploymentId; |


## <a name="role-id"></a>ID de função 

Obtém o ID de função atual para a instância.

| Tipo  | Exemplo |
| ----- | ------- |
| XPath | xpath="/RoleEnvironment/CurrentInstance/@id" |
| Código  | var id = RoleEnvironment.CurrentRoleInstance.Id; |


## <a name="update-domain"></a>Atualizar o domínio

Obtém o domínio de atualização da instância.

| Tipo  | Exemplo |
| ----- | ------- |
| XPath | xpath="/RoleEnvironment/CurrentInstance/@updateDomain" |
| Código  | var ud = RoleEnvironment.CurrentRoleInstance.UpdateDomain; |


## <a name="fault-domain"></a>Domínio de falhas

Obtém o domínio de falha da instância.

| Tipo  | Exemplo |
| ----- | ------- |
| XPath | xpath="/RoleEnvironment/CurrentInstance/@faultDomain" |
| Código  | var DF = RoleEnvironment.CurrentRoleInstance.FaultDomain; |


## <a name="role-name"></a>Nome de função

Obtém o nome da função das instâncias.

| Tipo  | Exemplo |
| ----- | ------- |
| XPath | xpath="/RoleEnvironment/CurrentInstance/@roleName" |
| Código  | var /RName = RoleEnvironment.CurrentRoleInstance.Role.Name;  |


## <a name="config-setting"></a>Definição de configuração

Obtém o valor da definição de configuração especificada.

| Tipo  | Exemplo |
| ----- | ------- |
| XPath | xpath="/RoleEnvironment/CurrentInstance/ConfigurationSettings/ConfigurationSetting[@name='Setting1']/@value" |
| Código  | definição de var = RoleEnvironment.GetConfigurationSettingValue("Setting1"); |
 
## <a name="local-storage-path"></a>Caminho de armazenamento local

Obtém o caminho de armazenamento local para a instância.

| Tipo  | Exemplo |
| ----- | ------- |
| XPath | xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='LocalStore1']/@path" |
| Código  | var localResourcePath = RoleEnvironment.GetLocalResource("LocalStore1"). RootPath; |


## <a name="local-storage-size"></a>Tamanho do armazenamento local

Obtém o tamanho do armazenamento local para a instância.

| Tipo  | Exemplo |
| ----- | ------- |
| XPath | xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='LocalStore1']/@sizeInMB" |
| Código  | var localResourceSizeInMB = RoleEnvironment.GetLocalResource("LocalStore1"). MaximumSizeInMegabytes; |

## <a name="endpoint-protocol"></a>Protocolo de ponto final 

Obtém o protocolo de ponto final para a instância.

| Tipo  | Exemplo |
| ----- | ------- |
| XPath | xpath="/RoleEnvironment/CurrentInstance/Endpoints/Endpoint[@name='Endpoint1']/@protocol" |
| Código  | var prot = RoleEnvironment.CurrentRoleInstance.InstanceEndpoints["Endpoint1"]. Protocolo; |

## <a name="endpoint-ip"></a>Ponto final de IP

Obtém o endereço IP do ponto final especificado.

| Tipo | Exemplo |
| ----- | ---- |
| XPath | xpath="/RoleEnvironment/CurrentInstance/Endpoints/Endpoint[@name='Endpoint1']/@address" |
| Código  | var endereço = RoleEnvironment.CurrentRoleInstance.InstanceEndpoints["Endpoint1"]. IPEndpoint.Address |

## <a name="endpoint-port"></a>Porta de ponto final 

Obtém a porta de ponto final para a instância.

| Tipo  | Exemplo |
| ----- | ------- |
| XPath | xpath="/RoleEnvironment/CurrentInstance/Endpoints/Endpoint[@name='Endpoint1']/@port" |
| Código  | var porta = RoleEnvironment.CurrentRoleInstance.InstanceEndpoints["Endpoint1"]. IPEndpoint.Port; |





## <a name="example"></a>Exemplo

Eis um exemplo de uma função de trabalho que cria uma tarefa de arranque com uma variável de ambiente denominada `TestIsEmulated` definido para o [ @emulated xpath valor](#app-running-in-emulator). 

```xml
<WorkerRole name="Role1">
    <ConfigurationSettings>
      <Setting name="Setting1" />
    </ConfigurationSettings>
    <LocalResources>
      <LocalStorage name="LocalStore1" sizeInMB="1024"/>
    </LocalResources>
    <Endpoints>
      <InternalEndpoint name="Endpoint1" protocol="tcp" />
    </Endpoints>
    <Startup>
      <Task commandLine="example.cmd inputParm">
        <Environment>
          <Variable name="TestConstant" value="Constant"/>
          <Variable name="TestEmptyValue" value=""/>
          <Variable name="TestIsEmulated">
            <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated"/>
          </Variable>
          ...
        </Environment>
      </Task>
    </Startup>
    <Runtime>
      <Environment>
        <Variable name="TestConstant" value="Constant"/>
        <Variable name="TestEmptyValue" value=""/>
        <Variable name="TestIsEmulated">
          <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated"/>
        </Variable>
        ...
      </Environment>
    </Runtime>
    ...
</WorkerRole>
```

## <a name="next-steps"></a>Próximos passos

Saiba mais sobre o ficheiro [ServiceConfiguration.cscfg](cloud-services-model-and-package.md#serviceconfigurationcscfg) .

Crie um pacote de [ServicePackage.cspkg](cloud-services-model-and-package.md#servicepackagecspkg) .

Active o [ambiente de trabalho remoto](cloud-services-role-enable-remote-desktop.md) para uma função.
