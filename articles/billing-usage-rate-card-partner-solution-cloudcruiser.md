<properties
   pageTitle="Na nuvem Cruiser e faturação API integração do Microsoft Azure | Microsoft Azure"
   description="Fornece uma perspetiva exclusiva do Microsoft Azure faturação parceiro Cruiser na nuvem, nas suas experiências integrar o seu produto APIs de faturação do Azure.  Este é especialmente útil para os clientes do Azure e nuvem Cruiser que estejam interessados em utilizar/tentar Cruiser na nuvem para o Microsoft Azure Pack."
   services=""
   documentationCenter=""
   authors="BryanLa"
   manager="mbaldwin"
   editor=""
   tags="billing"
   />

<tags
   ms.service="billing"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="billing"
   ms.date="09/08/2016"
   ms.author="mobandyo;sirishap;bryanla"/>

# <a name="cloud-cruiser-and-microsoft-azure-billing-api-integration"></a>Na nuvem Cruiser e faturação API integração do Microsoft Azure

Este artigo descreve como as informações recolhidas a partir do novo Microsoft Azure faturação APIs podem ser utilizadas na nuvem Cruiser simulação de custo de fluxo de trabalho e de análise.

## <a name="azure-ratecard-api"></a>API Azure RateCard
RateCard API fornece informações de taxa do Azure. Depois de autenticar com as credenciais inicial, pode consultar a API para recolher metadados sobre os serviços disponíveis no Azure, juntamente com as taxas associadas a sua oferta ID.

Segue-se uma resposta de exemplo a partir da API a mostrar os preços para o A0 instância (Windows):

    {
        "MeterId": "0e59ad56-03e5-4c3d-90d4-6670874d7e29",
        "MeterName": "Compute Hours",
        "MeterCategory": "Virtual Machines",
        "MeterSubCategory": "A0 VM (Windows)",
        "Unit": "Hours",
        "MeterRates":
        {
            "0": 0.029
        },
        "EffectiveDate": "2014-08-01T00:00:00Z",
        "IncludedQuantity": 0.0,
        "MeterStatus": "Active"
    },

### <a name="cloud-cruisers-interface-to-azure-ratecard-api"></a>Interface do Cruiser para Azure RateCard API em nuvem
Nuvem Cruiser pode tirar partido as informações de RateCard API formas diferentes. Este artigo, iremos mostrar como pode ser utilizado para tornar IaaS carga de trabalho de custo simulação e análise.

Para demonstrar este casos de utilização, imagine uma carga de trabalho de várias instâncias em execução no pacote do Microsoft Azure (WAP). O objetivo é simular este carga de trabalho mesmo no Azure e estimar custos de fazer essa migração. Para criar esta simulação, existem duas tarefas principais a ser executado:

1. **Processo de importação e as informações de serviço recolhidas a partir da API RateCard.** Esta tarefa também é executada em livros, onde extrair a partir da API RateCard é transformado e publicada para um novo plano taxa. Este novo plano taxa será utilizado nas simulações para estimar os preços Azure.

2. **Normaliza os serviços WAP e Azure services para IaaS.** Por predefinição, os serviços WAP são baseados no recursos individuais (CPU, tamanho da memória, tamanho do disco, etc.) enquanto Azure serviços são baseados no tamanho da instância (A0, A1, A2, etc.). Este primeira tarefa pode ser executada pelo motor ETL da nuvem Cruiser, denominado livros, onde podem ser agrupados em tamanhos de instância, semelhantes a serviços de instância Azure estes recursos.

### <a name="import-data-from-the-ratecard-api"></a>Importar dados a partir da API RateCard

Nuvem Cruiser livros fornecem uma forma automática para recolher e processar informações a partir da API RateCard.  Livros do ETL (carga da transformação extrair) permitem-lhe configurar as coleções de sites, transformação e publicação de dados para a base de dados Cruiser na nuvem.

Cada livro pode ter uma ou várias coleções de sites, permitindo-lhe relacionar informações de origens diferentes para complementar ou aumentar os dados de utilização. Capturas de ecrã de duas seguintes mostram como criar uma nova *coleção* de um livro existente e importar informações para a *coleção* de RateCard API:

