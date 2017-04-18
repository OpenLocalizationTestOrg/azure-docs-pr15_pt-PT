<properties
   pageTitle="Perguntas mais frequentes do Cofre de serviços de recuperação de palavras | Microsoft Azure"
   description="Esta versão das perguntas mais frequentes suporta a versão de pré-visualização público do serviço do Azure cópia de segurança. Respostas às perguntas mais frequentes sobre o agente de cópia de segurança, cópia de segurança e retenção, recuperação, segurança e outras perguntas comuns sobre a solução de cópia de segurança do Azure."
   services="backup"
   documentationCenter=""
   authors="markgalioto"
   manager="jwhit"
   editor=""
   keywords="solução de cópia de segurança; serviço de cópia de segurança"/>

<tags
   ms.service="backup"
   ms.workload="storage-backup-recovery"
     ms.tgt_pltfrm="na"
     ms.devlang="na"
     ms.topic="get-started-article"
     ms.date="10/21/2016"
     ms.author="trinadhk; markgal; jimpark;"/>

# <a name="recovery-services-vault---faq"></a>Cofre de serviços de recuperação - perguntas mais frequentes


Este artigo fornece informações específicas do Cofre de serviços de recuperação e suplementos das [Perguntas mais frequentes do Azure cópia de segurança](backup-azure-backup-faq.md). FAQ de cópia de segurança do Azure fornece o conjunto completo de perguntas e respostas sobre o serviço de cópia de segurança do Azure.  

Pode colocar questões sobre Azure cópia de segurança na secção de Disqus neste artigo ou um artigo relacionado. Também pode publicar perguntas sobre o serviço de cópia de segurança do Azure no [Fórum de debate](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazureonlinebackup).

## <a name="recovery-services-vaults-are-resource-manager-based-are-backup-vaults-classic-mode-still-supported-br"></a>Cofres de serviços de recuperação são Gestor de recursos com base. Cofres de cópia de segurança (modo clássico) continuam a ser suportados? <br/>
Sim, cofres de cópia de segurança ainda são suportadas. Crie cofres de cópia de segurança no [portal de clássica](https://manage.windowsazure.com). Crie serviços de recuperação cofres no [portal do Azure](https://portal.azure.com). No entanto Recomendamos vivamente que criar Cofre de serviços de recuperação como todos os melhoramentos futuros estará disponível apenas em serviços de recuperação cofre.

## <a name="can-i-migrate-a-backup-vault-to-a-recovery-services-vault-br"></a>Pode migrar um cofre de cópia de segurança para o Cofre serviços de recuperação? <br/>
Infelizmente não, neste momento não pode migrar os conteúdos de cofre uma cópia de segurança para um cofre de serviços de recuperação. Estamos a trabalhar sobre como adicionar esta funcionalidade, mas não está disponível como parte da pré-visualização do público.

## <a name="do-recovery-services-vaults-support-classic-vms-or-resource-manager-based-vms-br"></a>Serviços de recuperação cofres suportam VMs clássicos ou o Gestor de recursos com base VMs? <br/>
Cofres de serviços de recuperação de ambos os modelos de suportam.  Pode agregar uma VM criada no portal do clássico (que são VMs modo clássico) ou uma VM criada no portal do Azure (que são Gestor de recursos com base) para um cofre de serviços de recuperação.

## <a name="i-have-backed-up-my-classic-vms-in-backup-vault-now-i-want-to-migrate-my-vms-from-classic-mode-to-resource-manager-mode--how-can-i-backup-them-in-recovery-services-vault"></a>Posso ter cópia de segurança meu VMs clássicos num cofre de cópia de segurança. Agora quiser migrar meu VMs a partir do modo clássico para o modo de Gestor de recursos.  Como pode posso fazer cópia de segurança-los num cofre de serviços de recuperação?
Cópias de segurança das VMs clássicos num cofre de cópia de segurança não migrar automaticamente para o Cofre de serviços de recuperação quando migra as VMs clássica para o modo de Gestor de recursos. Siga estes passos de migração de cópias de segurança VM:

1. Na cópia de segurança cofre, aceda ao separador **Itens protegida** e selecione a VM. Clique em [Parar proteção](backup-azure-manage-vms-classic.md#stop-protecting-virtual-machines). Deixe *Eliminar dados de cópia de segurança associados* opção **desmarcada**.
2. Migre a máquina virtual a partir do modo clássico para o modo de Gestor de recursos. Certifique-se de armazenamento e de rede correspondente a máquina virtual também são migradas para o modo de Gestor de recursos.
3. Criar um cofre de serviços de recuperação e configurar a cópia de segurança na máquina virtual migrada utilizando a ação de **cópia de segurança** na parte superior de dashboard do cofre. Saiba mais sobre como [Ativar Cofre de serviços de recuperação de cópia de segurança](backup-azure-vms-first-look-arm.md)
