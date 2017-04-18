## <a name="scenario"></a>Cenário

Para ilustrar melhor como criar um VNet e sub-redes, este documento irá utilizar o cenário abaixo.

![Cenário de VNet](./media/virtual-networks-create-vnet-scenario-include/vnet-scenario.png)

Neste cenário irá criar um VNet **TestVNet** com um bloco CIDR reservado de **192.168.0.0./16**com o nome. O VNet irá conter as seguintes sub-redes: 

- **Front-end**, utilizar **192.168.1.0/24** como respectivo bloco CIDR.
- **Back-end**, utilizar **192.168.2.0/24** como respectivo bloco CIDR.

 