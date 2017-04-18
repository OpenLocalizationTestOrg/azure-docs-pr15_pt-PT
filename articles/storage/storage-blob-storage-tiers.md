<properties
    pageTitle="Azure armazenamento interessante para blobs | Microsoft Azure"
    description="Camadas de armazenamento para armazenamento de Blobs do Azure oferecem armazenamento eficiente de custo para os dados de objecto com base em padrões de acesso. A camada de armazenamento modernos está optimizada para dados que são acedidos com menos frequência."
    services="storage"
    documentationCenter=""
    authors="michaelhauss"
    manager="vamshik"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/18/2016"
    ms.author="mihauss"/>


# <a name="azure-blob-storage-hot-and-cool-storage-tiers"></a>Armazenamento de Blobs do Azure: Quente e arrefecer camadas de armazenamento

## <a name="overview"></a>Descrição geral

Armazenamento Azure oferece agora duas camadas de armazenamento para armazenamento de BLOBs (armazenamento de objeto), para que pode armazenar os dados de forma mais rentável dependendo de como utilizá-lo. A Azure **camada de armazenamento quente** está optimizado para armazenar os dados são acedidos frequentemente. A Azure **camada de armazenamento modernos** está optimizado para armazenar os dados que é com pouca frequência acedido e longa vida. Dados na camada armazenamento modernos podem tolerar uma disponibilidade ligeiramente inferior, mas ainda requer durabilidade alta, semelhante tempo para aceder e características de débito como dados quentes. Para dados interessantes, SLA de disponibilidade ligeiramente inferior e superiores custos de acesso são aceitáveis compromissos para muito reduzir os custos de armazenamento.

Hoje em dia, os dados armazenados na nuvem são cultura uma exponencial ritmo. Para gerir custos às suas necessidades de armazenamento de expansão, é útil organizar os seus dados com base em atributos, como a frequência de acesso e período de retenção planeada. Dados armazenados na nuvem, podem ser bastante diferentes em termos como-é gerado, processada e acedido através da sua vida útil. Alguns dados ativamente são acedidos e modificados em toda a sua vida útil. Alguns dados são acedidos frequentemente muito cedo em sua vida útil, com o access largando muito como as idades de dados. Alguns dados permanecem inactivos na nuvem e raramente são, se nunca, pode ser consultada uma vez armazenados.

Cada um destes cenários de acesso de dados descritos acima benefícios a partir de uma camada diferenciada do armazenamento está optimizada para um padrão específico do access. Com a introdução das camadas de armazenamento quente e fria, BLOBs do Azure armazenamento agora endereços esta necessidade de camadas de armazenamento diferenciada com separe os preços modelos.

## <a name="blob-storage-accounts"></a>Contas de armazenamento de BLOBs

**Contas de armazenamento de BLOBs** são armazenamento especializado contas para armazenar os seus dados não estruturados como blobs (objetos) no armazenamento do Windows Azure. Com contas de armazenamento de BLOBs, pode agora optar entre camadas de armazenamento quente e fria para armazenar os dados interessantes menos acedidos num armazenamento inferior de custos e armazenam dados quentes acedidos mais frequentemente em acesso a um inferior de custo. Contas de armazenamento de BLOBs são semelhantes às suas contas de armazenamento geral existentes e partilham todas as a durabilidade excelente, disponibilidade, escalabilidade e funcionalidades de desempenho que utiliza hoje em dia, incluindo a 100% consistência de API para blobs bloco e acrescentar blobs.

> [AZURE.NOTE] Contas de armazenamento de BLOBs suportam apenas bloco e acrescentar blobs e não blobs de página.

Contas de armazenamento de BLOBs expõem o atributo **Camada de acesso** , que permitem-lhe especificar a camada de armazenamento como **quente** ou **modernos** dependendo dos dados armazenados na conta. Se existir uma alteração no padrão de utilização dos seus dados, também pode alternar entre estas camadas de armazenamento em qualquer altura.

