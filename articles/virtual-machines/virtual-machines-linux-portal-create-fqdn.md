<properties
   pageTitle="Criar FQDN para uma VM no Azure portal | Microsoft Azure"
   description="Saiba como criar um nome de domínio completamente qualificado ou FQDN para um Gestor de recursos com base máquina virtual no portal do Azure."
   services="virtual-machines-linux"
   documentationCenter=""
   authors="iainfoulds"
   manager="timlt"
   editor="tysonn"
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure-services"
   ms.date="08/23/2016"
   ms.author="iainfou"/>

# <a name="create-a-fully-qualified-domain-name-in-the-azure-portal"></a>Criar um nome de domínio completamente qualificado no portal do Azure
Quando cria uma máquina de virtual (VM) no [portal do Azure](https://portal.azure.com) utilizando o modelo de implementação do Gestor de recursos, um recurso IP público para a máquina virtual é criado automaticamente. Utilize este endereço IP aceda a VM remotamente. Apesar do portal não criar um [nome de domínio totalmente qualificado](https://en.wikipedia.org/wiki/Fully_qualified_domain_name), ou um FQDN, por predefinição, pode adicionar um depois da VM é criada. Este artigo demonstra os passos para criar um nome de DNS ou um FQDN.

[AZURE.INCLUDE [virtual-machines-common-portal-create-fqdn](../../includes/virtual-machines-common-portal-create-fqdn.md)]

Pode agora ligar remotamente a VM utilizando este nome DNS, como com `ssh adminuser@testdnslabel.centralus.cloudapp.azure.com`.

## <a name="next-steps"></a>Próximos passos
Agora que a VM tem um nome DNS e IP público, pode implementar comuns quadros de aplicação ou serviços como o nginx MongoDB, Docker, etc.

Também pode ler mais sobre como [utilizar o Gestor de recursos](../azure-resource-manager/resource-group-overview.md) para sugestões sobre como construir nas suas implementações Azure.