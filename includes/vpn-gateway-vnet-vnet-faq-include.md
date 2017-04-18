- As redes virtuais podem ser na mesmas ou em diferentes Azure regiões (localizações).

- Um serviço na nuvem ou um ponto final de balanceamento de carga não é possível do intervalo através das redes virtuais, mesmo se estiverem ligados em conjunto.

- Ligar-se em conjunto várias redes virtuais Azure não necessita de qualquer gateways VPN no local, a menos que é necessária conectividade de publicação em local.

- VNet para VNet suporta a ligação de redes virtual. Não suporta a ligação máquinas virtuais ou na nuvem serviços não numa rede virtual.

- VNet para VNet requer gateways Azure VPN com RouteBased (anteriormente denominado encaminhamento dinâmico) tipos VPN. 

- A conectividade da rede virtual pode ser utilizada em simultâneo com VPNs múltiplos sites, com um máximo de 10 (padrão/predefinido Gateways) ou 30 (Gateways de desempenho alta) VPN para um gateway VPN rede virtual ligar a outras redes de virtuais túnel ou no local sites.

- Os espaços de endereço dos sites de rede local de redes e no local virtuais não sobrepor. Sobreposição de espaços de endereços causará a criação de ligações de VNet para VNet falha.

- Túneis redundantes entre um par de redes virtuais não são suportados.

- Todos os túneis VPN da rede virtual partilham a largura de banda disponível no gateway Azure VPN e o mesmo VPN gateway tempo de utilização SLA no Azure.

- Tráfego VNet para VNet percorre a Microsoft Network, não a Internet.

- Tráfego VNet para VNet dentro da mesma região é gratuito para ambas as direções; cruzada região VNet para VNet saída tráfego é cobrado com as taxas de transferência de dados de saída de VNet, nomeadamente com base nas regiões de origem. Consulte a [página de preços](https://azure.microsoft.com/pricing/details/vpn-gateway/) para obter detalhes.