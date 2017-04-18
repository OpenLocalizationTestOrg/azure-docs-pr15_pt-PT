<properties
    pageTitle="Utilizar os conjuntos de dados de exemplo no máquina aprendizagem Studio | Microsoft Azure"
    description="Descrições dos conjuntos de dados utilizados em modelos de exemplo incluídos no ML Studio. Pode utilizar estes conjuntos de dados de exemplo para as experiências."
    services="machine-learning"
    documentationCenter=""
    authors="garyericson"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/16/2016"
    ms.author="garye"/>


# <a name="use-the-sample-data-sets-in-azure-machine-learning-studio"></a>Utilizar os conjuntos de dados de exemplo no Azure máquina aprendizagem Studio

[top]: #machine-learning-sample-datasets

Quando cria uma nova área de trabalho no Azure máquina formação, um número de conjuntos de dados de exemplo e experiências está incluído por predefinição. Muitos dos seguintes conjuntos de dados de exemplo utilizados pelos modelos de exemplo na [Galeria de informações da empresa do Azure Cortana](http://gallery.cortanaintelligence.com/)e outras pessoas estão incluídas como exemplos de vários tipos de dados normalmente utilizados em aprender máquina.

Alguns dos seguintes conjuntos de dados estão disponíveis no armazenamento de Blobs do Azure. Para estes conjuntos de dados a tabela abaixo fornece uma ligação direta. Pode utilizar estes conjuntos de dados no seu experiências utilizando os [Importar dados] [ import-data] módulo.

O resto dos seguintes conjuntos de dados de exemplo são listados em **Conjuntos de dados guardado** na paleta de módulo à esquerda da tela experiência quando que abra ou crie uma nova experiência no ML Studio.
Pode utilizar qualquer um dos seguintes conjuntos de dados na sua própria experiência ao arrastá-lo à sua tela experiência.


<!--
For a list of sample experiments available in ML Studio, see [Machine Learning Sample Experiments][sample-experiments].

[sample-experiments]: machine-learning-sample-experiments.md
-->

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]


<table>

<tr>
  <th align=left>Nome do conjunto de dados</th>
  <th align=left>Descrição do conjunto de dados</th>
</tr>

<tr>
  <td valign=top>Conjunto de dados de contagem rendimento binário classificação adultos</td>
  <td valign=top>
Um subconjunto de 1994 contagem base de dados, utilizando o trabalho adultos através da idade de 16 com um índice rendimento ajustado > 100.<p> </p><b>Utilização:</b> classificar as pessoas que utilizem demografia para prever se uma pessoa ganha mais de 50K por ano.<p> </p><b>Investigação relacionada:</b> Kohavi, r, Becker, b, (1996). UCI máquina repositório <a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml</a>de formação. Irvine, CA: University of California, escola de informação e da ciência do computador </td>
</tr>

<tr ID=airport-codes-dataset>
  <td valign=top>Conjunto de códigos dos aeroportos dados</td>
  <td valign=top>
Códigos dos aeroportos dos EUA.<p> </p>Este conjunto de dados contém uma linha para cada aeroportos dos EUA, fornecer o número de ID dos aeroportos e o nome juntamente com a cidade de localização e o estado.
  </td>
</tr>

<tr>
  <td valign=top>Dados de automóveis preço (matéria-prima)</td>
  <td valign=top>
Obter informações sobre automóveis por fazer e modelar, incluindo o preço, funcionalidades, como o número de garrafas e MPG, bem como uma pontuação de risco de seguros.<p> </p>Pontuação risco associado ao inicialmente preço automática e, em seguida, ajustado para real risco num processo conhecido por Actuários como symboling. Um valor de + 3 indica que o automática é arriscada, e um valor de -3 que a mesma é muito provavelmente segura.<p> </p><b>Utilização:</b> prever pontuação risco por funcionalidades, utilizando a classificação de regressão ou multivariate. <p> </p><b>Investigação relacionada:</b> Schlimmer, J.C. (1987). UCI máquina repositório <a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml</a>de formação. Irvine, CA: University of California, escola de informação e da ciência do computador </td>
</tr>

