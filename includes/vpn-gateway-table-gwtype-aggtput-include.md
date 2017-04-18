|    | **Débito VPN Gateway (1)** | **Estabelece um VPN Gateway max IPsec túnel (2)** | **Débito ExpressRoute Gateway** | **VPN Gateway e ExpressRoute coexistência**|
|--- |----------------------------|-----------------------------------|-------------------------------------|-----------------------------------------|
| **SKU básica (3)(5)**              |  100 Mbps | 10                         |  500 Mbps                           | N   |
| **SKU padrão (4)(5)**           |  100 Mbps | 10                         | 1000 Mbps                           | Sim  |
| **Alto desempenho SKU (4)**   | 200 Mbps  | 30                         | 2000 Mbps                           | Sim  |

- (1) o débito VPN é uma estimativa aproximada com base nas medidas entre VNets na mesma região Azure. Não é um garantido débito para ligações de publicação em local através da Internet. É a medição débito possíveis máximo.
- (2) o número de túneis referenciar RouteBased VPNs. Está numa VPN PolicyBased só pode suportar um túnel VPN do Site para o Site.
- (3) não é suportada BGP para o SKU básica.
- (4) PolicyBased VPNs não são suportadas para este SKU. São suportadas para apenas o SKU básica.
- (5) ligações activo-activo S2S VPN Gateway não são suportadas para este SKU. Activo activo é suportado no SKU reversa apenas.