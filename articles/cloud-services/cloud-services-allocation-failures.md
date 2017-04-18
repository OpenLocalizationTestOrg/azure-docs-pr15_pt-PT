<properties
    pageTitle="Resolução de problemas de falha de alocação de serviço em nuvem | Microsoft Azure"
    description="Falha de alocação de resolução de problemas quando implementar serviços em nuvem no Azure"
    services="azure-service-management, cloud-services"
    documentationCenter=""
    authors="simonxjx"
    manager="felixwu"
    editor=""
    tags="top-support-issue"/>

<tags
    ms.service="cloud-services"
    ms.workload="na"
    ms.tgt_pltfrm="ibiza"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/12/2016"
    ms.author="v-six"/>



# <a name="troubleshooting-allocation-failure-when-you-deploy-cloud-services-in-azure"></a>Falha de alocação de resolução de problemas quando implementar serviços em nuvem no Azure

## <a name="summary"></a>Resumo
Quando implementar instâncias num serviço na nuvem ou adicionar novo Web site ou instâncias de função de trabalho, Microsoft Azure atribui cluster recursos. Poderá, ocasionalmente, receber erros ao efetuar estas operações mesmo antes de atingir os limites de subscrição Azure. Este artigo explica as causas de algumas das falhas de alocação comuns e sugere possível remediação. As informações também poderão ser útil quando planear a implementação dos seus serviços.

[AZURE.INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

### <a name="background--how-allocation-works"></a>Fundo – como funciona a alocação
Os servidores no Azure centros de dados são divididos em clusters. Um novo pedido de alocação de serviço de nuvem é tentado em vários clusters. Quando a primeira instância for implementada num serviço na nuvem (na transição ou produção), que o serviço de nuvem obtém afixada a um cluster de. Qualquer mais híbridas para o serviço de nuvem irão ocorrer no mesmo cluster. Neste artigo, iremos irá referir-se a esta tal como "afixados para um cluster de". Diagrama 1 abaixo ilustra as maiúsculas/minúsculas de uma atribuição de normal é tentada em vários clusters; Diagrama 2 ilustra as maiúsculas/minúsculas de uma atribuição que tem afixada à Cluster 2, uma vez que é onde está alojado a CS_1 de serviço de nuvem existente.

![Diagrama de alocação](./media/cloud-services-allocation-failure/Allocation1.png)

### <a name="why-allocation-failure-happens"></a>Por que motivo acontece falha de alocação
Quando um pedido de atribuição é afixado a um cluster, existe a oportunidade de a falhar obter recursos gratuitos, uma vez que o conjunto de recursos disponíveis está limitado a um cluster superior. Além disso, se o seu pedido de atribuição é afixado a um cluster, mas o tipo de recurso que solicitou não é suportado por esse cluster, o seu pedido irá falhar, mesmo se o cluster tiver recursos gratuito. Diagrama 3 abaixo ilustra as maiúsculas/minúsculas onde uma atribuição afixada falha porque o cluster candidatos apenas não possui recursos gratuitos. Diagrama de 4 ilustra as maiúsculas/minúsculas onde uma atribuição afixada falha porque o cluster candidatos apenas não suporta o tamanho da memória virtual pedido, apesar do cluster tem recursos gratuitos.

![Falha de alocação afixada](./media/cloud-services-allocation-failure/Allocation2.png)

## <a name="troubleshooting-allocation-failure-for-cloud-services"></a>Falha de atribuição de resolução de problemas para serviços em nuvem
### <a name="error-message"></a>Mensagem de erro
Poderá ver a seguinte mensagem de erro:

    "Azure operation '{operation id}' failed with code Compute.ConstrainedAllocationFailed. Details: Allocation failed; unable to satisfy constraints in request. The requested new service deployment is bound to an Affinity Group, or it targets a Virtual Network, or there is an existing deployment under this hosted service. Any of these conditions constrains the new deployment to specific Azure resources. Please retry later or try reducing the VM size or number of role instances. Alternatively, if possible, remove the aforementioned constraints or try deploying to a different region."

### <a name="common-issues"></a>Problemas comuns
Aqui estão os cenários de alocação comuns que fazem com que um pedido de atribuição de estar afixada num único cluster.

- Implementar para ranhura de teste - se num serviço na nuvem tem uma implementação no quer ranhura, em seguida, o serviço de nuvem inteira é afixado a um cluster específico.  Isto significa que se uma implementação já existe na ranhura produção, em seguida, uma nova implementação transição só pode ser atribuída no mesmo cluster como a ranhura de produção. Se o cluster está a chegar capacidade, poderá falhar o pedido.

- Dimensionamento - adicionar novas instâncias a um serviço de nuvem existente tem alocar no mesmo cluster.  Pedidos de dimensionamento pequeno normalmente pode ser atribuído, mas não sempre. Se o cluster está a chegar capacidade, poderá falhar o pedido.

- Grupo afinidade - uma nova implementação para um serviço em nuvem vazia pode ser atribuída ao ferro qualquer cluster nesse região, a menos que o serviço de nuvem é afixado a um grupo de afinidade. Implementações ao mesmo grupo afinidade serão tentadas no mesmo cluster. Se o cluster está a chegar capacidade, poderá falhar o pedido.

- Grupo afinidade vNet - redes virtuais mais antigos foram associados a afinidade grupos em vez de regiões e serviços em nuvem nestes redes virtuais seriam estar afixados à cluster afinidade do grupo. Implementações para este tipo de rede virtual serão tentadas no cluster afixado. Se o cluster está a chegar capacidade, poderá falhar o pedido.

## <a name="solutions"></a>Soluções

1. Implementar novamente para um novo serviço de nuvem - esta solução é provável que seja mais bem sucedida como-permite a plataforma escolher a partir de todos os clusters nesse região.

    - Implementar a carga de trabalho para um novo serviço em nuvem  

    - Atualizar o CNAME ou um registo que aponte o tráfego para o novo serviço na nuvem

    - Assim que zero tráfego vai para o site antigo, pode eliminar o serviço de nuvem antigo. Esta solução deve ser igual a zero tempo de inatividade.

2. Eliminar produção e faixas de transição - esta solução irá preservar o seu nome DNS existente, mas irá fazer com que o tempo de inatividade para a sua aplicação.

    - Eliminar a produção e teste faixas de um serviço de nuvem existente para que o serviço de nuvem está vazio e, em seguida,

    - Crie uma nova implementação no serviço de nuvem existente. Isto novamente tentará atribuição em todos os clusters na região. Certifique-se de que o serviço de nuvem não está associado a um grupo de afinidade.

3. IP reservado - esta solução irá preservar o seu IP existente endereço, mas irá fazer com que o tempo de inatividade para a sua aplicação.  

    - Criar uma reserva IP para a sua implementação existente utilizando o Powershell

    ```
    New-AzureReservedIP -ReservedIPName {new reserved IP name} -Location {location} -ServiceName {existing service name}
    ```

    - Siga #2 um ponto acima, certificando-se especificar a reserva de IP nova CSCFG o serviço.

4. Remova grupo afinidade para implementações novos - afinidade grupos já não são recomendadas. Siga os passos para #1 acima para implementar um novo serviço na nuvem. Certifique-se de que o serviço de nuvem não está num grupo afinidade.

5. Converta uma rede Virtual regionais - consulte o artigo [como migrar a partir de grupos de afinidade a uma rede Virtual regionais (VNet)](../virtual-network/virtual-networks-migrate-to-regional-vnet.md).
