### <a name="to-verify-your-connection-by-using-powershell"></a>Para verificar a sua ligação ao utilizar o PowerShell

Pode verificar que a sua ligação bem sucedido utilizando o `Get-AzureRmVirtualNetworkGatewayConnection` cmdlet, com ou sem `-Debug`. 

1. Utilize o exemplo seguinte cmdlet, configurar os valores para que correspondam aos seus próprios. Se lhe for pedido, selecione 'A' para poder executar 'Todos'. No exemplo, `-Name` refere-se para o nome da ligação que criou e pretende testar.

        Get-AzureRmVirtualNetworkGatewayConnection -Name MyGWConnection -ResourceGroupName MyRG

2. Quando tiver terminado o cmdlet, para ver os valores. No exemplo abaixo, o estado da ligação mostra como 'Ligado' e pode ver a entrada e saída bytes.

        Body:
        {
          "name": "MyGWConnection",
          "id":
        "/subscriptions/086cfaa0-0d1d-4b1c-94544-f8e3da2a0c7789/resourceGroups/MyRG/providers/Microsoft.Network/connections/MyGWConnection",
          "properties": {
            "provisioningState": "Succeeded",
            "resourceGuid": "1c484f82-23ec-47e2-8cd8-231107450446b",
            "virtualNetworkGateway1": {
              "id":
        "/subscriptions/086cfaa0-0d1d-4b1c-94544-f8e3da2a0c7789/resourceGroups/MyRG/providers/Microsoft.Network/virtualNetworkGa
        teways/vnetgw1"
            },
            "localNetworkGateway2": {
              "id":
        "/subscriptions/086cfaa0-0d1d-4b1c-94544-f8e3da2a0c7789/resourceGroups/MyRG/providers/Microsoft.Network/localNetworkGate
        ways/LocalSite"
            },
            "connectionType": "IPsec",
            "routingWeight": 10,
            "sharedKey": "abc123",
            "connectionStatus": "Connected",
            "ingressBytesTransferred": 33509044,
            "egressBytesTransferred": 4142431
          }

### <a name="to-verify-your-connection-by-using-the-azure-portal"></a>Para verificar a sua ligação utilizando o portal do Azure

No portal do Azure, pode ver o estado da ligação ao navegar para a ligação. Existem várias formas para o fazer. Os seguintes passos mostram uma forma de navegar para a sua ligação e verificar.

1. No [portal do Azure](http://portal.azure.com), clique em **todos os recursos** e navegue para o gateway de rede virtual.
2. No pá para o gateway de rede virtual, clique em **ligações**. Pode ver o estado de cada ligação.
3. Clique no nome da ligação que pretende verificar para abrir **Essentials**. No Essentials, pode ver mais informações sobre a ligação. O **Estado** é 'Com êxito' e 'Ligado' quando tiverem efetuado uma ligação com êxito.

    ![Verificar a ligação](./media/vpn-gateway-verify-connection-rm-include/connectionsucceeded.png)