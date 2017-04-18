<properties 
    pageTitle="Utilizar caso - criação de perfis de cliente" 
    description="Saiba como Azure dados fábrica é utilizada para criar um condicionados por dados fluxo de trabalho (pipeline) para criar um perfil clientes de jogos." 
    services="data-factory" 
    documentationCenter="" 
    authors="sharonlo101" 
    manager="jhubbard" 
    editor="monicar"/>

<tags 
    ms.service="data-factory" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/06/2016" 
    ms.author="shlo"/>

# <a name="use-case---customer-profiling"></a>Utilizar caso - criação de perfis de cliente

Azure fábrica de dados é um dos muitos serviços utilizados para implementar o conjunto de informações da empresa Cortana WAN solução.  Para mais informações sobre Cortana Intelligence, visite o [Conjunto de informações da empresa Cortana](http://www.microsoft.com/cortanaanalytics). Neste documento, descrevemos um caso de utilização simples para o ajudar a começar a trabalhar com Noções sobre como Azure dados fábrica pode resolver problemas comuns de análise.

Tudo o que precisa para aceder e experimentar este casos de utilização simples é uma [subscrição do Azure](https://azure.microsoft.com/pricing/free-trial/).  Pode implementar uma amostra que implementa este casos de utilização ao seguir os passos descritos no artigo [amostras](data-factory-samples.md) .

## <a name="scenario"></a>Cenário

Contoso é uma empresa de jogos que cria jogos para múltiplas plataformas: jogo consolas, bolso dispositivos e computadores pessoais (PCs). Leitores de reproduzir estes jogos, grande volume de dados do registo é produzida que controla os padrões de utilização, estilo de jogos e preferências do utilizador.  Quando o combinado com demográfica, regional e dados de produto, Contoso pode executar análises para orientá-los acerca de como melhorar a experiência dos leitores e compras de destino que lhes actualizações e no jogo. 

Objetivo da Contoso é identificar oportunidades de cima vender/cruzada vender com base no histórico de jogos dos seus leitores e adicionar funcionalidades apelativas para o crescimento de negócio de unidade e fornecer uma melhor experiência aos clientes. Para este casos de utilização, utilizamos uma empresa de jogos como um exemplo de um negócio. Pretende que a empresa otimizar o seus jogos com base no comportamento dos leitores. Estes princípios aplicam-se para as empresas que pretende participar aos seus clientes à volta dos seus produtos e serviços e melhore a experiência aos seus clientes.

## <a name="challenges"></a>Desafios


## <a name="solution-overview"></a>Descrição geral da solução

Neste caso utilize simples pode ser utilizado como um exemplo de como pode utilizar Azure dados fábrica para ingerir esta última, preparar, transformar, analisar e publicar dados.

![Fluxo de trabalho de ponto a ponto](./media/data-factory-customer-profiling-usecase/EndToEndWorkflow.png)

Esta figura mostra como as tubagens dados aparecem no portal do Azure depois de foram implementados.

1.  O **PartitionGameLogsPipeline** lê os eventos de jogos observou a partir do armazenamento de BLOBs e cria a partições com base em dia, mês e ano.
2.  O **EnrichGameLogsPipeline** associa eventos jogos com partições com dados de referência de código geo e enriquece os dados através do mapeamento de endereços IP para as localizações de geo correspondentes.
3.  Pipeline de **AnalyzeMarketingCampaignPipeline** utiliza os dados enriquecidos e processa-la com os dados de publicidade para criar o resultado final que contém marketing da eficácia da campanha.

Neste exemplo, a fábrica de dados é utilizada para orquestrar atividades que copie os dados de entrada, transformação e processo os dados e exportar os dados para uma base de dados do SQL Azure finais.  Também pode visualizar a rede de dados tubagens, geri-los e monitorizar o estado da IU.

## <a name="benefits"></a>Benefícios

Otimizar os seu análise de perfil de utilizador e alinhá-lo com objectivos empresariais, empresa de jogos é possível recolher rapidamente os padrões de utilização, e analisar a eficácia da suas campanhas de marketing.




