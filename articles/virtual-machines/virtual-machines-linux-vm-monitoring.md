<properties
   pageTitle="Ativar ou desativar a monitorização de Azure VM"
   description="Descreve como ativar ou desativar a monitorização de Azure VM"
   services="virtual-machines-linux"
   documentationCenter="virtual-machines"
   authors="kmouss"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure"
   ms.date="02/08/2016"
   ms.author="kmouss"/>
   
# <a name="enable-or-disable-azure-vm-monitoring"></a>Ativar ou desativar a monitorização de Azure VM

Esta secção descreve como ativar ou desativar a monitorização em máquinas virtuais em execução no Azure. Por predefinição monitorização é activado em máquinas virtuais do Azure caso implementado a partir do [portal do Azure](https://portal.azure.com) e monitorização gráficos são fornecidos por predefinição, com um período de minutos de 1. Pode ativar ou desativar a monitorização utilizando o portal ou a Interface de comandos do Azure para Mac, Linux e Windows (Azure CLI). 

## <a name="enable--disable-monitoring-through-the-azure-portal"></a>Ativar / desativar a monitorização através do Portal do Azure
 
Pode ativar a monitorização da sua VM Azure, que fornece dados sobre a instância nos períodos de minutos de 1. (aplicam alterações de armazenamento). Dados de diagnóstico detalhadas, em seguida, estão disponíveis para a VM nos gráficos portais ou através da API. Por predefinição, o Azure portal permite-monitorização, mas pode desativá-lo conforme descrito abaixo. Pode ativar a monitorização enquanto a VM está a ser executado ou no deixou de estado.

- Abra o portal na **Azure [https://portal.azure.com](https://portal.azure.com)**

- No painel de navegação esquerdo, clique em máquinas virtuais.

- Em máquinas virtuais do lista, selecione uma instância em execução ou parada. Máquina virtual blad será aberto.

- Clique em "Todas as definições de".

- Clique em "Diagnósticos".

- Alterar o estado para ativado ou desativado. Também pode selecionar a neste pá o nível de detalhes que pretende ativar para a sua máquina virtual da monitorização.

[Azure.Note] O parâmetro diagnósticos no é a predefinição quando cria uma nova máquina virtual

![Ativar / desativar a monitorização através do Portal do Azure.][1]


## <a name="enable--disable-monitoring-with-azure-cli"></a>Ativar / desativar a monitorização com clip Azure
 
Para ativar a monitorização para um VM Azure.

- Crie um ficheiro com o nome como PrivateConfig.json com o seguinte conteúdo.
        {"storageAccountName": "a conta de armazenamento para receber dados", "storageAccountKey": "a chave da conta"}
- Execute o seguinte comando de clip Azure.

        azure vm extension set myvm LinuxDiagnostic Microsoft.OSTCExtensions 2.0 --private-config-path PrivateConfig.json

[Azure.Note] Pode alterar a partir da versão 2.0 para uma versão posterior quando se encontra disponível. 

Para obter mais detalhes sobre como configurar a monitorização de métricas e amostras, visite o documento - * *[Utilizando Linux diagnóstico extensão para os dados de diagnóstico e o desempenho do Monitor Linux VM](virtual-machines-linux-classic-diagnostic-extension.md).

<!--Image references-->
[1]: ./media/virtual-machines-linux-vm-monitoring/portal-enable-disable.png
 