<tr ID=bike-rental-uci-dataset>
  <td valign=top>Conjunto de dados de aluguer UCI de bicicleta</td>
  <td valign=top>
UCI bicicleta aluguer conjunto de dados que é baseado no dados reais da empresa de Capital Bikeshare que mantém numa rede de bicicleta aluguer Washington Cc.<p> </p>O conjunto de dados tem uma linha para cada hora de cada dia no 2011 e 2012, para um total de 17,379 linhas. O intervalo de hora a hora aluguer de bicicleta é entre 1 977.

  </td>
</tr>

<tr ID=bill-gates-rgb-image>
  <td valign=top>Imagem RGB Bill Gates</td>
  <td valign=top>
Ficheiro de imagem publicamente disponível convertido aos dados CSV.<p> </p>O código para converter a imagem é fornecido na página de detalhes do modelo de <strong>cores quantization utilizando K meios clusters</strong> .
  </td>
</tr>

<tr>
  <td valign=top>Dados de donativos controlador de pressão</td>
  <td valign=top>
Um subconjunto de dados da base de dados do Centro de Hsin Chu de serviço o transfusão cidade, Taiwan dador controlador de pressão.<p> </p>Dados de dador incluem os meses desde a último donativos) e frequência ou o número total de donativos, tempo desde a último donativos e a quantidade de controlador de pressão doado.<p> </p><b>Utilização:</b> o objetivo é prever através de classificação se dador Doado controlador de pressão no de 2007 Março, onde 1 indica um dador durante o período de destino e 0 um não dador. <p> </p><b>Investigação relacionada:</b> yé satisfazerem, (2008). UCI máquina repositório <a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml</a>de formação. Irvine, CA: University of California, escola de informação e da ciência do computador <p> </p>Yé, posso-Cheng, Yang, rei-Jang e reunião, Tao-Ming "deteção de dados de conhecimento no modelo RFM utilizando a sequência de Bernoulli" especialista sistemas com aplicações, 2008, <a href="http://dx.doi.org/10.1016/j.eswa.2008.07.018">http://dx.doi.org/10.1016/j.eswa.2008.07.018</a>
  </td>
</tr>

<tr ID=book-reviews-from-amazon>
  <td valign=top>Livro de endereços revisões da Amazon</td>
  <td valign=top>
Revisões de livros no Amazon, tirado de Web site da amazon.com pelos investigadores Universidade de Pennsylvania (<a href="http://www.cs.jhu.edu/~mdredze/datasets/sentiment/">sentimento</a>). O papel de pesquisa, consulte o artigo "biografias, Bollywood, caixas de braço e misturas: adaptação de domínio para a classificação de sentimento" João Blitzer, marca Dredze e Fernando Pereira; Associação de linguística utilizaria (ACL), a 2007.<p> </p>O conjunto de dados original tem 975K revisões com classificações de 1, 2, 3, 4 ou 5. As revisões foram escritas em inglês e do período de tempo 1997-2007. Este conjunto de dados foi amostra para baixo para 10K revisões.
  </td>
</tr>

<tr>
  <td valign=top>Dados de cancro peito</td>
  <td valign=top>
Uma das três relacionados com cancro conjuntos de dados fornecidos pelo Instituto Oncology que aparece com frequência no especificações de formação do computador. Combina informações de diagnóstico com funcionalidades de análise de laboratório de cerca de 300 amostras de atualizações.<p> </p><b>Utilização:</b> classificar o tipo de cancro, com base em 9 atributos, algumas das quais são lineares e algumas são categorias. <p> </p><b>Investigação relacionada:</b> O.L. Wohlberg, W.H., rua, W.N. e Mangasarian, (1995). UCI máquina repositório <a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml</a>de formação. Irvine, CA: University of California, escola de informação e da ciência do computador </td>
</tr>

