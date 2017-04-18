<properties
    pageTitle="Expandir no local sempre em grupos de disponibilidade para Azure | Microsoft Azure"
    description="Neste tutorial utiliza recursos criados com o modelo clássico de implementação e descreve como utilizar o Assistente de adicionar réplica no SQL Server Management Studio (SSMS) para adicionar uma réplica sempre no grupo de disponibilidade no Azure."
    services="virtual-machines-windows"
    documentationCenter="na"
    authors="MikeRayMSFT"
    manager="jhubbard"
    editor=""
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="07/12/2016"
    ms.author="MikeRayMSFT" />

# <a name="extend-on-premises-always-on-availability-groups-to-azure"></a>Expandir no local sempre em grupos de disponibilidade para Azure

Sempre no disponibilidade grupos fornecem elevada disponibilidade para grupos de base de dados ao adicionar réplicas secundárias. Estas réplicas permitem a falhar sobre bases de dados em caso de uma falha. Além disso podem ser utilizadas para descarregar leitura das cargas de trabalho ou tarefas de cópia de segurança.

É possível expandir grupos de disponibilidade no local para Microsoft Azure aprovisionamento um ou mais VMs Azure com o SQL Server e, em seguida, adicionando-los como réplicas aos grupos de disponibilidade de no local.

Neste tutorial assume que tem o seguinte procedimento:

- Uma subscrição ativa do Azure. Pode [Inscrever-se para uma avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/).