![Figura 1 - criar uma nova coleção de][1]

![Figura 2 - importar dados a partir da coleção de novo][2]

Depois de importar os dados para o livro, é possível criar vários passos e processos de transformação, para modificar e modelar os dados. Neste exemplo, uma vez que recomendamos apenas estiver interessados em infraestrutura-como-a-Service (IaaS) pode utilizamos os passos de transformação para remover linhas desnecessárias ou registos, relacionada com serviços que não seja IaaS.

A captura de ecrã seguinte mostra os passos de transformação utilizados para os dados recolhidos a partir do RateCard API do processo:

![Figura 3 - transformação os passos para processar dados recolhidos, de RateCard API][3]

### <a name="defining-new-services-and-rate-plans"></a>Definir novos serviços e taxa de planos

Existem várias formas de definir serviços em nuvem Cruiser. Uma das opções é importar os serviços de dados de utilização. Este método é geralmente utilizado quando trabalha com nuvens públicos, onde os serviços já estão definidos pelo fornecedor.

Um plano de taxa é um conjunto de taxas ou preços que podem ser aplicados a serviços diferentes, com base nas datas efetivas ou grupo de clientes, entre outras opções. Taxa planos podem também ser utilizados na nuvem Cruiser para criar simulação ou cenários de "E se", para compreender como alterações nos serviços do podem afetar o custo total de uma carga de trabalho.

Neste exemplo, irá utilizamos as informações do serviço a partir da API RateCard para definir novos serviços na nuvem Cruiser. Da mesma forma, pode utilizamos as taxas associadas aos serviços para criar um novo plano de taxa na nuvem Cruiser.

No final do processo de transformação, é possível criar um novo passo e publicar os dados a partir da API RateCard como novos serviços e taxas.

![Figura 4 - publicar os dados a partir da API RateCard como novos serviços e taxas][4]

### <a name="verify-azure-services-and-rates"></a>Verifique se serviços Azure e taxas

Depois de publicar as taxas e serviços, pode verificar a lista de serviços importados no separador de *Serviços* da nuvem Cruiser:

![Figura 5 - verificar os novos serviços][5]

No separador *Taxa planos* , pode verificar o novo plano taxa denominado "AzureSimulation" com as taxas importado a partir da API RateCard.

![Figura 6 - a nova taxa planear e taxas associadas a verificar][6]

### <a name="normalize-wap-and-azure-services"></a>Normalizar WAP e nos serviços do Azure

Por predefinição, WAP fornece informações de utilização, com base na utilização de cluster, memória e recursos de rede. No Cruiser na nuvem, pode definir os serviços com base diretamente no alocação ou com tráfego limitado a utilização destes recursos. Por exemplo, pode definir uma taxa básica para cada hora de utilização da CPU ou cobrar GB de memória atribuída a uma instância.

Neste exemplo, para comparar os custos entre WAP e Azure, é necessário agregar a utilização de recursos no WAP para conjuntos, que, em seguida, pode ser mapeada para serviços Azure. Este transformação pode ser implementada facilmente nos livros:

![Figura 7 - transformar dados WAP a normalizar serviços][7]

O último passo no livro é publicar os dados para a base de dados Cruiser na nuvem. Durante este passo, os dados de utilização são agora agrupados em serviços (que mapeiam para os serviços do Azure) e associados à taxas predefinidas para criar os encargos.

Depois de concluir o livro, pode automatizar da transformação de dados, ao adicionar uma tarefa do scheduler e especificando a frequência e hora para o livro ser executado.

![Figura 8 - agendamento do livro][8]

### <a name="create-reports-for-workload-cost-simulation-analysis"></a>Criar relatórios para uma análise de custo simulação carga de trabalho

Depois de utilização de é recolhida e taxas são carregadas para a base de dados Cruiser na nuvem, podemos pode tirar partido do módulo nuvem Cruiser informações para criar a carga de trabalho simulação que recomendamos pretendem de custo.

Para poder demonstram este cenário, criámos o relatório seguinte:

![Comparação de custos][9]