<tr ID=breast-cancer-features>
  <td valign=top>Funcionalidades de cancro peito <td valign=top>
O conjunto de dados contém informações de regiões suspeita 102K (candidatos) raios de imagens de, cada descrito por 117 funcionalidades. As funcionalidades estão proprietárias e o seu significado não é apresentado pelos criadores do conjunto de dados (Siemens cuidados de saúde). 
  </td>
</tr>

<tr ID=breast-cancer-info>
  <td valign=top>Informações de cancro peito</td>
  <td valign=top>
O conjunto de dados contém informações adicionais para cada região suspeita da imagem raios. Cada exemplo fornece informações (por exemplo, etiqueta, paciente ID, coordenadas de patches relativamente às toda a imagem) sobre o número de linha correspondente no conjunto de funcionalidades de cancro os dados. Cada paciente tem um número de exemplos. Para doentes que têm um cancro, alguns exemplos são positivos e algumas forem negativas. Para doentes que não têm um cancro, todos os exemplos são negativos. O conjunto de dados tem 102K exemplos. O conjunto de dados é baseado 0,6% dos pontos forem positivos, negativos os restantes. O conjunto de dados foi disponibilizado pelos Siemens cuidados de saúde.
  </td>
</tr>

<tr ID=crm-appetency-labels-shared>
  <td valign=top>Etiquetas de Appetency CRM partilhadas</td>
  <td valign=top>
Etiquetas de desafio de previsão de relação de cliente KDD chávena 2009 (<a href="http://www.sigkdd.org/site/2009/files/orange_small_train_appetency.labels">orange_small_train_appetency.labels</a>).
  </td>
</tr>

<tr ID=crm-churn-labels-shared>
  <td valign=top>Etiquetas de vasilha CRM partilhadas</td>
  <td valign=top>
Etiquetas de desafio de previsão de relação de cliente KDD chávena 2009 (<a href="http://www.sigkdd.org/site/2009/files/orange_small_train_churn.labels">orange_small_train_churn.labels</a>).
  </td>
</tr>

<tr ID=crm-dataset-shared>
  <td valign=top>CRM conjunto de dados partilhado</td>
  <td valign=top>
Estes dados provém HIP de previsão de relação de cliente KDD chávena 2009 (<a href="http://www.sigkdd.org/kdd-cup-2009-customer-relationship-prediction - orange_small_train.data.zip">orange_small_train.data.zip</a>).<p> </p>O conjunto de dados contém 50K clientes da empresa de telecomunicações francês laranja. Cada cliente tem 230 funcionalidades anónimos 190 que são numéricos e 40 são categorias. As funcionalidades são muito dispersas.
  </td>
</tr>

<tr ID=crm-upselling-labels-shared>
  <td valign=top>Etiquetas de Upselling CRM partilhadas</td>
  <td valign=top>
Etiquetas de desafio de previsão de relação de cliente KDD chávena 2009 (<a href="http://www.sigkdd.org/site/2009/files/orange_large_train_upselling.labels">orange_large_train_upselling.labels</a>).
  </td>
</tr>

<tr>
  <td valign=top>Dados de regressão eficiência de energia</td>
  <td valign=top>
Uma coleção de perfis de energia simulada, com base em 12 edifício diferentes formas. Edifícios ser diferentes no que diz respeito à diferenciar por 8 funcionalidades, como vidro área, distribuição de área de vidro e orientação.<p> </p><b>Utilização:</b> utilizar regressão ou classificação para prever a eficiência de energia de classificação baseada em como uma das duas respostas reais avaliadas. Para a classe com várias classificação, é arredondar a variável de resposta para o número inteiro mais próximo. <p> </p><b>Investigação relacionada:</b> Xifara, respostas. & Tsanas, respostas. (2012). UCI máquina repositório <a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml</a>de formação. Irvine, CA: University of California, escola de informação e da ciência do computador </td>
</tr>