- Sempre no disponibilidade grupo existente no local. Para mais informações sobre os grupos de disponibilidade, consulte o artigo [Sempre no grupos de disponibilidade](https://msdn.microsoft.com/library/hh510230.aspx).

- Conectividade entre à rede no local e a sua rede virtual Azure. Para mais informações sobre como criar esta rede virtual, consulte o artigo [configurar um Site para o Site VPN no portal do Azure clássico](../vpn-gateway/vpn-gateway-site-to-site-create.md).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

## <a name="add-azure-replica-wizard"></a>Adicionar o Assistente de réplica do Azure

Esta secção mostra-lhe como utilizar o **Assistente de réplica do Azure adicionar** para expandir a sua solução sempre no grupo de disponibilidade para incluir réplicas Azure.

1. A partir do SQL Server Management Studio, expanda **Sempre no disponibilidade elevada** > **Grupos de disponibilidade** > **[nome do seu grupo de disponibilidade]**.

1. Botão direito do rato **Réplicas de disponibilidade**, em seguida, clique em **Adicionar réplica**.

1. Por predefinição, é apresentada a **Adicionar réplica ao Assistente de grupo de disponibilidade** . Clique em **seguinte**.  Se tiver selecionado a **que não apresentam esta página novamente** opção na parte inferior da página durante uma iniciação anterior deste assistente, este ecrã não ser apresentado.

    ![SQL](./media/virtual-machines-windows-classic-sql-onprem-availability/IC742861.png)

1. Vai ser necessário ligar a todas as réplicas secundárias existentes. Pode clicar em **ligar...** junto a cada réplica ou pode clicar em **Ligar-se todos os...** na parte inferior do ecrã. Após a autenticação, clique em **seguinte** para avançar para o ecrã seguinte.

1. Na página **Especificar réplicas** , vários separadores estão listados na parte superior: **réplicas**, **pontos finais**, **As preferências de cópia de segurança**e **escuta**. A partir do separador **réplicas** , clique em **Adicionar o Azure réplica...** para iniciar o Assistente de réplica do Azure adicionar.

    ![SQL](./media/virtual-machines-windows-classic-sql-onprem-availability/IC742863.png)

1. Selecione um certificado de gestão do Azure existente a partir do arquivo de certificados local do Windows, se tiver instalado um antes. Selecione ou introduza o id de uma subscrição do Azure se tiver utilizado o anterior. Pode clicar em transferir para transferir e instalar um certificado de gestão do Azure e transferir a lista de subscrições utilizando uma conta Azure.

    ![SQL](./media/virtual-machines-windows-classic-sql-onprem-availability/IC742864.png)

1. Irão povoar cada campo na página com os valores que será utilizada para criar o Azure Máquina Virtual (VM) que irá alojar a réplica.

  	|Definição|Descrição|
|---|---|
|**Imagem**|Selecione a combinação de sistema operativo e o SQL Server pretendida|
|**Tamanho da memória virtual**|Selecionar o tamanho de VM que melhor se adequa às suas necessidades de negócio|
|**Nome VM**|Especifique um nome exclusivo para o novo VM. O nome deve conter entre 3 e 15 caracteres, pode conter apenas letras, números e hífenes e tem começar por uma letra e terminar com uma letra ou o número.|
|**VM nome de utilizador**|Especificar um nome de utilizador que irão tornar-se a conta de administrador a VM|
|**Palavra-passe de administrador VM**|Especificar uma palavra-passe para a nova conta|
|**Confirmar palavra-passe**|Confirme a palavra-passe da nova conta|
|**Rede virtual**|Especifique a rede virtual Azure que deve utilizar a nova VM. Para mais informações sobre redes virtuais, consulte o artigo [Descrição geral da rede Virtual](../virtual-network/virtual-networks-overview.md).|
|**Rede virtual sub-rede**|Especificar a sub-rede de rede virtual que deve utilizar a nova VM|
|**Domínio**|Confirme que o valor previamente povoado para o domínio está correto|
|**Nome de utilizador do domínio**|Especificar uma conta que está no grupo de administradores local em nós de local cluster|
|**Palavra-passe**|Especificar a palavra-passe para o nome de utilizador do domínio|

1. Clique em **OK** para validar as definições de implementação.

1. Legais termos são apresentados a seguir. Leia e clique em **OK** se concordar com estes termos.

1. A página **Especificar réplicas** é apresentada novamente. Verifique as definições para a nova réplica Azure nos separadores **réplicas**, **pontos finais**e **Preferências de cópia de segurança** . Modificar definições aos seus requisitos de negócio.  Para mais informações sobre os parâmetros contidas nos seguintes separadores, consulte [Especificar réplicas página (disponibilidade Assistente/Adicionar réplica Assistente de novo grupo)](https://msdn.microsoft.com/library/hh213088.aspx). Tenha em atenção que não não possível criar listeners utilizando o separador de escuta para grupos de disponibilidade que contêm réplicas Azure. Além disso, se já tiver sido criada uma escuta antes de iniciar o assistente, receberá uma mensagem a indicar que não é suportada no Azure. Vamos abordar como criar listeners na secção **criar uma escuta de grupo de disponibilidade** .

    ![SQL](./media/virtual-machines-windows-classic-sql-onprem-availability/IC742865.png)

1. Clique em **seguinte**.

1. Selecione o método de sincronização de dados que pretende utilizar na página **Selecionar sincronização de dados inicial** e clique em **seguinte**. Para a maioria dos cenários, selecione **Sincronização completa de dados**. Para mais informações sobre métodos de sincronização de dados, consulte o artigo [Selecionar dados sincronização página inicial (sempre no disponibilidade grupo Assistentes)](https://msdn.microsoft.com/library/hh231021.aspx).

1. Reveja os resultados na página **validação** . Corrigir problemas pendentes e execute novamente a validação se for necessário. Clique em **seguinte**.

    ![SQL](./media/virtual-machines-windows-classic-sql-onprem-availability/IC742866.png)

1. Reveja as definições na página de **Resumo** , em seguida, clique em **Concluir**.

1. O processo de aprovisionamento começa. Quando o Assistente de completa com êxito, clique em **Fechar** para sair do assistente.

>[AZURE.NOTE] O Assistente de réplica do Azure adicionar cria um ficheiro de registo no Users\User Name\AppData\Local\SQL Server\AddReplicaWizard. Este ficheiro de registo pode ser utilizado para resolver a falha réplica Azure híbridas. Se o assistente falhar qualquer ação em execução, todas as operações anterior são recuperadas, incluindo a VM aprovisionada a eliminação.

## <a name="create-an-availability-group-listener"></a>Criar uma escuta de grupo de disponibilidade

Depois de ter sido criado o grupo de disponibilidade, deverá criar uma escuta para clientes ligar para as réplicas. Listeners direccionam ligações de entrada para a página principal ou uma réplica secundária só de leitura. Para mais informações sobre listeners, consulte o artigo [Configurar uma escuta ILB para sempre no grupos de disponibilidade no Azure](virtual-machines-windows-classic-ps-sql-int-listener.md).

## <a name="next-steps"></a>Próximos passos

Além de utilizar o **Assistente de réplica do Azure adicionar** para expandir a sua sempre no grupo de disponibilidade ao Azure, poderá também mover alguns cargas de trabalho do SQL Server completamente para Azure. Para começar, consulte o artigo [aprovisionamento uma máquina de Virtual do SQL Server no Azure](virtual-machines-windows-portal-sql-server-provision.md).

Para obter outros tópicos relacionados com a execução do SQL Server no Azure VMs, consulte o artigo [SQL Server em máquinas virtuais do Azure](virtual-machines-windows-sql-server-iaas-overview.md).
