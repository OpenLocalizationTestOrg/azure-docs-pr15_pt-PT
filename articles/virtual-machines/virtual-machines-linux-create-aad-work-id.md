<properties
   pageTitle="Criar uma identidade escolar ou profissional no AAD | Microsoft Azure"
   description="Saiba como criar uma identidade escolar ou profissional no Azure Active Directory para utilizar com o seu máquinas virtuais do Linux."
   services="virtual-machines-linux"
   documentationCenter=""
   authors="squillace"
   manager="timlt"
   editor=""
   tags="azure-service-management,azure-resource-manager"/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure"
   ms.date="08/23/2016"
   ms.author="rasquill"/>

# <a name="creating-a-work-or-school-identity-in-azure-active-directory-to-use-with-linux-vms"></a>Criar uma identidade escolares ou profissionais no Azure Active Directory para utilizar com Linux VMs

Se tiver criado uma conta pessoal do Azure ou tiver uma subscrição do MSDN pessoal e criou a conta Azure tirar partido dos créditos MSDN Azure – utilizou uma identidade de *conta Microsoft* criá-lo. Excelentes funcionalidades do Azure – [modelos de grupo de recursos](../azure-resource-manager/resource-group-overview.md) é um exemplo – a necessita de uma conta escolar ou profissional (uma identidade geridos pelo Azure Active Directory) para trabalhar. Pode seguir as instruções abaixo para criar uma nova conta escolar ou profissional, uma vez que Felizmente, os aspetos melhor sobre a sua conta Azure pessoal é inclui um domínio do Azure Active Directory predefinido que pode utilizar para criar uma nova conta escolar ou profissional que pode utilizar com o Azure funcionalidades que requerem-lo.

No entanto, as alterações mais recentes fazer possíveis gerir a sua subscrição com qualquer tipo de conta Azure utilizando o `azure login` método de início de sessão interactivo descrito [aqui](../xplat-cli-connect.md). Pode utilizar esse mecanismo ou pode seguir as instruções que se seguem. Também pode [criar uma trabalho ou escola identidade no Azure Active Directory para utilizar com o Windows VMs](virtual-machines-windows-create-aad-work-id.md).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

[AZURE.INCLUDE [virtual-machines-common-create-aad-work-id](../../includes/virtual-machines-common-create-aad-work-id.md)]