<tr ID=flight-delays-data>
  <td valign=top>Dados de atrasos em voos</td>
  <td valign=top>
Voo passageiros hora dados de desempenho tirados de recolha de dados de TranStats do departamento EUA de transporte (<a href="http://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time">Hora</a>).<p> </p>O conjunto de dados abrange o período de tempo de Abril de Outubro de 2013. Antes de carregar para Azure ML Studio, o conjunto de dados foi processado do seguinte modo:<ul><li>O conjunto de dados foram filtrado para cobrir apenas 70 ocupação aeroportos a continental dos e.u.a.</li><li>Voos cancelados foram denominados como atrasadas por mais de 15 minutos</li><li>Número de voos foram filtrados</li><li>As seguintes colunas foram selecionadas: ano, mês, DayofMonth, DayOfWeek, Carrier, OriginAirportID, DestAirportID, CRSDepTime, DepDelay, DepDel15, CRSArrTime, ArrDelay, ArrDel15, cancelada</li></ul>
</td>
</tr>

<tr>
  <td valign=top>Desempenho de hora voo (matéria-prima)</td>
  <td valign=top>
Registos de chegadas de voo de um avião e partidas dentro dos Estados Unidos de Outubro de 2011.<p> </p><b>Utilização:</b> prever atrasos em voos. <p> </p><b>Investigação relacionada:</b> a partir dos EUA Dept. o de transporte <a href="http://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time">http://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time</a>.
  </td>
</tr>

<tr>
  <td valign=top>Floresta é acionada dados</td>
  <td valign=top>
Contém dados Meteorologia, tais como temperatura e humidade índices e velocidade do vento, a partir de uma área de Portugal da Nordeste, combinado com registos de florestal.<p> </p><b>Utilização:</b> esta é uma tarefa de regressão difícil, onde a objectivo prever a área gravada da florestal. <p> </p><b>Investigação relacionada:</b> Cortez, P. & Morais, respostas. (2008). UCI máquina repositório <a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml</a>de formação. Irvine, CA: University of California, escola de informação e da ciência do computador <p> </p>[Cortez e Morais, 2007] P. Cortez e respostas. Morais. Uma abordagem de Data Mining dados para prever florestal utilizando dados meteorológicos. No Neves j, f m. Santos e J. Machado eds, novas tendências em Artificial informações da empresa, um processo de 13 de Maio de EPIA 2007 - português conferência no Artificial informações da empresa, Dezembro, Guimarães, Portugal, p. 512-523, 2007. APPIA, ISBN-13 978-989-95618-0-9. Disponíveis em: <a href="http://www.dsi.uminho.pt/~pcortez/fires.pdf">http://www.dsi.uminho.pt/~pcortez/fires.pdf</a>.
  </td>
</tr>

<tr ID=german-credit-card-uci-dataset>
  <td valign=top>Alemão cartão de crédito UCI conjunto de dados</td>
  <td valign=top>
O UCI Statlog (cartão de crédito alemão) conjunto de dados (<a href="http://archive.ics.uci.edu/ml/datasets/Statlog+(German+Credit+Data)">Statlog + alemão + crédito + dados</a>), utilizando o ficheiro german.data.<p> </p>O conjunto de dados classifica pessoas, descritas por um conjunto de atributos, como riscos de crédito alta ou baixa. Cada exemplo representa uma pessoa. Existem 20 funcionalidades, numéricas e categorias e uma etiqueta binária (o valor de risco de crédito). Entradas de risco de crédito alta têm etiqueta = 2, entradas de risco de crédito baixa têm etiqueta = 1. O custo de classifique incorrectamente um exemplo de baixo risco como alto for 1, Considerando que o custo de classifique incorrectamente um exemplo de alto risco como baixa for a 5.
  </td>
</tr>

<tr ID=imdb-movie-titles>
  <td valign=top>Títulos de filme IMDB</td>
  <td valign=top>
