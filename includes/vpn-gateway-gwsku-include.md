Quando cria um gateway de rede virtual, tem de especificar o gateway SKU que pretende utilizar. Quando seleciona um gateway superior SKU, mais CPUs e largura de banda de rede são atribuídos para o gateway e como resultado, o gateway pode suportar débito da rede superior à rede virtual.

VPN Gateway pode utilizar as SKUs do seguintes:

- Básicas
- Padrão
- Reversa

Quando seleciona um SKU, considere o seguinte:

- Se quiser utilizar um tipo de PolicyBased VPN, tem de utilizar o SKU básica. VPNs PolicyBased (anteriormente denominado encaminhamento estático) não são suportadas no quaisquer outros SKU.
- BGP não é suportada no SKU básica.
- ExpressRoute VPN Gateway coexistência configurações não são suportadas no SKU básica.
- Ligações de Gateway de VPN S2S activo activo podem ser configuradas no SKU reversa apenas.
