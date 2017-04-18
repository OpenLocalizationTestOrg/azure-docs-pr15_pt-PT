## <a name="service-chaining---transit-through-peered-vnet"></a>Encadeamento - trânsito VNet dispõe de serviço

Apesar da utilização de rotas sistema facilita tráfego automaticamente para a sua implementação, existem casos em que pretender para controlar o encaminhamento de pacotes através de uma aplicação virtual.
Neste cenário, existem dois VNets uma subscrição, HubVNet e VNet1 conforme descrito no diagrama abaixo. Implementar rede Appliance(NVA) Virtual no VNet HubVNet. Depois de estabelecer VNet efectuado entre HubVNet e VNet1, pode configurar o utilizador definidos rotas e especificar o próximo salto para NVA a HubVNet.

![NVA trânsito](./media/virtual-networks-create-vnetpeering-scenario-transit-include/figure01.PNG)

> [AZURE.NOTE] Para simplificar, partem do pressuposto de todos os VNets aqui estão na mesma subscrição. Mas também funciona para publicação em subscrição cenário.

A propriedade da chave para activar o encaminhamento de trânsito é o parâmetro "Permitir o tráfego reencaminhadas". Esta opção permite-aceitar e enviar tráfego ou para a NVA pelo VNet dispõe.  
