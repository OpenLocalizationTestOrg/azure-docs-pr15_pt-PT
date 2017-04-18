<properties
    pageTitle="Migrar IaaS recursos de clássico para o Gestor de recursos do Azure utilizando o clip do Azure | Microsoft Azure"
    description="Este artigo explica a migração suportadas plataforma dos recursos de clássico para o Gestor de recursos do Azure utilizando o clip do Azure"
    services="virtual-machines-linux"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/19/2016"
    ms.author="cynthn"/>

# <a name="migrate-iaas-resources-from-classic-to-azure-resource-manager-by-using-azure-cli"></a>Migrar IaaS recursos de clássico para o Gestor de recursos do Azure utilizando o clip do Azure

Estes passos mostram-lhe como utilizar comandos de interface de comandos Azure (CLI) para migrar infraestrutura como um serviço (IaaS) recursos a partir do modelo de implementação clássico para o modelo de implementação do Azure o Gestor de recursos. O artigo requer o [Clip do Azure](../xplat-cli-install.md).

>[AZURE.NOTE] Todas as operações descritas aqui ficam idempotent. Se tiver um problema diferente de uma funcionalidade não suportada ou um erro de configuração, recomendamos que repita preparar, interrupção ou a consolidação de operação. A plataforma irá, em seguida, tente novamente a ação.

## <a name="step-1-prepare-for-migration"></a>Passo 1: Preparar a migração

Eis alguns procedimentos recomendados que recomendamos como avaliar o migrar de IaaS de recursos a partir do clássico para o Gestor de recursos:

- Leia a [lista de configurações não suportadas ou funcionalidades](virtual-machines-windows-migration-classic-resource-manager.md). Se tiver máquinas virtuais que utilizam configurações não suportadas ou funcionalidades, recomendamos que espera para o suporte técnico da/a configuração de funcionalidades ser anunciada. Em alternativa, pode remover essa funcionalidade ou retirar que a configuração para ativar a migração, se se adequa às suas necessidades.
-   Se tiver automatizado scripts implementar a infraestrutura e as aplicações hoje, tente criar uma configuração de teste semelhante ao utilizar esses scripts para migração. Em alternativa, pode configurar ambientes de exemplo, utilizando o portal do Azure.

## <a name="step-2-set-your-subscription-and-register-the-provider"></a>Passo 2: Configurar a sua subscrição e registar o fornecedor

Cenários de migração, terá de configurar o seu ambiente para ambas as clássica e o Gestor de recursos. [Instalar o clip do Azure](../xplat-cli-install.md) e [Selecione a sua subscrição](../xplat-cli-connect.md).

Iniciar sessão para a sua conta.
    
    azure login

Selecione a subscrição Azure utilizando o comando seguinte.

    azure account set "<azure-subscription-name>"

>[AZURE.NOTE] O registo é um passo de tempo mas ser necessário uma vez antes de tentar migração. Sem registar verá a seguinte mensagem de erro 

>   *BadRequest: Subscrição não está registada para a migração.* 

Registe-se com o fornecedor de recursos de migração, utilizando o comando seguinte. Tenha em atenção que em alguns casos, este comando sempre. No entanto, o registo será bem sucedido.

    azure provider register Microsoft.ClassicInfrastructureMigrate

Aguarde cinco minutos para o registo concluir. Pode verificar o estado da aprovação utilizando o comando seguinte. Certifique-se de que está RegistrationState `Registered` antes de continuar.

    azure provider show Microsoft.ClassicInfrastructureMigrate

Agora mudar clip para o `asm` modo.

    azure config mode asm

## <a name="step-3-make-sure-you-have-enough-azure-resource-manager-virtual-machine-cores-in-the-azure-region-of-your-current-deployment-or-vnet"></a>Passo 3: Certifique-se de que tem o suficiente núcleos máquina de Virtual do Gestor de recursos do Azure na região da sua implementação atual ou VNET Azure

Para este passo irá precisar de alternar para `arm` modo. Fazê-lo com o seguinte comando.

```
azure config mode arm
```

