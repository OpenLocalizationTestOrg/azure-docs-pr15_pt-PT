<properties
    pageTitle="Ferramentas de Comunidade para a gestão de serviço do Azure a migração de Gestor de recursos do Azure"
    description="Este artigo catálogos as ferramentas que foi fornecidas pela Comunidade para o ajudar a migração dos recursos de IaaS da gestão de serviço do Azure para a pilha de Gestor de recursos do Azure."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="singhkays"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/29/2016"
    ms.author="singhkay"/>

# <a name="community-tools-for-azure-service-management-to-azure-resource-manager-migration"></a>Ferramentas de Comunidade para a gestão de serviço do Azure a migração de Gestor de recursos do Azure

Este artigo catálogos as ferramentas que foi fornecidas pela Comunidade para o ajudar a migração dos recursos de IaaS da gestão de serviço do Azure para a pilha de Gestor de recursos do Azure.

>[AZURE.NOTE]Estas ferramentas não são suportadas oficialmente pelo Microsoft Support. Por conseguinte, estiverem abertas origem no Github e estamos satisfeitos aceitar PRs para correções ou cenários adicionais. Para relatar um problema, utilize a funcionalidade de problemas de Github.
>
> Migrar com estas ferramentas irão provocar o tempo de inatividade para a sua máquina Virtual clássica. Se procura migração plataforma suportado, visite 
>
>- [Plataformas suportadas migração dos recursos de IaaS de clássico para pilha de Gestor de recursos do Azure](./virtual-machines-windows-migration-classic-resource-manager.md)
>- [Técnica vôo picado abrangente plataforma suportadas migração de clássico para o Gestor de recursos do Azure](./virtual-machines-windows-migration-classic-resource-manager-deep-dive.md)
>- [Migrar IaaS recursos de clássico para Gestor de recursos do Azure através do PowerShell do Azure](./virtual-machines-windows-ps-migration-classic-resource-manager.md)

## <a name="asm2arm"></a>ASM2ARM

Este é um módulo de script do PowerShell para migrar o **único** Máquina Virtual (VM) pilha de gestão de serviço do Azure para o Gestor de recursos do Azure pilha. 

[Ligação para a documentação de ferramenta](https://github.com/Azure/classic-iaas-resourcemanager-migration/tree/master/asm2arm)

## <a name="migaz"></a>migAz

migAz é uma opção adicional para migrar um conjunto completo de recursos do Azure Service gestão IaaS para recursos de IaaS de Gestor de recursos do Azure. A migração pode ocorrer dentro da mesma subscrição ou entre diferentes subscrições e os tipos de subscrição (ex: subscrições CSP).

[Ligação para a documentação de ferramenta](https://github.com/Azure/classic-iaas-resourcemanager-migration/tree/master/migaz)