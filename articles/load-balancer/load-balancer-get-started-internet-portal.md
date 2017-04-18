<properties
   pageTitle="Criar um balanceador de carga de acesso à Internet no Gestor de recursos utilizando o portal Azure | Microsoft Azure"
   description="Saiba como criar um balanceador de carga de acesso à Internet no Gestor de recursos utilizando o portal Azure"
   services="load-balancer"
   documentationCenter="na"
   authors="anavinahar"
   manager="narayan"
   editor=""
   tags="azure-resource-manager"
/>
<tags
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/14/2016"
   ms.author="annahar" />

# <a name="creating-an-internet-facing-load-balancer-using-the-azure-portal"></a>Criar um balanceador de carga de acesso à Internet através do portal Azure

[AZURE.INCLUDE [load-balancer-get-started-internet-arm-selectors-include.md](../../includes/load-balancer-get-started-internet-arm-selectors-include.md)]

[AZURE.INCLUDE [load-balancer-get-started-internet-intro-include.md](../../includes/load-balancer-get-started-internet-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]Este artigo abrange o modelo de implementação do Gestor de recursos. Pode também [saber como criar um balanceador de carga de acesso à Internet utilizando implementação clássica](load-balancer-get-started-internet-classic-portal.md)

[AZURE.INCLUDE [load-balancer-get-started-internet-scenario-include.md](../../includes/load-balancer-get-started-internet-scenario-include.md)]

Isto abrange a sequência de tarefas individuais que tem de ser executadas para criar um balanceador de carga e explicam em detalhe, o que está a ser feito para executar o objetivo.

## <a name="what-is-required-to-create-an-internet-facing-load-balancer"></a>O que é necessário para criar um balanceador de carga de acesso à Internet?

Tem de criar e configurar os seguintes objectos para implementar um balanceador de carga.

- Configuração de IP Front-end - contém endereços IP públicos para o tráfego de rede recebido.

- Conjunto de endereços de back-end - contém interfaces de rede (NIC) para as máquinas virtuais para receber o tráfego de rede de Balanceador de carga.

- Regras de balanceamento de carga - contém regras de mapeamento uma porta pública no balanceador de carga à porta no conjunto de endereços de back-end.

- NAT regras de entrada - contém regras mapear uma porta pública no balanceador de carga para uma porta para uma máquina virtual específica no conjunto de endereços de back-end.

- Sondas - contém sondas de estado de funcionamento utilizadas para verificar a disponibilidade de instâncias de máquinas virtuais no conjunto de endereços de back-end.

Pode aceder a mais informações sobre como carregar componentes balanceador com o Gestor de recursos do Azure na [Azure o Gestor de recursos de suporte para Balanceador de carga](load-balancer-arm.md).


## <a name="set-up-a-load-balancer-in-azure-portal"></a>Configurar um balanceador de carga no portal do Azure

> [AZURE.IMPORTANT] Este exemplo assume que tem uma rede virtual denominado **myVNet**. Referem-se para [criar rede virtual](../virtual-network/virtual-networks-create-vnet-arm-pportal.md) para o fazer. Assume também existe uma sub-rede dentro **myVNet** denominado **LB sub-rede ser** e duas VMs denominado **web1** e **web2** , respetivamente, dentro do mesmo conjunto de disponibilidade designado **myAvailSet** no **myVNet**. Consulte [esta ligação](../virtual-machines/virtual-machines-windows-hero-tutorial.md) para criar VMs.


1. A partir de um browser, navegue para o portal do Azure: [http://portal.azure.com](http://portal.azure.com) e inicie sessão com a sua conta Azure.

2. No lado superior esquerdo do ecrã, selecione **Novo** > **funcionamento em rede** > **Balanceador de carga.**

3. Na pá **Balanceador de carga de criar** , escreva um nome para o seu Balanceador de carga. Aqui é chamado **myLoadBalancer**.

4. Em **tipo**, selecione **público**.

5. Em **endereço IP público**, crie um novo IP público denominado **myPublicIP**.

6. Em grupo de recursos, selecione **myRG**. Em seguida, selecione uma **localização**de adequada e, em seguida, clique em **OK**. O Balanceador de carga, em seguida, será iniciado implementar e irá demorar alguns minutos para concluir a implementação com êxito.

![Atualizar o grupo de recursos do Balanceador de carga](./media/load-balancer-get-started-internet-portal/1-load-balancer.png)


## <a name="create-a-back-end-address-pool"></a>Criar um conjunto de dados back-end endereço

1. Assim que a sua Balanceador de carga tem implementado com êxito, selecione-a partir de dentro dos recursos. Em definições, selecione conjuntos de dados back-end. Escreva um nome para o conjunto de dados back-end. Em seguida, clique no botão **Adicionar** na parte superior da pá que aparece.

2. Clique em **Adicionar uma máquina virtual** na pá a **Adicionar conjunto de back-end** .  Selecione **Escolher um conjunto de disponibilidade** em **conjunto de disponibilidade** e selecione **myAvailSet**. Em seguida, selecione **Escolher as máquinas virtuais** na secção máquinas virtuais na pá e clique em **web1** e **web2**, as duas VMs criados para balanceamento de carga. Certifique-se de que ambos tem azuis marcas de verificação à esquerda, conforme mostrado na imagem abaixo. Em seguida, clique em **Seleccionar** nesse pá seguido OK na pá **máquinas virtuais escolher** e, em seguida, **OK** na pá **Adicionar conjunto de back-end** .

    ![Adição ao conjunto de endereço de back-end- ](./media/load-balancer-get-started-internet-portal/3-load-balancer-backend-02.png)

3. Certifique-se de que as notificações de lista pendente tem uma atualização relativo a guardar o conjunto de back-end do Balanceador de carga para além de atualizar a interface de rede de VMs **web1** e **web2**.


## <a name="create-a-probe-lb-rule-and-nat-rules"></a>Criar uma sonda, LB regra e NAT regras

1. Crie uma sonda de estado de funcionamento.

    Em definições do Balanceador de carga, selecione sondas. Em seguida, clique em **Adicionar** localizada na parte superior da pá.

    Existem duas formas para configurar uma sonda: HTTP ou TCP. Este exemplo mostra HTTP, mas TCP pode ser configuradas de forma semelhante.
    Atualize as informações necessárias. Tal como mencionado, **myLoadBalancer** irá carregar o tráfego de saldo na porta 80. O caminho selecionado é HealthProbe.aspx, intervalo é 15 segundos e limiar danificado é 2. Assim que tiver terminado, clique em **OK** para criar a sonda.

    Pairar o ponteiro sobre o i ícone para saber mais sobre estas configurações individuais e como pode ser alterados para satisfazer com os seus requisitos.

    ![Adicionar uma sonda](./media/load-balancer-get-started-internet-portal/4-load-balancer-probes.png)

2. Crie uma regra de Balanceador de carga.

    Clique em regras na secção definições do Balanceador de carga de balanceamento de carga. No pá novo, clique em **Adicionar**. Dê um nome a regra. Aqui, é HTTP. Escolha o front-end porta e porta de back-end. Aqui, 80 é escolhido para ambos. Selecione **LB-back-end** como o seu conjunto de dados back-end e o criado anteriormente **HealthProbe** como a sonda. Outras configurações podem ser definidas de acordo com as suas necessidades. Em seguida, clique em OK para guardar a regra balanceamento de carga.

    ![Adicionar uma regra de balanceamento de carga](./media/load-balancer-get-started-internet-portal/5-load-balancing-rules.png)

3. Criar regras de entrada NAT

    Clique em regras de entrada NAT na secção definições da sua Balanceador de carga. Na pá novo, clique em **Adicionar**. Em seguida, atribua um nome a regra NAT entrada. Aqui é chamado **inboundNATrule1**. O destino deve ser o endereço IP público criado anteriormente. Selecione Custom sob o serviço e selecione o protocolo que pretende utilizar. Aqui está selecionada TCP. Introduza a porta, 3441 e a porta de destino, neste caso, 3389. em seguida, clique em OK para guardar esta regra.

    Assim que a primeira regra é criada, repita este passo para a segunda regra NAT entrada chamada inboundNATrule2 a partir de porta 3442 à porta de destino 3389.

    ![Adicionar uma regra de NAT entrada](./media/load-balancer-get-started-internet-portal/6-load-balancer-inbound-nat-rules.png)

## <a name="remove-a-load-balancer"></a>Remover um balanceador de carga

Para eliminar um balanceador de carga, selecione o Balanceador de carga que pretende remover. No pá *Balanceador de carga* , clique em **Eliminar** localizada na parte superior da pá. Em seguida, selecione **Sim** quando lhe for pedido.

## <a name="next-steps"></a>Próximos passos

[Começar a configurar uma Balanceador de carga interno](load-balancer-get-started-ilb-arm-cli.md)

[Configurar um modo de distribuição do Balanceador de carga](load-balancer-distribution-mode.md)

[Configurar definições de tempo limite do TCP idle para Balanceador de carga](load-balancer-tcp-idle-timeout.md)