O gráfico superior mostra uma comparação de custo por serviços, comparar o preço de executar a carga de trabalho para cada serviço específico entre WAP (azul escuro) e o Azure (azul claro).

O gráfico inferior mostra os mesmos dados mas discriminados por departamento. Isto mostra os custos para cada departamento executar a sua carga de trabalho em WAP e Azure, juntamente com a diferença entre eles na barra de poupanças (verde).

## <a name="azure-usage-api"></a>API de utilização do Azure


### <a name="introduction"></a>Introdução

Microsoft recentemente introduzida API a utilização do Azure, permitindo que os subscritores através de programação separar nos dados de utilização para obter informações para o seu consumo. Este é excelentes notícias para clientes que Cruiser na nuvem que pode tirar partido do conjunto de dados mais rico disponível através desta API.

Nuvem Cruiser pode tirar partido da integração com a API a utilização de várias formas. A granularidade (por hora informações sobre a utilização) e informações de metadados de recursos disponíveis através da API fornecem o conjunto de dados necessário para suportar modelos de Showback ou encargos flexíveis. 

Neste tutorial, vamos apresentará um exemplo de como nuvem Cruiser beneficiar as informações de utilização de API. Mais especificamente, podemos irá criar um grupo de recursos no Azure, associar tags para a estrutura de conta, em seguida, descrevem o processo de no e processar as informações de etiqueta na nuvem Cruiser.
 
O objetivo final é possam criar relatórios, tal como o seguinte e ser possível analisar o custo e consumo baseada na estrutura de conta povoada por etiquetas.

![Figura 10 - relatório com desagregações utilizar etiquetas][10]

### <a name="microsoft-azure-tags"></a>Etiquetas do Microsoft Azure

Os dados disponíveis através da API a utilização do Azure incluem não só informações consumo, mas também metadados de recursos, incluindo quaisquer etiquetas associadas. Códigos de fornecem uma forma fácil para organizar os recursos, mas para poder ser eficaz, terá de se certificar de que:

- Etiquetas corretamente são aplicadas para os recursos no momento da disposição
- Etiquetas correctamente são utilizadas sobre o processo de Showback/encargos para associam a utilização da estrutura de conta da organização.

Ambos estes requisitos podem ser um desafio, especialmente quando existe um processo manual na disposição ou carregar o dispositivo lado. Etiquetas escritos incorretamente, incorretas ou até mesmo em falta são reclamações comuns de clientes quando utilizar etiquetas e estes erros pode dificultar vida no lado carregar muito.

Com a nova API a utilização de Azure nuvem Cruiser pode separar as informações de recurso e através de uma ferramenta ETL sofisticada denominada livros, corrigir estes erros comuns de marcação. Através de transformação utilizar expressões regulares e correlação de dados, nuvem Cruiser pode identificar recursos incorretamente com etiquetas e aplicar as tags corretas, garantindo que a associação correta dos recursos com o consumidor.

No lado a carregar, nuvem Cruiser automatiza o processo de Showback/encargos e pode tirar partido das informações da etiqueta para associam a utilização do consumidor adequado (departamento, divisão, Project, etc.). Este automatização fornece uma melhoria enorme e pode certificar-se um processo de carregar consistente e passíveis de auditoria.
 

### <a name="creating-a-resource-group-with-tags-on-microsoft-azure"></a>Criar um grupo de recursos com etiquetas no Microsoft Azure
O primeiro passo neste tutorial, é criar um grupo de recursos no portal do Azure, em seguida, criar etiquetas novas para associar os recursos. Neste exemplo, vamos vão criar as seguintes tags: departamento, ambiente, proprietário, o Project.

A captura de ecrã abaixo mostra um exemplo de grupo de recursos com os códigos associados.

![Figura 11 - grupo de recursos com etiquetas associadas no portal do Azure][11]

