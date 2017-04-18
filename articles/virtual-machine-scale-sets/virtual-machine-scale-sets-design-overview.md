<properties
    pageTitle="Estruturar a escala de Máquina Virtual conjuntos de escala | Microsoft Azure"
    description="Saiba mais sobre como estruturar os conjuntos de escala de Máquina Virtual para escala"
    keywords="conjuntos de escala de máquina virtual máquina de virtual Linux," 
    services="virtual-machine-scale-sets"
    documentationCenter=""
    authors="gatneil"
    manager="madhana"
    editor="tysonn"
    tags="azure-resource-manager" />

<tags
    ms.service="virtual-machine-scale-sets"
    ms.workload="na"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/28/2016"
    ms.author="gatneil"/>

# <a name="designing-vm-scale-sets-for-scale"></a>Estruturar VM escala conjuntos de escala

Este tópico aborda considerações de estrutura para conjuntos de escala de Máquina Virtual. Para obter informações sobre o que são conjuntos de escala de Máquina Virtual, consulte a [Descrição geral de conjuntos de escala de Máquina Virtual](virtual-machine-scale-sets-overview.md).


## <a name="storage"></a>Armazenamento

Um conjunto de escala utiliza contas de armazenamento para armazenar os discos SO dos VMs no conjunto. Recomendamos que um rácio de 20 VMs por conta de armazenamento ou menos. Recomendamos também que lhe distribuir por alfabeto os carateres de início dos nomes de conta de armazenamento. Ao fazê-ajuda-o a distribuir a carga por diferentes sistemas internos. Por exemplo, no modelo de seguinte, utilizamos a função de Gestor de recursos do modelo de uniqueString para gerar hashes prefixo que são anexados a nomes de conta de armazenamento: [https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-linux-nat](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-linux-nat).


## <a name="overprovisioning"></a>Overprovisioning

Começar com a versão da API "2016-03-30", conjuntos de escala VM assume a predefinição "overprovisioning" VMs. Com overprovisioning ativada, a escala definir realmente rotações o VMs mais do que lhe pedidas, em seguida, elimina os VMs extra que fiadas o último. Overprovisioning melhora aprovisionamento taxas de sucesso. Não são faturada para estes VMs extra e não contam por defeito até os limites de quota.

Enquanto overprovisioning melhorar o aprovisionamento de taxas de sucesso, pode fazer com que comportamento confuso para uma aplicação que não foi concebido para processar VMs desaparecer inesperadas. Para ativar overprovisioning desativar, certifique-se de que tem a seguinte cadeia no seu modelo: "overprovision": "false". Podem encontrar mais detalhes na [documentação VM escala definir REST API](https://msdn.microsoft.com/library/azure/mt589035.aspx).

Se desativar overprovisioning, pode obter ausente com um rácio maior de VMs por conta de armazenamento, mas não recomendamos aceder acima 40.


## <a name="limits"></a>Limites
Todos os VHDs de disco SO dentro de uma conta de armazenamento tem de criar um conjunto de escala criada com base numa imagem personalizada (um criado por si). Como resultado, o máximo recomendado número de VMs num conjunto de escala criada com base numa imagem personalizada é 20. Se desativar overprovisioning, pode aceder até 40.

Um conjunto de escala criada com base numa imagem de plataforma está atualmente limitado a 100 VMs (Recomendamos 5 contas de armazenamento para esta escala).

Para obter mais VMs que estes limites permitem, é necessário implementar vários conjuntos de escala, conforme mostrado [neste](https://github.com/Azure/azure-quickstart-templates/tree/master/301-custom-images-at-scale)modelo.