O conjunto de dados contém informações sobre filmes que foram classificados no Twitter tweets: IMDB filme ID, nome de filme e género, ano de produção. Existem 17K filmes o conjunto de dados. O conjunto de dados foi introduzido numa papel "S. Dooms, t De Pessemier e Martens L.. MovieTweetings: um conjunto de dados de classificação de filme recolhidas a partir do Twitter. Seminário no Crowdsourcing e cálculo humano para sistemas de recomendação, CrowdRec do RecSys 2013."
  </td>
</tr>

<tr>
  <td valign=top>Dados de classe IRIS duas</td>
  <td valign=top>
Talvez esta é a melhor conhecido base de dados a ser encontrado na bibliografia de reconhecimento padrão. O conjunto de dados é relativamente pequeno, contendo 50 exemplos de medidas Pétala a partir de três variedades iris.<p> </p><b>Utilização:</b> prever o tipo de iris partir as medidas.  <p> </p><b>Investigação relacionada:</b> Fisher, R.A. (1988). UCI máquina repositório <a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml</a>de formação. Irvine, CA: University of California, escola de informação e da ciência do computador </td>
</tr>

<tr ID=movie-tweets>
  <td valign=top>Tweets de filme</td>
  <td valign=top>
O conjunto de dados é uma versão expandida do conjunto de dados de filme Tweetings. O conjunto de dados tem 170K classificações para filmes, extraídas de tweets estruturados no Twitter. Cada instância representa um tweet e é uma cadeia de identificação: ID de utilizador, IMDB filme ID, classificação, carimbo data/hora, número de Favoritos para esta tweet e número de retweets deste tweet. O conjunto de dados foi disponibilizado pelos disse respostas., S. Dooms, B. Loni e Tikk D. de recomendação sistemas HIP 2014.
  </td>
</tr>

<tr>
  <td valign=top>Dados MPG para vários automóveis</td>
  <td valign=top>
Este conjunto de dados é uma versão de forma ligeiramente modificada do conjunto de dados fornecido pela biblioteca de StatLib da Carnegie Mellon University. O conjunto de dados foi utilizado no 1983 americana estatística associação manual.<p> </p>Consumo de combustível para vários automóveis no milhas por galão, juntamente com informações como o número de cilindros, deslocamento motor, potência, peso total e aceleração de listas de dados.<p> </p><b>Utilização:</b> prever economia de combustível com base em atributos descontínuos de valores múltiplos 3 e 5 atributos contínuos. <p> </p><b>Investigação relacionada:</b> StatLib Carnegie Mellon University, (1993). UCI máquina repositório <a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml</a>de formação. Irvine, CA: University of California, escola de informação e da ciência do computador </td>
</tr>

<tr>
  <td valign=top>Pima indianos Diabetes binário classificação conjunto de dados</td>
  <td valign=top>
Um subconjunto de dados do Instituto Diabetes nacionais e aparelho e doença de rim da base de dados. O conjunto de dados foi filtrado concentrar-se nas feminino pacientes de património indiano Pima. Os dados incluem dados médicos como glucose e níveis de inulina, bem como fatores de estilo de vida.<p> </p><b>Utilização:</b> prever se o assunto tem diabetes (binária classificação). <p> </p><b>Investigação relacionada:</b> Sigillito, V. (1990). Máquina UCI repositório de formação <a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml "</a>. Irvine, CA: University of California, escola de informação e da ciência do computador </td>
</tr>

<tr>
  <td valign=top>Dados do cliente de restaurante</td>
  <td valign=top>
Um conjunto de metadados sobre os clientes, incluindo demografia e preferências.<p> </p><b>Utilização:</b> utilizar este conjunto de dados, juntamente com os outros dois restaurante conjuntos de dados, para dar formação e testar um sistema de recomendação. <p> </p><b>Investigação relacionada:</b> Bache, k e Lichman, m (2013). UCI máquina repositório <a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml</a>de formação. Irvine, CA: University of California, escola de informação e ciência do computador.
  </td>
