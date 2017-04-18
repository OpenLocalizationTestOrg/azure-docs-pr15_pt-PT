<properties
   pageTitle="Começar a criar um balanceador de carga interno no Gestor de recursos utilizando o portal Azure | Microsoft Azure"
   description="Saiba como criar um balanceador de carga interno no Gestor de recursos utilizando o portal Azure"
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
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/24/2016"
   ms.author="sewhee" />

# <a name="create-an-internal-load-balancer-in-the-azure-portal"></a>Criar um balanceador de carga interno no portal do Azure

[AZURE.INCLUDE [load-balancer-get-started-ilb-arm-selectors-include.md](../../includes/load-balancer-get-started-ilb-arm-selectors-include.md)]

[AZURE.INCLUDE [load-balancer-get-started-ilb-intro-include.md](../../includes/load-balancer-get-started-ilb-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)][modelo de implementação clássica](load-balancer-get-started-ilb-classic-ps.md).

[AZURE.INCLUDE [load-balancer-get-started-ilb-scenario-include.md](../../includes/load-balancer-get-started-ilb-scenario-include.md)]

## <a name="get-started-creating-an-internal-load-balancer-using-azure-portal"></a>Começar a criar um balanceador de carga interno através do portal Azure

Utilize os passos seguintes para criar um balanceador de carga interno a partir do Portal do Azure.

1. Abra um browser, navegue para o [portal do Azure](http://portal.azure.com)e inicie sessão com a sua conta Azure.
2. No lado superior esquerdo do ecrã, clique em **Novo** > **funcionamento em rede** > **Balanceador de carga**.
3. Na pá **Balanceador de carga de criar** , introduza um **nome** para o seu Balanceador de carga.
4. Em **esquema de cores**, clique em **interna**.
5. Clique em **Rede Virtual**e, em seguida, selecione a rede virtual onde pretende criar o Balanceador de carga.

    >[AZURE.NOTE] Se não vir a rede virtual que pretende utilizar, verifique a **localização** que esteja a utilizar para o Balanceador de carga e alterá-lo em conformidade.

6. Clique em **sub-rede**e, em seguida, selecione sub-rede onde pretende criar o Balanceador de carga.
7. Em **atribuição de endereços IP**, clique em **dinâmico** ou **estático**, dependendo se pretende que o endereço IP do Balanceador de carga ser corrigido (estático) ou não.

    >[AZURE.NOTE] Se selecionar utilizar um endereço IP estático, terá de fornecer um endereço para o Balanceador de carga.

8. Em **grupo de recursos** especifique o nome de um novo grupo de recursos para Balanceador de carga, ou clique em **Selecionar existente** e selecione um grupo de recursos existente.
9. Clique em **Criar**.

## <a name="configure-load-balancing-rules"></a>Configurar regras de balanceamento de carga

Após a criação do Balanceador de carga, navegue para o recurso de Balanceador de carga configurá-lo.
Tem de configurar primeiro um conjunto de dados back-end endereço e uma sonda antes de configurar uma regra de balanceamento de carga.

### <a name="step-1-configure-a-back-end-pool"></a>Passo 1: Configurar um conjunto de dados back-end

1. No portal do Azure, clique em **Procurar** > **carregar balanceadores**e, em seguida, clique em Balanceador de carga que criou acima.
2. Na pá **Definições** , clique em **conjuntos de dados back-end**.
3. Na pá **back-end conjuntos de endereços** , clique em **Adicionar**.
4. Na pá **Adicionar conjunto de back-end** , introduza um **nome** para o conjunto de dados back-end e, em seguida, clique em **OK**.

### <a name="step-2-configure-a-probe"></a>Passo 2: Configurar uma sonda

1. No portal do Azure, clique em **Procurar** > **carregar balanceadores**e, em seguida, clique em Balanceador de carga que criou acima.
2. Na pá **Definições** , clique em **sondas**.
3. Na pá **sondas** , clique em **Adicionar**.
4. Na pá **sonda de adicionar** , introduza um **nome** para a sonda.
5. Em **protocolo**, selecione **HTTP** (para web sites) ou **TCP** (para outras aplicações de TCP com base).
6. Em **porta**, especifique a porta a utilizar ao aceder à sonda.
7. Em **caminho** (para HTTP sondas apenas), especifique o caminho para utilizar como uma sonda.
8. Em **intervalo** Especifique como com frequência para sonda a aplicação.
9. Em **limiar danificado**, especifique o número de tentativas deverão falhar antes da máquina de virtual back-end está marcada como danificada.
10. Clique em **OK** para criar sonda.

### <a name="step-3-configure-load-balancing-rules"></a>Passo 3: Configurar regras de balanceamento de carga

1. No portal do Azure, clique em **Procurar** > **carregar balanceadores**e, em seguida, clique em Balanceador de carga que criou acima.
2. Na pá **Definições** , clique em **regras de balanceamento de carga**.
3. Na pá **balanceamento de carga em regras** , clique em **Adicionar**.
4. Na pá **Adicionar regra balanceamento de carga** , introduza um **nome** para a regra.
5. Em **protocolo**, selecione **HTTP** (para web sites) ou **TCP** (para outras aplicações de TCP com base).
6. Em **porta**, especifique os clientes de porta ligam ao balanceador de carga.
7. Em **porta de back-end**, especifique a porta a ser utilizados no conjunto de back-end (normalmente, a porta do Balanceador de carga e a porta de back-end são as mesmas).
8. Em **conjunto de back-end**, selecione o conjunto de back-end que criou acima.
9. Em **persistente de sessão**, selecione como pretende que sessões para persistirem.
10. Em **Idle limite de tempo (minutos)**, especifique o tempo limite inactivo.
11. Em **Flutuantes IP (direto server retorno)**, clique em **desativado** ou **ativada**.
12. Clique em **OK**.

## <a name="next-steps"></a>Próximos passos

[Configurar um modo de distribuição do Balanceador de carga](load-balancer-distribution-mode.md)

[Configurar definições de tempo limite do TCP idle para Balanceador de carga](load-balancer-tcp-idle-timeout.md)