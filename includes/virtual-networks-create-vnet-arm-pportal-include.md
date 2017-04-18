## <a name="how-to-create-a-vnet-in-the-azure-portal"></a>Como criar um VNet no portal do Azure

Para criar um VNet com base no cenário acima utilizando o portal de pré-visualização Azure, siga os passos abaixo.

1. A partir de um browser, navegue até ao http://portal.azure.com e, se necessário, inicie sessão com a sua conta Azure.
2. Clique em **Novo** > **funcionamento em rede** > **da rede Virtual**, em seguida, clique em **Gestor de recursos** a partir da lista **Selecione um modelo de implementação** e, em seguida, clique em **Criar**, conforme apresentado na figura seguinte.

    ![Criar VNet no portal do Azure](./media/virtual-networks-create-vnet-arm-pportal-include/vnet-create-arm-pportal-figure1.gif)

3. No pá **criar rede virtual** , configure as definições de VNet conforme apresentado na figura abaixo.

    ![Criar pá de rede virtual](./media/virtual-networks-create-vnet-arm-pportal-include/vnet-create-arm-pportal-figure2.png)

4. Clique em **grupo de recursos** e selecione um grupo de recursos para adicionar VNet para ou clique em **Criar novo** para adicionar o VNet a um novo grupo de recursos. A figura seguinte mostra o recurso definições de grupo para um novo grupo de recursos denominado **TestRG**. Para obter mais informações sobre os grupos de recursos, visite [Descrição geral do Gestor de recursos do Azure](../articles/resource-group-overview.md#resource-groups).

    ![Grupo de recursos](./media/virtual-networks-create-vnet-arm-pportal-include/vnet-create-arm-pportal-figure3.png)

5. Se for necessário, altere as definições de **subscrição** e a **localização** para a sua VNet. 

6. Se não pretende ver o VNet como um mosaico na **Startboard**, desative o **Pin para Startboard**. 

7. Clique em **Criar** e repare o mosaico denominado **rede criar Virtual** conforme apresentado na figura seguinte.

    ![Criação de mosaico de rede virtual](./media/virtual-networks-create-vnet-arm-pportal-include/vnet-create-arm-pportal-figure4.png)

8. Aguardar VNet seja criado, em seguida, no pá **da rede Virtual** , clique em **todas as definições de** > **sub-redes** > **Adicionar** conforme visto abaixo.

    ![Adicionar sub-rede no portal do Azure](./media/virtual-networks-create-vnet-arm-pportal-include/vnet-create-arm-pportal-figure5.gif)

9. Especificar as definições de sub-rede para sub-rede *back-end* , conforme apresentado abaixo e, em seguida, clique em **OK**. 

    ![Definições de sub-rede](./media/virtual-networks-create-vnet-arm-pportal-include/vnet-create-arm-pportal-figure6.png)

10. Repare na lista de sub-redes, tal como apresentado na figura abaixo.

    ![Lista de sub-redes no VNet](./media/virtual-networks-create-vnet-arm-pportal-include/vnet-create-arm-pportal-figure7.png)