</tr>

<tr>
  <td valign=top>Dados da funcionalidade de restaurante</td>
  <td valign=top>
Um conjunto de metadados sobre restaurantes e suas funcionalidades, como tipo de alimentação, jantar estilo e localização.<p> </p><b>Utilização:</b> utilizar este conjunto de dados, juntamente com os outros dois restaurante conjuntos de dados, para dar formação e testar um sistema de recomendação. <p> </p><b>Investigação relacionada:</b> Bache, k e Lichman, m (2013). UCI máquina repositório <a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml</a>de formação. Irvine, CA: University of California, escola de informação e ciência do computador.
  </td>
</tr>

<tr>
  <td valign=top>Classificações de restaurante</td>
  <td valign=top>
Contém classificações atribuídas por utilizadores para restaurantes uma escala de 0 a 2.<p> </p><b>Utilização:</b> utilizar este conjunto de dados, juntamente com os outros dois restaurante conjuntos de dados, para dar formação e testar um sistema de recomendação. <p> </p><b>Investigação relacionada:</b> Bache, k e Lichman, m (2013). UCI máquina repositório <a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml</a>de formação. Irvine, CA: University of California, escola de informação e ciência do computador.
  </td>
</tr>

<tr>
  <td valign=top>Conjunto de dados de classe com várias aço Annealing</td>
  <td valign=top>
