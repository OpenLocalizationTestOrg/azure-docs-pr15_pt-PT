<properties
   pageTitle="Scripts personalizados no Linux VMs | Microsoft Azure"
   description="Automatizar tarefas de configuração de Linux VM utilizando a extensão de Script personalizado"
   services="virtual-machines-linux"
   documentationCenter=""
   authors="neilpeterson"
   manager="timlt"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure-services"
   ms.date="09/22/2016"
   ms.author="nepeters"/>

# <a name="using-the-azure-custom-script-extension-with-linux-virtual-machines"></a>Utilizar a extensão do Azure Script personalizado com máquinas virtuais de Linux

A extensão de Script personalizado transfere e executa scripts em máquinas virtuais Azure. Esta extensão é útil para a configuração de implementação de mensagem, instalação de software ou qualquer outra configuração / tarefas de gestão. Scripts podem ser transferidos a partir do Azure armazenamento ou outra localização na internet acessível ou fornecidos para a extensão de tempo de execução. A extensão de Script personalizado integra-se com os modelos de Gestor de recursos do Azure e também pode ser executada utilizando o clip do Azure, PowerShell, Azure portal ou os REST API do Azure Máquina Virtual.

Este documento detalhes de como utilizar a extensão de Script personalizado a partir do clip o Azure e um modelo de Gestor de recursos do Azure e também detalhes passos de resolução de problemas nos sistemas de Linux.

## <a name="extension-configuration"></a>Extensão de configuração

A configuração de extensão de Script personalizado especifica coisas como localização de script e o comando para ser executado. Esta configuração pode ser armazenada nos ficheiros de configuração, na linha de comandos, ou um modelo de Gestor de recursos do Azure especificado. Dados sensíveis a maiúsculas e podem ser armazenados numa configuração protegida, que é encriptada e apenas desencriptar dentro da máquina virtual. A configuração protegida é útil quando o comando de execução inclui segredos tal como uma palavra-passe.

### <a name="public-configuration"></a>Configuração pública

Esquema:

- **commandToExecute**: (necessário, cadeia) o script do ponto de entrada seja executado
- **fileUris**: (opcional, matriz de cadeia) os URLs para os ficheiros transferidos.
- **data/hora** (opcional, número inteiro) Utilize este campo apenas para acionar uma voltar a executar do script ao alterar o valor deste campo.

```none
{
  "fileUris": ["<url>"],
  "commandToExecute": "<command-to-execute>"
}
```

### <a name="protected-configuration"></a>Configuração protegida

Esquema:

- **commandToExecute**: (opcional, cadeia) o script do ponto de entrada para executar. Utilize este campo em vez disso, se o seu comando contiver segredos como palavras-passe.
- **storageAccountName**: (opcional, cadeia) o nome da conta de armazenamento. Se especificar as credenciais de armazenamento, todos os fileUris tem de ser URLs para Blobs do Azure.
- **storageAccountKey**: (opcional, cadeia) a tecla de acesso de conta de armazenamento.


```json
{
  "commandToExecute": "<command-to-execute>",
  "storageAccountName": "<storage-account-name>",
  "storageAccountKey": "<storage-account-key>"
}
```

## <a name="azure-cli"></a>Clip Azure

Quando utilizar o clip do Azure para executar a extensão de Script personalizado, crie um ficheiro de configuração ou ficheiros que contém pelo menos o uri de ficheiro e o comando de execução de script.

```none
azure vm extension set <resource-group> <vm-name> CustomScript Microsoft.Azure.Extensions 2.0 --auto-upgrade-minor-version --public-config-path /scirpt-config.json
```

Opcionalmente, o comando pode ser executado utilizando o `--public-config` e `--private-config` opção, que permite a configuração ser especificado durante a execução e sem um ficheiro de configuração em separado.

```none
azure vm extension set <resource-group> <vm-name> CustomScript Microsoft.Azure.Extensions 2.0 --auto-upgrade-minor-version --public-config '{"fileUris": ["https://gist.github.com/ahmetalpbalkan/b5d4a856fe15464015ae87d5587a4439/raw/466f5c30507c990a4d5a2f5c79f901fa89a80841/hello.sh"],"commandToExecute": "./hello.sh"}'
```

### <a name="azure-cli-examples"></a>Exemplos de clip Azure

**Exemplo 1** - configuração público com o ficheiro de script.

```json
{
  "fileUris": ["https://gist.github.com/ahmetalpbalkan/b5d4a856fe15464015ae87d5587a4439/raw/466f5c30507c990a4d5a2f5c79f901fa89a80841/hello.sh"],
  "commandToExecute": "./hello.sh"
}
```

Comando clip Azure:

```none
azure vm extension set <resource-group> <vm-name> CustomScript Microsoft.Azure.Extensions 2.0 --auto-upgrade-minor-version --public-config-path /public.json
```

