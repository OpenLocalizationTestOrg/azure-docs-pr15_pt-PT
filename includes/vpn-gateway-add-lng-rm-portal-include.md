1. No portal do, a partir de **todos os recursos**, clique em **+ Add**. Na **Tudo** pá caixa de pesquisa, escreva o **gateway de rede Local**, em seguida, clique em Procurar. Isto irá devolver uma lista. Clique em **gateway de rede Local** para abrir o pá, em seguida, clique em **Criar** para abrir o pá **Criar gateway de rede local** .

    ![criar o gateway de rede local](./media/vpn-gateway-add-lng-rm-portal-include/addlng250.png)

2. No **pá de gateway de rede local criar**, especifique um **nome** para o objeto de gateway de rede local.
 
3. Especificar um **endereço IP** público, válido, para o dispositivo VPN ou gateway de rede virtual ao qual pretende ligar.<br>Se esta rede local representa uma localização no local, este é o endereço IP público do dispositivo VPN ao qual pretende ligar. -Não pode ser por trás NAT e tem de estar acessíveis por Azure.<br>Se esta rede local representa VNet outra, irá especifique o endereço IP público que foi atribuído para o gateway de rede virtual para esse VNet.<br>

4. **Espaço de endereços** que se refere ao intervalos de endereços para a rede que representa esta rede local. Pode adicionar vários intervalos de espaço de endereços. Certifique-se de que os intervalos que especificar aqui não se sobrepõem com intervalos de outras redes, ao qual pretende ligar.
 
5. Para a **subscrição**, certifique-se de que é apresentada a subscrição correta.

6. Para o **Grupo de recursos**, selecione o grupo de recursos que pretende utilizar. Pode criar um novo grupo de recursos ou selecione uma que já criadas.

7. Para a **localização**, selecione a localização que este objeto será criado na. Poderá querer selecionar a mesma localização que seu VNet reside, mas não é necessário para fazê-lo.

8. Clique em **Criar** para criar um gateway de rede local.
