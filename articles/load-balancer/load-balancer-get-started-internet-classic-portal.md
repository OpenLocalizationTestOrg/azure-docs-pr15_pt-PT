
<properties
   pageTitle="Começar a criar uma Internet opostas Balanceador de carga no modelo de implementação clássica através do portal clássico Azure | Microsoft Azure"
   description="Saiba como criar um opostas Balanceador de carga no modelo de implementação clássica através do portal clássico Azure da Internet"
   services="load-balancer"
   documentationCenter="na"
   authors="sdwheeler"
   manager="carmonm"
   editor=""
   tags="azure-service-management"
/>
<tags
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/31/2016"
   ms.author="sewhee" />

# <a name="get-started-creating-an-internet-facing-load-balancer-classic-in-the-azure-classic-portal"></a>Começar a criar um opostas Balanceador de carga (clássico) no portal do Azure clássico da Internet

[AZURE.INCLUDE [load-balancer-get-started-internet-classic-selectors-include.md](../../includes/load-balancer-get-started-internet-classic-selectors-include.md)]

[AZURE.INCLUDE [load-balancer-get-started-internet-intro-include.md](../../includes/load-balancer-get-started-internet-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]Este artigo abrange o modelo de implementação clássica. Pode também [saber como criar um opostas Balanceador de carga de utilizar o Gestor de recursos do Azure da Internet](load-balancer-get-started-internet-arm-ps.md).

[AZURE.INCLUDE [load-balancer-get-started-internet-scenario-include.md](../../includes/load-balancer-get-started-internet-scenario-include.md)]


## <a name="set-up-an-internet-facing-load-balancer-for-virtual-machines"></a>Configurar um balanceador de carga de acesso à Internet para máquinas virtuais

Para carregar o tráfego de rede saldo a partir da Internet em máquinas virtuais de um serviço na nuvem, terá de criar um conjunto de balanceamento de carga. Este procedimento assume que já criou as máquinas virtuais e que estão todas as dentro do serviço de nuvem mesmo.

**Para configurar um conjunto de balanceamento de carga para máquinas virtuais**

1. No portal do Azure clássico, clique em **máquinas virtuais**e, em seguida, clique no nome de uma máquina virtual no conjunto de balanceamento de carga.

2. Clique em **pontos finais**e, em seguida, clique em **Adicionar**.

3. Na página **Adicionar um ponto final para uma máquina virtual** , clique na seta à direita.

4. Na página **especificar os detalhes do ponto final** :

    * Em **nome**, escreva um nome para o ponto final ou selecione o nome da lista de pontos finais predefinidos para protocolos comuns.
    * Em **protocolo**, selecione o protocolo requerido pelo tipo de ponto final, TCP ou UDP, conforme necessário.
    * Em **portas públicas e privadas**, escreva os números de porta que pretende que a máquina virtual para utilizar, conforme necessário. Pode utilizar a porta privada e as regras de firewall na máquina virtual para redirecionar o tráfego de uma forma que altura é adequado para a sua aplicação. A porta privada pode ser igual a porta pública. Por exemplo, para um ponto final para o tráfego de web (HTTP), poderá atribuir porta 80 a porta pública e privada.

5. Selecione **criar um conjunto de balanceamento de carga**e, em seguida, clique na seta à direita.

6. Na página **configurar o conjunto de balanceamento de carga** , escreva um nome para o conjunto de balanceamento de carga e, em seguida, atribua os valores para o comportamento de sonda do Balanceador de carga Azure. Balanceador de carga utiliza sondas para determinar se as máquinas virtuais do conjunto de balanceamento de carga estão disponíveis recebam tráfego de entrada.

7. Clique na marca de verificação para criar o ponto final de balanceamento de carga. Verá **Sim** na coluna **nome do conjunto de balanceamento de carga** da página **pontos finais** para a máquina virtual.

8. No portal do, clique em **máquinas virtuais**, clique no nome de uma máquina virtual adicional no conjunto de balanceamento de carga, clique em **pontos finais**e, em seguida, clique em **Adicionar**.

9. Na página **Adicionar um ponto final para uma máquina virtual** , clique em **ponto final de adicionar a um conjunto de balanceamento de carga existente**, selecione o nome do conjunto de balanceamento de carga e, em seguida, clique na seta à direita.

10. Na página **especificar os detalhes do ponto final** , escreva um nome para o ponto final e, em seguida, clique na marca de verificação.

Para adicionais máquinas virtuais do conjunto de balanceamento de carga, repita os passos 8-10.



## <a name="next-steps"></a>Próximos passos

[Começar a configurar uma Balanceador de carga interno](load-balancer-get-started-ilb-arm-ps.md)

[Configurar um modo de distribuição do Balanceador de carga](load-balancer-distribution-mode.md)

[Configurar definições de tempo limite do TCP idle para Balanceador de carga](load-balancer-tcp-idle-timeout.md)

