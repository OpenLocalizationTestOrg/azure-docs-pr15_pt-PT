
Para configurar o seu dispositivo VPN, terá do endereço IP público do gateway rede virtual para configurar o seu dispositivo VPN no local. Trabalhar com o fabricante para obter informações de configuração específicas do dispositivo e configure o seu dispositivo. Consulte os [Dispositivos de VPN](../articles/vpn-gateway/vpn-gateway-about-vpn-devices.md) para obter mais informações sobre os dispositivos VPN funciona bem com Azure.

Para localizar o endereço IP público do seu gateway de rede virtual através do PowerShell, utilize o exemplo seguinte:

    Get-AzureRmPublicIpAddress -Name GW1PublicIP -ResourceGroupName TestRG

Também pode ver o endereço IP público para o gateway de rede virtual utilizando o portal do Azure. Navegue para **Virtual gateways de rede**e, em seguida, clique no nome do seu gateway.