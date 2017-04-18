<properties
   pageTitle="Pré-requisitos técnicos para a criação de uma imagem de máquina virtual do Azure Marketplace | Microsoft Azure"
   description="Compreenda os requisitos para criar e implementar uma imagem de máquina virtual ao Azure Marketplace para outras pessoas para comprar."
   services="marketplace-publishing"
   documentationCenter=""
   authors="HannibalSII"
   manager="hascipio"
   editor=""/>

<tags
  ms.service="marketplace"
  ms.devlang="na"
  ms.topic="article"
  ms.tgt_pltfrm="Azure"
  ms.workload="na"
  ms.date="04/29/2016"
  ms.author="hascipio; v-divte"/>

# <a name="technical-prerequisites-for-creating-a-virtual-machine-image-for-the-azure-marketplace"></a>Pré-requisitos técnicos para a criação de uma imagem de máquina virtual do Azure Marketplace
Leia o processo de cuidadosamente antes de começar e compreender onde e por que motivo é executado cada passo. Quanto possível, qual deve preparar a informação da sua empresa e outros dados, transferir ferramentas necessárias e/ou criar componentes técnicos antes de iniciar o processo de criação de oferta. Estes itens devem ser limpar a partir de revisão deste artigo.  

## <a name="download-needed-tools--applications"></a>Transferir necessário ferramentas e aplicações
Que deve ter os seguintes itens preparados antes de iniciar o processo:

- Dependendo de qual é o sistema operativo está a filtrar, instale o [cmdlets do Azure PowerShell](https://www.microsoft.com/web/handlers/webpi.ashx/getinstaller/WindowsAzurePowershellGet.3f.3f.3fnew.appids) ou a [ferramenta de linha de comandos interface Linux](https://go.microsoft.com/fwlink/?LinkId=253472&clcid=0x409) a partir da página de [Transferências do Azure](https://azure.microsoft.com/downloads/) .
- Instale o Explorador de armazenamento Azure a partir de CodePlex.
- Transferir e instalar a ferramenta de teste de certificação para Azure certificação:
  - [http://go.microsoft.com/fwlink/?LinkID=526913](http://go.microsoft.com/fwlink/?LinkID=526913). Precisa de um computador baseado no Windows para executar a ferramenta de certificação. Se não tiver um computador baseado no Windows disponível, pode executar a ferramenta de utilizar uma VM baseados no Windows Azure.

## <a name="platforms-supported"></a>Plataformas suportadas
Pode desenvolver baseadas no Azure VMs no Windows ou Linux. Alguns elementos do processo de publicação – como criar um compatível com o Azure disco rígido virtual (VHD) – diferentes ferramentas de utilização e os passos dependendo de qual é o sistema operativo estiver a utilizar:  

- Se estiver a utilizar Linux, consulte a secção "Criar um VHD compatível com o Azure (baseado em Linux)" do [Guia de publicação de imagem de Máquina Virtual](marketplace-publishing-vm-image-creation.md).
- Se estiver a utilizar o Windows, consulte a secção "Criar um VHD compatível com o Azure (baseados no Windows)" do [Guia de publicação de imagem de Máquina Virtual](marketplace-publishing-vm-image-creation.md).

> [AZURE.NOTE] Tem de ter acesso a uma máquina baseados no Windows para:
- Execute a ferramenta de validação de certificação.
- Crie o URL de assinatura do access VHD partilhado para a apresentação de certificação VHD.

## <a name="develop-your-vhd"></a>Desenvolver o seu VHD
Pode desenvolver Azure VHDs na nuvem ou no local:

- Baseado na nuvem desenvolvimento significa que todos os passos de desenvolvimento são executados remotamente num VHD residência no Azure.
- Necessita de desenvolvimento no local transferir um VHD e desenvolvê-lo utilizando infraestrutura no local. Apesar de esta é possível, não recomendamos. Tenha em atenção que desenvolver para Windows ou SQL no local requer que tenha as teclas de licença relevantes no local. Não pode incluir ou instalar o SQL Server depois de criar uma VM. Também tem basear a sua oferta numa imagem a partir do portal Azure SQL aprovada. Se decidir desenvolver no local, tem de executar alguns passos diferente se foram desenvolver na nuvem. Pode encontrar informações relevantes no [artigo criar uma imagem VM no local](marketplace-publishing-vm-image-creation-on-premise.md).

## <a name="next-steps"></a>Próximos passos
Agora que revisto os pré-requisitos e concluídas as tarefas necessárias, pode avançar com a sua oferta de imagem de máquina virtual como detalhadas no [Guia de publicação de imagem de Máquina Virtual](marketplace-publishing-vm-image-creation.md)de criação.

## <a name="see-also"></a>Consulte também
- [Introdução: como publicar uma oferta ao Azure Marketplace](marketplace-publishing-getting-started.md)
- [Criar uma máquina de virtual a executar o Windows no portal do Azure pré-visualização](../virtual-machines/virtual-machines-windows-hero-tutorial.md)


[link-acct-creation]:marketplace-publishing-accounts-creation-registration.md
