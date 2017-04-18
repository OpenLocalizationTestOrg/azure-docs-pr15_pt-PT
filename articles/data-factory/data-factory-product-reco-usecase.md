<properties 
    pageTitle="Caso de utilização de fábrica do mesmo dados - recomendações de produto" 
    description="Saiba mais sobre um caso de utilização implementado através da utilização do Azure dados fábrica juntamente com outros serviços." 
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
    ms.date="09/01/2016" 
    ms.author="shlo"/>

# <a name="use-case---product-recommendations"></a>Utilizar caso - recomendações de produto 

Azure fábrica de dados é um dos muitos serviços utilizados para implementar o conjunto de informações da empresa Cortana WAN solução.  Consulte page do [Conjunto de informações da empresa Cortana](http://www.microsoft.com/cortanaanalytics) para obter detalhes sobre este conjunto de aplicações. Neste documento, descrevemos um caso de utilização comuns que utilizadores Azure já tenham resolvido e implementado utilizando a fábrica de dados do Azure e outros serviços de componentes de informações da empresa de Cortana.

## <a name="scenario"></a>Cenário

Revendedores onlinehttps frequentemente querer levar os seus clientes para comprar os produtos apresentando-os com os produtos que são mais provável estar interessado e, consequentemente, provavelmente comprar. Para realizar esta tarefa, o revendedores onlinehttps tem de personalizar a sua experiência de utilizador online utilizando recomendações de produto personalizada para esse utilizador específico. Estas recomendações personalizadas estão a ser efectuadas com base nas suas atuais e histórico de dados de comportamento de, informações de produto, recentemente introduzidas marcas e dados de segmentação de cliente e produtos de compras.  Para além disso, podem fornecer as recomendações de produto do utilizador com base na análise geral a utilização comportamento a partir do todos os seus utilizadores combinadas.

O objetivo destes revendedores é otimizar para as conversões de clique para venda de utilizador e ganhar receitas de vendas superior.  Estes alcançar esta conversão mediante a entrega recomendações contextual, com base em comportamento de produto com base no cliente interesses e ações. Para este casos de utilização, utilizamos revendedores onlinehttps como um exemplo de empresas que pretende para otimizar o dos seus clientes. No entanto, estes princípios aplicam-se para as empresas que pretende participar aos seus clientes à volta dos seus produtos e serviços e melhore a experiência de compra aos seus clientes com recomendações de produto personalizada.

## <a name="challenges"></a>Desafios

Existem muitos desafios esse nominal revendedores online ao tentar implementar este tipo de casos de utilização. 

Em primeiro lugar, tem de ser penetração dados de diferentes tamanhos e formas a partir de várias origens de dados, ambos no local e na nuvem. Estes dados incluem dados de produto, dados de comportamento de cliente históricas e dados de utilizador, tal como o utilizador navega o site de revenda online. 

Recomendações de produto de segundo e personalizado devem ser razoável e com exatidão método de cálculo e previstas. Para além de produto, marca corporativa e dados de comportamento e o browser do cliente, revendedores onlinehttps também precisa de incluir comentários no passado compras para determinar as melhor recomendações de produto para o utilizador em consideração. 

Em terceiro lugar, as recomendações tem de ser imediatamente material a entregar ao utilizador para fornecer um total de navegação e compra de experiência e forneça as recomendações mais recentes e relevantes. 

Por fim, revendedores tem de medir a eficácia da sua abordagem ao controlar geral de venda de cima e vender de publicação em clique para conversão vendas sucessos e ajustar às suas recomendações futuras.

## <a name="solution-overview"></a>Descrição geral da solução

Neste caso de utilização de exemplo foi resolvido e implementado pelos utilizadores real Azure utilizando a fábrica de dados do Azure e outros serviços de componentes Cortana informações de empresa, incluindo [HDInsight](https://azure.microsoft.com/services/hdinsight/) e o [Power BI](https://powerbi.microsoft.com/).

O revendedor online utiliza uma loja de Blobs do Azure, um SQL server no local, Azure SQL DB e um ser de dados relacionais, como as opções de armazenamento de dados ao longo do fluxo de trabalho.  O arquivo de BLOBs contém informações de cliente, dados de comportamento do cliente e dados de informações do produto. Os dados de informações do produto incluem informações de marca de produto e um produto armazenados no local num armazém de dados SQL do catálogo. 

Todos os dados é combinada e alimentado num sistema de recomendação de produto para a prestação recomendações personalizadas com base em interesses de cliente e ações, enquanto o utilizador navega produtos no catálogo de no Web site. Os clientes também ver produtos estão relacionados com o produto são olhar com base em padrões de utilização do Web site global não relacionados com qualquer um utilizador.

![utilizar o diagrama de casos](./media/data-factory-product-reco-usecase/diagram-1.png)

Gigabytes de ficheiros de registo de web não processado são gerados diariamente a partir do website o revendedor online como ficheiros semiestruturados. Os ficheiros de registo de web não processados e as informações do catálogo cliente e o produto é penetração regularmente para um armazenamento de Blobs do Azure utilizando movimento globalmente implementado dados de Data Factory como um serviço. Os ficheiros de registo não processados para o dia são divididos (por ano e mês) no armazenamento blob do armazenamento a longo prazo.  [Azure HDInsight](https://azure.microsoft.com/services/hdinsight/) é utilizada para criar a partições os ficheiros de registo não processados na loja blob e os registos aspirados na escala de utilização de scripts ramo e porco do processo. A web com partições os registos de dados, em seguida, é processada deverão ser extraídas as entradas conforme seja necessárias para uma sistema de recomendação para gerar as recomendações de produto personalizadas de aprendizagem automática.

O sistema de recomendação utilizado para o computador de formação neste exemplo é uma máquina de abrir origem plataforma recomendação a partir do [Apache Mahout](http://mahout.apache.org/)de formação.  Qualquer [Azure máquina formação](https://azure.microsoft.com/services/machine-learning/) ou o modelo personalizado pode ser aplicado para o cenário.  O modelo de Mahout é utilizado para prever as semelhanças entre itens no Web site com base em geral padrões de utilização e para gerar as recomendações personalizadas com base no utilizador individual.

Por fim, o conjunto de resultados de recomendações de produto personalizada é movido para uma ser de dados relacionais para consumo por site do revendedor.  O conjunto de resultados também poderia ser acedido diretamente a partir do armazenamento de BLOBs por outra aplicação ou movido para lojas adicionais para as outras consumidores e casos de utilização.

## <a name="benefits"></a>Benefícios

Por otimizar a sua estratégia de recomendação de produto e alinhá-la com os objectivos comerciais, se a solução satisfeito ajuste o revendedor online e marketing objetivos. Para além disso, terem sido conseguir operationalize e gerir o fluxo de trabalho de recomendação do produto de uma forma eficaz, fiável e rentável. A abordagem efetuadas mais facilmente-los atualizar o seu modelo e ajustar a sua eficácia com base nas medidas de sucessos de conversão de clique em vendas. Ao utilizar a fábrica de dados do Azure, terem sido conseguir abandonar a gestão de recursos os respetivos moroso e dispendioso nuvem manual e deslocar-se para a gestão de recursos nuvem a pedido. Por conseguinte, terem sido conseguir poupar tempo, dinheiro e reduzir o seu tempo a implementação de solução. Vistas de linhagem de dados e o estado de funcionamento do serviço operacional se tornou mais fácil visualizar e resolver problemas com a intuitiva dados fábrica monitorização e gestão ao IU disponível a partir do portal do Azure. Sua solução agora pode ser agendada e gerida para que dados concluído estão sujeito produzidos e entregues aos utilizadores e dados e as dependências de processamento são automaticamente geridas sem intervenção humana.

Ao fornecer este experiência personalizada compras, o revendedor online criado um cliente mais competitivo, interessantes experiência e, consequentemente, aumente a satisfação do cliente de vendas e geral.



  