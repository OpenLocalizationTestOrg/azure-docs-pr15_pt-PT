## <a name="how-to-create-a-vnet-in-the-azure-portal"></a>Como criar um VNet no portal do Azure

Para criar um VNet com base no cenário acima, siga os passos abaixo.

1. A partir de um browser, navegue até ao http://manage.windowsazure.com e, se necessário, inicie sessão com a sua conta Azure.
2. Clique em **Novo** > **Serviços de rede** > **Rede VIRTUAL** > **Personalizada criar** conforme apresentado na figura abaixo.

    ![Criar VNet no portal](./media/virtual-networks-create-vnet-classic-portal-include/vnet-create-portal-figure1.gif)

3. Na página **Detalhes da rede Virtual** , escreva o **nome** da VNet, selecione a **localização**e, em seguida, clique na seta no canto inferior direita da página para avançar para o passo 2. A figura seguinte mostra as definições para os nossos cenário.

    ![Página de detalhes de rede virtual](./media/virtual-networks-create-vnet-classic-portal-include/vnet-create-portal-figure2.png)

4. Na página **DNS Servers e grupo análise** , especifique o nome e endereço IP do até 9 servidores DNS a utilizar. Se não especificar um servidor de DNS, o seu VNet irá utilizar a resolução de resolução de nomenclatura interno, fornecida pela Azure. Para o cenário, não recomendamos irá configurar os servidores DNS.
5. Se pretender fornecer acesso VPN ponto-para-site à sua VNet, ative a caixa de verificação **configurar um site do ponto de VPN** . Se configurar não está numa VPN ponto-para-site, pode adicioná-la para o seu VNet em qualquer altura após a criação. Para o cenário, podemos não irá configurar um site do ponto de VPN.
6. Se quiser fornecer site para o site VPN conectividade entre o seu VNet e outra VNet ou à sua rede no local, ativar a caixa de verificação **configurar um site para o site VPN** e especifique se pretende utilizar **ExpressRoute** ou nota e o nome da rede para ligar à. Se configurar não está numa VPN do site para o site, pode adicioná-la para o seu VNet em qualquer altura após a criação. Para o cenário, não recomendamos irá configurar uma VPN do site para o site.
7. Clique na seta no canto inferior direita da página para avançar para o passo 3-. A imagem abaixo mostra as definições para os nossos cenário.

    ![Página de conectividade VPN e os servidores DNS](./media/virtual-networks-create-vnet-classic-portal-include/vnet-create-portal-figure3.png)

8. Na página **Virtual espaços de endereços de rede** , em **IP inicial**, clique no *10.0.0.0* para alterar o espaço de endereços VNet e, em seguida, escreva o inicial espaço de endereços que pretende utilizar. Para o cenário, escreva *192.168.0.0*. 
9. Em **CIDR (endereço CONTAGEM)** selecione o número de bits para a máscara de sub-rede. Para o cenário, selecione *16 (65536)*.
10. Em **sub-redes**, clique em *sub-rede 1* e mudar o nome da sub-rede se for necessário. Para o cenário, o nome-lo para *front-end*.

    >[AZURE.NOTE] Se clicar em fora da caixa de texto nome para uma sub-rede não conseguir editar o nome se a sub-rede novamente. Para corrigir, que precisa de remover a sub-rede ao clicar no botão X à direita, em seguida, adicione uma nova sub-rede conforme descrito no passo 13 abaixo.

11. Em **IP inicial** para a primeira sub-rede, especifique o endereço IP inicial para a sub-rede. Para o cenário, escreva *192.168.1.0*.
12. Em **CIDR (endereço CONTAGEM)** selecione o número de bits para a máscara de sub-rede para a primeira sub-rede. Para o cenário, selecione *24 (256)*.
13. Clique em **Adicionar sub-rede** para adicionar uma nova sub-rede, se necessário. Para o cenário, adicione uma sub-rede e repita os passos 10 a 12 para configurar o VNet conforme apresentado na figura seguinte.

    ![Página de espaços de endereços de rede virtual](./media/virtual-networks-create-vnet-classic-portal-include/vnet-create-portal-figure4.png)

14. Clique no botão de marca de verificação no canto inferior direita da página para criar o VNet. Depois de aguardar alguns segundos seu VNet será apresentado na lista de VNets disponíveis, tal como apresentado na figura abaixo.

    ![Nova rede virtual](./media/virtual-networks-create-vnet-classic-portal-include/vnet-create-portal-figure5.png)