<properties
    pageTitle="Exportar modelo de Gestor de recursos do Azure | Microsoft Azure"
    description="Utilize o Azure gerir de recursos para exportar um modelo a partir de um grupo de recursos existente."
    services="azure-resource-manager"
    documentationCenter=""
    authors="tfitzmac"
    manager="timlt"
    editor="tysonn"/>

<tags
    ms.service="azure-resource-manager"
    ms.workload="multiple"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/20/2016"
    ms.author="tomfitz"/>

# <a name="export-an-azure-resource-manager-template-from-existing-resources"></a>Exportar um modelo de Gestor de recursos do Azure de recursos existentes

Gestor de recursos permite-lhe exportar um modelo de Gestor de recursos do recursos existentes na sua subscrição. Pode utilizar esse modelo gerado para saber mais sobre a sintaxe de modelo ou para automatizar a nova implementação da sua solução conforme necessário.

É importante ter em atenção que não existem duas formas diferentes para exportar um modelo:

- Pode exportar o modelo real que utilizou para uma implementação. O modelo exportado inclui todos os parâmetros e variáveis exatamente como apareciam no modelo de apresentação original. Esta abordagem é útil quando tiver implementado recursos através do portal. Agora, que pretende ver como construir o modelo para criar os recursos.
- Pode exportar um modelo que representa o estado atual do grupo de recursos. O modelo exportado não é baseado em qualquer modelo que utilizou para implementação. Em vez disso, que cria um modelo que é um instantâneo do grupo de recursos. O modelo exportado tiver muitos valores codificada e provavelmente não tantas parâmetros, tal como faria normalmente, define. Esta abordagem é útil quando tiverem modificados o grupo de recursos através do portal ou scripts. Agora, tem de capturar o grupo de recursos, como um modelo.

Este tópico mostra as duas abordagens.

Neste tutorial, ao iniciar sessão no portal do Azure, criar uma conta de armazenamento e exportar o modelo para essa conta de armazenamento. Adicionar uma rede virtual para modificar o grupo de recursos. Por fim, exportar um novo modelo que representa o seu estado atual. Apesar deste artigo foca-se numa infraestrutura simplificada, pode utilizar estes passos para exportar um modelo para uma solução mais complicada.

## <a name="create-a-storage-account"></a>Criar uma conta de armazenamento