Este conjunto de dados contém uma série de registos de aço recozimento tentativas com os atributos físicas (largura, espessura, (bobina, folha, etc.) do resultante aço tipos de tipo.<p> </p><b>Utilização:</b> prever qualquer um dos dois atributos de classe numérico; dureza ou rutura. Também poderá analisar correlação entre atributos.<p> </p>Tipos de aço siga um conjunto padrão, definido pelo SAE e de outras organizações. Está à procura um específico 'grau' (a variável de classe) e pretender compreender os valores necessários. <p> </p><b>Investigação relacionada:</b> esterlina, D. & Buntine, w. (disp). UCI máquina repositório <a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml</a>de formação. Irvine, CA: University of California, escola de informação e da ciência do computador <p> </p>Um guia útil para tipos de aço pode ser encontrado aqui: <a href="http://www.outokumpu.com/SiteCollectionDocuments/Outokumpu-steel-grades-properties-global-standards.pdf">http://www.outokumpu.com/SiteCollectionDocuments/Outokumpu-steel-grades-properties-global-standards.pdf</a>
  </td>
</tr>

<tr>
  <td valign=top>Telescópio dados</td>
  <td valign=top>
Registos de alta energia gama partícula rajada juntamente com o ruído de fundo, ambos simulado utilizando um processo Monte Carlo.<p> </p>A intenção de simulação foi melhorar a exatidão de terra atmosféricas Cherenkov gama telescópios, utilizando métodos estatísticos para diferenciar entre o sinal pretendida (Cherenkov radiações bem) e o ruído de fundo (hadronic bem iniciado por raios cosmic na atmosfera superior).<p> </p>Os dados tem sido previamente transformados para criar um cluster alongado com o long eixo está orientado para o Centro de câmara. As características deste elipse, (frequentemente chamado Hillas parâmetros) estão entre os parâmetros de imagem que podem ser utilizados para discriminação.<p> </p><b>Utilização:</b> prever se a imagem de uma festa representa ruído sinal ou em segundo plano.<p> </p><b>Notas:</b> precisão de classificação simples não é relevante para estes dados desde classificar um evento de fundo como sinal está pior do que classificar um evento de sinal como fundo. Para comparação entre diferentes classificadores grafo do ROC deve ser utilizado. A probabilidade de aceitar um evento de fundo como sinal tem de ser abaixo de um dos seguintes limiares: 0,01, 0,02, 0,05, 0,1 ou 0,2 mais próximo.<p> </p>Além disso, tenha em atenção que o número de eventos de fundo (h, para bem hadronic) é subestimado, Considerando que em reais medidas, a classe de h ou ruído representa a maioria dos eventos. <p> </p><b>Investigação relacionada:</b> Bock, R.K. (1995). UCI máquina repositório <a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml</a>de formação. Irvine, CA: Universidade da Califórnia, escola de informação </td>
</tr>

<tr ID=weather-dataset>
  <td valign=top>Meteorologia conjunto de dados</td>
  <td valign=top>
Observações com base no terreno Meteorologia horários de NOAA (<a href="http://cdo.ncdc.noaa.gov/qclcd_ascii/, merged data from 201304 to 201310">dados intercalados a partir do 201304 para 201310</a>).<p> </p>Os dados meteorológicos abrange observações criadas a partir dos aeroportos Meteorologia estações, que abrangem o período de tempo de Abril de Outubro de 2013. Antes de carregar para Azure ML Studio, o conjunto de dados foi processado do seguinte modo:<ul><li>IDs de estação de Meteorologia foram mapeados para aeroporto correspondente IDs</li><li>Meteorologia estações não associadas os 70 aeroportos ocupação foram filtradas</li><li>A coluna data foi dividir em colunas separadas, dia, mês e ano</li><li>As seguintes colunas foram selecionadas: AirportID, ano, mês, dia, hora, fuso horário, SkyCondition, visibilidade, WeatherType, DryBulbFarenheit, DryBulbCelsius, WetBulbFarenheit, WetBulbCelsius, DewPointFarenheit, DewPointCelsius, RelativeHumidity, velocidade do vento, WindDirection, ValueForWindCharacter, StationPressure, PressureTendency, PressureChange, SeaLevelPressure, Tiporegisto, HourlyPrecip, altímetro</li></ul>
  </td>
</tr>

<tr ID=wikipedia-sp-500-dataset>
  <td valign=top>Wikipédia SP 500 conjunto de dados</td>
  <td valign=top>
Dados deriva da Wikipédia (<a href="http://www.wikipedia.org/">http://www.wikipedia.org/</a>), com base em artigos de cada empresa S & P 500, armazenados como dados XML.<p> </p>Antes de carregar para Azure ML Studio, o conjunto de dados foi processado do seguinte modo:<ul><li>Extrair conteúdo de texto para cada empresa específica</li><li>Remover a formatação do wiki</li><li>Remover carateres não-alfanuméricos</li><li>Converter todo o texto para minúsculas</li><li>Categorias de empresa conhecidos foram adicionadas</li></ul><p> </p>Tenha em atenção que, para que algumas empresas um artigo não foi possível encontrar, pelo que o número de registos inferiores a 500.
  </td>
</tr>





<tr ID=direct-marketing>
  <td valign=top><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/direct_marketing.csv">direct_marketing.csv</a></td>
  <td valign=top>
O conjunto de dados contém os dados do cliente e indicações sobre a sua resposta a uma campanha de correio direta. Cada linha representa um cliente. O conjunto de dados contém 9 funcionalidades sobre demografia de utilizador e passados comportamento e 3 colunas de etiqueta (visita, conversão e passam a).  Visite é uma coluna binária que indica que um cliente visitados após a campanha de marketing, conversão indica um cliente comprado algo e passam é a quantidade gasto.  O conjunto de dados foi disponibilizado pelos Kevin Hillstrom para MineThatData correio electrónico Analytics e dados de Data Mining HIP.
  </td>
</tr>

<tr ID=lyrl2004-tokens-test>
  <td valign=top><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/lyrl2004_tokens_test.csv">lyrl2004_tokens_test.csv</a></td>
  <td valign=top>
Funcionalidades de exemplos de teste do conjunto de dados de notícias and RCV1 V2 Reuters. Artigos de notícias 781K juntamente com os IDs de tem o conjunto de dados (primeira coluna do conjunto de dados). Cada artigo é com token, stopworded e haste. O conjunto de dados foi disponibilizado pelos David. D. Luís.
  </td>
</tr>

<tr ID=lyrl2004-tokens-train>
  <td valign=top><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/lyrl2004_tokens_train.csv">lyrl2004_tokens_train.csv</a></td>
  <td valign=top>
Funcionalidades de exemplos de formação do conjunto de dados de notícias and RCV1 V2 Reuters. Artigos de notícias 23K juntamente com os IDs de tem o conjunto de dados (primeira coluna do conjunto de dados). Cada artigo é com token, stopworded e haste. O conjunto de dados foi disponibilizado pelos David. D. Luís.
  </td>
</tr>

<tr ID=intrusion-detection>
  <td valign=top><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/network_intrusion_detection.csv">network_intrusion_detection.csv</a><br></td>
  <td valign=top>
Conjunto de dados a partir da deteção de dados de conhecimento KDD chávena 1999 e dados extrair ferramentas concorrência (<a href="http://kdd.ics.uci.edu/databases/kddcup99/kddcup99.html">kddcup99.html</a>).<p> </p>O conjunto de dados foi transferido e armazenadas no armazenamento de Blobs do Azure (<a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/network_intrusion_detection.csv">network_intrusion_detection.csv</a>) e inclui formação e testes de conjuntos de dados. O conjunto de dados de formação tem aproximadamente 126K linhas e 43 colunas, incluindo as etiquetas; 3 colunas fazem parte das informações da etiqueta e 40 colunas, que consiste funcionalidades numéricos e de cadeia categorias, estão disponíveis para o modelo de formação. Os dados de teste tem aproximadamente 22,5 K testar exemplos com as mesmas 43 colunas tal como os dados de formação.

  </td>
</tr>

<tr ID=rcv1-v2-topics-qrels>
  <td valign=top><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/rcv1-v2.topics.qrels.csv">rcv1 v2.topics.qrels.csv</a></td>
  <td valign=top>
Atribuições de tópico para o conjunto de dados de notícias and RCV1 V2 Reuters artigos notícias. Pode ser atribuído um artigo de notícias para vários tópicos. O formato de cada linha é "<topic name>  <document id> 1". O conjunto de dados contém atribuições de tópico 2.6M. O conjunto de dados foi disponibilizado pelos David. D. Luís.
  </td>
</tr>

<tr ID=student-performance>
  <td valign=top><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/student_performance.txt">student_performance.txt</a></td>
  <td valign=top>
Estes dados provém o desafio de avaliação de desempenho KDD chávena 2010 estudantes (<a href="http://www.kdd.org/kdd-cup-2010-student-performance-evaluation">avaliação de desempenho de estudantes</a>). Dados utilizados são o conjunto de formação do Algebra_2008_2009 (Stamper, j, Niculescu-Mizil, respostas., Ritter, s, Chris, G.J. e Koedinger, k (2010). Álgebra posso 2008 2009. Contestação conjunto de dados a partir do KDD chávena 2010 educacional dados Mining HIP. Encontrá-lo em <a href="http://pslcdatashop.web.cmu.edu/KDDCup/downloads.jsp">downloads.jsp</a> ou <a href="http://www.kdd.org/sites/default/files/kddcup/site/2010/files/algebra_2008_2009.zip">algebra_2008_2009.zip</a>.<p> </p>O conjunto de dados foi transferido e armazenadas no armazenamento de Blobs do Azure (<a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/student_performance.txt">student_performance.txt</a>) e contém os ficheiros de registo a partir de um aluno explicações sistema. As funcionalidades fornecidas incluem ID do problema e respetiva descrição breve, estudantes ID, carimbo e quantas tentativas estudantes efectuadas antes de resolver o problema da forma à direita. O conjunto de dados original tem 8.9M registos, este conjunto de dados que foram recolhidos para baixo para as primeiras linhas N 100. O conjunto de dados tem 23 colunas separado por tabulações de vários tipos: numéricos, categorias e de data/hora.

  </td>
</tr>




</table>


<!-- Module References -->
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
