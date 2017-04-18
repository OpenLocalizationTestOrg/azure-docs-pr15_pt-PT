<properties 
pageTitle="Implementar o SAP comunicação IDES: EHP7 SP3 para SAP ERP 6.0 no Microsoft Azure | Microsoft Azure" 
description="Implementar o SAP comunicação IDES: EHP7 SP3 para SAP ERP 6.0 no Microsoft Azure" 
services="virtual-machines-windows" 
documentationCenter="" 
authors="hermanndms" 
manager="timlt" 
editor="" 
tags="azure-resource-manager" 
keywords=""/> 
<tags 
ms.service="virtual-machines-windows" 
ms.devlang="na" 
ms.topic="article" 
ms.tgt_pltfrm="vm-windows" 
ms.workload="infrastructure-services" 
ms.date="09/16/2016" 
ms.author="hermannd"/> 


# <a name="deploying-sap-ides-ehp7-sp3-for-sap-erp-60-on-microsoft-azure"></a>Implementar o SAP comunicação IDES: EHP7 SP3 para SAP ERP 6.0 no Microsoft Azure 

Este artigo descreve como implementar comunicação IDES: do SAP com o SQL Server e o sistema operativo Windows no Microsoft Azure através do SAP nuvem aparelho biblioteca 3.0. As capturas de ecrã a mostram o processo passo a passo. Implementar outras soluções na lista funciona da mesma forma a partir de uma perspetiva de processo. Um tem apenas selecionar uma solução diferente.

Para começar com SAP nuvem aparelho biblioteca (SAP CAL) aceda [aqui](https://cal.sap.com/). Existe um blogue do SAP sobre o novo [SAP nuvem aparelho biblioteca 3.0](http://scn.sap.com/community/cloud-appliance-library/blog/2016/05/27/sap-cloud-appliance-library-30-came-with-a-new-user-experience). 


As capturas de ecrã seguintes mostram passo a passo como implementar comunicação IDES do SAP: no Microsoft Azure. O processo funciona da mesma forma para outras soluções.


![](./media/virtual-machines-windows-sap-cal-ides-erp6-ehp7-sp3-sql/ides-pic1.jpg)

Na primeira imagem mostra todas as soluções que estão disponíveis no Microsoft Azure. Realçada solução de comunicação baseados no Windows IDES do SAP: que só está disponível no Azure foi escolhida até o processo.

![](./media/virtual-machines-windows-sap-cal-ides-erp6-ehp7-sp3-sql/ides-pic2.jpg)

Primeiro uma nova conta do SAP CAL tem de ser criado. Existem duas opções para Azure - padrão do Azure e Azure no continente China que é operado pela 21Vianet do parceiro.

![](./media/virtual-machines-windows-sap-cal-ides-erp6-ehp7-sp3-sql/ides-pic3.jpg)

Em seguida, um tem introduza o ID da subscrição Azure que pode ser encontrado no portal do Azure - Consulte também ainda mais para baixo como obtê-lo. Posteriormente um certificado de gestão Azure tem de ser transferidos.

![](./media/virtual-machines-windows-sap-cal-ides-erp6-ehp7-sp3-sql/ides-pic6.jpg)

O novo Azure um portal localiza o item "Subscrições" no lado esquerdo. Clique na mesma para mostrar todas as subscrições ativas para o utilizador.

![](./media/virtual-machines-windows-sap-cal-ides-erp6-ehp7-sp3-sql/ides-pic7.jpg)

Selecionar uma das subscrições e, em seguida, selecionar "Certificados de gestão da" explica que não existem são um novo conceito utilizando "principais de serviço" para o novo modelo de Gestor de recursos do Azure.
SAP CAL não está ainda adaptado para este novo modelo e ainda requer o modelo de "clássico" e o portal do Azure antigo para trabalhar com certificados de gestão.

![](./media/virtual-machines-windows-sap-cal-ides-erp6-ehp7-sp3-sql/ides-pic4.jpg)

Aqui um pode ver o portal do Azure antigo. O carregamento de um certificado de gestão dá SAP CAL as permissões para criar máquinas virtuais dentro de uma subscrição de cliente. Em "Subscrições" separador um pode localizar o ID da subscrição que tem de ser introduzida no portal do SAP CAL.

![](./media/virtual-machines-windows-sap-cal-ides-erp6-ehp7-sp3-sql/ides-pic5.jpg)

No separador segundo, em seguida, é possível carregar o certificado de gestão que foi transferido antes de SAP CAL.

![](./media/virtual-machines-windows-sap-cal-ides-erp6-ehp7-sp3-sql/ides-pic8.jpg)

Uma pequena caixa de diálogo é apresentado para selecionar o ficheiro de certificado transferidos.

![](./media/virtual-machines-windows-sap-cal-ides-erp6-ehp7-sp3-sql/ides-pic9.jpg)

Assim que o certificado foi carregado a ligação entre SAP CAL e o cliente Azure subscrição pode ensaiar dentro SAP CAl. Uma pequena mensagem deve pop o que lhe indica que a ligação é válida.

![](./media/virtual-machines-windows-sap-cal-ides-erp6-ehp7-sp3-sql/ides-pic10.jpg)

Após a configuração de uma conta de um tem de selecionar uma solução que deve ser implementada e cria uma instância.
Com o modo de "base", é realmente comum. Introduza um nome da instância, selecione uma região Azure e definir a palavra-passe principal para a solução.

![](./media/virtual-machines-windows-sap-cal-ides-erp6-ehp7-sp3-sql/ides-pic11.jpg)

Após algum tempo consoante o tamanho e complexidade da solução (uma estimativa é dado pela SAP CAL) é apresentado como "ativo" e prontos a utilizar. É muito simple.

![](./media/virtual-machines-windows-sap-cal-ides-erp6-ehp7-sp3-sql/ides-pic12.jpg)

Consultar alguns detalhes de uma solução pode ver o tipo de VMs foram implementadas. Neste caso, não existe uma VM Azure único do tamanho D12 que foi criado pelo SAP CAL.

![](./media/virtual-machines-windows-sap-cal-ides-erp6-ehp7-sp3-sql/ides-pic13.jpg)

No portal do Azure, a máquina virtual podem ser encontrada começando com o mesmo nome da instância que foi consta do SAP CAL.

![](./media/virtual-machines-windows-sap-cal-ides-erp6-ehp7-sp3-sql/ides-pic14.jpg)

Agora é possível ligar para a solução através do botão Ligar no portal do SAP CAL. A caixa de diálogo pequeno contém uma ligação para um guia de utilizador que descreve todas as credenciais de predefinido para trabalhar com a solução.
[Aqui](https://caldocs.hana.ondemand.com/caldocs/help/Getting_Started_Guide_IDES607MSSQL.pdf) é a ligação para o guia para a solução de comunicação IDES:.

![](./media/virtual-machines-windows-sap-cal-ides-erp6-ehp7-sp3-sql/ides-pic15.jpg)

Outra opção é iniciar sessão para a VM do Windows e comece, por exemplo, a interface gráfica do SAP pré-configurado utilizador.





