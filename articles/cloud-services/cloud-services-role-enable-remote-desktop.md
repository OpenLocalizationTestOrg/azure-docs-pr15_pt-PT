<properties 
pageTitle="Ativar a ligação ao ambiente de trabalho remoto para uma função de serviços em nuvem Azure" 
description="Como configurar a sua aplicação de serviço de nuvem azure para permitir ligações de ambiente de trabalho remotas" 
services="cloud-services" 
documentationCenter="" 
authors="sbtron" 
manager="timlt" 
editor=""/>
<tags 
ms.service="cloud-services" 
ms.workload="tbd" 
ms.tgt_pltfrm="na" 
ms.devlang="na" 
ms.topic="article" 
ms.date="02/17/2016" 
ms.author="saurabh"/>

# <a name="enable-remote-desktop-connection-for-a-role-in-azure-cloud-services"></a>Ativar a ligação ao ambiente de trabalho remoto para uma função de serviços em nuvem Azure

>[AZURE.SELECTOR]
- [Azure portal clássico](cloud-services-role-enable-remote-desktop.md)
- [PowerShell](cloud-services-role-enable-remote-desktop-powershell.md)
- [Visual Studio](../vs-azure-tools-remote-desktop-roles.md)


Ambiente de trabalho remoto permite-lhe aceder ao ambiente de trabalho de uma função em execução no Azure. Pode utilizar uma ligação de ambiente de trabalho remoto para resolver problemas e diagnosticar problemas com a aplicação enquanto estiver em execução. 

Pode ativar uma ligação de ambiente de trabalho remoto no seu perfil durante o desenvolvimento, incluindo os módulos de ambiente de trabalho remoto na sua definição de serviço ou pode optar por ativar o ambiente de trabalho remoto através da extensão de ambiente de trabalho remoto. A abordagem preferida é utilizar a extensão de ambiente de trabalho remoto como pode ativar o ambiente de trabalho remoto mesmo após a aplicação é implementada sem ter de implementar novamente a sua aplicação. 


## <a name="configure-remote-desktop-from-the-azure-classic-portal"></a>Configurar o ambiente de trabalho remoto a partir do Azure portal clássico
Portal clássico do Azure utiliza a abordagem extensão de ambiente de trabalho remoto, por isso, pode ativar o ambiente de trabalho remoto mesmo após a aplicação é implementada. Página **Configurar** do seu serviço de nuvem permite-lhe ativar o ambiente de trabalho remoto, altere a conta de administrador local utilizada para ligar a máquinas virtuais, o certificado utilizado na autenticação e defina a data de expiração. 


1. Clique em **Serviços em nuvem**, clique no nome do serviço em nuvem e, em seguida, clique em **Configurar**.