Pode utilizar o seguinte comando clip para verificar a quantidade atual de núcleos que tiver no Gestor de recursos do Azure. Para saber mais acerca de quotas principais, consulte o artigo [limites e o Gestor de recursos do Azure](../articles/azure-subscription-service-limits.md#limits-and-the-azure-resource-manager)

```
azure vm list-usage -l "<Your VNET or Deployment's Azure region"
```

Quando terminar de verificar este passo, pode mudar para o `asm` modo.

    azure config mode asm


## <a name="step-4-option-1---migrate-virtual-machines-in-a-cloud-service"></a>Passo 4: Opção 1 - migrar máquinas virtuais num serviço de nuvem 

Obter a lista de serviços em nuvem utilizando o seguinte comando e, em seguida, escolha o serviço de nuvem que pretende migrar. Tenha em atenção que, se VMs no serviço de nuvem estiverem numa rede virtual ou se tiverem funções web/trabalhador, irá obter uma mensagem de erro.

    azure service list

Execute o seguinte comando para obter o nome de implementação para o serviço de nuvem a partir do resultado verboso. Na maioria dos casos, o nome de implementação é o mesmo que o nome do serviço na nuvem.

    azure service show <serviceName> -vv

Prepare as máquinas virtuais no serviço de nuvem para migração. Tem duas opções para escolher a partir de.

Se pretender migrar as VMs a uma rede virtual criado de plataforma, utilize o seguinte comando.

    azure service deployment prepare-migration <serviceName> <deploymentName> new "" "" ""

Se pretender migrar a uma rede virtual existente no modelo de implementação de Gestor de recursos, utilize o seguinte comando.

    azure service deployment prepare-migration <serviceName> <deploymentName> existing <destinationVNETResourceGroupName> subnetName <vnetName>

Depois da operação de preparação é efetuada com êxito, pode percorrer o resultado para obter o estado de migração das VMs e certifique-se de que estão no verboso a `Prepared` estado.

    azure vm show <vmName> -vv

Verifique a configuração para os recursos preparadas utilizando clip ou o portal do Azure. Se não estiver pronto para migração e voltar para o estado antigo, utilize o seguinte comando.

    azure service deployment abort-migration <serviceName> <deploymentName>

Se a configuração preparada agrado, pode avançar e consolidar os recursos utilizando o comando seguinte.

    azure service deployment commit-migration <serviceName> <deploymentName>


    
## <a name="step-4-option-2----migrate-virtual-machines-in-a-virtual-network"></a>Passo 4: Opção 2 - migrar máquinas virtuais numa rede virtual

Escolha a rede virtual que pretende migrar. Tenha em atenção que, se a rede virtual contiver funções web/trabalhador ou VMs com configurações não suportadas, obterá uma mensagem de erro de validação.

Obter todas as redes virtuais na subscrição utilizando o seguinte comando.

    azure network vnet list
    
O resultado terá o aspeto algo parecido com:

![Captura de ecrã da linha de comandos com o nome de toda a rede virtual realçada.](./media/virtual-machines-linux-cli-migration-classic-resource-manager/vnet.png)

No exemplo acima, a **virtualNetworkName** é o nome completo **"Grupo classicubuntu16 classicubuntu16"**.

Prepare a rede da sua preferência virtual migração utilizando o comando seguinte.

    azure network vnet prepare-migration <virtualNetworkName>

Verifique a configuração para as máquinas virtuais preparadas utilizando clip ou o portal do Azure. Se não estiver pronto para migração e voltar para o estado antigo, utilize o seguinte comando.

    azure network vnet abort-migration <virtualNetworkName>

Se a configuração preparada agrado, pode avançar e consolidar os recursos utilizando o comando seguinte.

    azure network vnet commit-migration <virtualNetworkName>

## <a name="step-5-migrate-a-storage-account"></a>Passo 5: Migrar uma conta de armazenamento

Quando terminar de migrar as máquinas virtuais, recomendamos que migra a conta de armazenamento.

Preparar a conta de armazenamento migração utilizando o comando seguinte

    azure storage account prepare-migration <storageAccountName>

Verifique a configuração da conta de armazenamento preparada utilizando clip ou o portal do Azure. Se não estiver pronto para migração e voltar para o estado antigo, utilize o seguinte comando.

    azure storage account abort-migration <storageAccountName>

Se a configuração preparada agrado, pode avançar e consolidar os recursos utilizando o comando seguinte.

    azure storage account commit-migration <storageAccountName>

## <a name="next-steps"></a>Próximos passos

- [Plataformas suportadas migração de IaaS recursos a partir do clássico para o Gestor de recursos](virtual-machines-windows-migration-classic-resource-manager.md)
- [Técnico abrangente vôo picado no suportadas plataforma migração de clássico para o Gestor de recursos](virtual-machines-windows-migration-classic-resource-manager-deep-dive.md)
