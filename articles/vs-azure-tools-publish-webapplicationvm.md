<properties
   pageTitle="WebApplicationVM publicar | Microsoft Azure"
   description="Saiba como implementar uma aplicação web para uma máquina virtual. Este script cria os recursos necessários na sua subscrição do Azure caso não existam."
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="08/15/2016"
   ms.author="tarcher" />

# <a name="publish-webapplicationvm-windows-powershell-script"></a>Publicar WebApplicationVM (script do Windows PowerShell)

Implementar uma aplicação web para uma máquina virtual. O script cria os recursos necessários na sua subscrição do Azure caso não existam.

```
Publish-WebApplicationVM
–Configuration <configuration>
-SubscriptionName <subscriptionName>
-WebDeployPackage <packageName>
-VMPassword @{Name = "name"; Password = "password")
-DatabaseServerPassword @{Name = "name"; Password = "password"}
-SendHostMessagesToOutput
-Verbose
```

### <a name="configuration"></a>Configuração

O caminho para o ficheiro de configuração de JSON que descreva os detalhes da implementação.

|Aliases|nenhum|
|---|---|
|Obrigatório?|VERDADEIRO|
|Posição|com o nome|
|Valor predefinido|nenhum|
|Aceitar a entrada de tubagem?|FALSO|
|Aceitar carateres universais?|FALSO|

### <a name="subscriptionname"></a>SubscriptionName

O nome da subscrição do Azure na qual pretende criar a máquina virtual.

|Aliases|nenhum|
|---|---|
|Obrigatório?|FALSO|
|Posição|com o nome|
|Valor predefinido|Utiliza a subscrição primeira existentes no ficheiro de subscrição|
|Aceitar a entrada de tubagem?|FALSO|
|Aceitar carateres universais?|FALSO|

### <a name="webdeploypackage"></a>WebDeployPackage

O caminho para o pacote de implementação de web para publicar a máquina virtual. Pode criar este pacote utilizando o Assistente de Web publicar no Visual Studio. Consulte o artigo [como: criar um pacote de implementação de Web no Visual Studio](https://msdn.microsoft.com/library/dd465323.aspx).

|Aliases|nenhum|
|---|---|
|Obrigatório?|FALSO|
|Posição|com o nome|
|Valor predefinido|nenhum|
|Aceitar a entrada de tubagem?|FALSO|
|Aceitar carateres universais?|FALSO|

### <a name="allowuntrusted"></a>AllowUntrusted

Se for VERDADEIRO, permita a utilização de certificados sem ter sessão iniciada por uma autoridade de raiz fidedigna.

|Aliases|nenhum|
|---|---|
|Obrigatório?|FALSO|
|Posição|com o nome|
|Valor predefinido|FALSO|
|Aceitar a entrada de tubagem?|FALSO|
|Aceitar carateres universais?|FALSO|

### <a name="vmpassword"></a>VMPassword

As credenciais para a conta de máquina virtual. Exemplo: - VMPassword @{Name = "administrador"; Palavra-passe = "palavra-passe"}

|Aliases|nenhum|
|---|---|
|Obrigatório?|FALSO|
|Posição|com o nome|
|Valor predefinido|nenhum|
|Aceitar a entrada de tubagem?|FALSO|
|Aceitar carateres universais?|FALSO|

### <a name="databaseserverpassword"></a>DatabaseServerPassword

As credenciais para a base de dados SQL Azure. Exemplo: - DatabaseServerPassword @{Name = "administrador"; Palavra-passe = "palavra-passe"}

|Aliases|nenhum|
|---|---|
|Obrigatório?|FALSO|
|Posição|com o nome|
|Valor predefinido|nenhum|
|Aceitar a entrada de tubagem?|FALSO|
|Aceitar carateres universais?|FALSO|

### <a name="sendhostmessagestooutput"></a>SendHostMessagesToOutput

Se for VERDADEIRO, imprimir mensagens são o script na sequência de saída.

|Aliases|nenhum|
|---|---|
|Obrigatório?|FALSO|
|Posição|com o nome|
|Valor predefinido|FALSO|
|Aceitar a entrada de tubagem?|FALSO|
|Aceitar carateres universais?|FALSO|

## <a name="remarks"></a>Observações

Para obter uma explicação completa de como utilizar o script para criar ambientes de teste de verificação e Dev Center, consulte o artigo [Utilizar o Windows PowerShell Scripts para publicar para Dev Center e ambientes de teste](vs-azure-tools-publishing-using-powershell-scripts.md).

O ficheiro de configuração de JSON Especifica os detalhes do que está a ser implementada. Inclui as informações que especificou quando criou o projeto, tal como o nome, o grupo afinidade, a imagem VHD e o tamanho da máquina virtual. Também inclui os pontos finais na máquina virtual, as bases de dados para aprovisionar, se existirem e parâmetros de implementação da web. O código seguinte mostra um ficheiro de configuração de JSON exemplo:

```
{
    "environmentSettings": {
        "cloudService": {
            "name": "myvmname",
            "affinityGroup": "",
            "location": "West US",
            "virtualNetwork": "",
            "subnet": "",
            "availabilitySet": "",
            "virtualMachine": {
                "name": "myvmname",
                "vhdImage": "a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-R2-201404.01-en.us-127GB.vhd",
                "size": "Small",
                "user": "vmuser1",
                "password": "",
                "enableWebDeployExtension": true,
                "endpoints": [
                    {
                        "name": "Http",
                        "protocol": "TCP",
                        "publicPort": "80",
                        "privatePort": "80"
                    },
                    {
                        "name": "Https",
                        "protocol": "TCP",
                        "publicPort": "443",
                        "privatePort": "443"
                    },
                    {
                        "name": "WebDeploy",
                        "protocol": "TCP",
                        "publicPort": "8172",
                        "privatePort": "8172"
                    },
                    {
                        "name": "Remote Desktop",
                        "protocol": "TCP",
                        "publicPort": "3389",
                        "privatePort": "3389"
                    },
                    {
                        "name": "Powershell",
                        "protocol": "TCP",
                        "publicPort": "5986",
                        "privatePort": "5986"
                    }
                ]
            }
        },
        "databases": [
            {
                "connectionStringName": "",
                "databaseName": "",
                "serverName": "",
                "user": "",
                "password": ""
            }
        ],
        "webDeployParameters": {
            "iisWebApplicationName": "Default Web Site"
        }
    }
}
```

Pode editar o ficheiro de configuração de JSON para alterar o que está aprovisionado. Uma máquina virtual e um serviço na nuvem são necessários, mas a secção de base de dados é opcional.
