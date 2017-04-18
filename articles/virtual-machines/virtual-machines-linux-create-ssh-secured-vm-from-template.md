<properties
    pageTitle="Criar uma VM Linux utilizando um modelo de Azure | Microsoft Azure"
    description="Crie uma VM Linux no Azure utilizando um modelo de Gestor de recursos do Azure."
    services="virtual-machines-linux"
    documentationCenter=""
    authors="vlivech"
    manager="timlt"
    editor=""
    tags="azure-service-management,azure-resource-manager" />

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="10/24/2016"
    ms.author="v-livech"/>

# <a name="create-a-linux-vm-using-an-azure-template"></a>Criar uma VM Linux utilizando um modelo do Azure

Este artigo mostra-lhe como implementar rapidamente uma máquina de Virtual Linux no Azure utilizando um modelo de Azure.  Requer o artigo:

- uma conta Azure ([obter uma avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/)).

- [Clip do Azure](../xplat-cli-install.md) tem sessão iniciada com `azure login`.

- o modo de Gestor de recursos do Azure _tem de estar no_ Azure clip `azure config mode arm`.

Pode também rapidamente implementar um modelo de Linux VM utilizando o [Azure portal](virtual-machines-linux-quick-create-portal.md).

## <a name="quick-command-summary"></a>Resumo dos comandos rápida

```bash
azure group create \
-n myResourceGroup \
-l westus \
--template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json
```

## <a name="detailed-walkthrough"></a>Obter instruções detalhadas sobre

Modelos permitem-lhe criar VMs no Azure com as definições que pretende personalizar durante o lançamento, definições, como nomes de utilizador e nomes de anfitriões. Este artigo, iremos são iniciar um modelo do Azure utilizando um VM Ubuntu juntamente com um grupo de segurança de rede (NSG) com porta 22 abrir para SSH.

Modelos de Gestor de recursos Azure são ficheiros JSON que podem ser utilizados para tarefas pontual simples como iniciar uma VM Ubuntu como concluídos neste artigo.  Modelos Azure podem também ser utilizados para construir configurações Azure complexas de ambientes inteiras como uma pilha de implementação de testes, Dev Center ou de produção.

## <a name="create-the-linux-vm"></a>Criar a VM Linux

O exemplo de código seguinte mostra como chamar `azure group create` para criar um grupo de recursos e implementar um VM de Linux protegido SSH ao mesmo tempo utilizando [Este modelo de Gestor de recursos do Azure](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json). Lembre-se de que o exemplo tem de utilizar nomes que sejam únicos ao seu ambiente. Este exemplo utiliza `myResourceGroup` como o nome do grupo de recursos, e `myVM` como o nome VM.

```bash
azure group create \
--name myResourceGroup \
--location westus \
--template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json
```

O resultado deve aspeto o bloco de saída seguinte:

```bash
info:    Executing command group create
+ Getting resource group myResourceGroup
+ Creating resource group myResourceGroup
info:    Created resource group myResourceGroup
info:    Supply values for the following parameters
sshKeyData: ssh-rsa AAAAB3Nza<..ssh public key text..>VQgwjNjQ== myAdminUser@myVM
+ Initializing template configurations and parameters
+ Creating a deployment
info:    Created template deployment "azuredeploy"
data:    Id:                  /subscriptions/<..subid text..>/resourceGroups/myResourceGroup
data:    Name:                myResourceGroup
data:    Location:            westus
data:    Provisioning State:  Succeeded
data:    Tags: null
data:
info:    group create command OK
```

Esse exemplo implementado um VM utilizando o `--template-uri` parâmetro.  Também pode transferir ou criar um modelo localmente e passar o modelo utilizando o `--template-file` parâmetro com um caminho para o ficheiro de modelo como um argumento. O clip do Azure pede-lhe os parâmetros de uma ferramenta necessária ao modelo.

## <a name="next-steps"></a>Próximos passos

Procure na [Galeria de modelos](https://azure.microsoft.com/documentation/templates/) para descobrir que quadros de aplicação para implementar o seguinte.
