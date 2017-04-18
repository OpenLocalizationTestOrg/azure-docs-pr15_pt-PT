Para criar um VNet no modelo de implementação de Gestor de recursos utilizando o portal do Azure, siga os passos abaixo. As capturas de ecrã são fornecidas como exemplos. Certifique-se de que substitua os valores com o seu próprio. Para mais informações sobre como trabalhar com redes virtuais, consulte o artigo [Descrição geral da rede Virtual](../articles/virtual-network/virtual-networks-overview.md).

1. A partir de um browser, navegue para o [portal do Azure](http://portal.azure.com) e, se necessário, inicie sessão com a sua conta Azure.

2. Clique em **Novo**. No campo de **pesquisa de mercado** , escreva "Rede Virtual". Localize a **Rede Virtual** a partir da lista devolvida e clique para abrir o pá **Rede Virtual** .

    ![Localize a rede Virtual pá de recursos] (./media/vpn-gateway-basic-vnet-rm-portal-include/newvnetportal700.png "Pá de recursos de rede virtual localize")

3. Perto da parte inferior da pá rede Virtual, na lista **Selecione um modelo de implementação** , selecione **O Gestor de recursos**e, em seguida, clique em **Criar**.


    ![Selecione o Gestor de recursos] (./media/vpn-gateway-basic-vnet-rm-portal-include/resourcemanager250.png "Selecione o Gestor de recursos")

4. No pá **criar rede virtual** , configure as definições de VNet. Quando preencha os campos, o ponto de exclamação vermelho irão tornar-se uma marca de verificação verde quando os carateres introduzidos no campo são válidos.

    ![Validação de campo] (./media/vpn-gateway-basic-vnet-rm-portal-include/checkmark300.png "Validação de campo")

5. O pá **criar rede virtual** ter um aspeto semelhante ao seguinte exemplo. Poderão existir valores que são preenchidas automática. Se Sim, substitua os valores com o seu próprio.

    ![Criar pá de rede virtual] (./media/vpn-gateway-basic-vnet-rm-portal-include/createvnet300.png "Criar pá de rede virtual")

6. **Nome**: introduza o nome para a sua rede Virtual.

7. **Espaço de endereços**: introduza o espaço de endereços. Se tiver vários espaços de endereço para adicionar, adicione a sua primeira espaço de endereços. Pode adicionar espaços adicionais endereço mais tarde, depois de criar o VNet.
 
8. **Nome de sub-rede**: adicionar o nome de sub-rede e o intervalo de endereço de sub-rede. Pode adicionar sub-redes adicionais mais tarde, depois de criar o VNet.

10. **Subscrição**: Certifique-se de que a subscrição listada é o correto. Pode alterar as subscrições ao utilizar o menu pendente.

11. **Grupo de recursos**: selecione um grupo de recursos existente ou crie um novo ao escrever um nome para o novo grupo de recursos. Se estiver a criar um novo grupo, dê um nome de grupo de recursos de acordo com os valores de configuração planeada. Para obter mais informações sobre os grupos de recursos, visite [Descrição geral do Gestor de recursos do Azure](resource-group-overview.md#resource-groups).

12. **Localização**: selecione a localização para a sua VNet. A localização determina onde os recursos que implementar para este VNet residirão.

13. Selecione **Afixar ao dashboard** se pretender que possam localizar o seu VNet facilmente no dashboard de e, em seguida, clique em **Criar**.
    
    ![Afixar ao dashboard] (./media/vpn-gateway-basic-vnet-rm-portal-include/pintodashboard150.png "Afixar ao dashboard")

14. Depois de clicar em **Criar**, irá ver um mosaico no seu dashboard que vai refletir as o progresso da sua VNet. O mosaico alterações à medida que o VNet está a ser criado.

    ![Rede virtual criar dispor em mosaico] (./media/vpn-gateway-basic-vnet-rm-portal-include/deploying150.png "Rede virtual criar dispor em mosaico")