1. No [portal do Azure](https://portal.azure.com), selecione **Novo** > **armazenamento** > **conta de armazenamento**.

      ![criar armazenamento](./media/resource-manager-export-template/create-storage.png)

2. Crie uma conta de armazenamento com o nome de **armazenamento**, as suas iniciais e a data. O nome da conta de armazenamento tem de ser exclusivo ao longo do Azure. Se já estiver a utilizar o nome, será apresentada uma mensagem de erro indicando que o nome é utilizado. Experimente uma variação. Grupo de recursos, crie um novo grupo de recursos e o nome **ExportGroup**. Pode utilizar os valores predefinidos para as outras propriedades. Selecione **Criar**.

      ![Forneça os valores de armazenamento](./media/resource-manager-export-template/provide-storage-values.png)

A implementação poderá demorar um minuto. Depois de concluir a implementação, a sua subscrição contém a conta de armazenamento.

## <a name="view-a-template-from-deployment-history"></a>Ver um modelo a partir do histórico de implementação

1. Vá para o separador de grupo de recursos para o novo grupo de recursos. Repare que o pá mostra o resultado da última implementação. Seleccione esta hiperligação.

      ![Pá de grupo de recursos](./media/resource-manager-export-template/resource-group-blade.png)

2. Ver um histórico das híbridas para o grupo. No seu caso, a pá lista provavelmente apenas uma implementação. Selecione esta implementação.

     ![última implementação](./media/resource-manager-export-template/last-deployment.png)

3. O pá apresenta um resumo da implementação. O resumo inclui o estado de implementação e as suas operações e os valores que forneceu para parâmetros. Para ver o modelo que utilizou para a implementação, selecione o **modelo de vista**.

     ![Ver resumo de implementação](./media/resource-manager-export-template/deployment-summary.png)

4. Gestor de recursos obtém os seguintes seis ficheiros por si:

   1. **Modelo** - o modelo que define a infraestrutura de para a sua solução. Quando criou a conta de armazenamento através do portal do, Gestor de recursos utilizado um modelo de implementá-lo e guardados nesse modelo para referência futura.
   2. **Parâmetros** - um ficheiro de parâmetro que pode utilizar para passar valores durante a implementação. Contém os valores que forneceu durante a primeira implementação, mas pode alterar qualquer um destes valores quando voltar a implementar o modelo.
   3. **Clip** - An Azure comandos linha interface (CLI) ficheiro de script que pode utilizar para implementar o modelo.
   4. **PowerShell** - ficheiro de script do PowerShell do Azure que pode utilizar para implementar o modelo.
   5. **.NET** - classe de respostas .NET que pode utilizar para implementar o modelo.
   6. **Rubi** - classe Rubi A que pode utilizar para implementar o modelo.

     Os ficheiros estão disponíveis através de ligações entre o pá. Por predefinição, o pá apresenta o modelo.

       ![modelo de vista](./media/resource-manager-export-template/view-template.png)

     Vamos especial atenção para o modelo. O modelo deverá ter um aspeto semelhante a:

        {"$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#", "contentVersion": "1.0.0.0", "parâmetros": {"nome": {"tipo": "Cadeia"}, "accountType": {"tipo": "Cadeia"}, "localização": {"tipo": "Cadeia"}, "encryptionEnabled": {"ValorPredefinido": FALSO, "tipo": "Booleano"}}, "recursos": [{"tipo": "Microsoft.Storage/storageAccounts", "sku": {"nome": "[parameters('accountType')]"} "tipo": "Armazenamento", "nome": "[parameters('name')]", "apiVersion": "2016-01-01", "localização": "[parameters('location')]", "Propriedades": {"encriptação": {"serviços": {"blob": {"ativado": "[parameters('encryptionEnabled')]"}}, "keySource": "Microsoft.Storage"}}}]}
 
Este modelo é o modelo real utilizado para criar a sua conta de armazenamento. Repare que contém parâmetros permitem-lhe implementar diferentes tipos de contas de armazenamento. Para saber mais sobre a estrutura de um modelo, consulte o artigo [modelos de criação de Gestor de recursos do Azure](resource-group-authoring-templates.md). Para a lista completa das funções que pode utilizar um modelo, consulte [funções de modelo de Gestor de recursos do Azure](resource-group-template-functions.md).


## <a name="add-a-virtual-network"></a>Adicionar uma rede virtual

O modelo que transferiu na secção anterior representada a infraestrutura para esse implementação original. No entanto, não irá conta para quaisquer alterações realizadas depois da implementação.
Para ilustrar este problema, vamos modificar o grupo de recursos, adicionando uma rede virtual através do portal.

1. No pá de grupo de recursos, selecione **Adicionar**.

      ![Adicionar recurso](./media/resource-manager-export-template/add-resource.png)

2. Selecione **Rede Virtual** a partir dos recursos disponíveis.

      ![Selecione rede virtual](./media/resource-manager-export-template/select-vnet.png)

2. Atribua um nome à rede virtual **VNET**e utilizar os valores predefinidos para as outras propriedades. Selecione **Criar**.

      ![definir um alerta](./media/resource-manager-export-template/create-vnet.png)

3. Depois da rede virtual tem implementado com êxito ao seu grupo de recursos, observe novamente o histórico de implementação. Visualize duas implementações. Se não vir a segunda implementação, terá de fechar a sua pá de grupo de recursos e abri-lo novamente. Selecione a implementação mais recente.

      ![histórico de implementação](./media/resource-manager-export-template/deployment-history.png)

4. Ver o modelo para esse implementação. Repare que define-la apenas a rede virtual. Não inclui a conta de armazenamento que implementado anterior. Já não tiver um modelo que representa todos os recursos no seu grupo de recursos.

## <a name="export-the-template-from-resource-group"></a>Exportar o modelo a partir do grupo de recursos

Para obter o estado atual do seu grupo de recursos, exporte um modelo que mostra um instantâneo do grupo de recursos.  

> [AZURE.NOTE] Não pode exportar um modelo para um grupo de recursos que tem mais de 200 recursos.

1. Para ver o modelo para um grupo de recursos, selecione **script de automatização**.

      ![exportar o grupo de recursos](./media/resource-manager-export-template/export-resource-group.png)

     Nem todos os tipos de recursos de suporte a função de modelo de exportação. Se o seu grupo de recursos só contém a conta de armazenamento e a rede virtual neste artigo, não vir um erro. No entanto, se tiver criado outros tipos de recursos, poderá ver um erro a indicar que existe um problema com a exportação. Saiba como gerir esses problemas na secção [corrigir problemas de exportação](#fix-export-issues) .

      

2. Verá os seis ficheiros que pode utilizar para implementar a solução novamente, mas desta vez o modelo está um pouco diferentes. Este modelo tem apenas dois parâmetros: uma para o nome da conta de armazenamento e outra para o nome de rede virtual.

        "parameters": {
          "virtualNetworks_VNET_name": {
            "defaultValue": "VNET",
            "type": "String"
          },
          "storageAccounts_storagetf05092016_name": {
            "defaultValue": "storagetf05092016",
            "type": "String"
          }
        },

     Gestor de recursos não obter os modelos que utilizou durante a implementação. Em vez disso, gerado a um novo modelo que é baseado na configuração atual dos recursos. Por exemplo, o modelo define o valor de localização e a replicação da conta de armazenamento:

        "location": "northeurope",
        "tags": {},
        "properties": {
            "accountType": "Standard_RAGRS"
        },

3. Tem duas opções para continuar a trabalhar com este modelo. Pode transferir o modelo e trabalhar nele localmente com um editor de JSON. Em alternativa, pode guardar o modelo à sua biblioteca e trabalhar no mesmo através do portal.

     Se estiver familiarizado utilizando um editor de JSON como [Código VS](resource-manager-vs-code.md) ou [Visual Studio](vs-azure-tools-resource-groups-deployment-projects-create-deploy.md), poderá preferir transferir o modelo localmente e utilizar esse editor. Se não estão configurados com um editor de JSON, poderá preferir editar o modelo através do portal. O restante deste tópico assume que guardou o modelo à sua biblioteca no portal. No entanto, efetuar as mesma sintaxe alterações ao modelo quer localmente a trabalhar com um editor de JSON ou através do portal.

     Para trabalhar localmente, selecione **Transferir**.

      ![Transferir o modelo](./media/resource-manager-export-template/download-template.png)

     Para trabalhar através do portal do, selecione **Adicionar à biblioteca**.

      ![Adicionar à biblioteca](./media/resource-manager-export-template/add-to-library.png)

     Quando adicionar um modelo para a biblioteca, dê ao modelo um nome e descrição. Em seguida, selecione **Guardar**.

     ![valores do conjunto de modelo](./media/resource-manager-export-template/set-template-values.png)

4. Para ver um modelo guardado na sua biblioteca, selecione **mais serviços**, escreva **modelos** para filtrar os resultados, selecione **modelos**.

      ![localizar modelos](./media/resource-manager-export-template/find-templates.png)

5. Selecione o modelo com o nome que guardou.

      ![Selecione o modelo](./media/resource-manager-export-template/select-library-template.png)

## <a name="customize-the-template"></a>Personalizar o modelo

O modelo exportado ajustar funciona se pretende criar a mesma conta de armazenamento e a rede virtual para cada implementação. No entanto, o Gestor de recursos fornece opções para que pode implementar modelos com muitas mais flexibilidade. Por exemplo, durante a implementação, poderá pretender especificar o tipo de conta de armazenamento para criar ou os valores a utilizar para o prefixo do endereço de rede virtual e o prefixo de sub-rede.

Nesta secção, adicione parâmetros para o modelo exportado para que possa reutilizar o modelo quando implementar estes recursos outros ambientes. Também é adicionar algumas funcionalidades ao seu modelo para diminuir a probabilidade de se deparar com um erro quando implementar o modelo. Já não tiver adivinhar um nome exclusivo para a sua conta de armazenamento. Em vez disso, o modelo cria um nome exclusivo. Restringir os valores que podem ser especificados para o tipo de conta de armazenamento para apenas as opções válidas.

1. Selecione **Editar** para personalizar o modelo.

     ![Mostrar o modelo](./media/resource-manager-export-template/show-template.png)

1. Selecione o modelo.

     ![Editar modelo](./media/resource-manager-export-template/edit-template.png)

1. Para poder-se de que passam os valores que poderá pretender especificar durante a implementação, substitua a secção **parâmetros** novas definições de parâmetro. Repare que os valores de **allowedValues** para **storageAccount_accountType**. Se acidentalmente fornecer um valor inválido, esse erro é reconhecido antes de inicia a implementação. Além disso, repare que estão a fornecer apenas um prefixo para o nome da conta de armazenamento, o prefixo está limitado à 11 caracteres. Quando limitar o prefixo a 11 caracteres, pode garante que o nome completo não exceda o número máximo de carateres para uma conta de armazenamento. O prefixo permite-lhe aplicar uma convenção de nomenclatura às suas contas de armazenamento. Verá como criar um nome exclusivo no próximo passo.

        "parameters": {
          "storageAccount_prefix": {
            "type": "string",
            "maxLength": 11
          },
          "storageAccount_accountType": {
            "defaultValue": "Standard_RAGRS",
            "type": "string",
            "allowedValues": [
              "Standard_LRS",
              "Standard_ZRS",
              "Standard_GRS",
              "Standard_RAGRS",
              "Premium_LRS"
            ]
          },
          "virtualNetwork_name": {
            "type": "string"
          },
          "addressPrefix": {
            "defaultValue": "10.0.0.0/16",
            "type": "string"
          },
          "subnetName": {
            "defaultValue": "subnet-1",
            "type": "string"
          },
          "subnetAddressPrefix": {
            "defaultValue": "10.0.0.0/24",
            "type": "string"
          }
        },

2. A secção de **variáveis** do seu modelo está atualmente vazia. Na secção de **variáveis** , crie valores simplificam a sintaxe para o resto do seu modelo. Substitua esta secção com uma nova definição de variável. A variável de **storageAccount_name** concatena o prefixo do parâmetro para uma cadeia exclusivo que é gerado com base num identificador do grupo de recursos. Já não tiver adivinhar um nome exclusivo ao fornecer um valor de parâmetro.

        "variables": {
          "storageAccount_name": "[concat(parameters('storageAccount_prefix'), uniqueString(resourceGroup().id))]"
        },

3. Para utilizar os parâmetros e variável as definições de recursos, substitua a secção de **recursos** por novas definições de recursos. Repare que pouco tiver sido alterada nas definições recurso que não seja o valor que é atribuído para a propriedade do recurso. As propriedades são o mesmo que as propriedades do modelo exportado. Propriedades simplesmente estiver a atribuir a valores de parâmetros em vez dos valores codificada. A localização dos recursos está definida para utilizar a mesma localização, como o grupo de recursos através da expressão **resourceGroup () .location** . A variável que criou para o nome da conta de armazenamento é referenciada através da expressão de **variáveis** .

        "resources": [
          {
            "type": "Microsoft.Network/virtualNetworks",
            "name": "[parameters('virtualNetwork_name')]",
            "apiVersion": "2015-06-15",
            "location": "[resourceGroup().location]",
            "properties": {
              "addressSpace": {
                "addressPrefixes": [
                  "[parameters('addressPrefix')]"
                ]
              },
              "subnets": [
                {
                  "name": "[parameters('subnetName')]",
                  "properties": {
                    "addressPrefix": "[parameters('subnetAddressPrefix')]"
                  }
                }
              ]
            },
            "dependsOn": []
          },
          {
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[variables('storageAccount_name')]",
            "apiVersion": "2015-06-15",
            "location": "[resourceGroup().location]",
            "tags": {},
            "properties": {
                "accountType": "[parameters('storageAccount_accountType')]"
            },
            "dependsOn": []
          }
        ]

4. Selecione **OK** quando tiver terminado a editar o modelo.

5. Selecione **Guardar** para guardar as alterações ao modelo.

     ![Guardar modelo](./media/resource-manager-export-template/save-template.png)

6. Para implementar o modelo atualizado, selecione **Implementar**.

     ![implementar o modelo](./media/resource-manager-export-template/deploy-template.png)

7. Forneça os valores de parâmetros e selecione um novo grupo de recursos para implementar os recursos para.

## <a name="update-the-downloaded-parameters-file"></a>Atualizar o ficheiro transferido parâmetros

Se estiver a trabalhar com os ficheiros transferidos (em vez da biblioteca do portal), terá de atualizar o ficheiro transferido parâmetro. Já não corresponda aos parâmetros no seu modelo. Não possui a utilizar um ficheiro de parâmetro, mas -pode simplificar o processo quando voltar a implementar um ambiente. Utilize os valores predefinidos que são definidos no modelo para muitos dos parâmetros para que o seu ficheiro de parâmetro só necessita de dois valores.

Substitua o conteúdo do ficheiro parameters.json com:

```
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageAccount_prefix": {
      "value": "storage"
    },
    "virtualNetwork_name": {
      "value": "VNET"
    }
  }
}
```

O ficheiro de parâmetro actualizado fornece valores apenas para os parâmetros que não têm um valor predefinido. Pode fornecer valores para os outros parâmetros quando pretender que um valor que é a diferença entre o valor predefinido.

## <a name="fix-export-issues"></a>Corrigir problemas de exportação

Nem todos os tipos de recursos de suporte a função de modelo de exportação. Gestor de recursos especificamente não exportar alguns tipos de recursos para impedir que expor os dados confidenciais. Por exemplo, se tiver uma cadeia de ligação no seu site config, provavelmente não pretende que seja explicitamente apresentado num modelo de exportado. Pode aceder à volta deste problema ao adicionar manualmente os recursos em falta novamente no seu modelo.

> [AZURE.NOTE] Apenas a encontrar problemas de exportação ao exportar a partir de um grupo de recursos, em vez do seu histórico de implementação. Se a sua implementação última com exatidão representa o estado atual do grupo de recursos, deve exportar o modelo a partir do histórico de implementação em vez do grupo de recursos. Exporte apenas a partir de um grupo de recursos quando tiverem efetuado alterações ao grupo de recursos que não estão definidas num único modelo.

Por exemplo, se estiver a exportar um modelo para um grupo de recursos que contém uma aplicação web, base de dados SQL e cadeia de ligação a configuração do site, consulte a seguinte mensagem:

![Mostrar o erro](./media/resource-manager-export-template/show-error.png)

Selecionar a mensagem mostra-lhe exatamente não foram exportados quais os tipos de recursos. 
     
![Mostrar o erro](./media/resource-manager-export-template/show-error-details.png)

Este tópico mostra correções comuns.

### <a name="connection-string"></a>Cadeia de ligação

O recurso de web sites, adicione uma definição de para a cadeia de ligação à base de dados:

```
{
  "type": "Microsoft.Web/sites",
  ...
  "resources": [
    {
      "apiVersion": "2015-08-01",
      "type": "config",
      "name": "connectionstrings",
      "dependsOn": [
          "[concat('Microsoft.Web/Sites/', parameters('<site-name>'))]"
      ],
      "properties": {
          "DefaultConnection": {
            "value": "[concat('Data Source=tcp:', reference(concat('Microsoft.Sql/servers/', parameters('<database-server-name>'))).fullyQualifiedDomainName, ',1433;Initial Catalog=', parameters('<database-name>'), ';User Id=', parameters('<admin-login>'), '@', parameters('<database-server-name>'), ';Password=', parameters('<admin-password>'), ';')]",
              "type": "SQLServer"
          }
      }
    }
  ]
}
```    

### <a name="web-site-extension"></a>Extensão do Web site

No recurso de web site, adicione uma definição para o código de instalar:

```
{
  "type": "Microsoft.Web/sites",
  ...
  "resources": [
    {
      "name": "MSDeploy",
      "type": "extensions",
      "location": "[resourceGroup().location]",
      "apiVersion": "2015-08-01",
      "dependsOn": [
        "[concat('Microsoft.Web/sites/', parameters('<site-name>'))]"
      ],
      "properties": {
        "packageUri": "[concat(parameters('<artifacts-location>'), '/', parameters('<package-folder>'), '/', parameters('<package-file-name>'), parameters('<sas-token>'))]",
        "dbType": "None",
        "connectionString": "",
        "setParameters": {
          "IIS Web Application Name": "[parameters('<site-name>')]"
        }
      }
    }
  ]
}
```

### <a name="virtual-machine-extension"></a>Extensão de máquina virtual

Para obter exemplos extensões de máquina virtual, consulte o artigo [Amostras de configuração de extensão de VM Windows Azure](./virtual-machines/virtual-machines-windows-extensions-configuration-samples.md).

### <a name="virtual-network-gateway"></a>Gateway de rede virtual

Adicione um tipo de recurso de gateway de rede virtual.

```
{
  "type": "Microsoft.Network/virtualNetworkGateways",
  "name": "[parameters('<gateway-name>')]",
  "apiVersion": "2015-06-15",
  "location": "[resourceGroup().location]",
  "properties": {
    "gatewayType": "[parameters('<gateway-type>')]",
    "ipConfigurations": [
      {
        "name": "default",
        "properties": {
          "privateIPAllocationMethod": "Dynamic",
          "subnet": {
            "id": "[resourceId('Microsoft.Network/virtualNetworks/subnets', parameters('<vnet-name>'), parameters('<new-subnet-name>'))]"
          },
          "publicIpAddress": {
            "id": "[resourceId('Microsoft.Network/publicIPAddresses', parameters('<new-public-ip-address-Name>'))]"
          }
        }
      }
    ],
    "enableBgp": false,
    "vpnType": "[parameters('<vpn-type>')]"
  },
  "dependsOn": [
    "Microsoft.Network/virtualNetworks/codegroup4/subnets/GatewaySubnet",
    "[concat('Microsoft.Network/publicIPAddresses/', parameters('<new-public-ip-address-Name>'))]"
  ]
},
```

### <a name="local-network-gateway"></a>Gateway de rede local

Adicione um tipo de recurso de gateway de rede local.

```
{
    "type": "Microsoft.Network/localNetworkGateways",
    "name": "[parameters('<local-network-gateway-name>')]",
    "apiVersion": "2015-06-15",
    "location": "[resourceGroup().location]",
    "properties": {
      "localNetworkAddressSpace": {
        "addressPrefixes": "[parameters('<address-prefixes>')]"
      }
    }
}
```

### <a name="connection"></a>Ligação

Adicione um tipo de recurso de ligação.

```
{
    "apiVersion": "2015-06-15",
    "name": "[parameters('<connection-name>')]",
    "type": "Microsoft.Network/connections",
    "location": "[resourceGroup().location]",
    "properties": {
        "virtualNetworkGateway1": {
        "id": "[resourceId('Microsoft.Network/virtualNetworkGateways', parameters('<gateway-name>'))]"
      },
      "localNetworkGateway2": {
        "id": "[resourceId('Microsoft.Network/localNetworkGateways', parameters('<local-gateway-name>'))]"
      },
      "connectionType": "IPsec",
      "routingWeight": 10,
      "sharedKey": "[parameters('<shared-key>')]"
    }
},
```


## <a name="next-steps"></a>Próximos passos

Parabéns! Aprendeu como exportar um modelo a partir de recursos que criou no portal.

- Pode implementar um modelo através do [PowerShell](resource-group-template-deploy.md), [Azure clip](resource-group-template-deploy-cli.md)ou [REST API](resource-group-template-deploy-rest.md).
- Para ver como exportar um modelo através do PowerShell, consulte o artigo [Utilizar o PowerShell Azure com o Gestor de recursos do Azure](powershell-azure-resource-manager.md).
- Para ver como exportar um modelo através do Azure clip, consulte o artigo [utilizar o clip do Azure para Mac, Linux e o Windows com o Gestor de recursos do Azure](xplat-cli-azure-resource-manager.md).
