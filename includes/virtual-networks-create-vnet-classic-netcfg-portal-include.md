## <a name="how-to-create-a-vnet-using-a-network-config-file-in-the-azure-portal"></a>Como criar um VNet com um ficheiro de configuração de rede no portal do Azure

Azure utiliza um ficheiro xml para definir todas as VNets disponíveis para uma subscrição. Pode transferir este ficheiro e editá-la para modificar ou eliminar VNets existente e criar novos. Neste documento, irá aprender a transferir este ficheiro, designado de ficheiro de configuração (ou netcgf) de rede e editá-lo para criar um novo VNet. Verificar o [esquema de configuração de rede virtual Azure](https://msdn.microsoft.com/library/azure/jj157100.aspx) para saber mais sobre o ficheiro de configuração de rede.

Para criar um VNet com um ficheiro de netcfg através do portal do Azure, siga os passos abaixo.

1. A partir de um browser, navegue até ao http://manage.windowsazure.com e, se necessário, inicie sessão com a sua conta Azure.
2. Deslocar para baixo na lista de serviços e, clique em **redes** conforme visto abaixo.

    ![Redes virtuais Azure](./media/virtual-networks-create-vnet-classic-portal-xml-include/vnet-create-portal-netcfg-figure1.gif)

3. Na parte inferior da página, clique no botão **EXPORTAR** , conforme apresentado abaixo.

    ![Botão Exportar](./media/virtual-networks-create-vnet-classic-portal-xml-include/vnet-create-portal-netcfg-figure2.png)

4. Na página **configuração de rede de exportação** , selecione a subscrição que pretende exportar a configuração de rede virtual a partir de e, em seguida, clique no botão de marca de verificação no canto inferior esquerdo da página.
5. Siga as instruções para guardar o ficheiro **NetworkConfig.xml** seu browser. Certifique-se de que tenha em atenção onde esteja a guardar o ficheiro.
6. Abra o ficheiro que guardou no passo 5 acima utilizando qualquer aplicação de editor de XML ou texto e procure o **<VirtualNetworkSites>** elemento. Se tiver quaisquer redes já criadas, todas as redes serão apresentada como a sua própria **<VirtualNetworkSite>** elemento.
7. Para criar a rede virtual descrita neste cenário, adicione o XML seguinte em apenas a **<VirtualNetworkSites>** elemento:

        <VirtualNetworkSite name="TestVNet" Location="Central US">
          <AddressSpace>
            <AddressPrefix>192.168.0.0/16</AddressPrefix>
          </AddressSpace>
          <Subnets>
            <Subnet name="FrontEnd">
              <AddressPrefix>192.168.1.0/24</AddressPrefix>
            </Subnet>
            <Subnet name="BackEnd">
              <AddressPrefix>192.168.2.0/24</AddressPrefix>
            </Subnet>
          </Subnets>
        </VirtualNetworkSite>

8.  Guarde o ficheiro de configuração de rede.
9.  No portal do Azure, no canto inferior esquerdo da página, clique em **Novo**, em seguida, clique em **Serviços de rede**, em seguida, clique em **Rede VIRTUAL**e, em seguida, clique em **Importar a configuração** , conforme apresentado na figura abaixo.

    ![Importar a configuração](./media/virtual-networks-create-vnet-classic-portal-xml-include/vnet-create-portal-netcfg-figure3.gif)

10.  Na página **importar o ficheiro de configuração de rede** , clique em **Procurar para ficheiro...**, em seguida, navegue para a pasta que guardou o ficheiro no passo 8 acima, selecione o ficheiro e, em seguida, clique em **Abrir**. A página web deverá ter um aspeto semelhante da figura abaixo. No canto inferior direita da página, clique no botão de seta para mover para o passo seguinte.

    ![Página de ficheiro de configuração de rede de importação](./media/virtual-networks-create-vnet-classic-portal-xml-include/vnet-create-portal-netcfg-figure4.png)

11.   Na página **construir a sua rede** , repare a entrada para a sua nova VNet, tal como apresentado na figura abaixo.

    ![Construir a sua página de rede](./media/virtual-networks-create-vnet-classic-portal-xml-include/vnet-create-portal-netcfg-figure5.png)

12.   Clique no botão de marca de verificação no canto inferior direita da página para criar o VNet. Depois de aguardar alguns segundos seu VNet será apresentado na lista de VNets disponíveis, tal como apresentado na figura abaixo.

    ![Nova rede virtual](./media/virtual-networks-create-vnet-classic-portal-xml-include/vnet-create-portal-netcfg-figure6.png)