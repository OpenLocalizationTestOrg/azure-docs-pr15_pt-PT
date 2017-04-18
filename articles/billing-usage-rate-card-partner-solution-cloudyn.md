<properties
   pageTitle="A utilização do Microsoft Azure e Cloudyn RateCard APIs ativar para fornecer ITFM para clientes | Microsoft Azure"
   description="Fornece uma perspetiva exclusiva do Microsoft Azure faturação parceiro Cloudyn, nas suas experiências integrar o seu produto APIs de faturação do Azure.  Este é especialmente útil para os clientes do Azure e Cloudyn que estejam interessados em utilizar/experimentar Cloudyn para dos serviços do Azure."
   services=""
   documentationCenter=""
   authors="BryanLa"
   manager="mbaldwin"
   editor=""
   tags="billing"/>

<tags
   ms.service="billing"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="billing"
   ms.date="08/16/2016"
   ms.author="mobandyo;bryanla"/>

# <a name="microsoft-azure-usage-and-ratecard-apis-enable-cloudyn-to-provide-itfm-for-customers"></a>A utilização do Microsoft Azure e RateCard APIs permitem Cloudyn proporcionar ITFM para clientes

Cloudyn, um parceiro de desenvolvimento da Microsoft e um fornecedor à esquerda das capacidades da gestão de nuvem, foi escolhido para obter uma pré-visualização da utilização de recursos do Microsoft Azure e RateCard APIs novo privada.  A utilização de API fornece acesso aos dados de consumo Azure estimado para uma subscrição. RateCard API fornece informações sobre preços completos de todos os serviços Azure, para clientes que não sejam - Enterprise Agreement EA. Integrado em conjunto, estas APIs proporcionam uma base de obter informações completas introduções para ferramentas de gestão financeira TI (ITFM) como aqueles fornecida pela Cloudyn.

## <a name="introduction"></a>Introdução

A chamados "multiplicação" dados a partir da utilização de API com os dados a partir da API RateCard (preço de utilização [unidades] [$unit] = detalhadas a utilização e custo) cria mais granulares, exactas e fiáveis as informações de faturação disponíveis para Azure hoje.

![Descrição geral ITFM][1]

Consumir estas APIs fornece informações chave sobre a utilização dos clientes e custos, permitindo-Cloudyn para analisar as contas de cliente de uma forma simple e programação e para realizar várias tarefas ITFM para os seus clientes.

