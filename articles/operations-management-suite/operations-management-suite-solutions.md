<properties
   pageTitle="Soluções no conjunto de gestão de operações (OMS) | Microsoft Azure"
   description="Soluções aumentam a funcionalidade de conjunto de aplicações de gestão de operações (OMS), fornecendo cenários de gestão embalado que os clientes podem adicionar a sua área de trabalho OMS.  Este artigo fornece detalhes sobre como personalizadas soluções criadas por clientes e parceiros."
   services="operations-management-suite"
   documentationCenter=""
   authors="bwren"
   manager="jwhit"
   editor="tysonn" />
<tags
   ms.service="operations-management-suite"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/17/2016"
   ms.author="bwren" />

# <a name="management-solutions-in-operations-management-suite-oms-preview"></a>Soluções de gestão no conjunto de aplicações de gestão de operações (OMS) (pré-visualização)

>[AZURE.NOTE]Este é prévia documentação para soluções de gestão de OMS que estão atualmente em pré-visualização.    

Soluções de gestão de aumentam a funcionalidade de conjunto de aplicações de gestão de operações (OMS), fornecendo cenários de gestão embalado que podem adicionar clientes ao seu ambiente de trabalho.  Para além de [soluções fornecidas pela Microsoft](../log-analytics/log-analytics-add-solutions.md), parceiros e clientes, podem criar soluções de gestão para ser utilizado na sua própria ambiente ou disponibilizada para os clientes através da Comunidade.

## <a name="finding-and-installing-management-solutions"></a>Localizar e instalar as soluções de gestão
Existem vários métodos para localizar e instalar as soluções de gestão, tal como descrito nas secções seguintes.

### <a name="azure-marketplace"></a>Azure Marketplace
Soluções de gestão de fornecida pela Microsoft e parceiros fidedignos podem estar instalados do Azure Marketplace no portal do Azure.