O próximo passo é utilizar as informações a partir da API a utilização de Cruiser na nuvem. A utilização de API atualmente fornece respostas no formato JSON. Eis uma amostra dos dados obtidos:


    {
      "id": "/subscriptions/bb678b04-0e48-4b44-XXXX-XXXXXXX/providers/Microsoft.Commerce/UsageAggregates/Daily_BRSDT_20150623_0000",
      "name": "Daily_BRSDT_20150623_0000",
      "type": "Microsoft.Commerce/UsageAggregate",
      "properties":
      {
        "subscriptionId": "bb678b04-0e48-4b44-XXXX-XXXXXXXXX",
        "usageStartTime": "2015-06-22T00:00:00+00:00",
        "usageEndTime": "2015-06-23T00:00:00+00:00",
        "meterName": "Compute Hours",
        "meterRegion": "",
        "meterCategory": "Virtual Machines",
        "meterSubCategory": "Standard_D1 VM (Non-Windows)",
        "unit": "Hours",
        "instanceData": "{\"Microsoft.Resources\":{\"resourceUri\":\"/subscriptions/bb678b04-0e48-4b44-XXXX-XXXXXXXX/resourceGroups/DEMOUSAGEAPI/providers/Microsoft.Compute/virtualMachines/MyDockerVM\",\"location\":\"eastus\",\"tags\":{\"Department\":\"Sales\",\"Project\":\"Demo Usage API\",\"Environment\":\"Test\",\"Owner\":\"RSE\"},\"additionalInfo\":{\"ImageType\":\"Canonical\",\"ServiceType\":\"Standard_D1\"}}}",
        "meterId": "e60caf26-9ba0-413d-a422-6141f58081d6",
        "infoFields": {},
        "quantity": 8

      },
    },


### <a name="import-data-from-the-usage-api-into-cloud-cruiser"></a>Importar dados de utilização de API para Cruiser na nuvem

Nuvem Cruiser livros fornecem uma forma automática para recolher e processar informações a partir da API de utilização. Um livro do (carga da transformação extrair) ETL permite-lhe configurar as coleções de sites, da transformação e publicação de dados para a base de dados Cruiser na nuvem.

Cada livro pode ter uma ou várias coleções de sites. Esta opção permite-lhe relacionar informações de origens diferentes para complementar ou aumentar os dados de utilização. Neste exemplo, vamos irá criar uma nova folha no livro modelo Azure (_UsageAPI)_ e configurar uma nova _coleção_ para importar informações de utilização de API.

![Figura 3 - dados de utilização API importados para a folha de UsageAPI][12]

Repare que neste livro já dispõe de outras folhas para importar serviços a partir do Azure (_ImportServices_) e processar as informações de consumo a partir da API faturação (_PublishData_).

Em Pedimos irá utilizar a API de utilização para preencher a folha de _UsageAPI_ e correlacionar as informações com os dados de consumo a partir da API de faturação na folha _PublishData_ .

### <a name="processing-the-tag-information-from-the-usage-api"></a>Processar as informações de etiqueta a partir da API de utilização

Depois de importar os dados para o livro, podemos irá criar passos de transformação na folha de _UsageAPI_ para as informações a partir da API do processo. Primeiro passo é utilizar um processador "JSON dividir" para extrair os códigos de um único campo, em seguida, criar campos para cada um deles (departamento, Project, proprietário e ambiente).

![Figura 4 - criar novos campos para as informações de etiqueta][13]

Repare que o serviço de "Funcionamento em rede" não é apresentado as informações da etiqueta (caixa amarela), mas recomendamos pode verificar que é a parte do mesmo grupo de recursos verificando o campo _ResourceGroupName_ . Uma vez que temos etiquetas para os outros recursos a partir deste grupo de recursos, podemos pode utilizar estas informações para aplicar as etiquetas em falta a este recurso mais à frente no processo.

O passo seguinte é criar uma tabela de referência associar as informações de etiquetas para o _ResourceGroupName_. Esta tabela de referência será utilizada no próximo passo para enriquecer os dados de consumo com informações de etiqueta.

### <a name="adding-the-tag-information-to-the-consumption-data"></a>Adicionar as informações da etiqueta aos dados consumo

Agora que possamos saltar para folha de _PublishData_ , processa as informações de consumo a partir da API faturação e adicione os campos extraídos de etiquetas. Este processo é executado verificando a tabela de pesquisa que criou no passo anterior, utilizando o _ResourceGroupName_ como a chave para as pesquisas.

