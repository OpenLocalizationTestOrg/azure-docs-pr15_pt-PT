<properties
   pageTitle="Aplicar a encriptação do disco no Centro de segurança do Azure | Microsoft Azure"
   description="Este documento mostra-lhe como implementar o recomendação do Centro de segurança do Azure **Aplicar encriptação de disco**."
   services="security-center"
   documentationCenter="na"
   authors="TerryLanfear"
   manager="MBaldwin"
   editor=""/>

<tags
   ms.service="security-center"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/29/2016"
   ms.author="terrylan"/>

# <a name="apply-disk-encryption-in-azure-security-center"></a>Aplicar a encriptação do disco no Centro de segurança do Azure

Centro de segurança do Azure irá Recomendamos que aplique encriptação do disco se tiver o Windows ou Linux VM discos que não são encriptados utilizar encriptação do Azure do disco. Encriptação do disco permite-lhe encriptar discos do Windows e Linux IaaS VM.  Encriptação é recomendada para os volumes o sistema operativo e os dados no seu VM.


Encriptação do disco tira partido a funcionalidade de [BitLocker](https://technet.microsoft.com/library/cc732774.aspx) padrão do setor do Windows e a funcionalidade de [Encriptação DM](https://en.wikipedia.org/wiki/Dm-crypt) do Linux para fornecer o sistema operativo e encriptação de dados para o ajudar a proteger e proteger os seus dados e respeitar os seus compromissos de segurança e conformidade organizacionais. Encriptação do disco está integrada com o [Azure chave cofre](https://azure.microsoft.com/documentation/services/key-vault/) para ajudá-lo a controlar e gerir as chaves de encriptação do disco e segredos na sua subscrição do cofre chave, garantindo que todos os dados no discos VM são encriptados em repouso no seu [Armazenamento do Windows Azure](https://azure.microsoft.com/documentation/services/storage/).

> [AZURE.NOTE] Encriptação do disco Azure é suportada nos seguintes Windows server sistemas operativos - Windows Server 2008 R2, Windows Server 2012 e Windows Server 2012 R2. Encriptação do disco é suportada no seguintes Linux sistemas operativos do servidor - Ubuntu, CentOS, SUSE e SUSE Linux Enterprise Server (SLES).

## <a name="implement-the-recommendation"></a>Implementar a recomendação

1. Na pá **recomendações** , selecione **Aplicar encriptação de disco**.
2. No pá **Aplicar encriptação de disco** , irá ver uma lista de VMs para as quais é recomendável encriptação do disco.
3. Siga as instruções para aplicar a encriptação a estes VMs.

![][1]

Para encriptar máquinas virtuais do Azure que foram consideradas ao centro de segurança necessitar de encriptação, recomendamos que os seguintes passos:

- Instalar e configurar o Azure PowerShell. Isto permite-lhe executar os comandos do PowerShell necessários para configurar os pré-requisitos necessários para encriptar máquinas virtuais do Azure.
- Obter e executar o script do PowerShell do Azure disco encriptação pré-requisitos Azure.
- Encripte máquinas virtuais.

[Encriptar uma máquina de Virtual Azure](security-center-disk-encryption.md) irá guiá-lo ao seguir estes passos.  Este tópico assume que está a utilizar o Windows 10 como computador do cliente a partir do qual irá configurar encriptação do disco.

Existem muitos abordagens que podem ser utilizadas para as pré-requisitos de configuração e para configurar a encriptação para máquinas virtuais do Azure. Se já estiverem bem versed no Azure PowerShell ou clip de Azure, em seguida, poderá preferir utilizar abordagens alternativas. Para obter informações sobre estas outras abordagens consulte [encriptação Azure do disco](../security/azure-security-disk-encryption.md).



## <a name="see-also"></a>Consulte também

Este documento mostrava como implementar o recomendação do Centro de segurança "Encriptação de disco aplicar". Para saber mais sobre encriptação do disco, consulte o seguinte:

- [Encriptação e gestão de chaves com Cofre de chave do Azure](https://azure.microsoft.com/documentation/videos/azurecon-2015-encryption-and-key-management-with-azure-key-vault/) (vídeo, 36 mínimo 39 sec) – Saiba como utilizar a gestão de encriptação do disco para IaaS VMs e Azure chave cofre para ajudar a proteger e proteger os seus dados.
- [Encriptar uma Máquina Virtual Azure](security-center-disk-encryption.md) (documento) – Saiba como encriptar máquinas virtuais do Azure.
- [Encriptação Azure do disco](../security/azure-security-disk-encryption.md) (documento) – Saiba como ativar encriptação do disco para Windows e Linux VMs.

Para saber mais sobre o Centro de segurança, consulte o seguinte:

- [Definir políticas de segurança no Centro de segurança do Azure](security-center-policies.md) – Saiba como configurar políticas de segurança.
- [Monitorização de estado de funcionamento de segurança no Centro de segurança do Azure](security-center-monitoring.md) – Saiba como monitorizar o estado de funcionamento dos seus recursos Azure.
- [Gerir e a responder a alertas de segurança no Centro de segurança do Azure](security-center-managing-and-responding-alerts.md) – Saiba como gerir e responder a alertas de segurança.
- [Gerir as recomendações de segurança no Centro de segurança do Azure](security-center-recommendations.md) – Saiba como recomendações ajudam a proteger os recursos do Azure.
- [Perguntas mais frequentes do Centro de segurança do azure](security-center-faq.md) localizar perguntas mais frequentes sobre como utilizar o serviço.
- Mensagens de [blogue do azure Security](http://blogs.msdn.com/b/azuresecurity/) – localizar blogue sobre Azure segurança e conformidade.



<!--Image references-->
[1]: ./media/security-center-apply-disk-encryption/apply-disk-encryption.png
