1. No portal do, aceda ao **Novo**. Escreva "Gateway de rede Virtual" na pesquisa. Localize o **gateway de rede Virtual** na pesquisa remetente e clique na entrada. Esta ação abre a pá **gateway de rede virtual de criar** .
2. Clique em **Criar** na parte inferior da pá **gateway de rede Virtual** . O **gateway de rede virtual criar** pá será aberto. Preencha os valores para o gateway de rede virtual.

    ![Campos de pá criar rede virtual gateway] (./media/vpn-gateway-add-gw-rm-portal-include/createvnetgw300.png "Campos de pá criar rede virtual gateway")

3. **Nome**: nome do seu gateway. Esta não é igual a atribuição de nomes de sub-rede um gateway. É o nome do objeto gateway que está a criar.

4. **Tipo de gateway**: selecione **VPN**. Os gateways VPN utilizam o tipo de gateway rede virtual **VPN**. 

5. **Tipo de VPN**: selecione o tipo VPN especificada para a configuração. Maior parte das configurações necessitam de um tipo VPN baseada em encaminhar.

6. **SKU**: selecione o gateway SKU na lista pendente. SKUs listadas na lista pendente variam consoante o tipo VPN que selecionar.

7. **Localização**: ajustar o campo de **localização** para apontar para a localização onde se encontra a sua rede virtual.
 
8. Selecione a rede virtual ao qual pretende adicionar este gateway. Clique em **Rede Virtual** para abrir o pá de **Escolher uma rede virtual** . Selecione o VNet. Se não vir o seu VNet, certifique-se que no campo **localização** é apontar para a região em que a sua rede virtual está localizado.

9. Selecione um endereço IP público. Clique em **endereço IP público** para abrir o **endereço IP público escolher** pá. Clique em **+ criar novas** para abrir o **Criar pá de endereço IP público**. Introduza um nome para o endereço IP público. Este pá cria um objeto de endereço IP público para o qual um endereço IP público forem dinamicamente atribuído.<br>Clique em **OK** para guardar as alterações para este pá.

10. **Subscrição**: Certifique-se de que a subscrição correta está selecionada.

11. **Grupo de recursos**: esta definição é determinada pela rede Virtual que selecionar. 

12. Não ajuste a **localização** depois de ter especificado as definições anteriores.

13. Verifique as definições. Pode selecionar **Afixar ao dashboard** na parte inferior da pá se pretender que o gateway seja apresentado no dashboard.

14. Clique em **Criar** para começar a criar o gateway. As definições serão validadas e verá "gateway de rede implementar Virtual" dispor em mosaico no dashboard. Criar um gateway pode demorar até 45 minutos. Poderá ter de atualizar a página do portal para ver o estado concluído.

    ![Implementar Virtual gateway de rede] (./media/vpn-gateway-add-gw-rm-portal-include/deployvnetgw150.png "Implementar Virtual gateway de rede")

11. Depois do gateway é criado, pode ver o endereço IP que foi atribuído ao mesmo verificando a rede virtual no portal. O gateway será apresentado como um dispositivo ligado. Pode clicar no dispositivo ligado (o gateway rede virtual) para ver mais informações.



