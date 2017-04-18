## <a name="how-to-create-a-classic-vnet-in-the-azure-portal"></a>Como criar um VNet clássica no portal do Azure

Para criar um VNet clássica com base no cenário acima, siga os passos abaixo.

1. A partir de um browser, navegue até ao http://portal.azure.com e, se necessário, inicie sessão com a sua conta Azure.
2. Clique em **Novo** > **funcionamento em rede** > **da rede Virtual**, repare que a lista **Selecione um modelo de implementação** já mostra **clássica**e, em seguida, clique em **Criar**, conforme apresentado na figura seguinte.

    ![Criar VNet no portal do Azure](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure1.gif)

3. No pá **da rede Virtual** , escreva o **nome** da VNet e, em seguida, clique em **espaço de endereços**. Configurar as definições de espaço de endereços para sua sub-rede primeiro e o VNet, em seguida, clique em **OK**. A figura seguinte mostra as definições de bloqueio CIDR para o cenário.

    ![Pá de espaço de endereços](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure2.png)

4. Clique em **Grupo de recursos** e selecione um grupo de recursos para adicionar VNet para ou clique em **Criar novo grupo de recursos** para adicionar o VNet a um novo grupo de recursos. A figura seguinte mostra o recurso definições de grupo para um novo grupo de recursos denominado **TestRG**. Para obter mais informações sobre os grupos de recursos, visite [Descrição geral do Gestor de recursos do Azure](../articles/virtual-network/resource-group-overview.md#resource-groups).

    ![Criar pá do grupo de recursos](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure3.png)

5. Se for necessário, altere as definições de **subscrição** e a **localização** para a sua VNet. 

6. Se não pretende ver o VNet como um mosaico na **Startboard**, desative o **Pin para Startboard**. 

7. Clique em **Criar** e repare o mosaico denominado **rede criar Virtual** conforme apresentado na figura seguinte.

    ![Criar VNet no portal](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure4.png)

8. Aguardar VNet seja criado e, quando vir o mosaico abaixo, clique na mesma para adicionar mais sub-redes.

    ![Criar VNet no portal](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure5.png)

9. Deverá visualizar a **configuração** para o seu VNet conforme apresentado abaixo. 

    ![Criar VNet no portal](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure6.png)

10. Clique em **sub-redes** > **Adicionar**, em seguida, escreva um **nome** e especifique um **intervalo de endereço (bloco CIDR)** para sua sub-rede e, em seguida, clique em **OK**. A figura seguinte mostra as definições para os nossos cenário atual.

    ![Criar VNet no portal do Azure](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure7.gif)