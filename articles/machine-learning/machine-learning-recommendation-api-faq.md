<properties 
    pageTitle="Configurar e utilizar a API de recomendações de aprendizagem máquina | Microsoft Azure" 
    description="Microsoft recomendações API criadas com Azure máquina aprendizagem perguntas mais frequentes" 
    services="machine-learning" 
    documentationCenter="" 
    authors="LuisCabrer" 
    manager="jhubbard" 
    editor="cgronlun"/>

<tags 
    ms.service="machine-learning" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/08/2016" 
    ms.author="luisca"/> 

#<a name="setting-up-and-using-machine-learning-recommendations-api-faq"></a>Configurar e utilizar máquina aprendizagem recomendações API perguntas mais frequentes


**O que é recomendações?**

>[AZURE.NOTE]Deverá começar a utilizar o serviço de disfunção API recomendações em vez de nesta versão. O serviço de disfunção recomendações irá substituição deste serviço e todas as novas funcionalidades, serão desenvolvidas aí. Tem novas funcionalidades como lotes suporte, uma melhor API Explorer, uma experiência de início de sessão no/faturação superfície, mais consistente API mais limpeza, etc.
> Saiba mais sobre como [Migrar para o novo serviço disfunção](http://aka.ms/recomigrate)

Para organizações e empresas que dependem de recomendações para publicação em vender e vender up produtos e serviços aos seus clientes, recomendações no Azure máquina formação fornece um motor de gestão personalizada recomendações. É uma implementação de filtragem de colaboração que utiliza factorization de matriz como o respectivo algoritmo de core. Os programadores de aplicações podem aceder a recomendações utilizando REST APIs. 

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

**O que posso fazer com recomendações?**

RECOMENDAÇÕES assume como entrada de um item ou um conjunto de itens e devolve uma lista de recomendações relevantes. Por exemplo: um cliente de um revendedor online clica um produto. O revendedor online envia desse produto como entrada para recomendações, obtém uma lista de produtos em troca e decide qual destes produtos serão apresentados para o cliente. Pretende utilizar as recomendações para otimizar o seu arquivo online ou mesmo para informar a sua interior Centro de departamento ou numa chamada de vendas.

**Existem algumas limitações da utilização?**

Recomendações tem as seguintes limitações da utilização:
* Número máximo de modelos de por subscrição: 10
* Número máximo de itens que pode conter um catálogo: 100,000
* O número máximo de pontos de utilização que são mantidas é ~ 5,000,000. O mais antigo será eliminado se novos vão ser carregados ou comunicados.
* Tamanho máximo de dados que podem ser enviados por correio eletrónico (por exemplo, importar catálogo dados, importar dados de utilização) é 200 MB
* Número de transações por segundo (TP) para uma compilação de modelo de recomendações que não esteja ativa é TP ~ 2. Uma compilação de modelo de recomendações que está ativa pode conter até 20 TP.

##<a name="purchase-and-billing"></a>Comprar e faturação 


**Quanto o custo de recomendações durante o período de iniciação?**

Recomendações é um serviço baseado em subscrição. Carregar o dispositivo é baseado volume de transações por mês. Pode verificar a [página da oferta] (https://datamarket.azure.com/dataset/amla/recommendations) no Microsoft Azure Marketplace para informação de preços.

**Existem eventuais custos associados tendo recomendações controlar e armazenam actividade do utilizador para mim?**

Não, neste momento.

**O recomendações tem uma versão de avaliação gratuita?**

Existe uma pista de gratuita está limitada a 10.000 transações por mês.

**Quando irá posso de ser faturado para obter recomendações?**

Uma subscrição paga é qualquer subscrição para a qual existe uma taxa mensal. Quando compra uma subscrição paga, são imediatamente cobradas para utilizar o primeiro mês. São cobradas o valor em que está associado a oferta na página subscrição (mais impostos aplicáveis). Este encargo mensal é tornado cada mês na mesma data do calendário como a compra original até se cancelar a subscrição. 

**Como atualizar para um serviço de camada superior?**

Pode comprar ou atualizar a sua subscrição da [página oferta] página (https://datamarket.azure.com/dataset/amla/recommendations) no Microsoft Azure Marketplace.

Quando atualizar uma subscrição:

* Transações são restante na sua subscrição antiga não são adicionadas à sua nova subscrição. 
* Pagar preço completo para a nova subscrição, apesar de ter transações não utilizadas na sua subscrição antiga.

Processo para atualizar uma subscrição:

* Nevigate para [página oferta] (https://datamarket.azure.com/dataset/amla/recommendations).
* Se já não tiver iniciado sessão, inicie sessão no mercado.
* No painel direito, todos os planos disponíveis estão listados. Clique no botão de opção para o plano que pretende atualizar para.
* Se pretende atualizar, clique em **OK**. Se não pretende atualizar, clique em **Cancelar**.

**Importante** Leia a caixa de diálogo cuidadosamente antes de atualizar uma vez que existem implicações de faturação e de utilização.

**Quando termina a minha subscrição de recomendações?**

A sua subscrição terminará ao cancelá-lo. Se pretender para cancelar as suas subscrições, consulte as instruções seguintes.

**Como posso cancelar a minha subscrição de recomendações?**

Para cancelar a sua subscrição, utilize os passos seguintes. Se a sua subscrição atual é uma subscrição paga, a sua subscrição em efeito continua a até ao final do período de faturação atual. Se precisar de cancelamento entre em vigor imediatamente, contacte-no [Suporte da Microsoft](https://support.microsoft.com/oas/default.aspx?gprid=17024&st=1&wfxredirect=1&sd=gn).

**Nota** Sem reembolso é dado se cancelar antes do final do período de faturação ou para transações não utilizadas no período de faturação.

* Navegue para [página oferta] (https://datamarket.azure.com/dataset/amla/recommendations).
* Se já não tiver iniciado sessão, inicie sessão no mercado.
* Clique em **Cancelar** para a direita do nome do conjunto de dados e do Estado. Pode utilizar esta subscrição até ao final do período de faturação atual ou a sua da transação é atingido (consoante o valor que ocorre pela primeira vez).

Se pretender para cancelar a sua subscrição imediatamente, por isso, pode comprar uma nova subscrição, ficheiros uma permissão no [Suporte da Microsoft](https://support.microsoft.com/oas/default.aspx?gprid=17024&st=1&wfxredirect=1&sd=gn).

##<a name="getting-started-with-recommendations"></a>Introdução ao recomendações

**É recomendações para mim?** 

Recomendações em aprender máquina é para organizações e empresas que dependem de recomendações para publicação em vender e vender up produtos ou serviços aos seus clientes. Se tiver um Web site destinado ao cliente, força de vendas, um interior força de vendas ou de um centro de atendimento telefónico, e se oferecer um catálogo de mais do que alguns seis produtos ou serviços, a linha inferior pode beneficiar da utilização de recomendações. 

Experiências com recomendações foi concebido para ser bastante simples. A versão atual baseadas em API requer conhecimentos básicos de programação. Se precisar de assistência, contacte o fornecedor quem desenvolvido o seu Web site. Se tiver um departamento de TI interno ou um programador interno, deverá poder obter recomendações para trabalhar para si. 

**Quais são os pré-requisitos para configurar o recomendações?**

Recomendações requer que tenha um registo das opções do utilizador que diz respeito ao catálogo. Se não t ter como um registo e possui um Web site opostas de cliente, recomendações podem recolher actividade do utilizador para si. 

Recomendações também requer um catálogo de produtos ou serviços. Se não t ter o catálogo, recomendações podem utilizar os dados de utilização de cliente real e convertê um catálogo. Um catálogo implícito não irá incluir itens que não foram relatados como parte das transações do utilizador.

**Como posso configurar recomendações pela primeira vez?**

Após [subscrever] (https://datamarket.azure.com/dataset/amla/recommendations) para recomendações, deve utilizar a documentação da API no [Azure máquina aprendizagem recomendações guia de introdução](machine-learning-recommendation-api-quick-start-guide.md) configurar o serviço.

**Onde posso encontrar documentação da API?** 

A documentação da API é [Azure máquina aprendizagem recomendações guia de introdução](machine-learning-recommendation-api-quick-start-guide.md).

**Que opções tenho de ter que carregar o catálogo e a utilização de dados para recomendações?**

Tem duas opções para carregar os dados de catálogo e a utilização: pode exportar os dados a partir do seu sistema CRM ou outros registos e carregue-o para recomendações ou pode adicionar etiquetas ao seu Web site que vai controlar atividades de utilizador. Se utiliza o último método, os dados serão armazenados no Azure.

##<a name="maintenance-and-support"></a>Manutenção e suporte

**Como grandes podem meu conjunto de dados ser?**

Cada conjunto de dados pode conter até 100.000 itens de catálogo e para cima para 2048 MB de dados de utilização.
Além disso, uma subscrição pode conter até 10 conjuntos de dados (modelos).

**Onde posso obter suporte técnico para obter recomendações?**

Suporte técnico está disponível no site de [Suporte da Microsoft Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=MachineLearning) .

**Onde posso encontrar os termos de utilização?**

[Aprendizagem recomendações API termos de serviço de automática do Microsoft Azure](https://datamarket.azure.com/dataset/amla/recommendations#terms).



 
