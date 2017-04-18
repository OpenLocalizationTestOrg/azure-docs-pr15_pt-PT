<properties 
   pageTitle="Implementar uma VM com um endereço IP público estático através do portal Azure no Gestor de recursos | Microsoft Azure"
   description="Saiba como implementar VMs com um endereço IP público estático utilizando o portal de zure no Gestor de recursos"
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"
/>
<tags  
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/04/2016"
   ms.author="jdial" />

# <a name="deploy-a-vm-with-a-static-public-ip-using-the-azure-portal"></a>Implementar uma VM com um endereço IP público estático através do portal Azure

[AZURE.INCLUDE [virtual-network-deploy-static-pip-arm-selectors-include.md](../../includes/virtual-network-deploy-static-pip-arm-selectors-include.md)]

[AZURE.INCLUDE [virtual-network-deploy-static-pip-intro-include.md](../../includes/virtual-network-deploy-static-pip-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)]modelo de implementação clássica.

[AZURE.INCLUDE [virtual-network-deploy-static-pip-scenario-include.md](../../includes/virtual-network-deploy-static-pip-scenario-include.md)]

## <a name="create-a-vm-with-a-static-public-ip"></a>Criar uma VM com um endereço IP público estático 

Para criar uma VM com um endereço IP estático público no portal do Azure, siga os passos abaixo.

1. A partir de um browser, navegue para o [portal do Azure](https://portal.azure.com) e, se necessário, inicie sessão com a sua conta Azure.
2. No canto superior esquerdo do portal, clique em **Novo**>>**Calcular**>**Centro de dados do Windows Server 2012 R2**.
3. Na lista **Selecione um modelo de implementação** , selecione **O Gestor de recursos** e clique em **Criar**.
4. Na pá **Noções básicas** , introduza as informações de VM conforme apresentado abaixo e, em seguida, clique em **OK**.

    ![Portal Azure - Noções básicas](./media/virtual-network-deploy-static-pip-arm-portal/figure1.png)

5. Na pá **Escolher um tamanho** , clique em **A1 padrão** conforme apresentado abaixo e, em seguida, clique em **Selecionar**.

    ![Azure portal - escolher um tamanho de](./media/virtual-network-deploy-static-pip-arm-portal/figure2.png)

6. No pá **Definições** , clique em **endereço IP público**, em seguida, na pá **endereço IP público criar** , em **atribuição**, clique em **estática** conforme apresentado abaixo. E, em seguida, clique em **OK**.

    ![Azure portal - Criar endereço IP público](./media/virtual-network-deploy-static-pip-arm-portal/figure3.png)

7. Na pá **Definições** , clique em **OK**.
8. Reveja pá **Resumo** , conforme apresentado abaixo e, em seguida, clique em **OK**.

    ![Azure portal - Criar endereço IP público](./media/virtual-network-deploy-static-pip-arm-portal/figure4.png)

9. Repare que o novo mosaico no seu dashboard.

    ![Azure portal - Criar endereço IP público](./media/virtual-network-deploy-static-pip-arm-portal/figure5.png)

10. Quando a VM estiver criada, o pá **Definições** será apresentada conforme apresentado abaixo

    ![Azure portal - Criar endereço IP público](./media/virtual-network-deploy-static-pip-arm-portal/figure6.png)