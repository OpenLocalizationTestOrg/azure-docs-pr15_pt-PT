<properties
    pageTitle="Perguntas mais frequentes sobre bases de dados do ClearDB MySql com a aplicação de serviço de Azure | Microsoft Azure"
    description="Respostas a perguntas frequentes sobre como utilizar bases de dados do ClearDB MySQL com a aplicação de serviço de Azure."
    documentationCenter="php"
    services=""
    authors="sunbuild"
    manager="yochayk"
    editor=""
    tags="mysql"/>

<tags
    ms.service="multiple"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/27/2016"
    ms.author="sumuth"/>

# <a name="faq-for-cleardb-mysql-databases-with-azure-app-service"></a>Perguntas mais frequentes sobre bases de dados do ClearDB MySql com o serviço de aplicação do Azure

Este artigo responde a questões recorrentes sobre a utilização e compra de bases de dados ClearDB MySQL para Azure Web Apps.

## <a name="what-options-do-i-have-for-mysql-on-azure"></a>Que opções tenho de ter para MySQL no Azure?

Tem várias opções:

* [Base de dados do MySQL partilhadas do ClearDB](/marketplace/partners/cleardb/databases/)

* [Clusters ClearDB MySQL Premium](/marketplace/partners/cleardb-clusters/cluster/)

