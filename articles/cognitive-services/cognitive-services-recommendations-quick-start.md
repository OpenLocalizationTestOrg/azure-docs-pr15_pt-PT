<properties
    pageTitle="Guia de introdução: máquina aprendizagem recomendações API | Microsoft Azure"
    description="Azure máquina aprendizagem recomendações - guia de introdução"
    services="cognitive-services"
    documentationCenter=""
    authors="luiscabrer"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="cognitive-services"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/22/2016"
    ms.author="luisca"/>

# <a name="quick-start-guide-for-the-cognitive-services-recommendations-api"></a>Guia de introdução para a disfunção API de recomendações de serviços

Este documento descreve como incorporada para o serviço ou aplicação para utilizar a [API recomendações](http://go.microsoft.com/fwlink/?LinkId=759710).
Pode encontrar mais detalhes sobre a API recomendações e outros serviços disfunção [aqui](http://go.microsoft.com/fwlink/?LinkId=759709). Ao longo deste guia, poderá também encontrar a [Referência da API recomendações](http://go.microsoft.com/fwlink/?LinkId=759348) útil.


<a name="Overview"></a>
## <a name="general-overview"></a>Descrição geral

Este documento é um guia passo a passo. Sobre nosso objetivo é guiá-lo os passos necessários para formar um modelo de e para apontar para recursos que permitirão consumir o modelo a partir do seu ambiente de produção.

Este exercício irá demorar cerca de 30 minutos.

Para utilizar a [API recomendações](http://go.microsoft.com/fwlink/?LinkId=759710), terá de siga os passos seguintes:

1. Criar um modelo de-um modelo é um contentor de dados de utilização, dados do catálogo e o modelo de recomendação.
1. Importar dados de catálogo - um catálogo contém informações de metadados sobre os itens.
1. Importar dados de utilização - dados de utilização podem ser carregados numa das formas de 2 (ou ambas):
  -  Ao carregar um ficheiro que contém os dados de utilização.
  -  Enviar dados aquisição eventos.
  Normalmente carregar um ficheiro de utilização para poder criar um modelo de recomendação inicial (arranque) e utilizá-la até o sistema reúne suficiente dados utilizando o formato de aquisição de dados.
1. Construa um modelo de recomendação - esta é uma operação assíncrona na qual o sistema de recomendação leva-o até todos os dados de utilização e cria um modelo de recomendação. Esta operação pode demorar alguns minutos ou várias horas dependendo do tamanho dos dados e os parâmetros de configuração da compilação. Quando acionar a compilação, irá obter um ID de compilação. Utilizá-lo para verificar quando o processo de criação foi terminado antes de começar a consumir recomendações.
1. Consuma recomendações - obter recomendações para um item específico ou lista de itens.

<a name="GetStarted"></a>
### <a name="lets-get-started"></a>Vamos começar!

Será iniciado construir um modelo de recomendações. Em seguida, podemos irá guiá-lo sobre como utilizar os resultados gerados pelo modelo de recomendações power num site do comércio electrónico.

<a name="Ex1Task1"></a>
#### <a name="task-1---signing-up-for-the-recommendations-api"></a>Tarefa 1 - assinatura para a API de recomendações ####

Nesta tarefa, irá inscrever-se para o serviço de recomendações API e criar um modelo de recomendações.

1. Aceda ao **Portal do Azure** [http://portal.azure.com/](http://portal.azure.com/) e inicie sessão com a sua conta Azure.

1.  Clique em **+ Novo**.

1. Selecione a opção de **informações da empresa** .

1. Selecione o produto **Disfunção APIs de serviços** .
Este produto permite-lhe iniciar uma subscrição para qualquer um dos serviços disfunção APIs (nominal, a análise de texto, visão do computador, etc.). Hoje em dia podemos irá focar API recomendações.

1. Na página de destino disfunção API de serviços, introduza o **nome da conta** para a sua subscrição de recomendações. (Para instace: "MyRecommendations"). Este nome não deve ter espaços no mesmo.

1. No **tipo de API**, selecione **recomendações**.

1. **Camadas de preços**, pode selecionar um plano. Poderá seleccionar a camada **livre** para 10.000 transações/mês. Este é um plano gratuito, pelo que é uma boa forma de iniciar o sistema está a tentar. Depois de aceder a produção, recomendamos que considere o volume do pedido e alterar o tipo de plano, consoante adequado. (Nota: pode ter apenas uma subscrição gratuita camada uma vez)

1. Selecione um **Grupo de recursos**ou crie um novo se ainda não tiver um.

1. Pode alterar outros elementos na caixa de diálogo Criar. Devemos indicar que o fornecedor do recurso hoje só é suportado a partir dos Estados Unidos centros de dados.

1. Depois de ter com qualquer seleções, clique em **Criar**.

1. Aguarde alguns minutos para o recurso a ser implementada.
Assim que tiver sido implementado, pode aceder à secção **teclas** pá de **Definições** onde serão fornecidos uma chave principal e secundária para utilizar a API.  Copie a chave primária, tal como irá precisar quando criar o seu primeiro modelo.

<a name="Ex1Task2"></a>
#### <a name="task-2---did-you-bring-your-data"></a>Tarefa 2 - fez colocar os seus dados? ####

API recomendações irá aprender a partir do seu catálogo e as suas transações para fornecer recomendações de boa produto. Isto significa que precisa de feed-lo com dados boas sobre os produtos (chamamos isto um ficheiro de **catálogo** ) e um conjunto de transações grandes o suficiente para o mesmo encontrar interessantes padrões de consumo (chamamos esta **utilização**).

1. Normalmente, teria de consulta a base de dados de transações para estas partes de informações.
Caso não tivê-los útil, fornecemos alguns dados de exemplo para si com base em dados da transação Microsoft Store.

 Pode transferir os dados a partir de [aqui](http://aka.ms/RecoSampleData). Copiar e descompactar a MsStoreData.Zip para uma pasta no seu computador local.

 > **Nota:** O código de exemplo que irá transferir e executar no tarefa 3 tem os dados de exemplo incorporados dentro da mesma – para que esta tarefa ser opcional.  Assim sendo, esta tarefa irá permitem-lhe transferir mais reais conjuntos de dados e permitem-lhe compreender as entradas para API recomendações melhor.

1.  Agora vamos dar uma o ficheiro de catálogo. Navegue para a localização onde copiou os dados.
 Abra o ficheiro de catálogo no **Bloco de notas**.

 Irá Repare que o ficheiro de catálogo é bastante simple. Que tem o seguinte formato`<itemid>,<item name>,<product category>`

 >  Office AAA-04294, OfficeLangPack 2013 de 32/64 E34 Online DwnLd, <br>
 > Office AAA-04303, OfficeLangPack 2013 de 32/64 e Online DwnLd,  <br>
 > C9F 00168, KRUSELL Kiruna inverter rosto para Nokia Lumia 635 - camelo, acessórios

 Devemos indicar que um ficheiro de catálogo pode ser muito mais completo, por exemplo pode adicionar metadados sobre os produtos (chamamos destas *funcionalidades do item*). Deverá ver secção [formato](http://go.microsoft.com/fwlink/?LinkID=760716) do catálogo na referência API para obter mais detalhes sobre o formato de catálogo.

1. Vamos fazer o mesmo com os dados de utilização. Irá notar que a data de utilização está do formato `<User Id>,<Item Id>,<Time Stamp>,<Event>`.

  > 00037FFEA61FCA16, 288186200, 2015/08/04T11:02:52, 0003BFFDD4C2148C de compra, 297833400, 2015/08/04T11:02:50, 0003BFFDD4C2118D de compra, 297833300, 2015/08/04T11:02:40, 00030000D16C4237 de compra, 297833300, 2015/08/04T11:02:37, 0003BFFDD4C20B63 de compra, 297833400, 2015/08/04T11:02:12, 00037FFEC8567FB8 de compra, 297833400, 2015/08/04T11:02:04, compra

Repare que os primeiros três elementos são obrigatórios. O tipo de evento é opcional. Pode verificar o [formato de utilização](http://go.microsoft.com/fwlink/?LinkID=760712) para obter mais informações sobre este tópico.

 > **Quantidade de dados que precisa?**
 <p>
Bem, na verdade depende os dados de utilização propriamente ditos. O sistema aprende quando os utilizadores compram itens diferentes. Para alguns compilações como FBT, é importante saber quais os itens sejam comprados transações a mesma. (Chamamos este *ocorrências cocriação*). É uma boa regra útil para que a maioria dos itens estar no 20 transações ou mais, para que se tinha 10.000 itens no seu catálogo, recomendamos que tem, pelo menos, 20 horas esse número de operações ou cerca 200.000 operações.
Mais uma vez, esta é uma regra útil. Terá de experimentar com os seus dados.
</p>

<a name="Ex1Task3"></a>
####Tarefa 3 - criar um modelo de recomendações####

Agora que tem uma conta e tiver dados, vamos criar o seu primeiro modelo.

Nesta tarefa, irá utilizar a aplicação de exemplo para criar o seu primeiro modelo.

1. Em primeiro lugar deverá conhecer da [Referência da API recomendações](http://go.microsoft.com/fwlink/?LinkId=759348).

1. Transferir a [Aplicação de exemplo](http://go.microsoft.com/fwlink/?LinkID=759344) para uma pasta local.

1. Abra no Visual Studio a solução de **RecommendationsSample.sln** localizada na pasta **c#** .

1. Abra o ficheiro **SampleApp.cs** . Nota a que os seguintes passos no ficheiro:
 + Criar um modelo
 + Adicionar um ficheiro de catálogo
 + Adicionar um ficheiro de utilização
 + Acionar uma compilação para o modelo
 + Obter uma recomendação com base num par de itens
<p></p>

1. Substitua o valor do campo **AccountKey** com a chave da tarefa 1.

1. Passo através da solução e verá como um modelo é criado.

1. Tente substituir os ficheiros de catálogo e a utilização que transferiu apenas para criar um novo modelo para the Microsoft Store, ou para obter recomendações de livro de endereços. Irá precisar de alterar o nome do modelo, assim e os itens para a qual lhe pede recomendações.

1. Quando o modelo é criado, tome nota do **ID de modelo** que irá precisá-lo quando pedir recomendações no seu ambiente de produção.

>  Saiba mais sobre como criar tipos e como avaliar a qualidade da compilações [aqui](cognitive-services-recommendations-buildtypes.md).

<a name="Ex1Task4"></a>
### <a name="putting-your-model-in-production"></a>Colocar o seu modelo de produção! ###

Agora que a compreender como criar um modelo e consumir recomendações, o próximo passo é colocá-la na produção no seu site, a aplicação móvel ou integrar o seu sistema CRM ou ERP.
Obviamente, cada uma das seguintes implementações seria diferente. Uma vez que a API recomendações forem pedidas como um serviço web, deverá conseguir ligar facilmente a partir de qualquer um destes ambientes diferentes.

**Importantes:**  Se quiser apresentar recomendações de um cliente público (por exemplo, o site de comércio eletrónico), deve criar um servidor proxy para fornecer as recomendações. O que é importante para que a sua chave de API não sejam exposto a entidades (potencialmente ou não fidedignas) externas.

Aqui estão algumas ideias das localizações onde pode utilizar recomendações:

### <a name="product-page"></a>Página de produto

**Recomendações de item**
<p>Se o modelo foi preparado nos dados de compra, permitirá seu cliente para *descobrir produtos que são ser de interesse para as pessoas que tem adquirido o item de origem*.</p>
<p>Se o modelo foi formação em, clique em dados,-permitirá que o seu cliente para *descobrir produtos que provavelmente visitados por pessoas que visitou o item de origem*. Este tipo de modelo pode devolver itens semelhantes.</p>

**Frequentemente em conjunto adquiriu recomendações**
<p>A frequentemente em conjunto comprou compilação poderia ter formação, para que possa receber provavelmente conjuntos de itens de ser comprados juntamente com este item.</p>

### <a name="check-out-page"></a>Dar saída de página

**Recomendações de item**
<p>Um recomendações modelo pode demorar como uma lista de itens de introdução. Por isso, é passar todos os itens no cesto como entrada para obter recomendações.
Neste caso, o modelo irá fornecer recomendações que sejam de interesse atribuído todos os itens no cesto.
</p>

### <a name="landing-page"></a>Página de destino

**Recomendações de utilizador**
<p>
Um recomendações pode tomar modelo como o id de utilizador de entrada.  Isto irá utilizar o histórico de transações por esse utilizador para fornecer recomendações personalizadas para o utilizador especificado.
</p>

Consulte o artigo [Obter documentação do Item recomendações](http://go.microsoft.com/fwlink/?LinkID=760719).

<a name="Ex1Task6"></a>
### <a name="whats-next"></a>O que se segue?
Parabéns se que tenha efetuado-isto extremidade! Para saber que mais pode visitar a [Referência da API recomendações](http://go.microsoft.com/fwlink/?LinkId=759348) completa se tiver questões, não hesite em Contacte-namlapi@microsoft.com
