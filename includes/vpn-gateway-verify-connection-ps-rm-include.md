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