> [AZURE.NOTE] Alterar a camada de armazenamento pode resultar em taxas adicionais. Consulte a secção de [preços e de faturação](storage-blob-storage-tiers.md#pricing-and-billing) para obter mais detalhes.

Cenários de utilização de exemplo para a camada de armazenamento quente incluem:

- Dados que está a ser utilizado ativo ou esperado ser acedido (leitura a partir de e escritas) frequentemente.
- Dados estão testados para processamento e eventual migração para a camada de armazenamento modernos.

Cenários de utilização de exemplo para a camada de armazenamento modernos incluem:

- Cópia de segurança, arquivo e conjuntos de dados de recuperação de falhas.
- Conteúdo de multimédia mais antigo não visualizadas frequentemente deixem, mas é esperado esteja disponível imediatamente quando acedidas.
- Grandes conjuntos de dados que precisam de ser armazenado custo eficazmente enquanto estão a ser reuniu mais dados para processamento futuro. (*por exemplo*, a longo prazo armazenamento dos dados científicos, dados de telemetria não processado a partir de uma funcionalidade de fabrico)
- Originais (dados não processados) têm de ser preservados, mesmo depois de ter sido processado no formulário utilizável final. (*por exemplo*, ficheiros de multimédia não processado após transcodificação para outros formatos)
- Conformidade e arquivo dados que tem de ser armazenado muito tempo e quase nunca são acedidos. (*por exemplo*, imagens da câmara de segurança, antigo X-Rays/MRIs para organizações cuidados de saúde, gravações de áudio e transcrições de chamadas de cliente dos serviços financeiros)

Consulte o artigo [sobre Azure contas de armazenamento](storage-create-storage-account.md) para mais informações sobre contas de armazenamento.

Para aplicações que requerem apenas bloquear ou acrescentar armazenamento de BLOBs, recomendamos que utilize contas de armazenamento de BLOBs, para tirar partido do modelo diferenciado preços de armazenamento em camadas. No entanto, compreendemos que este poderá não ser possível em determinadas circunstâncias onde utilizando as contas de armazenamento geral seria a forma de aceder, tais como:

- Tem de utilizar o tabelas, consultas ou ficheiros e pretender seu armazenados na mesma conta de armazenamento de blobs. Nota que não existe nenhuma partido técnico para armazenar estes na mesma conta que não está a ter o mesmo partilhado teclas.
- Ainda precisa de utilizar o modelo de implementação clássica. Contas de armazenamento de BLOBs só estão disponíveis através do modelo de implementação de Gestor de recursos do Azure.
- Tem de utilizar blobs de página. Contas de armazenamento de BLOBs não suportam blobs de página. Recomendamos que utilizar blobs do bloco, exceto se tiver uma necessidade específica para blobs de página.
- Utilizar uma versão do [Armazenamento dos serviços REST API](https://msdn.microsoft.com/library/azure/dd894041.aspx) é anteriores ao 2014-02-14 ou uma biblioteca de cliente com uma versão inferior a 4. x e não é possível atualizar a sua aplicação.

> [AZURE.NOTE] Contas de armazenamento de BLOBs são atualmente suportadas na maioria das regiões Azure com mais a seguir. Pode encontrar na lista actualizada de regiões disponíveis na página [Dos serviços do Azure por região](https://azure.microsoft.com/regions/#services) .

## <a name="comparison-between-the-storage-tiers"></a>Comparação entre as camadas de armazenamento

A tabela seguinte realça a comparação entre as duas camadas de armazenamento:

<table border="1" cellspacing="0" cellpadding="0" style="border: 1px solid #000000;">
<col width="250">
<col width="250">
<col width="250">
<tbody>
<tr>
    <td><strong><center></center></strong></td>
    <td><strong><center>Camadas de armazenamento quente</center></strong></td>
    <td><strong><center>Camadas de armazenamento modernos</center></strong></td
</tr>
<tr>
    <td><strong><center>Disponibilidade</center></strong></td>
    <td><center>99,9%</center></td>
    <td><center>99%</center></td>
</tr>
<tr>
    <td><strong><center>Disponibilidade<br>(Lê GRS RT)</center></strong></td>
    <td><center>99,99%</center></td>
    <td><center>99,9%</center></td>
</tr>
<tr>
    <td><strong><center>Custos de utilização</center></strong></td>
    <td><center>Custos de armazenamento mais elevados<br>Reduzir os custos de acesso e da transação</center></td>
    <td><center>Reduzir os custos de armazenamento<br>Custos de acesso e da transação mais elevados</center></td>
</tr>
<tr>
    <td><strong><center>Tamanho mínimo de objeto<center></strong></td>
    <td colspan="2"><center>N/D</center></td>
</tr>
<tr>
    <td><strong><center>Duração de armazenamento mínima<center></strong></td>
    <td colspan="2"><center>N/D</center></td>
</tr>
<tr>
    <td><strong><center>Latência<br>(Tempo para o primeiro byte)<center></strong></td>
    <td colspan="2"><center>milissegundos</center></td>
</tr>
<tr>
    <td><strong><center>Desempenho e escalabilidade destinos<center></strong></td>
    <td colspan="2"><center>Mesmo que as contas de armazenamento geral</center></td>
</tr>
</tbody>
</table>

> [AZURE.NOTE] Contas de armazenamento de BLOBs suportam o desempenho do mesmo e destinos escalabilidade como contas de armazenamento geral. Para mais informações, consulte [escalabilidade de armazenamento do Azure e metas de desempenho](storage-scalability-targets.md) .

## <a name="pricing-and-billing"></a>Preços e faturação

Contas de armazenamento de BLOBs utilizam um novo modelo comparar para o armazenamento de Blobs com base na camada armazenamento. Ao utilizar uma conta de armazenamento de BLOBs, aplicam-se as seguintes considerações de faturaçãohttps:

- **Os custos de armazenamento**: para além da quantidade de dados armazenados, os custos do armazenamento dos dados variam consoante a camada de armazenamento. O custo por gigabyte é inferior para a camada de armazenamento modernos que para a camada de armazenamento quente.
- **Os custos de acesso a dados**: para os dados na camada armazenamento modernos, será cobrado uma taxa de acesso de dados por gigabyte para operações de leitura e escrita.
- **Custos da transação**: existe uma carga da transação por para ambas as camadas. No entanto, o custo por transação para a camada de armazenamento modernos é superior para a camada de armazenamento quente.
- **Os custos de transferência de dados de replicação geo**: Isto aplica-se apenas para contas com geo-replicação configurado, incluindo GRS e GRS RT. Transferência de dados de replicação geo como uma taxa por gigabyte.
- **Transferência de custos de dados de saída**: transferências de dados de saída (dados que são transferidas terminar uma região Azure) implicam faturação para a utilização da largura de banda numa base por gigabyte, consistente com contas de armazenamento geral.
- **Alterar a camada de armazenamento**: alterar a camada de armazenamento a partir de interessantes para quente irá impliquem igual a todos os dados existentes na conta de armazenamento para cada transição de leitura. Por outro lado, alterar a camada de armazenamento a partir de mais ativos para interessantes serão gratuito do custo.

> [AZURE.NOTE] Para permitir que os utilizadores experimentar as novas camadas de armazenamento e lançamento de mensagem de funcionalidade, a imposição para alterar o armazenamento de validar camada de interessantes para quente irão ser aplicada desativar até º de Junho 30. de 2016. Iniciar de Julho de 1. º de 2016, o encargo será aplicado a todas as transições de interessantes para quente. Para obter mais detalhes sobre o modelo de preços de armazenamento de BLOBs contas, consulte [Preços de armazenamento do Azure](https://azure.microsoft.com/pricing/details/storage/) página. Para obter mais detalhes sobre os dados de saída taxas de transferência, consulte a página de [Detalhes de preços de transferências de dados](https://azure.microsoft.com/pricing/details/data-transfers/) .

## <a name="quick-start"></a>Guia de introdução

Nesta secção podemos vai demonstrar os seguinte cenários utilizando o portal Azure:

- Como criar uma conta de armazenamento de Blobs.
- Como gerir uma conta de armazenamento de Blobs.

### <a name="using-the-azure-portal"></a>Utilizar o portal do Azure

#### <a name="create-a-blob-storage-account-using-the-azure-portal"></a>Criar uma conta de armazenamento de BLOBs utilizando o portal do Azure

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

2. No menu concentrador, selecione **Novo** > **dados + armazenamento** > **conta de armazenamento**.

3. Introduza um nome para a sua conta de armazenamento.

    Este nome deve ser globalmente exclusivo; é utilizada como parte do URL utilizado para aceder aos objetos na conta de armazenamento.  

4. Selecione o **Gestor de recursos** que o modelo de implementação.

    Armazenamento em camadas só pode ser utilizado com contas de armazenamento do Gestor de recursos; Este é o modelo de implementação recomendado para novos recursos. Para mais informações, consulte o artigo [Descrição geral do Gestor de recursos do Azure](../azure-resource-manager/resource-group-overview.md).  

5. Na lista pendente Tipo de conta, selecione o **Armazenamento de BLOBs**.

    Este é onde pode selecionar o tipo de conta de armazenamento. Armazenamento em camadas não está disponível no armazenamento uso geral; só está disponível na conta de tipo de armazenamento de Blobs.    

    Tenha em atenção que, quando seleccionar esta opção, a camada de desempenho está definida para padrão. Armazenamento em camadas não está disponível com a camada de desempenho Premium.

6. Selecione a opção de replicação para a conta de armazenamento: **LRS**, **GRS**ou **RT GRS**. A predefinição é **GRS RT**.

    LRS = armazenamento localmente redundante; GRS = armazenamento geo redundantes (2 regiões); RT GRS é armazenamento geo redundantes acesso de leitura (2 regiões com acesso de leitura para a segunda).

    Para obter mais detalhes sobre as opções de replicação de armazenamento do Windows Azure, consulte o artigo [replicação de armazenamento do Windows Azure](storage-redundancy.md).

7. Selecione a camada de armazenamento à direita para as suas necessidades: defina a **camada de acesso** para **modernos** ou **quente**. A predefinição é **quente**.

8. Selecione a subscrição na qual pretende criar a nova conta de armazenamento.

9. Especificar um novo grupo de recursos ou selecione um grupo de recursos existente. Para mais informações sobre os grupos de recursos, consulte o artigo [Descrição geral do Gestor de recursos do Azure](../azure-resource-manager/resource-group-overview.md).

10. Selecione a região para a sua conta de armazenamento.

11. Clique em **Criar** para criar a conta de armazenamento.

#### <a name="change-the-storage-tier-of-a-blob-storage-account-using-the-azure-portal"></a>Alterar a camada de armazenamento de uma conta de armazenamento de BLOBs através do portal Azure

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

2. Para navegar para a sua conta de armazenamento, selecione todos os recursos, em seguida, selecione a sua conta de armazenamento.

3. No separador Definições, clique em **configuração** para ver e/ou alterar a configuração de conta.

4. Selecione a camada de armazenamento à direita para as suas necessidades: defina a **camada de acesso** para **modernos** ou **quente**.

5. Clique em Guardar no topo da pá.

> [AZURE.NOTE] Alterar a camada de armazenamento pode resultar em taxas adicionais. Consulte a secção de [preços e de faturação](storage-blob-storage-tiers.md#pricing-and-billing) para obter mais detalhes.

## <a name="evaluating-and-migrating-to-blob-storage-accounts"></a>Avaliar e migrar para contas de armazenamento de BLOBs

É o objetivo nesta secção ajudar os utilizadores para tornar uma transição suave para utilizando as contas de armazenamento de Blobs. Existem dois cenários de utilizador:

- Tem uma conta de armazenamento geral existente e pretende avaliar uma alteração a uma conta de armazenamento de Blobs com a camada de armazenamento à direita.
- Tiver decidido utilizar uma conta de armazenamento de BLOBs ou já tiver um e pretende avaliar se deve utilizar a camada de armazenamento quentes ou modernos.

Em ambos os casos, o primeiro na ordem de trabalhos é calcular o custo de armazenar e aceder aos seus dados armazenados numa conta de armazenamento de BLOBs e compare com os custos atuais.

### <a name="evaluating-blob-storage-account-tiers"></a>Avaliar camadas de conta de armazenamento de BLOBs

Para calcular o custo de armazenar e aceder a dados armazenados numa conta de armazenamento de BLOBs, terá de avaliar o padrão de utilização existente ou aproximar seu padrão de utilização prevista. Em geral, irá querer saber:

- Consumo seu armazenamento - como quantidade de dados que está a ser armazenado e como é que isto alterar mensais?
- O padrão de acesso de armazenamento - quantidade de dados está a ser lerem e escritas à conta (incluindo novos dados)? Quantas transações são utilizadas para acesso aos dados e que tipos de transações são?

#### <a name="monitoring-existing-storage-accounts"></a>Monitorização de contas de armazenamento existentes

Para monitorizar as suas contas de armazenamento existentes e reunir estes dados, pode tornar a utilização do Azure armazenamento Analytics que executa o registo e fornece métricas dados para uma conta de armazenamento.
Análise de armazenamento pode armazenar métricas que incluam da transação agregado estatísticas e a capacidade de dados sobre os pedidos para o serviço de armazenamento de BLOBs para tanto contas de armazenamento geral, bem como contas de armazenamento de Blobs.
Estes dados são armazenados em tabelas famoso na mesma conta de armazenamento.

Para obter mais detalhes, consulte o artigo [Sobre métricas de análise de armazenamento](https://msdn.microsoft.com/library/azure/hh343258.aspx) e [Esquema de tabela de métricas de análise de armazenamento](https://msdn.microsoft.com/library/azure/hh343264.aspx)

> [AZURE.NOTE] Contas de armazenamento de BLOBs expõem o ponto final de serviço tabela apenas para armazenar e aceder aos dados métricas dessa conta.

Para monitorizar o consumo de armazenamento para o serviço de armazenamento de BLOBs, terá de ativar as métricas de capacidade.
Com esta opção activada, dados de capacidade são registados diária do serviço de BLOBs de uma conta de armazenamento e gravado como uma entrada de tabela escrito à tabela *$MetricsCapacityBlob* dentro da mesma conta de armazenamento.

Para monitorizar o padrão de acesso de dados para o serviço de armazenamento de BLOBs, terá de ativar as preço por hora métricas da transação a um nível de API.
Com esta ativado, por API transações são agregadas a cada hora e registadas como uma entrada da tabela escrito à tabela *$MetricsHourPrimaryTransactionsBlob* dentro da mesma conta de armazenamento. A tabela *$MetricsHourSecondaryTransactionsBlob* registos transações para o ponto final secundário em caso de contas de armazenamento GRS RT.

> [AZURE.NOTE] No caso de ter uma conta de armazenamento geral nos quais armazenou blobs de página e discos de máquina virtual juntamente com bloco e acrescentar dados blob, este processo estimativa não é aplicável. Esta é uma vez que não terá de nenhuma forma de capacidade distintiva e métricas da transação com base no tipo de BLOBs para apenas bloquear e acrescentar blobs que podem ser migradas para uma conta de armazenamento de Blobs.

Para obter uma boa aproximação de consumo dados e o padrão de acesso, recomendamos que escolha um período de retenção para os que é o representante da sua utilização regular métricas e extrapolar.
É uma opção manter os dados de métricas de 7 dias e reunir os dados de cada semana, para uma análise no final do mês.
Outra opção é manter os dados de métricas de últimos 30 dias e recolher e analisar os dados no final do período de 30 dias.

Para obter detalhes sobre como ativar, recolher e visualizar dados de métricas, consulte o artigo, [métricas de armazenamento do Windows Azure ativar e visualizar dados de métricas](storage-enable-and-view-metrics.md).

> [AZURE.NOTE] Armazenar, aceder a e transferir dados de análise também são cobrada tal como faria com dados de utilizador habitual.

#### <a name="utilizing-usage-metrics-to-estimate-costs"></a>Recorrendo métricas de utilização para estimar custos

##### <a name="storage-costs"></a>Custos de armazenamento

A entrada mais recente na capacidade métricas tabela *$MetricsCapacityBlob* com a linha de chave *'dados'* mostra a capacidade de armazenamento média consumida por dados de utilizador.
A entrada mais recente na capacidade métricas tabela *$MetricsCapacityBlob* com a chave de linha *'analytics'* mostra a capacidade de armazenamento média consumida pelos registos de análise.

Esta capacidade total média consumida por ambos os dados de utilizador e os registos de análise (Se ativado), em seguida, podem ser utilizados para calcular o custo do armazenamento dos dados na conta de armazenamento.
O mesmo método podem também ser utilizados para estimar custos de armazenamento para o bloco de e acrescentar blobs nas contas de armazenamento geral.

##### <a name="transaction-costs"></a>Custos da transação

A soma de *'TotalBillableRequests'*, através de todas as entradas para uma API na tabela da transação métricas indica o número total de transações para esse API específico. *por exemplo*, o número total de transações *'GetBlob'* num determinado período pode ser calculado pela soma dos pedidos de cobrar total para todas as entradas com a chave de linha *' utilizador; GetBlob'*.

Para poder estimar custos da transação para contas de armazenamento de BLOBs, terá de divide das transações para três grupos, uma vez que estes estão preços de forma diferente.

- Escreva as transações como *'PutBlob'*, *'PutBlock'*, *'PutBlockList'*, *'AppendBlock'*, *'ListBlobs'*, *'ListContainers'*, *'CreateContainer'*, *'SnapshotBlob'*e *'CopyBlob'*.
- Elimine as transações como *'DeleteBlob'* e *'DeleteContainer'*.
- Todas as outras operações.

Para poder estimar custos da transação para contas de armazenamento geral, tem de agregar todas as operações independentemente da operação/API.

##### <a name="data-access-and-geo-replication-data-transfer-costs"></a>Os custos de transferência de dados de replicação geo e o acesso de dados

Enquanto a análise de armazenamento não fornece a quantidade de dados lerem e escrita para uma conta de armazenamento, pode ser estimado de aproximadamente verificando a tabela de métricas da transação.
A soma de *'TotalIngress'* em todas as entradas para uma API na tabela da transação métricas indica a quantidade total de dados de penetração em bytes para esse API específico.
Da mesma forma a soma de *'TotalEgress'* indica a quantidade total de dados de saída, em bytes.

Para calcular os custos de acesso de dados para contas de armazenamento de BLOBs, terá de divide as transações em dois grupos.

- A quantidade de dados obtidos a partir da conta de armazenamento pode ser estimada verificando a soma de *'TotalEgress'* para principalmente as operações de *'GetBlob'* e *'CopyBlob'* .
- A quantidade de escrita para a conta de armazenamento de dados pode ser estimada verificando a soma de *'TotalIngress'* para principalmente o *'PutBlob'*, *'PutBlock'*, *'CopyBlob'* e *'AppendBlock'* operações.

Também pode ser calculado o custo geo replicação de transferência de dados para contas de armazenamento de BLOBs utilizando a estimativa para a quantidade de dados escritos em caso de uma conta de armazenamento GRS ou RT GRS.

> [AZURE.NOTE] Por exemplo um mais detalhado sobre como calcular os custos para utilizar a camada de armazenamento quentes ou modernos, tirar um olhar sobre as perguntas mais frequentes intituladas *'o que são camadas de acesso de quente e interessantes e como deve determinar qual o monitor a use?'* no [Azure armazenamento preços de página](https://azure.microsoft.com/pricing/details/storage/).

### <a name="migrating-existing-data"></a>Migrar dados existentes

Uma conta de armazenamento de BLOBs especializado para armazenar apenas bloco e acrescentar blobs. Contas de armazenamento geral existentes, permitem-lhe armazenar tabelas, consultas, ficheiros e discos, bem como a blobs, não podem ser convertidas para contas de armazenamento de Blobs. Para utilizar as camadas de armazenamento, terá de criar novas contas de armazenamento de BLOBs e migrar os seus dados existentes para contas de recentemente criadas.
Pode utilizar os seguintes métodos para migrar os dados existentes para contas de armazenamento de BLOBs a partir de dispositivos de armazenamento no local, fornecedores de armazenamento na nuvem de terceiros ou de contas no Azure armazenamento uso geral existente:

#### <a name="azcopy"></a>AzCopy

AzCopy é um utilitário da linha de comandos do Windows concebido para copiar de alto desempenho de dados para e a partir do armazenamento do Windows Azure. Pode utilizar AzCopy para copiar dados para a sua conta de armazenamento de BLOBs de contas armazenamento uso geral existente ou para carregar os dados a partir do seu dispositivos de armazenamento no local para a sua conta de armazenamento de Blobs.

Para obter mais detalhes, consulte o artigo [Transferir dados com o utilitário AzCopy da linha de comandos](storage-use-azcopy.md).

#### <a name="data-movement-library"></a>Biblioteca de movimento de dados

Azure biblioteca de movimento de dados de armazenamento para .NET baseia-se a arquitetura de movimento de dados core oferece AzCopy. A biblioteca foi concebida para operações de transferência de dados de alto desempenho, fiável e fácil semelhante ao AzCopy. Esta opção permite-lhe tirar vantagens completos de funcionalidades fornecidas pelo AzCopy na sua aplicação vierem sem ter de lidar com a executar e monitorização externas instâncias do AzCopy.

Para obter mais detalhes, consulte o artigo [Biblioteca movimento de dados de armazenamento do Azure para .net](https://github.com/Azure/azure-storage-net-data-movement)

#### <a name="rest-api-or-client-library"></a>REST API ou a biblioteca do cliente

Pode criar uma aplicação personalizada para migrar os seus dados para uma conta de armazenamento de BLOBs utilizando um das bibliotecas do cliente Azure ou os serviços de armazenamento Azure REST API. Armazenamento Azure fornece bibliotecas do cliente formatado para vários idiomas e plataformas como .NET, Java, C++, Node.JS, PHP, Rubi e Python. As bibliotecas do cliente fornecem funcionalidades avançadas como lógica de repetição, registo e carregamentos paralelos. Também pode desenvolver diretamente contra REST API, que podem ser designadas por qualquer idioma que faz com que os pedidos de HTTP/HTTPS.

Para obter mais detalhes, consulte o artigo [Introdução ao armazenamento de Blobs do Azure](storage-dotnet-how-to-use-blobs.md).

> [AZURE.NOTE] BLOBs encriptados através de encriptação do lado do cliente armazenam metadados relacionadas com a encriptação armazenados com o blob. É realmente fundamental que qualquer mecanismo cópia deve Certifique-se de que os metadados blob e especialmente os relacionadas com a encriptação metadados, é preservada. Se copiar blobs sem este metadados, o conteúdo de BLOBs não será recuperável novamente. Para obter mais detalhes sobre relacionadas com a encriptação metadados, consulte o artigo [encriptação do lado do cliente de armazenamento do Windows Azure](storage-client-side-encryption.md).

## <a name="faqs"></a>Perguntas mais frequentes

1. **São existentes contas de armazenamento continua disponíveis?**

    Sim, armazenamento contas ainda estão disponíveis e existentes são inalteradas preços ou funcionalidade.  Se não possui a capacidade de escolher uma camada de armazenamento e não terão capacidades tiering no futuro.

2. **Por que motivo e quando deve posso começar a utilizar contas de armazenamento de BLOBs?**

    Contas de armazenamento de BLOBs são especializadas para armazenar blobs e permitem-nos apresentar as novas funcionalidades centrados em blob. Passará, contas de armazenamento de BLOBs são o caminho recomendado para armazenar blobs, como capacidades futuras, tais como o armazenamento hierárquico e tiering será introduzida com base neste tipo de conta. No entanto, é até-o quando pretender para migrar com base nos seus requisitos de negócio.

3. **Pode converter minha conta de armazenamento existente a uma conta de armazenamento de BLOBs?**

    Não. Conta de armazenamento de BLOBs é um tipo diferente da conta de armazenamento e terá de criá-lo nova e migrar os seus dados tal como é explicado acima.

4. **Pode armazenar objetos em ambas as camadas de armazenamento na mesma conta?**

    O atributo *' acesso camada '* que indica a camada de armazenamento é definido a um nível de conta e aplica-se a todos os objetos dessa conta. Não é possível definir o atributo de níveis de acesso a um nível de objeto.

5. **Pode alterar a camada de armazenamento da minha conta de armazenamento de BLOBs?**

    Sim. Será capaz de alterar a camada de armazenamento ao definir o atributo *' acesso camada '* na conta de armazenamento. Alterar a camada de armazenamento será aplicada a todos os objetos armazenados na conta. Alterar a camada de armazenamento de quente para interessantes não será assumir quaisquer encargos, ao alterar a partir de interessantes para quente irá implicam uma por GB custo para todos os dados na conta de leitura.

6. **Com que frequência posso alterar a camada de armazenamento da minha conta de armazenamento de BLOBs?**

    Enquanto não podemos impor uma limitação de frequência pode ser alterada a camada de armazenamento, tenha em atenção que alterar a camada de armazenamento de interessantes para quente irá implicam taxas significativas. Recomendamos que não altere a camada de armazenamento frequentemente.

7. **Irão blobs na camada armazenamento modernos um comportamento diferente daqueles na camada quente armazenamento?**

    BLOBs na camada armazenamento quente tem a mesma latência como blobs nas contas de armazenamento geral. BLOBs na camada armazenamento modernos tem uma latência semelhante (em milissegundos) como blobs nas contas de armazenamento geral.

    BLOBs na camada armazenamento modernos terá um nível de serviço disponibilidade de ligeiramente abaixo (SLA) que blobs armazenados na camada quente armazenamento. Para obter mais detalhes, consulte o artigo [SLA armazenamento](https://azure.microsoft.com/support/legal/sla/storage).

8. **Os posso armazenar blobs de página e discos de máquina virtual nas contas de armazenamento de BLOBs?**

    Contas de armazenamento de BLOBs suportam apenas bloco e acrescentar blobs e não blobs de página. Azure máquina virtual discos de segurança abrange os por blobs de página e como resultado de contas de armazenamento de BLOBs não podem ser utilizadas para armazenar discos máquina virtual. No entanto é possível armazenar as cópias de segurança dos discos máquina virtual como blobs bloco numa conta de armazenamento de Blobs.

9. **Irá precisar de alterar as minhas aplicações para utilizar contas de armazenamento de Blobs do existentes?**

    Contas de armazenamento de BLOBs estão 100% API consistente com as contas de armazenamento geral para bloco e acrescentar blobs. Desde que a aplicação é utilizar bloquear blobs ou acrescentar blobs e estiver a utilizar a versão de 2014-02-14 do [Armazenamento dos serviços REST API](https://msdn.microsoft.com/library/azure/dd894041.aspx) ou maior, em seguida, a aplicação deverá simplesmente funcionar. Se estiver a utilizar uma versão mais antiga do protocolo, terá de atualizar a aplicação para utilizar a nova versão publicados para trabalhar de forma totalmente integrada com os dois tipos de contas de armazenamento. Em geral, sempre recomendar utilizando a versão mais recente, independentemente da qual a conta de armazenamento escreva utilizamos.

10. **Vai existir uma alteração na experiência de utilizador?**

    Contas de armazenamento de BLOBs são muito semelhantes às contas armazenamento uso geral para armazenar bloco acrescentar blobs e suportam todas as funcionalidades principais de armazenamento do Windows Azure, incluindo durabilidade alta e disponibilidade, escalabilidade, desempenho e segurança. Diferente das funcionalidades e restrições específicas para contas de armazenamento de BLOBs e respetivas camadas de armazenamento realçadas acima, tudo o que mais preciso permanece a mesma.

## <a name="next-steps"></a>Próximos passos

### <a name="evaluate-blob-storage-accounts"></a>Avaliar as contas de armazenamento de BLOBs

[Verificar a disponibilidade de contas de armazenamento de BLOBs por região](https://azure.microsoft.com/regions/#services)

[Avaliar a utilização das suas contas de armazenamento atual ao ativar métricas de armazenamento do Windows Azure](storage-enable-and-view-metrics.md)

[Verificar o armazenamento de BLOBs preços por região](https://azure.microsoft.com/pricing/details/storage/)

[Transferências de dados de verificação preços](https://azure.microsoft.com/pricing/details/data-transfers/)

### <a name="start-using-blob-storage-accounts"></a>Começar a utilizar contas de armazenamento de BLOBs

[Introdução ao armazenamento de Blobs do Azure](storage-dotnet-how-to-use-blobs.md)

[Mover dados de armazenamento do Windows Azure e para](storage-moving-data.md)

[Transferir dados com o utilitário AzCopy da linha de comandos](storage-use-azcopy.md)

[Navegue e explore as suas contas de armazenamento](http://storageexplorer.com/)
