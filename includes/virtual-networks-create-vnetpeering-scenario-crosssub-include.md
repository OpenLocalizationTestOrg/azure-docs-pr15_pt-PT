## <a name="peering-across-subscriptions"></a>Efectuado entre subscrições

Neste cenário irá criar um efectuado entre dois VNets que pertencem a diferentes subscrições.

![cenário de sub cruzada](./media/virtual-networks-create-vnetpeering-scenario-crosssub-include/figure01.PNG)

VNet efectuado baseia-se sobre o controlo de acesso baseado em funções (RBCA) para autorização. Cenário de publicação em subscrições, primeiro terá de conceder permissão aos utilizadores que irão criar a ligação peering:

> [AZURE.NOTE] Se o mesmo utilizador tenha o privilégio sobre ambas as subscrições, em seguida, pode ignorar passo1-4 abaixo.
