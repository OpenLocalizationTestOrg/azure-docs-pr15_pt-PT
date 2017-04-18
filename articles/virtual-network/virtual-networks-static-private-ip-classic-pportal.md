<properties 
   pageTitle="Como configurar um endereço IP estático privado no modo clássico utilizando o Portal do Azure | Microsoft Azure"
   description="Noções sobre estático IPs privado e como geri-los no modo clássico através do portal Azure"
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor="tysonn"
   tags="azure-service-management"
/>
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/04/2016"
   ms.author="jdial" />

# <a name="how-to-set-a-static-private-ip-address-classic-in-the-azure-portal"></a>Como configurar um endereço IP estático privado (clássico) no portal do Azure

[AZURE.INCLUDE [virtual-networks-static-private-ip-selectors-classic-include](../../includes/virtual-networks-static-private-ip-selectors-classic-include.md)]

[AZURE.INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]Este artigo abrange o modelo de implementação clássica. Também pode [Gerir um endereço IP estático privado no modelo de implementação de Gestor de recursos](virtual-networks-static-private-ip-arm-pportal.md).

[AZURE.INCLUDE [virtual-networks-static-ip-scenario-include](../../includes/virtual-networks-static-ip-scenario-include.md)]

Os passos de exemplo abaixo esperam um ambiente simple já criado. Se pretender executar os passos, tal como estes são apresentados neste documento, primeiro a criar o ambiente de teste descrito na [criar um vnet](virtual-networks-create-vnet-classic-pportal.md).

## <a name="how-to-specify-a-static-private-ip-address-when-creating-a-vm"></a>Como especificar um endereço IP estático privado quando criar uma VM
Para criar uma VM denominada *DNS01* sub-rede *FrontEnd* de uma VNet denominada *TestVNet* com um endereço IP estático privado de *192.168.1.101*, siga os passos abaixo:

1. A partir de um browser, navegue até ao http://portal.azure.com e, se necessário, inicie sessão com a sua conta Azure.
2. Clique em **Novo** > **Calcular** > **Centro de dados do Windows Server 2012 R2**, repare que a lista **Selecione um modelo de implementação** já mostra **clássica**e, em seguida, clique em **Criar**.

    ![Criar VM no portal do Azure](./media/virtual-networks-static-ip-classic-pportal/figure01.png)

3. Na pá **Criar VM** , introduza o nome da VM seja criado (*DNS01* nosso cenário), a conta de administrador local e a palavra-passe.

    ![Criar VM no portal do Azure](./media/virtual-networks-static-ip-classic-pportal/figure02.png)

4. Clique em **Configuração opcional** > **rede** > **Rede Virtual**e, em seguida, clique em **TestVNet**. Se **TestVNet** não estiver disponível, certifique-se de que está a utilizar a localização *Central (EUA)* e tiver criado o ambiente de teste descrito no início deste artigo.

    ![Criar VM no portal do Azure](./media/virtual-networks-static-ip-classic-pportal/figure03.png)

5. No pá **rede** , certifique-se a sub-rede atualmente seleccionada é *front-end*, em seguida, clique em **endereços IP**, em **atribuição de endereços IP** clique em **estática**e, em seguida, introduza *192.168.1.101* endereço **IP** conforme visto abaixo.

    ![Criar VM no portal do Azure](./media/virtual-networks-static-ip-classic-pportal/figure04.png)   

6. Clique em **OK** na pá **endereços IP** , em seguida, clique em **OK** na pá a **rede** e clique em **OK** na pá **config opcional** .
7. Na pá **VM criar** , clique em **Criar**. Tenha em atenção o mosaico abaixo apresentado no dashboard.

    ![Criar VM no portal do Azure](./media/virtual-networks-static-ip-classic-pportal/figure05.png)

## <a name="how-to-retrieve-static-private-ip-address-information-for-a-vm"></a>Como obter estáticas informações de endereço IP privadas para uma VM

Para ver as estáticas privadas IP informações de endereço a VM criada com os passos acima, execute os passos abaixo.

1. A partir do portal do Azure Azure, clique em **Procurar tudo** > **máquinas virtuais (clássico)** > **DNS01** > **todas as definições de** > **endereços IP** e aviso a atribuição de endereços IP e endereços IP conforme visto abaixo.

    ![Criar VM no portal do Azure](./media/virtual-networks-static-ip-classic-pportal/figure06.png)

## <a name="how-to-remove-a-static-private-ip-address-from-a-vm"></a>Como remover um endereço IP estático privado de uma VM
Para remover o endereço IP estático privado de VM criado anteriormente, siga os passos abaixo.
    
1. O pá **endereços IP** mostrada acima, clique em **dinâmico** à direita da **atribuição de endereços IP**, em seguida, clique em **Guardar**e, em seguida, clique em **Sim**.

    ![Criar VM no portal do Azure](./media/virtual-networks-static-ip-classic-pportal/figure07.png)

## <a name="how-to-add-a-static-private-ip-address-to-an-existing-vm"></a>Como adicionar um endereço IP estático privado a um VM existente
Para adicionar um endereço IP estático privado para a VM criada utilizando os passos acima, siga os passos abaixo:

1. O pá **endereços IP** mostrada acima, clique em **estático** à direita da **atribuição de endereços IP**.
2. Escreva *192.168.1.101* **endereço IP**, em seguida, clique em **Guardar**e, em seguida, clique em **Sim**.

## <a name="next-steps"></a>Próximos passos

- Saiba mais sobre endereços [IP público reservada](virtual-networks-reserved-public-ip.md) .
- Saiba mais sobre os endereços de [nível da instância IP público (ILPIP)](virtual-networks-instance-level-public-ip.md) .
- Consulte as [IP reservado REST APIs](https://msdn.microsoft.com/library/azure/dn722420.aspx).