1. Inicie sessão no portal do Azure.
2. No painel esquerdo, selecione **mais serviços**.
3. Desloque para baixo até **soluções** ou escreva *soluções* na caixa de diálogo **filtro** .
4. Clique no botão **+ Adicionar** .
5. Procurar soluções que está interessado em através de navegação, clicando no botão **filtro** ou escrever na caixa de **Pesquisa Everthing** .
6. Clique num item marketplace para ver as suas informações detalhadas.
4. Clique em **Criar** para abrir o painel **Adicionar solução** .
5. Será pedido para informações necessárias, como a [área de trabalho OMS e a conta de automatização](#oms-workspace-and-automation-account) para além de valores para quaisquer parâmetros na solução.
6. Clique em **Criar** para instalar a solução.

### <a name="oms-portal"></a>OMS Portal
Soluções de gestão de fornecido pela Microsoft, poderão estar instaladas a partir da Galeria de soluções no portal do OMS.

1. Inicie sessão no portal do OMS.
2. Clique no mosaico da **Galeria de soluções** .
2. Na página Galeria de soluções OMS, saiba mais sobre cada solução disponível. Clique no nome da solução que pretende adicionar a OMS.
3. Na página para a solução que tenha escolhido, informações detalhadas sobre a solução são apresentadas. Clique em **Adicionar**.
4. Um novo mosaico para a solução que tenha adicionado é apresentado a descrição geral do página nas OMS e pode começar a utilizá-la depois do serviço OMS processa os seus dados.

### <a name="azure-quickstart-templates"></a>Guia de introdução Azure modelos
Membros da Comunidade podem submeter soluções de gestão para modelos de guia de introdução do Azure.  Pode transferir estes modelos de instalação posterior ou inspecioná-los para saber como [criar o seus próprio soluções](#creating-a-solution).

1. Siga o processo descrito na [área de trabalho OMS e a conta de automatização](#oms-workspace-and-automation-account) para ligar a uma área de trabalho e conta.
2. Vá para o [Guia de introdução Azure modelos](https://azure.microsoft.com/documentation/templates/).  
3. Procurar uma solução que está interessado em.
4. Selecione a solução a partir dos resultados para ver os detalhes.
5. Clique no botão **implementar para Azure** .
6. Vai ser-lhe para fornecer informações como o grupo de recursos e a localização para além de valores para quaisquer parâmetros na solução.
7. Clique em **comprar** para instalar a solução.

### <a name="deploy-azure-resource-manager-template"></a>Implementar o modelo de Gestor de recursos do Azure
Soluções de que obter na Comunidade do ou esse [criados por si](#creating-a-solution) são implementadas como um modelo de Gestor de recursos e pode utilizar qualquer um dos métodos padrão para [Implementar um modelo](../resource-group-template-deploy-portal.md).  Tenha em atenção que antes de instalar a solução, tem de criar e ligar a [área de trabalho OMS e a conta de automatização](#oms-workspace-and-automation-account).

## <a name="oms-workspace-and-automation-account"></a>Área de trabalho OMS e a conta de automatização
A maioria das soluções de gestão de requerem uma [área de trabalho OMS](../log-analytics/log-analytics-manage-access.md) para contêm vistas e uma [conta de automatização](../automation/automation-security-overview.md#automation-account-overview) para conter runbooks e recursos relacionados. A área de trabalho e a conta tem os seguintes requisitos.

- Uma solução só pode utilizar uma OMS da área de trabalho e uma conta de automatização.  
- A área de trabalho OMS e a conta de automatização utilizado por uma solução devem estar associadas a um do outro. Uma área de trabalho OMS apenas pode ser ligada a uma conta de automatização e uma conta de automatização apenas pode ser ligada a uma área de trabalho OMS.
- Para ser ligadas a área de trabalho OMS e a conta de automatização tem de ser no mesmo grupo de recursos e região.  A exceção é uma área de trabalho OMS na região do Leste dos EUA e e conta de automatização no Leste dos EUA 2.

### <a name="creating-a-link-between-an-oms-workspace-and-automation-account"></a>Criar uma ligação entre uma área de trabalho OMS e a conta de automatização
Como pode especificar a área de trabalho OMS e conta automatização depende do método de instalação para a sua solução.

- Quando instalar uma solução Microsoft através do portal do OMS, estiver instalado na área de trabalho OMS atual e não é necessária nenhuma conta de automatização.

- Quando instalar uma solução através do Azure Marketplace, lhe for pedido para uma área de trabalho OMS e a conta de automatização e a ligação entre elas é criada por si.  

- Para obter soluções fora do Azure Marketplace, tem de ligar a área de trabalho OMS e a conta de automatização antes de instalar a solução.  Pode fazê-lo ao selecionar qualquer solução no Azure Marketplace e selecionar a área de trabalho OMS e a conta de automatização.  Não tem de instalar realmente a solução porque a ligação será criada assim que a área de trabalho OMS e a conta de automatização estão selecionados.  Quando a ligação estiver criada, em seguida, pode utilizar nessa área de trabalho OMS e a conta de automatização para qualquer solução. 

### <a name="verifying-the-link-between-an-oms-workspace-and-automation-account"></a>Verificar a ligação entre uma área de trabalho OMS e a conta de automatização
Pode verificar a ligação entre uma área de trabalho OMS e uma conta de automatização utilizando o procedimento seguinte.

1. Selecione a conta de automatização no portal do Azure.
2. Desloque-se para a parte inferior do painel de **Definições** .
3. Se existir uma secção denominada **Recursos OMS** no painel **Definições** , esta conta é anexada a uma área de trabalho OMS.
4. Selecione a **área de trabalho** para ver os detalhes da área de trabalho OMS ligadas a esta conta de automatização.


## <a name="listing-management-solutions"></a>Soluções de gestão de lista
Utilize o procedimento seguinte para ver as soluções de gestão nas áreas de trabalho associadas à sua subscrição do Azure.

1. Inicie sessão no portal do Azure.
2. No painel esquerdo, selecione **mais serviços**.
3. Desloque para baixo até **soluções** ou escreva *soluções* na caixa de diálogo **filtro** .
4. Serão listadas soluções instaladas em todas as suas áreas de trabalho.

Tenha em atenção que pode ver as soluções do Microsoft instaladas na área de trabalho atual, utilizando o portal OMS.

## <a name="removing-a-management-solution"></a>Remover uma solução de gestão
Quando uma solução de gestão é removida, todos os recursos da solução também são removidos.  

1. Localize a solução no portal do Azure utilizando o procedimento na [listagem soluções](#listing-solutions).
2. Selecione a solução que pretende remover.
3. Clique no botão **Eliminar** .

## <a name="creating-a-management-solution"></a>Criar uma solução de gestão
Concluída orientações sobre como criar soluções de gestão de estão disponíveis no [criar soluções no conjunto de aplicações de gestão de operações (OMS)](operations-management-suite-solutions-creating.md). 


## <a name="next-steps"></a>Próximos passos

- Pesquisar [Modelos do Azure guia de introdução](https://azure.microsoft.com/documentation/templates) para obter exemplos das diferentes modelos de Gestor de recursos.
- Crie o seu próprio [soluções de gestão](operations-management-suite-solutions-creating.md).
 