A tabela seguinte lista os requisitos de gateways PolicyBased e RouteBased VPN. Esta tabela aplica-se para o Gestor de recursos e de modelos de implementação clássica. Para o modelo clássico, gateways PolicyBased VPN são a mesma do estáticos gateways e com base em encaminhar gateways são a mesma do gateways dinâmicos.


|   | **PolicyBased Basic VPN Gateway** | **RouteBased Basic VPN Gateway** | **Padrão RouteBased VPN Gateway**   | **RouteBased alto desempenho VPN Gateway** |
|---|---------------------------------------|---------------------------------------|----------------------------|----------------------------------|
|    **Conectividade de site para o Site (S2S)**  | Configuração PolicyBased VPN        | Configuração RouteBased VPN  | Configuração RouteBased VPN     | Configuração RouteBased VPN    |
| Conectividade **ponto-para-Site (P2S**)      | Não é suportado   | Suportados (pode coexistência com S2S)  | Suportados (pode coexistência com S2S)  | Suportados (pode coexistência com S2S) |
| **Método de autenticação**                 |    Chave pré-partilhada  | Chave previamente partilhada para S2S conectividade, certificados para P2S conectividade | Chave previamente partilhada para S2S conectividade, certificados para P2S conectividade | Chave previamente partilhada para S2S conectividade, certificados para P2S conectividade |
| **Número máximo de ligações de S2S**       | 1                              | 10                                                                    | 10                                | 30                               |
| **Número máximo de ligações de P2S**       | Não é suportado                  | 128                                                                   | 128                               | 128                              |
|**Suporte de encaminhamento ativo (BGP)**           | Não é suportado                  | Não é suportado                                                         | Suportada                     | Suportada                   |
 