**Exemplo 2** - configuração público com um ficheiro de script de nenhum.

```json
{
  "commandToExecute": "apt-get -y update && apt-get install -y apache2"
}
```

Comando clip Azure:

```none
azure vm extension set <resource-group> <vm-name> CustomScript Microsoft.Azure.Extensions 2.0 --auto-upgrade-minor-version --public-config-path /public.json
```

**Exemplo 3** - um ficheiro de configuração público é utilizada para especificar o ficheiro de script URI e um ficheiro de configuração protegida é utilizado para especificar o comando para ser executada.

Ficheiro de configuração público:

```json
{
  "fileUris": ["https://gist.github.com/ahmetalpbalkan/b5d4a856fe15464015ae87d5587a4439/raw/466f5c30507c990a4d5a2f5c79f901fa89a80841/hello.sh"],
}
```

Ficheiro de configuração protegida:  

```json
{
  "commandToExecute": "./hello.sh <password>"
}
```

Comando clip Azure:

```none
azure vm extension set <resource-group> <vm-name> CustomScript Microsoft.Azure.Extensions 2.0 --auto-upgrade-minor-version --public-config-path ./public.json --private-config-path ./protected.json
```

## <a name="resource-manager-template"></a>Modelo de Gestor de recursos

A extensão de Script do Azure personalizados podem ser executada no momento da implementação Máquina Virtual através de um modelo de Gestor de recursos. Para fazê-lo, adicione JSON devidamente formatado para o modelo de implementação.

### <a name="resource-manager-examples"></a>Exemplos de Gestor de recursos

**Exemplo 1** - configuração público.

```json
{
    "name": "scriptextensiondemo",
    "type": "extensions",
    "location": "[resourceGroup().location]",
    "apiVersion": "2015-06-15",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', parameters('scriptextensiondemoName'))]"
    ],
    "tags": {
        "displayName": "scriptextensiondemo"
    },
    "properties": {
        "publisher": "Microsoft.Azure.Extensions",
        "type": "CustomScript",
        "typeHandlerVersion": "2.0",
        "autoUpgradeMinorVersion": true,
      "settings": {
        "fileUris": [
          "https://gist.github.com/ahmetalpbalkan/b5d4a856fe15464015ae87d5587a4439/raw/466f5c30507c990a4d5a2f5c79f901fa89a80841/hello.sh"
        ],
        "commandToExecute": "sh hello.sh"
      }
    }
}
```

**Exemplo 2** - comando execução configuração protegida.

```json
{
  "name": "config-app",
  "type": "extensions",
  "location": "[resourceGroup().location]",
  "apiVersion": "2015-06-15",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', concat(variables('vmName'),copyindex()))]"
  ],
  "tags": {
    "displayName": "config-app"
  },
  "properties": {
    "publisher": "Microsoft.Azure.Extensions",
    "type": "CustomScript",
    "typeHandlerVersion": "2.0",
    "autoUpgradeMinorVersion": true,
    "settings": {
      "fileUris": [
        "https://gist.github.com/ahmetalpbalkan/b5d4a856fe15464015ae87d5587a4439/raw/466f5c30507c990a4d5a2f5c79f901fa89a80841/hello.sh
      ]              
    },
    "protectedSettings": {
      "commandToExecute": "sh hello.sh <password>"
    }
  }
}
```

Consulte o artigo o .net Core música arquivo de demonstração para um exemplo completo - [Demonstração de arquivo de música](https://github.com/neilpeterson/nepeters-azure-templates/tree/master/dotnet-core-music-linux-vm-sql-db).

## <a name="troubleshooting"></a>Resolução de problemas

Quando executa a extensão de Script personalizado, o script é criado ou transferido para um diretório semelhante ao exemplo seguinte. O resultado do comando também é guardado para este diretório na `stdout` e `stderr` ficheiro.

```none
/var/lib/azure/custom-script/download/0/
```

A extensão de Script do Azure gera um registo, que pode ser encontrado aqui.

```none
/var/log/azure/customscript/handler.log
```

Também pode ser obtido o estado de execução da extensão de Script personalizado com o clip do Azure.

```none
azure vm extension get <resource-group> <vm-name>
```

O resultado aspeto o seguinte texto:

```none
info:    Executing command vm extension get
+ Looking up the VM "scripttst001"
data:    Publisher                   Name                                      Version  State
data:    --------------------------  ----------------------------------------  -------  ---------
data:    Microsoft.Azure.Extensions  CustomScript                              2.0      Succeeded
data:    Microsoft.OSTCExtensions    Microsoft.Insights.VMDiagnosticsSettings  2.3      Succeeded
info:    vm extension get command OK
```

## <a name="next-steps"></a>Próximos passos

Para obter informações sobre outras extensões de Script VM, consulte o artigo [Descrição geral da extensão do Azure Script para Linux](./virtual-machines-linux-extensions-features.md).