2. Clique em **remoto**.
    
    ![Serviços em nuvem remoto](./media/cloud-services-role-enable-remote-desktop/CloudServices_Remote.png)
    
    > [AZURE.WARNING] Todas as instâncias de função vai ser reiniciadas quando primeiro de ativar o ambiente de trabalho remoto e clique em OK (marca de verificação). Para impedir que um reinício, tem de estar instalado o certificado utilizado para encriptar a palavra-passe na função. Para impedir que um reinício, [carregar um certificado para o serviço de nuvem](cloud-services-how-to-create-deploy/#how-to-upload-a-certificate-for-a-cloud-service) e, em seguida, regresse a esta caixa de diálogo.
    

3. Em **funções**, selecione a função que pretende atualizar ou selecionar **Tudo** para todas as funções.

4. Efetue qualquer uma das seguintes alterações:
    
    - Para ativar o ambiente de trabalho remoto, selecione a caixa de verificação **Ativar o ambiente de trabalho remoto** . Para desativar o ambiente de trabalho remoto, desmarque a caixa de verificação.
    
    - Crie uma conta a utilizar em ligações de ambiente de trabalho remoto para as instâncias de função.
    
    - Atualize a palavra-passe da conta existente.
    
    - Selecione um certificado carregado para utilizar de autenticação (transferir o certificado utilizando **carregar** na página de **certificados** ) ou criar um novo certificado. 
    
    - Altere a data de expiração para a configuração de ambiente de trabalho remoto.

5. Quando terminar as suas atualizações de configuração, clique em **OK** (marca de verificação).


## <a name="remote-into-role-instances"></a>Remote para instâncias de função
Depois de ambiente de trabalho remoto está ativado nas funções pode remote para uma instância de função através de vários ferramentas.

Para ligar a uma instância de função a partir do portal clássico Azure:
    
  1.   Clique em **instâncias** para abrir a página de **instâncias** .
  2.   Selecione uma instância de função que tem o ambiente de trabalho remoto configurado.
  3.   Clique em **Ligar**e siga as instruções para abrir o ambiente de trabalho. 
  4.   Clique em **Abrir** e, em seguida, **Ligar** para iniciar a ligação de ambiente de trabalho remoto. 


### <a name="use-visual-studio-to-remote-into-a-role-instance"></a>Utilizar o Visual Studio para remoto para uma instância de função

No Visual Studio, Server Explorer:

1. Expandir a **Azure\\serviços em nuvem\\[nome do serviço de nuvem]** nó.
2. Expanda **teste** ou de **produção**.
3. Expanda a função individual.
4. Botão direito do rato das instâncias de função, clique em **Ligar utilizando Remote Desktop …**e, em seguida, introduza o nome de utilizador e palavra-passe. 

![Ambiente de trabalho remoto do Explorador de servidor](./media/cloud-services-role-enable-remote-desktop/ServerExplorer_RemoteDesktop.png)


### <a name="use-powershell-to-get-the-rdp-file"></a>Utilizar o PowerShell para obter o ficheiro RDP
Pode utilizar o cmdlet [Get-AzureRemoteDesktopFile](https://msdn.microsoft.com/library/azure/dn495261.aspx) para obter o ficheiro RDP. Em seguida, pode utilizar o ficheiro RDP com a ligação de ambiente de trabalho remoto para aceder ao serviço de nuvem.

### <a name="programmatically-download-the-rdp-file-through-the-service-management-rest-api"></a>Através de programação transferir o ficheiro RDP através da gestão de serviço REST API
Pode utilizar a operação de resto de [Ficheiro de RDP transferir](https://msdn.microsoft.com/library/jj157183.aspx) para transferir o ficheiro RDP. 



## <a name="to-configure-remote-desktop-in-the-service-definition-file"></a>Para configurar o ambiente de trabalho remoto no ficheiro de definição de serviço

Este método permite-lhe ativar o ambiente de trabalho remoto para a aplicação durante o desenvolvimento. Esta abordagem requer palavras-passe encriptadas ser armazenado na sua configuração do serviço de ficheiros e quaisquer actualizações para a configuração de ambiente de trabalho remota seriam exigir uma nova implementação da aplicação. Se pretender evitar estes downsides deve utilizar a abordagem de extensão de ambiente de trabalho remoto, com base descrita acima.  

Pode utilizar o Visual Studio para [Ativar uma ligação de ambiente de trabalho remoto](../vs-azure-tools-remote-desktop-roles.md) utilizando a abordagem de ficheiro de definição de serviço.  
Os passos abaixo descrevem as alterações necessárias para os ficheiros de modelo de serviço para ativar o ambiente de trabalho remoto. Visual Studio toma automaticamente estas alterações ao quando de publicação.

### <a name="set-up-the-connection-in-the-service-model"></a>Configurar a ligação no modelo de serviço 
Utilize o elemento de **importação** para importar o módulo de **acesso remoto** e o módulo **RemoteForwarder** para o ficheiro de [ServiceDefinition.csdef](cloud-services-model-and-package.md#csdef) .

Ficheiro de definição do serviço deverá ser semelhante ao exemplo seguinte com o `<Imports>` elemento adicionado.

```xml
<ServiceDefinition name="<name-of-cloud-service>" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition" schemaVersion="2013-03.2.0">
    <WebRole name="WebRole1" vmsize="Small">
        <Sites>
            <Site name="Web">
                <Bindings>
                    <Binding name="Endpoint1" endpointName="Endpoint1" />
                </Bindings>
            </Site>
        </Sites>
        <Endpoints>
            <InputEndpoint name="Endpoint1" protocol="http" port="80" />
        </Endpoints>
        <Imports>
            <Import moduleName="Diagnostics" />
            <Import moduleName="RemoteAccess" />
            <Import moduleName="RemoteForwarder" />
        </Imports>
    </WebRole>
</ServiceDefinition>
```
O ficheiro de [ServiceConfiguration.cscfg](cloud-services-model-and-package.md#cscfg) deverá ser semelhante ao exemplo seguinte, tenha em atenção o `<ConfigurationSettings>` e `<Certificates>` elementos. O certificado especificado tem de ser [carregado para o serviço de nuvem](../cloud-services-how-to-create-deploy.md#how-to-upload-a-certificate-for-a-cloud-service).

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceConfiguration serviceName="<name-of-cloud-service>" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration" osFamily="3" osVersion="*" schemaVersion="2013-03.2.0">
    <Role name="WebRole1">
        <Instances count="2" />
        <ConfigurationSettings>
            <Setting name="Microsoft.WindowsAzure.Plugins.RemoteAccess.Enabled" value="true" />
            <Setting name="Microsoft.WindowsAzure.Plugins.RemoteAccess.AccountUsername" value="[name-of-user-account]" />
            <Setting name="Microsoft.WindowsAzure.Plugins.RemoteAccess.AccountEncryptedPassword" value="[base-64-encrypted-user-password]" />
            <Setting name="Microsoft.WindowsAzure.Plugins.RemoteAccess.AccountExpiration" value="[certificate-expiration]" />
            <Setting name="Microsoft.WindowsAzure.Plugins.RemoteForwarder.Enabled" value="true" />
        </ConfigurationSettings>
        <Certificates>
            <Certificate name="Microsoft.WindowsAzure.Plugins.RemoteAccess.PasswordEncryption" thumbprint="[certificate-thumbprint]" thumbprintAlgorithm="sha1" />
        </Certificates>
    </Role>
</ServiceConfiguration>
```


## <a name="additional-resources"></a>Recursos adicionais

[Como configurar os serviços em nuvem](cloud-services-how-to-configure.md)