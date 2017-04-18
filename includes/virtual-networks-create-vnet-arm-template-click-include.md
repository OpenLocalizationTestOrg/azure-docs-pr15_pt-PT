## <a name="deploy-the-arm-template-by-using-click-to-deploy"></a>Implementar o modelo de processador utilizando o clique para implementar

Pode reutilizar carregar de modelos de processador predefinido para um repositório de github mantido pela Microsoft e abrir para a Comunidade. Estes modelos podem ser implementados diretamente terminar github, ou transferidos e modificados para corresponder às suas necessidades. Para implementar um modelo que cria uma VNet com duas sub-redes, siga os passos abaixo.

1. A partir de um browser, navegue até ao [https://github.com/Azure/azure-quickstart-templates](https://github.com/Azure/azure-quickstart-templates).
2. Desloque-se para baixo na lista de modelos e clique em **101-vnet duas sub-redes**. Dar entrada do ficheiro de **README.md** , conforme apresentado abaixo.

    ![Ficheiro de READEME.md no github](./media/virtual-networks-create-vnet-arm-template-click-include/figure1.png)

3. Clique em **Implementar Azure**. Se for necessário, introduza as suas credenciais de início de sessão Azure. 
4. Na pá **parâmetros** , introduza os valores que pretende utilizar para criar o seu novo VNet e, em seguida, clique em **OK**. A figura seguinte mostra os valores para os nossos cenário.

    ![PROCESSADOR parâmetros de modelo](./media/virtual-networks-create-vnet-arm-template-click-include/figure2.png)

4. Clique em **grupo de recursos** e selecione um grupo de recursos para adicionar VNet para ou clique em **Criar novo** para adicionar o VNet a um novo grupo de recursos. A figura seguinte mostra o recurso definições de grupo para um novo grupo de recursos denominado **TestRG**.

    ![Grupo de recursos](./media/virtual-networks-create-vnet-arm-template-click-include/figure3.png)

5. Se for necessário, altere as definições de **subscrição** e a **localização** para a sua VNet.
6. Se não pretende ver o VNet como um mosaico na **Startboard**, desative o **Pin para Startboard**.
5. Clique em **termos de Leagl**, leia os termos e clique em **comprar** para aceitar. 
6. Clique em **Criar** para criar o VNet.

    ![Mosaico de implementação evoluir no portal de pré-visualização](./media/virtual-networks-create-vnet-arm-template-click-include/figure4.png)

7. Depois de concluir o processo de implementação, clique em **TestVNet** > **todas as definições de** > **sub-redes** para ver as propriedades de sub-rede, conforme apresentado abaixo.

    ![Criar VNet no portal de pré-visualização](./media/virtual-networks-create-vnet-arm-template-click-include/figure5.gif)