## <a name="integrating-cloudyn-with-the-ratecard-and-usage-apis"></a>Integrar Cloudyn com a RateCard e a utilização APIs
RateCard API requer parâmetros de entrada várias – como informações de região, moeda e região – mas o mais importantes é OfferDurableID, que especifica o tipo de Azure perguntar se o cliente está a utilizar (repartição, planos de consolidação de 6 e 12 meses legado, MSDN ofertas, MPN ofertas, ofertas promocionais e outras pessoas). Pode encontrar o OfferDurableID na [utilização do Azure e portal de faturação](https://account.windowsazure.com/Subscriptions), na "oferecer ID" para a subscrição determinada.

Durante o registo para serviços de [Cloudyn para Azure](https://www.cloudyn.com/microsoft-azure/) , clientes podem adicionar os respetivos código OfferDurableID, que lhe permite Cloudyn separar os respetivos informações relevantes comparar através da API RateCard.  Obter informações sobre os diferentes tipos de ofertas podem encontrar um a página [Detalhes da oferta do Microsoft Azure](https://azure.microsoft.com/support/legal/offer-details/) .

![Descrição geral do motor de Cloudyn ITFM][2]

Utilizações Cloudyn a utilização tanto APIs RateCard, para além de API do desempenho do Azure, para criar camadas adicionais de visualização, análise, alertar, relatórios, gestão de custos e recomendações acionáveis, fornecendo clientes Azure uma ferramenta ITFM enterprise fiável na nuvem.

## <a name="cloudyn-itfm-use-cases-enabled-by-usage-and-ratecard-api-integration"></a>Casos de utilização de Cloudyn ITFM ativados através da integração de utilização e RateCard API
Ativado por utilização de casos de utilização de comuns Cloudyn ITFM e RateCard APIs incluem:

+ **Análise de custos** - permite na nuvem custos ser discriminados para baixo para qualquer dimensão identificativa nativo (fornecedor, serviço, conta, região, etc.). A utilização de Azure e RateCard APIs tornar esta é uma tarefa fácil, ao fornecer a desagregação mais granular da utilização da e dos custos de dados por conta, em seguida, agrupado e cujo filtrada por Cloudyn e é apresentada ao utilizador, num formulário de gráfico ou tabular.

![Gráfico circular de análise de custos][3]

+ **Custo alocação 360** - Finanças ativa e os gestores de TI para descobrir a divisão de custo real, controladores e as tendências da sua implementação na nuvem. Ainda mais permite que os gestores para facilmente associar despesas de implementação unidades empresariais, os departamentos, regiões e obter mais informações, fornecendo informações sem precedentes para os custos de nuvem e facilitar os encargos de empresa e showbacks. A utilização de Azure e RateCard APIs age como entrada ao motor de alocação do Cloudyn custo, que completa as APIs ao definir métodos e lógica de negócio para atribuir recursos untaggable ou não marcados.

![Gráfico de alocação custos 360][4]

+ **Redimensionamento cost-Effective** - fornece recomendações de redimensionamento da direita para máquinas virtuais subaproveitadas, que reduz despesas do cliente no tamanho grande ou indevidamente aprovisionadas máquinas. Para o fazer, examinar máquina virtual CPU e métricas de RAM (através do desempenho API), horas de tempo de execução (através da utilização de API) e o custo (através do RateCard API). Cloudyn, em seguida, fornece recomendações de redimensionamento para a direita com base nas subaproveitado CPU ou RAM recursos (desempenho) e calcula poupanças estimadas ao multiplicar: a variação de preço (RateCard) entre VMs pela real tempo-utilização (utilização) do computador subaproveitado.

![Rentável de dimensionamento][5]

+ **Recomendações de migrar de nuvem** - fornece financeiros conselhos sobre nuvem migrar. Examina custos atual de um utilizador de recursos na nuvem que são implementados no fornecedores de nuvem principais e compara-o com o custo de uma implementação equivalente no Azure. Em seguida, fornece granular, por recurso, anulação com base em migrar recomendações para Azure. Depois de avaliar a implementação equivalente necessária nos Azure (com base nas preferências de utilizador e de métricas de desempenho), Cloudyn utiliza a API RateCard para avaliar o custo de implementação equivalente no Azure.

+ Os **Relatórios de desempenho** - ativado por desempenho estabelecidos do Azure API, estes relatórios fornecem uma matriz de funcionalidades de utilização da CPU e de RAM para recomendações de otimização. Abaixo está um exemplo de relatório de utilização instância, apresentar desagregação instância pela utilização da CPU média.

![Relatórios de desempenho][6]

+ **Gestor da categoria** - uma funcionalidade poderosa no Cloudyn dá ordem ao nuvem desorganizada recursos. Fornece aos utilizadores a liberdade para criar os seus próprios categorias exclusivas (etiquetas) para eficaz de medição e relatórios que está em linha com práticas de negócios. Além disso, os utilizadores podem facilmente regular e categorizar etiquetagem inconsistente (ou seja, erros tipográficos e outras discrepâncias) e detetar automaticamente aplicações não marcados recursos para imputação de custos precisos.

![Gestor de categoria][7]

## <a name="video"></a>Vídeo

Eis um breve vídeo que mostra como um cliente do Azure pode utilizar o Cloudyn para Azure e Azure faturação API do, para obter informações a partir dos seus dados consumo Azure.

> [AZURE.VIDEO cloudyn-provides-cloud-itfm-tools-via-microsoft-azure-apis]


## <a name="next-steps"></a>Próximos passos

+ Inicie uma avaliação gratuita do [Cloudyn para Azure](https://www.cloudyn.com/microsoft-azure/) para saber como pode obter a transparência de custo com relatórios personalizados e análises para a sua implementação de nuvem Microsoft Azure.
+ Consulte o artigo [obter informações para o consumo de recursos do Microsoft Azure](billing-usage-rate-card-overview.md) para uma descrição geral da utilização de recursos do Azure e RateCard APIs.
+ Dar saída a [Referência da Azure faturação REST API](https://msdn.microsoft.com/library/azure/1ea5b323-54bb-423d-916f-190de96c6a3c) para obter mais informações sobre as duas APIs, que fazem parte de um conjunto de APIs fornecidos pelo Gestor de recursos do Azure.
+ Se pretender para ficar aceder à direita o código de exemplo, consulte o artigo nosso Microsoft Azure faturação API exemplos de código em [Amostras de código do Azure](https://azure.microsoft.com/documentation/samples/?term=billing).

## <a name="learn-more"></a>Saiba mais
+ Para saber mais sobre ofertas do Microsoft Azure Enterprise Agreement (EA), visite [licenciamento Azure para a empresa] (https://azure.microsoft.com/pricing/enterprise-agreement/)
+ Consulte o artigo [Descrição geral do Gestor de recursos do Azure](azure-resource-manager/resource-group-overview.md) para saber mais sobre o Gestor de recursos do Azure.
+ Para obter informações adicionais sobre o conjunto de ferramentas necessárias para ajudar a ganhar a compreensão de nuvem passam, consulte Gartner artigo [Guia de mercado para ferramentas de gestão financeira TI (ITFM)](http://www.gartner.com/technology/reprints.do?id=1-212F7AL&ct=140909&st=sb).

<!--Image references-->
[1]: ./media/billing-usage-rate-card-partner-solution-cloudyn/Cloudyn-ITFM-Overview.png
[2]: ./media/billing-usage-rate-card-partner-solution-cloudyn/Cloudyn-ITFM-Engine-Overview.png
[3]: ./media/billing-usage-rate-card-partner-solution-cloudyn/Cloudyn-Cost-Analysis-Pie-Chart.png
[4]: ./media/billing-usage-rate-card-partner-solution-cloudyn/Cloudyn-Cost-Allocation-360-Chart.png
[5]: ./media/billing-usage-rate-card-partner-solution-cloudyn/Cloudyn-Cost-Effective-Sizing.png
[6]: ./media/billing-usage-rate-card-partner-solution-cloudyn/Cloudyn-Performance-Reports.png
[7]: ./media/billing-usage-rate-card-partner-solution-cloudyn/Cloudyn-Category-Manager.png