* [Cluster de MySQL em execução numa VM Azure](https://github.com/azure/azure-quickstart-templates/tree/master/mysql-replication)

* [Única instância do MySQL em execução numa VM Azure](virtual-machines/virtual-machines-windows-classic-mysql-2008r2.md)

ClearDB é um serviço de alojamento de MySQL e gere a infraestrutura do MySQL por si. Quando executa o seu próprio MySQL cluster ou base de dados numa máquina de Virtual do Azure, tem de configurar o servidor do MySQL e mantê-lo actualizado com patches.

## <a name="do-i-need-a-credit-card-for-the-web-app--mysql-template-in-the-azure-marketplace"></a>Preciso de um cartão de crédito para o modelo do MySQL no Azure Marketplace + Web app?

Isto depende do tipo de subscrição que está a utilizar. Aqui estão alguns tipos de subscrição utilizadas mais frequentemente:

* [Colar como pague](/offers/ms-azr-0003p/): requer um cartão de crédito e, quando compra uma base de dados do MySQL paga é cobrada seu cartão de crédito.

* [Versão de avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/): inclui créditos para utilização com o Microsoft Azure serviços mas não permite a compra de recursos de terceiros. Comprar serviços de terceiros ou uma base de dados do MySQL paga que tem de utilizar uma subscrição de cartão de crédito ativado. Para aplicações Web, pode criar uma base de dados do MySQL de ClearDB gratuito.

* [Subscrição do MSDN](/pricing/member-offers/msdn-benefits/) e **MSDN Dev Center teste pagar à medida que aceda**: semelhante à versão de avaliação gratuita, uma subscrição do MSDN requer que tenha um cartão de crédito para comprar uma solução MySQL paga a partir do ClearDB.

* [Enterprise Agreement (EA)](/pricing/enterprise-agreement/): clientes EA são faturados contra os respetivos EA cada trimestre para todas as suas compras (terceiros) do Azure Marketplace numa fatura em separado, consolidada. São faturada fora da consolidação monetária para qualquer compras marketplace. Tenha em atenção que, neste momento, não está disponível para os clientes inscritos no Azerbaijão, Croácia, Noruega e Porto Rico Azure loja. 

*  [DreamSpark](https://www.dreamspark.com/Product/Product.aspx?productid=99): pode criar apenas ClearDB gratuito bases de dados para aplicações Web. Não existe nenhum limite o número de bases de dados do MySQL gratuito do ClearDB que pode criar. Note que são gratuitos bases de dados não pode ser utilizado para aplicações web de produção, tal como este serviço destina-se apenas para a versão de avaliação.

## <a name="why-was-i-charged-350-for-a-web-app--mysql-from-the-azure-marketplace"></a>Por que motivo foi a cobrada $3.50 para uma aplicação Web + MySQL do Azure Marketplace?

A opção de base de dados predefinido é Titan, que é $3.50. Vamos não mostrar o custo durante a criação de base de dados e, por engano pode adquirir uma base de dados que não queira. Vamos está a tentar encontrar uma forma para melhorar a experiência mas até, em seguida, tem de verificar todas as sua camadas comparar selecionadas para a web app e base de dados antes de clicar em **Criar** e começar a implementação dos recursos.

## <a name="i-am-running-mysql-on-my-own-azure-virtual-machine-can-i-connect-my-azure-web-app-to-my-database"></a>Estou a executar o MySQL no meu próprio máquina virtual Azure. Pode ligar minha aplicação Azure web a minha base de dados?

Sim. Pode ligar a sua aplicação web para a base de dados desde a VM Azure concedeu acesso remoto para a sua aplicação web. Para mais informações, consulte o artigo [Instalar MySQL numa máquina virtual](virtual-machines/virtual-machines-windows-classic-mysql-2008r2.md).

## <a name="in-which-countries-are-cleardb-premium-mysql-clusters-supported"></a>Em que países/regiões são clusters ClearDB Premium MySQL suportados?

[Clusters de ClearDB Premium MySQL](/marketplace/partners/cleardb-clusters/cluster/) estão disponíveis em todas as regiões Azure em todo o mundo, à exceção dos Índia, Austrália, sul do Brasil e China.

## <a name="can-i-create-a-new-cluster-prior-to-creating-a-database-with-cleardb-premium-cluster-solution"></a>Pode criar um novo cluster antes de criar uma base de dados com a solução de cluster ClearDB premium?

Não, a criar vazios ClearDB clusters não é suportada. Portal do Azure permite-lhe criar bases de dados num cluster, que pode criar um novo cluster ao mesmo tempo.

## <a name="will-i-be-warned-if-i-try-to-delete-a-cleardb-mysql-database-that-is-in-use-by-one-of-my-applications"></a>Será posso avisado se tento eliminar uma base de dados do ClearDB MySQL que está a ser utilizado por uma das minhas aplicações?

Não, Azure irá não avisá-lo se eliminar uma compra única marketplace que depende da sua aplicação.

## <a name="which-regions-can-i-create-cleardb-databases-in"></a>Que regiões posso criar ClearDB bases de dados?

Azure Marketplace não está disponível para os clientes inscritos no Azerbaijão, Croácia, Noruega ou Porto Rico. ClearDB não está disponível nas seguintes regiões.

## <a name="what-pricing-tier-should-i-choose-for-a-production-web-app-and-database"></a>Que camada comparar devo escolher para uma aplicação web de produção e a base de dados?

Utilize Basic ou uma camada preços mais elevada para Web Apps. Para ClearDB, recomendamos que um Saturno ou Júpiter plano. Rever as funcionalidades e limitações de cada camada comparar para [Aplicações Web](/pricing/details/app-service/) e [bases de dados do ClearDB MySQL](/marketplace/partners/cleardb/databases/) escolher aquele que se adequa às suas necessidades.

## <a name="how-do-i-upgrade-my-cleardb-database-from-one-plan-to-another"></a>Como atualizar minha base de dados ClearDB de um plano para outro?

No [portal do Azure](https://portal.azure.com), pode dimensionar um ClearDB alojamento base de dados partilhada. Leia este [artigo](https://blogs.msdn.microsoft.com/appserviceteam/2016/10/06/upgrade-your-cleardb-mysql-database-in-azure-portal/) para obter mais informações. Atualmente não suportamos atualização para clusters ClearDB Premium no portal do Azure.

## <a name="i-cant-see-my-cleardb-database-in-azure-portal"></a>Não consigo ver a minha base de dados ClearDB no Azure portal?

Se criamos ClearDB da base de dados utilizando o Gestor de recursos do Azure ou [Novo Portal Azure](https://portal.azure.com), não estarão visível no [Portal do Azure antigo](https://manage.windowsazure.com). Para trabalho-à volta esta é a ligação da base de dados manualmente a aplicação web. Da mesma forma se criar ClearDB base de dados no [portal antiga](https://manage.windowsazure.com) não poderá ver a sua base de dados no [Novo Portal Azure](https://portal.azure.com). Não existe nenhuma trabalho-à volta para o último cenário.

## <a name="who-do-i-contact-for-support-when-my-database-is-down"></a>Quem posso contactar para obter suporte quando a minha base de dados está indisponível?

Contacto [ClearDB de suporte](https://www.cleardb.com/developers/help/support) para quaisquer bases de dados relacionados com problemas. Ser preparado para fornecê-los com as suas informações de subscrição Azure.

## <a name="can-i-create-additional-users-for-my-cleardb-mysql-database-cluster-solution"></a>Pode criar utilizadores adicionais para minha solução de cluster de base de dados do ClearDB MySQL? 

Não. Não é possível criar utilizadores adicionais, mas pode criar bases de dados adicionais no seu cluster de base de dados ClearDB.  

## <a name="can-basicpro-series-databases-be-upgraded-in-place-similar-to-planetary-plans-today-on-cleardb-portal"></a>Pode série Basic/Pro bases de dados ser atualizado no local semelhante aos planos planetários hoje no portal de ClearDB?

Sim, série básica bases de dados podem ser atualizado no local (60 básicas através de 500 básicas). Série Pro pode ser atualizado no local (125 Pro através de Pro 1000), à exceção Pro 60. Não suporta a atualizar atualmente Pro 60 base de dados. 

## <a name="when-i-migrate-my-resources-from-one-subscription-to-another-does-my-cleardb-mysql-database-get-migrated-as-well"></a>Quando migrar os meus recursos a partir de uma subscrição para outro, minha base de dados do ClearDB MySQL é migrado também? 

Quando executar a migração de recursos entre subscrições, aplicam-se algumas [limitações](./app-service-web/app-service-move-resources.md) . Uma base de dados do ClearDB MySQL é um serviço de terceiros e, consequentemente, não é migrado durante a migração de subscrição Azure. Se não gerir a migração da base de dados MySQL antes de migrar recursos Azure, as bases de dados do ClearDB MySQL podem ser desativadas. Migre manualmente as bases de dados pela primeira vez e, em seguida, efetue migração Azure subscrição para a sua aplicação web. 

## <a name="can-i-transfer-a-cleardb-database-from-a-credit-card-subscription-to-an-ea-subscription"></a>Posso transferir uma base de dados ClearDB de uma subscrição de cartão de crédito para uma subscrição do EA?

Bases de dados existentes ClearDB utilizam o cartão de crédito associado com as subscrições existentes. Para utilizar uma subscrição do EA que precisar de migrar os seus dados para uma nova base de dados:

* Compre uma nova base de dados de ClearDB com a sua subscrição EA.
* Migrar os seus dados para a nova base de dados.
* Atualize a aplicação para utilizar a nova base de dados.
* Elimine a base de dados ClearDB antigo.

Quando criar uma nova aplicação web com MySQL (ClearDB) ou criar uma base de dados do MySQL (ClearDB), a subscrição que escolher determina a forma como irá pagar para o serviço. Com uma subscrição de EA, irá não bloquearmos contratos dos serviços de terceiros como ClearDB no portal do Azure. Subscrições de EA são faturadas fora da consolidação monetários e são faturadas trimestral e, em atraso. O cliente EA teria que configurar um método de pagamento, como o cartão de crédito para pagar a quaisquer serviços de terceiros marketplace.

## <a name="where-can-i-see-the-charges-for-cleardb-resources-in-an-ea-subscription"></a>Onde pode ver os custos para recursos ClearDB uma subscrição do EA?

Para clientes EA direta, encargos Azure Marketplace estão visíveis no Portal do Enterprise. Note que todos os marketplace compras e consumo são faturadas fora da consolidação monetários e são faturadas trimestral e, em atraso. Clientes EA tem de pagar diretamente para os fornecedores de serviços de terceiros e podem fazê-lo ao ativar um método de pagamento, como o cartão de crédito, com a sua conta EA.

Clientes EA indireto podem encontrar as subscrições do Azure Marketplace na página **Gerir subscrições** do Portal da empresa, mas preços estiver oculto. Os clientes devem contactar os respetivos LSP para obter informações sobre taxas marketplace.

Acesso ao Azure Marketplace para serviços de terceiros pode ser gerido pelos administradores de inscrição EA Azure. Que ele pode desativar ou reativar o acesso às compras de festa 3º a partir da loja no gerir contas e subscrições na secção de contas no Portal do Enterprise.

## <a name="who-do-i-contact-for-questions-about-my-bill-for-cleardb-services-in-my-ea-subscription"></a>Quem posso contactar para as questões sobre a minha fatura para serviços de ClearDB na minha subscrição EA?

Contacte o [Suporte ao cliente da empresa](http://aka.ms/AzureEntSupport) no que diz respeito faturação em sua inscrição EA. A equipa de suporte do Portal de EA irá responder à pergunta ou ajudar a resolver o problema.

 



## <a name="more-information"></a>Obter mais informações

[Perguntas mais frequentes do Azure Marketplace](/marketplace/faq/)
