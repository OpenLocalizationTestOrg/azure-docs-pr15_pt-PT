<properties
   pageTitle="Modelo de Gestor de recursos para armazenamento | Microsoft Azure"
   description="Mostra o esquema do Gestor de recursos para implementar contas de armazenamento através de um modelo."
   services="azure-resource-manager,storage"
   documentationCenter="na"
   authors="tfitzmac"
   manager="timlt"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="04/05/2016"
   ms.author="tomfitz"/>

# <a name="storage-account-template-schema"></a>Esquema do modelo de armazenamento conta

Cria uma conta de armazenamento.

## <a name="schema-format"></a>Formato de esquema

Para criar uma conta de armazenamento, adicione o esquema que se segue à secção de recursos do seu modelo.

    {
        "type": "Microsoft.Storage/storageAccounts",
        "apiVersion": "2015-06-15",
        "name": string,
        "location": string,
        "properties": 
        {
            "accountType": string
        }
    }

## <a name="values"></a>Valores

As tabelas seguintes descrevem os valores que tem de definir no esquema.

| Nome | Valor |
| ---- | ---- |
| tipo | Enumeração<br />Obrigatório<br />**Microsoft.Storage/storageAccounts**<br /><br />O tipo de recurso para criar. |
| apiVersion | Enumeração<br />Obrigatório<br />**2015-06-15** ou **2015-05-01-pré-visualizar**<br /><br />A versão de API para utilizar para criar o recurso. | 
| nome | Cadeia<br />Obrigatório<br />Entre 3 e 24 caracteres, apenas números e letras minúsculas.<br /><br />O nome da conta de armazenamento para criar. O nome deve ser exclusivo ao longo das Azure. Considere utilizar a função [uniqueString](resource-group-template-functions.md#uniquestring) com convenção de nomenclatura, conforme mostrado no exemplo abaixo. |
| localização | Cadeia<br />Obrigatório<br />Uma região que suporta contas de armazenamento. Para determinar regiões válidos, consulte o artigo [suportadas regiões](resource-manager-supported-services.md#supported-regions).<br /><br />Região para a conta de armazenamento do anfitrião. |
| Propriedades | Objecto<br />Obrigatório<br />[objecto de propriedades](#properties)<br /><br />Um objeto que especifica o tipo de conta de armazenamento para criar. |

<a id="properties" />
### <a name="properties-object"></a>objecto de propriedades

| Nome | Valor |
| ---- | ---- | 
| accountType | Cadeia<br />Obrigatório<br />**Standard_LRS**, **Standard_ZRS**, **Standard_GRS**, **Standard_RAGRS**ou **Premium_LRS**<br /><br />O tipo de conta de armazenamento. Os valores permitidos correspondem à padrão localmente redundantes, redundantes de zona padrão, Geo-Redundant padrão, padrão Geo-Redundant acesso de leitura e Premium localmente redundantes. Para obter informações sobre estes tipos de conta, consulte o artigo [replicação de armazenamento do Windows Azure](./storage/storage-redundancy.md ). |

    
## <a name="examples"></a>Exemplos

O exemplo seguinte implementa uma conta de armazenamento padrão localmente redundantes com um nome exclusivo com base em id de grupo de recursos.

    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {},
        "variables": {},
        "resources": [
            {
                "type": "Microsoft.Storage/storageAccounts",
                "apiVersion": "2015-06-15",
                "name": "[concat('storage', uniqueString(resourceGroup().id))]",
                "location": "[resourceGroup().location]",
                "properties": 
            {
                    "accountType": "Standard_LRS"
            }
            }
        ],
        "outputs": {}
    }

## <a name="quickstart-templates"></a>Modelos de guia de introdução

Existem muitos modelos de guia de introdução, que incluem uma conta de armazenamento. Os seguintes modelos de ilustram alguns cenários comuns:

- [Criar uma conta de armazenamento padrão](https://azure.microsoft.com/documentation/templates/101-storage-account-create)
- [Implementação Simple de uma VM do Windows](https://azure.microsoft.com/documentation/templates/101-vm-simple-windows)
- [Implementação Simple de uma VM Linux](https://azure.microsoft.com/documentation/templates/101-vm-simple-linux)
- [Criar um perfil de CDN, um ponto final de CDN com uma conta de armazenamento como origem](https://azure.microsoft.com/documentation/templates/201-cdn-with-storage-account)
- [Criar um Farm do SharePoint Availabilty alta com 9 VMs utilizando a extensão de DSC Powershell](https://azure.microsoft.com/documentation/templates/sharepoint-server-farm-ha)
- [Implementação Simple de 5 nó seguro serviço ferro Cluster com WAD activado](https://azure.microsoft.com/documentation/templates/service-fabric-secure-cluster-5-node-1-nodetype-wad)
- [Criar uma Máquina Virtual a partir de uma imagem do Windows com 4 discos de dados vazia](https://azure.microsoft.com/documentation/templates/101-vm-multiple-data-disk)


## <a name="next-steps"></a>Próximos passos

- Para obter informações gerais sobre o armazenamento, consulte o artigo [Introdução ao Microsoft Azure armazenamento](./storage/storage-introduction.md).
- Por exemplo modelos que utilizam uma nova conta de armazenamento com uma Máquina Virtual, consulte o artigo [Implementar um simples Linux VM](https://azure.microsoft.com/documentation/templates/101-simple-linux-vm/) ou [Implementar uma VM simples do Windows](https://azure.microsoft.com/documentation/templates/101-simple-windows-vm/).
