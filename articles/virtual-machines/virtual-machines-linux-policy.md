<properties
    pageTitle="Aplicar políticas para máquinas virtuais do Azure Gestor de recursos | Microsoft Azure"
    description="Como aplicar uma política para uma Azure Gestor de recursos Linux Máquina Virtual"
    services="virtual-machines-linux"
    documentationCenter=""
    authors="singhkays"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="04/13/2016"
    ms.author="singhkay"/>

# <a name="apply-policies-to-azure-resource-manager-virtual-machines"></a>Aplicar políticas para máquinas virtuais do Azure Gestor de recursos

Ao utilizar as políticas, uma organização pode impor a vários convenções e regras em toda a empresa. Imposição do comportamento pretendido pode ajudar a mitigar o risco enquanto que contribuem para o sucesso da organização. Neste artigo, irá descrevemos como pode utilizar o Gestor de recursos do Azure políticas para definir o comportamento pretendido para máquinas virtuais da sua organização.

O destaque para obter os passos realizar esta tarefa está como abaixo

1. Política de Gestor de recursos do Azure 101
2. Definir uma política de Máquina Virtual
3. Criar a política
4. Aplicar a política de

## <a name="azure-resource-manager-policy-101"></a>Política de Gestor de recursos do Azure 101

Para começar a trabalhar com as políticas de Gestor de recursos do Azure, recomendamos que ler o artigo abaixo e, em seguida, prosseguir com os passos neste artigo. O artigo abaixo descreve o definição básica e a estrutura de uma política, como as políticas de obter avaliada e dá vários exemplos de definições de política.

* [Utilizar a política para gerir os recursos e controlar o acesso](../resource-manager-policy.md)

## <a name="define-a-policy-for-your-virtual-machine"></a>Definir uma política de Máquina Virtual

Uma dos cenários comuns de uma empresa poderá ser permitir apenas os seus utilizadores criar máquinas virtuais a partir de sistemas operativos específicos que foram testados para ser compatível com uma aplicação LOB. Utilizar uma política de Gestor de recursos do Azure esta tarefa pode ser feita alguns passos. Neste exemplo de política, vamos para permitir apenas Ubuntu 14.04.2-LTS máquinas virtuais para ser criado. A definição de política aspeto abaixo

```
"if": {
  "allOf": [
    {
      "field": "type",
      "equals": "Microsoft.Compute/virtualMachines"
    },
    {
      "not": {
        "allOf": [
          {
            "field": "Microsoft.Compute/virtualMachines/imagePublisher",
            "equals": "Canonical"
          },
          {
            "field": "Microsoft.Compute/virtualMachines/imageOffer",
            "equals": "UbuntuServer"
          },
          {
            "field": "Microsoft.Compute/virtualMachines/imageSku",
            "equals": "14.04.2-LTS"
          }
        ]
      }
    }
  ]
},
"then": {
  "effect": "deny"
}
```

A política acima facilmente pode ser modificada para um cenário onde poderá pretender permitir que qualquer imagem Ubuntu LTS ser utilizado para uma implementação de Máquina Virtual com o abaixo da alteração

```
{
  "field": "Microsoft.Compute/virtualMachines/imageSku",
  "like": "*LTS"
}
```

#### <a name="virtual-machine-property-fields"></a>Campos de propriedade máquina virtual

A tabela abaixo descreve as propriedades de Máquina Virtual que podem ser utilizadas como campos na sua definição de política. Para saber mais sobre os campos de política, consulte o artigo abaixo:

* [Campos e origens](../resource-manager-policy.md#fields-and-sources)


| Nome do campo     | Descrição                                        |
|----------------|----------------------------------------------------|
| imagePublisher | Especifica o publisher da imagem               |
| imageOffer     | Especifica a oferta para o Editor de imagem que selecionou |
| imageSku       | Especifica o SKU para a oferta que selecionou             |
| imageVersion   | Especifica a versão de imagem para o SKU seleccionado     |

## <a name="create-the-policy"></a>Criar a política

Uma política de facilmente pode ser criada com a API REST diretamente ou os cmdlets do PowerShell. Para criar a política, consulte o artigo abaixo:

* [Criar uma política de](../resource-manager-policy.md#creating-a-policy)


## <a name="apply-the-policy"></a>Aplicar a política de

Depois de criar a política terá de aplicá-lo no âmbito de um definido. O âmbito pode ser uma subscrição, grupo de recursos ou até mesmo recurso. Para aplicar a política, consulte o artigo abaixo:

* [Criar uma política de](../resource-manager-policy.md#applying-a-policy)