![Figura 5 - preencher a estrutura de conta com as informações das pesquisas][14]

Repare que os campos de estrutura de conta apropriado para o serviço de "Funcionamento em rede" foram aplicados, corrigir o problema com os códigos em falta. Recomendamos também povoada campos de estrutura da conta para recursos que não seja o nosso alvo grupo de recursos com "Outros", para poder diferenciar nos relatórios.

Agora precisamos acabou de adicionar um passo para publicar dados de utilização. Durante este passo, as taxas adequadas para cada serviço foi definido no nosso plano taxa serão aplicadas a informações de utilização, com o encargo resultante carregado para a base de dados.

A parte melhor é que tem apenas uma vez seguir este processo. Quando o livro estiver concluído, só precisa de adicioná-lo para o agendador e é executada diária ou hora a hora a hora agendada. Em seguida, é apenas uma questão de criar novos relatórios ou personalizar os existentes, para analisar os dados para obter informações significativas a partir da sua utilização na nuvem.

### <a name="next-steps"></a>Próximos passos

+ Para obter instruções detalhadas sobre como criar livros Cruiser na nuvem e relatórios, consulte online [documentação](http://docs.cloudcruiser.com/) (válido início de sessão necessário da nuvem Cruiser).  Para mais informações sobre Cruiser na nuvem, contacte o suportehttp [info@cloudcruiser.com](mailto:info@cloudcruiser.com).
+ Consulte o artigo [obter informações para o consumo de recursos do Microsoft Azure](billing-usage-rate-card-overview.md) para uma descrição geral da utilização de recursos do Azure e RateCard APIs.
+ Dar saída a [Referência da Azure faturação REST API](https://msdn.microsoft.com/library/azure/1ea5b323-54bb-423d-916f-190de96c6a3c) para obter mais informações sobre as duas APIs, que fazem parte de um conjunto de APIs fornecidos pelo Gestor de recursos do Azure.
+ Se pretender para ficar aceder à direita o código de exemplo, consulte o artigo nosso Microsoft Azure faturação API exemplos de código em [Amostras de código do Azure](https://azure.microsoft.com/documentation/samples/?term=billing).

### <a name="learn-more"></a>Saiba mais
+ Consulte o artigo [Descrição geral do Gestor de recursos do Azure](azure-resource-manager/resource-group-overview.md) para saber mais sobre o Gestor de recursos do Azure.

<!--Image references-->
 
[1]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Create-New-Workbook-Collection.png "Figura 1 - criar uma nova coleção de"
[2]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Import-Data-From-RateCard.png "Figura 2 - importar dados a partir da coleção de novo"
[3]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Transformation-Steps-Process-RateCard-Data.png "Figura 3 - transformação os passos para processar dados recolhidos, de RateCard API"
[4]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Publish-RateCard-Data-New-Services-Rates.png "Figura 4 - publicar os dados a partir da API RateCard como novos serviços e taxas"
[5]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Verify-Azure-Services-And-Pricing1.png "Figura 5 - verificar os novos serviços"
[6]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Verify-Azure-Services-And-Pricing2.png "Figura 6 - a nova taxa planear e taxas associadas a verificar"
[7]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Transforming-WAP-Normalize-Services.png "Figura 7 - transformar dados WAP a normalizar serviços"
[8]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Workbook-Scheduling.png "Figura 8 - agendamento do livro"
[9]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Workload-Cost-Simulation-Report.png "Figura 9 - relatório de exemplo para o cenário de comparação de custo de carga de trabalho"
[10]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/1_ReportWithTags.png "Figura 10 - relatório com desagregações utilizar etiquetas"
[11]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/2_ResourceGroupsWithTags.png "Figura 11 - grupo de recursos com etiquetas associadas no portal do Azure"
[12]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/3_ImportIntoUsageAPISheet.png "Figura 12 - dados de utilização API importados para a folha de UsageAPI"
[13]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/4_NewTagField.png "Figura 13 - criar novos campos para as informações de etiqueta"
[14]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/5_PopulateAccountStructure.png "Figura 14 - preencher a estrutura de conta com as informações das pesquisas"
