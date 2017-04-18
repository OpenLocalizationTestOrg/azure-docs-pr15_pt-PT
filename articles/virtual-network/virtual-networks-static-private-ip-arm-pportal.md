<properties 
   pageTitle="Como configurar um endereço IP estático privado no modo de processador através do portal Azure | Microsoft Azure"
   description="Noções sobre IPs privada (diminuições) e como geri-los no modo de processador utilizando o portal do Azure"
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor="tysonn"
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

# <a name="how-to-set-a-static-private-ip-address-in-the-azure-portal"></a>Como configurar um endereço IP estático privado no portal do Azure

[AZURE.INCLUDE [virtual-networks-static-private-ip-selectors-arm-include](../../includes/virtual-networks-static-private-ip-selectors-arm-include.md)]

[AZURE.INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]Este artigo abrange o modelo de implementação do Gestor de recursos. Também pode [Gerir estático endereço IP privado no modelo de implementação clássica](virtual-networks-static-private-ip-classic-pportal.md).

[AZURE.INCLUDE [virtual-networks-static-ip-scenario-include](../../includes/virtual-networks-static-ip-scenario-include.md)]

Os passos de exemplo abaixo esperam um ambiente simple já criado. Se pretender executar os passos, tal como estes são apresentados neste documento, primeiro a criar o ambiente de teste descrito na [criar um vnet](virtual-networks-create-vnet-arm-pportal.md).

## <a name="how-to-create-a-vm-for-testing-static-private-ip-addresses"></a>Como criar uma VM para testar os endereços IP estáticos privados

Não pode definir um endereço IP estático privado durante a criação de uma VM no modo de implementação do Gestor de recursos utilizando o portal do Azure. Tem de criar a VM pela primeira vez, dimensão defina o seu IP privado para ser estático.

Para criar uma VM denominada *DNS01* sub-rede *FrontEnd* de uma VNet denominada *TestVNet*, siga os passos abaixo.

1. A partir de um browser, navegue até ao http://portal.azure.com e, se necessário, inicie sessão com a sua conta Azure.
2. Clique em **Novo** > **Calcular** > **Centro de dados do Windows Server 2012 R2**, repare que a lista **Selecione um modelo de implementação** já mostra **O Gestor de recursos**e, em seguida, clique em **Criar**, conforme apresentado na figura seguinte.

    ![Criar VM no portal do Azure](./media/virtual-networks-static-ip-arm-pportal/figure01.png)

3. Na pá **Noções básicas** , introduza o nome da VM seja criado (*DNS01* nosso cenário), a conta de administrador local e a palavra-passe, conforme apresentado na figura seguinte.

    ![Pá Noções básicas](./media/virtual-networks-static-ip-arm-pportal/figure02.png)

4. Certifique-se da **localização** selecionada é *Central (EUA)*, em seguida, clique em **Selecionar existente** em **grupo de recursos**, em seguida, clique novamente, **grupo de recursos** , em seguida, clique em *TestRG*e, em seguida, clique em **OK**.

    ![Pá Noções básicas](./media/virtual-networks-static-ip-arm-pportal/figure03.png)

5. Na pá **Escolher um tamanho** , selecione **A1 padrão**e, em seguida, clique em **Selecionar**.

    ![Selecione uma pá tamanho](./media/virtual-networks-static-ip-arm-pportal/figure04.png) 

6. No pá **Definições** , certifique-se de que estão definidas as seguintes propriedades são definidos com os valores abaixo e, em seguida, clique em **OK**.

    -**Conta de armazenamento**: *vnetstorage*
    - **Rede**: *TestVNet*
    - **Sub-rede**: *front-end*

    ![Selecione uma pá tamanho](./media/virtual-networks-static-ip-arm-pportal/figure05.png)  

7. Na pá **Sumário** , clique em **OK**. Tenha em atenção o mosaico abaixo apresentado no dashboard.

    ![Criar VM no portal do Azure](./media/virtual-networks-static-ip-arm-pportal/figure06.png)

## <a name="how-to-retrieve-static-private-ip-address-information-for-a-vm"></a>Como obter estáticas informações de endereço IP privadas para uma VM

Para ver as estáticas privadas IP informações de endereço a VM criada com os passos acima, execute os passos abaixo.

1. A partir do portal do Azure Azure, clique em **Procurar tudo** > **máquinas virtuais** > **DNS01** > **todas as definições de** > **interfaces de rede** e, em seguida, clique na interface de rede só listados.

    ![Implementar o mosaico VM](./media/virtual-networks-static-ip-arm-pportal/figure07.png)

2. No pá **interface da rede** , clique em **todas as definições de** > **endereços IP** e repare os valores de **atribuição** e o **endereço IP** .

    ![Implementar o mosaico VM](./media/virtual-networks-static-ip-arm-pportal/figure08.png)

## <a name="how-to-add-a-static-private-ip-address-to-an-existing-vm"></a>Como adicionar um endereço IP estático privado a um VM existente
Para adicionar um endereço IP estático privado para a VM criada utilizando os passos acima, siga os passos abaixo:

1. O pá **endereços IP** mostrada acima, clique em **estático** em **atribuição**.
2. Escreva *192.168.1.101* endereço **IP**e, em seguida, clique em **Guardar**.

    ![Criar VM no portal do Azure](./media/virtual-networks-static-ip-arm-pportal/figure09.png)

>[AZURE.NOTE] Se depois de clicar em **Guardar** notar que a atribuição ainda está definida para **dinâmicos**, significa que o endereço IP que escreveu já está em utilização. Experimente um endereço IP diferente.

## <a name="how-to-remove-a-static-private-ip-address-from-a-vm"></a>Como remover um endereço IP estático privado de uma VM
Para remover o endereço IP estático privado de VM criado anteriormente, siga o passo abaixo.
    
1. O pá **endereços IP** mostrada acima, clique em **dinâmicos** em **atribuição**e, em seguida, clique em **Guardar**.

## <a name="next-steps"></a>Próximos passos

- Saiba mais sobre endereços [IP público reservada](virtual-networks-reserved-public-ip.md) .
- Saiba mais sobre os endereços de [nível da instância IP público (ILPIP)](virtual-networks-instance-level-public-ip.md) .
- Consulte as [IP reservado REST APIs](https://msdn.microsoft.com/library/azure/dn722420.aspx).