<properties
    pageTitle="Inicie sessão estruturador de vista de análise | Microsoft Azure"
    description="Estruturador de vista no registo de análise permite-lhe criar vistas personalizadas na consola do OMS que contêm diferentes visualizações de dados no repositório de OMS. Este artigo fornece uma referência das definições para cada uma das partes de visualização disponíveis para utilização no seu vistas personalizadas."
    services="log-analytics"
    documentationCenter=""
    authors="bwren"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="log-analytics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/20/2016"
    ms.author="bwren"/>

# <a name="log-analytics-view-designer-visualization-part-reference"></a>Referência de peça registo visualização do estruturador de vista de análise
O estruturador de vista no registo de análise permite-lhe criar vistas personalizadas na consola do OMS que contêm diferentes visualizações de dados a partir do repositório OMS. Este artigo fornece uma referência das definições para cada uma das partes de visualização disponíveis para utilização no seu vistas personalizadas.

Outros artigos disponíveis para o estruturador de vista são:

- [Estruturador de vista](log-analytics-view-designer.md) - descrição geral do estruturador de vista e procedimentos para criar e editar vistas personalizadas.
- [Referência de mosaico](log-analytics-view-designer-tiles.md) - referência das definições para cada um dos mosaicos disponíveis para utilização no seu vistas personalizadas. 

A tabela seguinte descreve os diferentes tipos de mosaicos disponíveis no estruturador de vista.  As secções abaixo descrevem cada tipo de mosaico detalhes e as respectivas propriedades.

| Tipo de vista | Descrição |
|:--|:--|
| [Lista de consultas](#list-of-queries-part) | Apresenta uma lista de consultas de pesquisa de registo.  O utilizador pode clicar em cada consulta para apresentar os seus resultados.  |
| [Número & lista](#number-amp-list-part) | Cabeçalho tem uma única a mostrar a contagem do número de registos a partir de uma consulta de pesquisa de registo.  Lista apresenta os resultados de dez principais de uma consulta com um gráfico que indica o valor relativo de uma coluna numérica ou a sua alteração ao longo do tempo. |
| [Dois números e lista](#two-numbers-amp-list-part) | Cabeçalho tem dois números a mostrar a contagem de registos de consultas de pesquisa do registo em separado.  Lista apresenta os resultados de dez principais de uma consulta com um gráfico que indica o valor relativo de uma coluna numérica ou a sua alteração ao longo do tempo. |
| [Anel & lista](#donut-amp-list-part) | Cabeçalho apresenta um único número resumido de uma coluna de valor numa consulta de registo.  O anel graficamente apresenta os resultados dos três registos superiores. |
| [Duas linhas cronológicas e lista](#two-timelines-amp-list-part) | Cabeçalho apresenta os resultados de duas consultas de registo ao longo do tempo, como os gráficos de colunas com uma nota de aviso com um número único resumido de uma coluna de valor numa consulta de registo.  Lista apresenta os resultados de dez principais de uma consulta com um gráfico que indica o valor relativo de uma coluna numérica ou a sua alteração ao longo do tempo. |   
| [Informações](#information-part) | Cabeçalho apresenta em texto estático e uma ligação de opcional.  Lista apresenta um ou mais itens com em texto estático e título. |
| [Gráfico de linhas, nota de aviso e lista](#line-chart-callout-amp-list-part) | Cabeçalho apresenta um gráfico de linhas com várias séries de uma consulta de registo ao longo do tempo e de uma nota de aviso com um valor resumido.  Lista apresenta os resultados de dez principais de uma consulta com um gráfico que indica o valor relativo de uma coluna numérica ou a sua alteração ao longo do tempo. |
| [Gráfico de linhas e de lista](#line-chart-amp-list-part) | Cabeçalho apresenta um gráfico de linhas com várias séries de uma consulta de registo ao longo do tempo.  Lista apresenta os resultados de dez principais de uma consulta com um gráfico que indica o valor relativo de uma coluna numérica ou a sua alteração ao longo do tempo. |
| [Pilha de peça de gráficos de linhas](#stack-of-line-charts-part) | Apresenta três linha separada gráficos com várias séries de uma consulta de registo ao longo do tempo. |


## <a name="list-of-queries-part"></a>Lista de peça de consultas

Apresenta uma lista de consultas de pesquisa de registo.  O utilizador pode clicar em cada consulta para apresentar os seus resultados.  A vista irá incluir numa única consulta por predefinição e pode clicar em **+ consulta** para adicionar consultas adicionais.

![Lista de vista de consultas](media/log-analytics-view-designer/view-list-queries.png)

| Definição | Descrição |
|:--|:--|
| **Geral** |
| Título | Texto a apresentar na parte superior da vista. |
| Novo grupo | Selecione para criar um novo grupo na vista começando na vista atual. |
| Filtros previamente selecionados | Lista de propriedades para incluir no painel filtros para a esquerda quando o utilizador seleciona uma consulta delimitado por vírgulas. |
| Modo de composição | Vista inicial apresentada quando a consulta estiver selecionada.  O utilizador pode selecionar qualquer vistas disponíveis depois de abrir a consulta. |
| **Consultas** |
| Consulta de pesquisa | Para executar a consulta. |
| Nome amigável | Nome descritivo da consulta para apresentar ao utilizador. |


## <a name="number--list-part"></a>Peça & lista de número

Cabeçalho tem uma única a mostrar a contagem do número de registos a partir de uma consulta de pesquisa de registo.  Lista apresenta os resultados de dez principais de uma consulta com um gráfico que indica o valor relativo de uma coluna numérica ou a sua alteração ao longo do tempo.


![Lista de vista de consultas](media/log-analytics-view-designer/view-number-list.png)

| Definição | Descrição |
|:--|:--|
| **Geral** |
| Título do grupo | Texto a apresentar na parte superior da vista. |
| Novo grupo | Selecione para criar um novo grupo na vista começando na vista atual. |
| Ícone | Ficheiro de imagem para apresentar junto ao resultado no cabeçalho.
| Utilizar ícone | Selecione até para ter a ícone apresentação. |
| **Título** |
| Legenda | Texto a apresentar na parte superior do cabeçalho. |
| Consulta | Consulta para executar para o cabeçalho.  Será apresentada a contagem do número de registos devolvidos pela consulta. |
| **Lista** |
| Consulta | Consulta para executar para a lista.  As duas primeiras propriedades para os dez primeiros registos nos resultados da serão apresentadas.  A primeira propriedade deve ser um valor de texto e a propriedade segunda um valor numérico.  Barras são criadas automaticamente com base no valor relativo da coluna numérico.<br><br>Utilize o comando de ordenação na consulta para ordenar os registos na lista.  O utilizador pode clicar em ver todas as para executar a consulta e devolver todos os registos. |
| Ocultar graph | Selecione esta opção para desativar o gráfico para a direita da coluna numérica. |
| Ativar os gráficos sparkline | Selecione esta opção para apresentar o gráfico sparkline em vez da barra horizontal.  Consulte o artigo [Definições comuns](#sparklines) para obter detalhes. |
| Cor | Cor de barras ou gráficos sparkline. |
| Nome e o separador de valor | Delimitador um caráter se pretender analisar a propriedade de texto para múltiplos valores.  Consulte o artigo [Definições comuns](#name-value-separator) para obter detalhes. |
| Consulta de navegação | Consulta seja executada quando o utilizador seleciona um item na lista.  Consulte o artigo [Definições comuns](#navigation-query) para obter detalhes. |
| **Lista** | **> Títulos de coluna** |
| Nome | Texto a apresentar na parte superior da primeira coluna da lista. |
| Valor | Texto a apresentar na parte superior da segunda coluna da lista. |
| **Lista** | **> Limiares de** |
| Ativar limiares | Selecione esta opção para ativar limiares.  Consulte o artigo [Definições comuns](#thresholds) para obter detalhes. |


## <a name="two-numbers--list-part"></a>Dois números e peça de lista

Cabeçalho tem dois números a mostrar a contagem de registos de consultas de pesquisa do registo em separado.  Lista apresenta os resultados de dez principais de uma consulta com um gráfico que indica o valor relativo de uma coluna numérica ou a sua alteração ao longo do tempo.

![Dois números e vista de lista](media/log-analytics-view-designer/view-two-numbers-list.png)

| Definição | Descrição |
|:--|:--|
| **Geral** |
| Título do grupo | Texto a apresentar na parte superior da vista. |
| Novo grupo | Selecione para criar um novo grupo na vista começando na vista atual. |
| Ícone | Ficheiro de imagem para apresentar junto ao resultado no cabeçalho.
| Utilizar ícone | Selecione até para ter a ícone apresentação. |
| **Título** |
| Legenda | Texto a apresentar na parte superior do cabeçalho. |
| Consulta | Consulta para executar para o cabeçalho.  Será apresentada a contagem do número de registos devolvidos pela consulta. |
| **Lista** |
| Consulta | Consulta para executar para a lista.  As duas primeiras propriedades para os dez primeiros registos nos resultados da serão apresentadas.  A primeira propriedade deve ser um valor de texto e a propriedade segunda um valor numérico.  Barras são criadas automaticamente com base no valor relativo da coluna numérico.<br><br>Utilize o comando de ordenação na consulta para ordenar os registos na lista.  O utilizador pode clicar em ver todas as para executar a consulta e devolver todos os registos. |
| Ocultar graph | Selecione esta opção para desativar o gráfico para a direita da coluna numérica. |
| Ativar os gráficos sparkline | Selecione esta opção para apresentar o gráfico sparkline em vez da barra horizontal.  Consulte o artigo [Definições comuns](#sparklines) para obter detalhes. |
| Cor | Cor de barras ou gráficos sparkline. |
| Operação | Operação para executar para o gráfico sparkline.  Consulte o artigo [Definições comuns](#sparklines) para obter detalhes. |
| Nome e o separador de valor | Delimitador um caráter se pretender analisar a propriedade de texto para múltiplos valores.  Consulte o artigo [Definições comuns](#name-value-separator) para obter detalhes. |
| Consulta de navegação | Consulta seja executada quando o utilizador seleciona um item na lista.  Consulte o artigo [Definições comuns](#navigation-query) para obter detalhes. |
| **Lista** | **> Títulos de coluna** |
| Nome | Texto a apresentar na parte superior da primeira coluna da lista. |
| Valor | Texto a apresentar na parte superior da segunda coluna da lista. |
| **Lista** | **> Limiares de** |
| Ativar limiares | Selecione esta opção para ativar limiares.  Consulte o artigo [Definições comuns](#thresholds) para obter detalhes. |

## <a name="donut--list-part"></a>Peça anel & lista

Cabeçalho apresenta um único número resumido de uma coluna de valor numa consulta de registo.  O anel graficamente apresenta os resultados dos três registos superiores.

![Vista de lista & anel](media/log-analytics-view-designer/view-donut-list.png)

| Definição | Descrição |
|:--|:--|
| **Geral** |
| Título do grupo | Texto a apresentar na parte superior do mosaico. |
| Novo grupo | Selecione para criar um novo grupo na vista começando na vista atual. |
| Ícone | Ficheiro de imagem para apresentar junto ao resultado no cabeçalho. |
| Utilizar ícone | Selecione até para ter a ícone apresentação. |
| **Cabeçalho** |
| Título | Texto a apresentar na parte superior do cabeçalho.
| Esquerda para a direita | Texto a apresentar sob o título na parte superior do cabeçalho.
| **Anel** |
| Consulta | Consulta para executar o para o anel.  A primeira propriedade deve ser um valor de texto e a propriedade segunda um valor numérico. |
| **Anel** |  **> Centro de** |
| Texto | Texto a apresentar em valor dentro de anel. |
| Operação | A operação a executar na propriedade valor para resumir um único valor.<br><br>-Soma: Adicione os valores de todos os registos.<br>-Percentagem: Percentagem os registos devolvidos pelos valores nos **valores de resultados utilizadas numa operação de centro** para o total de registos numa consulta. |
| Utilizada numa operação de centro de valores de resultados | Opcionalmente, clique no sinal de adição para adicionar um ou mais valores.  Os resultados da consulta serão limitados aos registos com os valores de propriedade que especificar.  Se forem adicionados sem valores, todos os registos são incluídos na consulta. |
| **Opções adicionais** | **> Cores** |
| Cor 1<br>Cor de 2<br>Cor 3 | Selecione a cor para a cada um dos valores apresentados no anel. |
| **Opções adicionais** | **> Mapeamento de cor avançadas** |
| Valor do campo | Escreva o nome de um campo para apresentá-lo como uma cor diferente, caso se encontre incluído no anel. |
| Cor | Selecione a cor para o campo exclusivo. |
| **Lista** |
| Consulta | Consulta para executar para a lista.  Será apresentada a contagem do número de registos devolvidos pela consulta. |
| Ocultar graph | Selecione esta opção para desativar o gráfico para a direita da coluna numérica. |
| Ativar os gráficos sparkline | Selecione esta opção para apresentar o gráfico sparkline em vez da barra horizontal.  Consulte o artigo [Definições comuns](#sparklines) para obter detalhes. |
| Cor | Cor de barras ou gráficos sparkline. |
| Operação | Operação para executar para o gráfico sparkline.  Consulte o artigo [Definições comuns](#sparklines) para obter detalhes. |
| Nome e o separador de valor | Delimitador um caráter se pretender analisar a propriedade de texto para múltiplos valores.  Consulte o artigo [Definições comuns](#name-value-separator) para obter detalhes. |
| Consulta de navegação | Consulta seja executada quando o utilizador seleciona um item na lista.  Consulte o artigo [Definições comuns](#navigation-query) para obter detalhes. |
| **Lista** | **> Títulos de coluna** |
| Nome | Texto a apresentar na parte superior da primeira coluna da lista. |
| Valor | Texto a apresentar na parte superior da segunda coluna da lista. |
| **Lista** | **> Limiares de** |
| Ativar limiares | Selecione esta opção para ativar limiares.  Consulte o artigo [Definições comuns](#thresholds) para obter detalhes. |

## <a name="two-timelines--list-part"></a>Duas peça & lista de linhas cronológicas

Cabeçalho apresenta os resultados de duas consultas de registo ao longo do tempo, como os gráficos de colunas com uma nota de aviso com um número único resumido de uma coluna de valor numa consulta de registo.  Lista apresenta os resultados de dez principais de uma consulta com um gráfico que indica o valor relativo de uma coluna numérica ou a sua alteração ao longo do tempo.

![Ver duas linhas cronológicas e lista](media/log-analytics-view-designer/view-two-timelines-list.png)

| Definição | Descrição |
|:--|:--|
| **Geral** |
| Título do grupo | Texto a apresentar na parte superior do mosaico. |
| Novo grupo | Selecione para criar um novo grupo na vista começando na vista atual. |
| Ícone | Ficheiro de imagem para apresentar junto ao resultado no cabeçalho. |
| Utilizar ícone | Selecione até para ter a ícone apresentação. |
| **Primeiro gráfico<br>segundo gráfico** |
| Legenda | Texto a apresentar em nota de aviso para a primeira série. |
| Cor | Cor para utilizar para as colunas na série. |
| Consulta | Consulta para executar o para a primeira série.  A contagem do número de registos através de cada intervalo de tempo vai ser representada pelas colunas do gráfico. |
| Operação | A operação a executar na propriedade valor para resumir um único valor para a nota de aviso.<br><br>-Soma: Soma do valor a partir de todos os registos.<br>-Média: Média do valor a partir de todos os registos.<br>-Exemplo último: Valor a partir do último intervalo incluído no gráfico.<br>-Exemplo primeiro: Valor a partir do primeiro intervalo incluído no gráfico.<br>-Contagem: Contagem de todos os registos devolvidos pela consulta.|
| **Lista** |
| Consulta | Consulta para executar para a lista.  Será apresentada a contagem do número de registos devolvidos pela consulta. |
| Ocultar graph | Selecione esta opção para desativar o gráfico para a direita da coluna numérica. |
| Ativar os gráficos sparkline | Selecione esta opção para apresentar o gráfico sparkline em vez da barra horizontal.  Consulte o artigo [Definições comuns](#sparklines) para obter detalhes. |
| Cor | Cor de barras ou gráficos sparkline. |
| Operação | Operação para executar para o gráfico sparkline.  Consulte o artigo [Definições comuns](#sparklines) para obter detalhes. |
| Consulta de navegação | Consulta seja executada quando o utilizador seleciona um item na lista.  Consulte o artigo [Definições comuns](#navigation-query) para obter detalhes. |
| **Lista** | **> Títulos de coluna** |
| Nome | Texto a apresentar na parte superior da primeira coluna da lista. |
| Valor | Texto a apresentar na parte superior da segunda coluna da lista. |
| **Lista** | **> Limiares de** |
| Ativar limiares | Selecione esta opção para ativar limiares.  Consulte o artigo [Definições comuns](#thresholds) para obter detalhes. |

## <a name="information-part"></a>Peça de informações

Cabeçalho apresenta em texto estático e uma ligação de opcional.  Lista apresenta um ou mais itens com em texto estático e título.

![Vista de informações](media/log-analytics-view-designer/view-information.png)

| Definição | Descrição |
|:--|:--|
| **Geral** |
| Título do grupo | Texto a apresentar na parte superior do mosaico. |
| Novo grupo | Selecione para criar um novo grupo na vista começando na vista atual. |
| Cor | Cor de fundo para o cabeçalho. |
| **Cabeçalho** |
| Imagem | Ficheiro de imagem a apresentar no cabeçalho. |
| Etiqueta | Texto a apresentar no cabeçalho. |
| **Cabeçalho** | **> Ligação** |
| Etiqueta | Texto da ligação. |
| URL | URL para a ligação. |
| **Itens de informações** |
| Título | Texto a apresentar para o título de cada item. |
| Conteúdo | Texto a apresentar para cada item. |


## <a name="line-chart-callout--list-part"></a>Gráfico de linhas, nota de aviso e peça de lista

Cabeçalho apresenta um gráfico de linhas com várias séries de uma consulta de registo ao longo do tempo e de uma nota de aviso com um valor resumido.  Lista apresenta os resultados de dez principais de uma consulta com um gráfico que indica o valor relativo de uma coluna numérica ou a sua alteração ao longo do tempo.

![Gráfico de linhas, nota de aviso e vista de lista](media/log-analytics-view-designer/view-line-chart-callout-list.png)

| Definição | Descrição |
|:--|:--|
| **Geral** |
| Título do grupo | Texto a apresentar na parte superior do mosaico. |
| Novo grupo | Selecione para criar um novo grupo na vista começando na vista atual. |
| Ícone | Ficheiro de imagem para apresentar junto ao resultado no cabeçalho. |
| Utilizar ícone | Selecione até para ter a ícone apresentação. |
| **Cabeçalho** |
| Título | Texto a apresentar na parte superior do cabeçalho. |
| Esquerda para a direita | Texto a apresentar sob o título na parte superior do cabeçalho. |
| **Gráfico de linhas** |
| Consulta | Consulta para executar o para o gráfico de linhas.  A primeira propriedade deve ser um valor de texto e a propriedade segunda um valor numérico.  Isto é, normalmente, uma consulta que utiliza a palavra-chave de **medida** para resumir os resultados.  Se a consulta utiliza a palavra-chave de **intervalo** , em seguida, o eixo x do gráfico irá utilizar este intervalo de tempo.  Se a consulta não incluir a palavra-chave do **intervalo de** intervalos de hora a hora são utilizados para o eixo x. |
| **Gráfico de linhas** | **> Nota de aviso** |
| Título de nota de aviso | Texto a apresentar acima do valor de nota de aviso. |
| Nome da série | Valor da propriedade da série a utilizar para o valor de nota de aviso.  Não se for fornecido nenhum série, são utilizados todos os registos da consulta. |
| Operação | A operação a executar na propriedade valor para resumir um único valor para a nota de aviso.<br><br>-Média: Média do valor a partir de todos os registos.<br>-Contagem de contagem de todos os registos devolvidos pela consulta.<br>-Exemplo último: Valor a partir do último intervalo incluído no gráfico.<br>-Máximo: Valor máximo entre os intervalos incluídos no gráfico.<br>-Min: Valor mínimo entre os intervalos incluídos no gráfico.<br>-Soma: Soma do valor a partir de todos os registos. |
| **Gráfico de linhas** | **> Eixo do Y** |
| Utilizar escala logarítmica | Selecione utilizar uma escala logarítmica para o eixo dos YY. |
| Unidades | Especificar as unidades para os valores devolvidos pela consulta.  Estas informações são utilizadas para apresentar as etiquetas no gráfico que indica os tipos de valor e, opcionalmente, para converter os valores.  O tipo de unidade Especifica a categoria da unidade e define os valores de tipo de unidade atual que estão disponíveis.  Se selecionar um valor numa converta, em seguida, os valores numéricos são convertidas na partir do tipo de unidade atual converter em escreva. |
| Etiqueta personalizada | Texto a apresentar para o eixo do Y junto a etiqueta para o tipo de unidade.  Se não for especificada nenhum etiqueta, apenas o tipo de unidade é apresentado. |
| **Lista** |
| Consulta | Consulta para executar para a lista.  Será apresentada a contagem do número de registos devolvidos pela consulta. |
| Ocultar graph | Selecione esta opção para desativar o gráfico para a direita da coluna numérica. |
| Ativar os gráficos sparkline | Selecione esta opção para apresentar o gráfico sparkline em vez da barra horizontal.  Consulte o artigo [Definições comuns](#sparklines) para obter detalhes. |
| Cor | Cor de barras ou gráficos sparkline. |
| Operação | Operação para executar para o gráfico sparkline.  Consulte o artigo [Definições comuns](#sparklines) para obter detalhes. |
| Nome e o separador de valor | Delimitador um caráter se pretender analisar a propriedade de texto para múltiplos valores.  Consulte o artigo [Definições comuns](#name-value-separator) para obter detalhes. |
| Consulta de navegação | Consulta seja executada quando o utilizador seleciona um item na lista.  Consulte o artigo [Definições comuns](#navigation-query) para obter detalhes. |
| **Lista** | **> Títulos de coluna** |
| Nome | Texto a apresentar na parte superior da primeira coluna da lista. |
| Valor | Texto a apresentar na parte superior da segunda coluna da lista. |
| **Lista** | **> Limiares de** |
| Ativar limiares | Selecione esta opção para ativar limiares.  Consulte o artigo [Definições comuns](#thresholds) para obter detalhes. |

## <a name="line-chart--list-part"></a>Peça de gráfico e lista de linha

Cabeçalho apresenta um gráfico de linhas com várias séries de uma consulta de registo ao longo do tempo.  Lista apresenta os resultados de dez principais de uma consulta com um gráfico que indica o valor relativo de uma coluna numérica ou a sua alteração ao longo do tempo.

![Vista de gráfico e lista de linha](media/log-analytics-view-designer/view-line-chart-callout-list.png)

| Definição | Descrição |
|:--|:--|
| **Geral** |
| Título do grupo | Texto a apresentar na parte superior do mosaico. |
| Novo grupo | Selecione para criar um novo grupo na vista começando na vista atual. |
| Ícone | Ficheiro de imagem para apresentar junto ao resultado no cabeçalho. |
| Utilizar ícone | Selecione até para ter a ícone apresentação. |
| **Cabeçalho** |
| Título | Texto a apresentar na parte superior do cabeçalho. |
| Esquerda para a direita | Texto a apresentar sob o título na parte superior do cabeçalho. |
| **Gráfico de linhas** |
| Consulta | Consulta para executar o para o gráfico de linhas.  A primeira propriedade deve ser um valor de texto e a propriedade segunda um valor numérico.  Isto é, normalmente, uma consulta que utiliza a palavra-chave de **medida** para resumir os resultados.  Se a consulta utiliza a palavra-chave de **intervalo** , em seguida, o eixo x do gráfico irá utilizar este intervalo de tempo.  Se a consulta não incluir a palavra-chave do **intervalo de** intervalos de hora a hora são utilizados para o eixo x. |
| **Gráfico de linhas** | **> Eixo do Y** |
| Utilizar escala logarítmica | Selecione utilizar uma escala logarítmica para o eixo dos YY. |
| Unidades | Especificar as unidades para os valores devolvidos pela consulta.  Estas informações são utilizadas para apresentar as etiquetas no gráfico que indica os tipos de valor e, opcionalmente, para converter os valores.  O tipo de unidade Especifica a categoria da unidade e define os valores de tipo de unidade atual que estão disponíveis.  Se selecionar um valor numa converta, em seguida, os valores numéricos são convertidas na partir do tipo de unidade atual converter em escreva. |
| Etiqueta personalizada | Texto a apresentar para o eixo do Y junto a etiqueta para o tipo de unidade.  Se não for especificada nenhum etiqueta, apenas o tipo de unidade é apresentado. |
| **Lista** |
| Consulta | Consulta para executar para a lista.  Será apresentada a contagem do número de registos devolvidos pela consulta. |
| Ocultar graph | Selecione esta opção para desativar o gráfico para a direita da coluna numérica. |
| Ativar os gráficos sparkline | Selecione esta opção para apresentar o gráfico sparkline em vez da barra horizontal.  Consulte o artigo [Definições comuns](#sparklines) para obter detalhes. |
| Cor | Cor de barras ou gráficos sparkline. |
| Operação | Operação para executar para o gráfico sparkline.  Consulte o artigo [Definições comuns](#sparklines) para obter detalhes. |
| Nome e o separador de valor | Delimitador um caráter se pretender analisar a propriedade de texto para múltiplos valores.  Consulte o artigo [Definições comuns](#name-value-separator) para obter detalhes. |
| Consulta de navegação | Consulta seja executada quando o utilizador seleciona um item na lista.  Consulte o artigo [Definições comuns](#navigation-query) para obter detalhes. |
| **Lista** | **> Títulos de coluna** |
| Nome | Texto a apresentar na parte superior da primeira coluna da lista. |
| Valor | Texto a apresentar na parte superior da segunda coluna da lista. |
| **Lista** | **> Limiares de** |
| Ativar limiares | Selecione esta opção para ativar limiares.  Consulte o artigo [Definições comuns](#thresholds) para obter detalhes. |

## <a name="stack-of-line-charts-part"></a>Pilha de peça de gráficos de linhas

Apresenta três linha separada gráficos com várias séries de uma consulta de registo ao longo do tempo.

![Pilha de gráficos de linhas](media/log-analytics-view-designer/view-stack-line-charts.png)

| Definição | Descrição |
|:--|:--|
| **Geral** |
| Título do grupo | Texto a apresentar na parte superior do mosaico. |
| Novo grupo | Selecione para criar um novo grupo na vista começando na vista atual. |
| Ícone | Ficheiro de imagem para apresentar junto ao resultado no cabeçalho. |
| **Gráfico 1<br>gráfico 2<br>gráfico 3** | **> Cabeçalho** |
| Título | Texto a apresentar na parte superior do gráfico. |
| Esquerda para a direita | Texto a apresentar sob o título na parte superior do gráfico. |
| **Gráfico 1<br>gráfico 2<br>gráfico 3** | **Gráfico de linhas** |
| Consulta | Consulta para executar o para o gráfico de linhas.  A primeira propriedade deve ser um valor de texto e a propriedade segunda um valor numérico.  Isto é, normalmente, uma consulta que utiliza a palavra-chave de **medida** para resumir os resultados.  Se a consulta utiliza a palavra-chave de **intervalo** , em seguida, o eixo x do gráfico irá utilizar este intervalo de tempo.  Se a consulta não incluir a palavra-chave do **intervalo de** intervalos de hora a hora são utilizados para o eixo x. |
| **Gráfico** | **> Eixo do Y** |
| Utilizar escala logarítmica | Selecione utilizar uma escala logarítmica para o eixo dos YY. |
| Unidades | Especificar as unidades para os valores devolvidos pela consulta.  Estas informações são utilizadas para apresentar as etiquetas no gráfico que indica os tipos de valor e, opcionalmente, para converter os valores.  O tipo de unidade Especifica a categoria da unidade e define os valores de tipo de unidade atual que estão disponíveis.  Se selecionar um valor numa converta, em seguida, os valores numéricos são convertidas na partir do tipo de unidade atual converter em escreva. |
| Etiqueta personalizada | Texto a apresentar para o eixo do Y junto a etiqueta para o tipo de unidade.  Se não for especificada nenhum etiqueta, apenas o tipo de unidade é apresentado. |

## <a name="common-settings"></a>Definições comuns
As secções seguintes descrevem definições comuns a várias partes de visualização.

### <a name="name-value-separator">Nome e o separador de valor</a>
Delimitador um caráter se pretender analisar a propriedade texto de uma consulta de lista para múltiplos valores.  Se especificar um delimitador, pode fornecer nomes para cada campo separados por do mesmo delimitador na caixa nome.

Por exemplo, considere uma propriedade denominada *localização* que incluído valores como *Redmond edifício 41* e *Belavista Building12*.  Pode especificar – para o nome e separação de valor e *City Building* do nome.  Isto de analisar cada valor para duas propriedades denominadas *Cidade* e *modular*. 

### <a name="navigation-query">Consulta de navegação</a>
Consulta seja executada quando o utilizador seleciona um item na lista.  Utilize *{item selecionado}* para incluir a sintaxe para o item que o utilizador selecionado.

Por exemplo, se a consulta tiver uma coluna denominada *computador* e a consulta de navegação é *{item selecionado}*, uma consulta como *computador = "Meu computador"* seria executado quando o utilizador selecionado um computador.  Se a consulta de navegação for *tipo = evento {item selecionado}* , em seguida, a consulta *tipo = evento computador = "Meu computador"* seria ser executado.

### <a name="sparklines">Gráficos Sparkline</a>
Um gráfico sparkline é um gráfico de linhas pequenas que ilustra o valor de uma entrada de lista ao longo do tempo.  Peças de visualização com uma lista, pode selecionar se pretende apresentar uma barra horizontal a indicar o valor relativo de uma coluna numérica ou de um gráfico sparkline que indica o valor ao longo do tempo.

A tabela seguinte descreve as definições de gráficos sparkline.

| Definição | Descrição |
|:--|:--|
| Ativar os gráficos sparkline | Selecione esta opção para apresentar o gráfico sparkline em vez da barra horizontal. |
| Operação | Se estiverem ativados gráficos sparkline, esta é a operação para desempenhar em cada propriedade na lista para calcular os valores para o gráfico sparkline.<br><br>-Exemplo último: Último valor da série ao longo do intervalo de tempo.<br>-Máximo: Valor máximo da série ao longo do intervalo de tempo.<br>-Min: Valor mínimo da série ao longo do intervalo de tempo.<br>-Soma: Soma de valores para as séries de ao longo do intervalo de tempo.<br>-Resumo: Utiliza o mesmo comando de medida, como a consulta no cabeçalho. |

### <a name="thresholds">Limiares</a>
Limiares permitem-lhe apresentar um ícone colorido ao lado de cada item numa lista que lhe dá um indicador visual rápido de itens que exceder um determinado valor ou de se situar dentro de um determinado intervalo.  Por exemplo, pode apresentar um ícone verde para itens com um valor aceitável, amarelo, se o valor for dentro de um intervalo que indica um aviso e vermelho se exceder um valor de erro.

Quando ativa limiares para uma peça, tem de especificar um ou mais limiares.  Se o valor de um item for maior do que um valor limite e menor que o valor de limiar seguinte, em seguida, essa cor é utilizada.  Se o item for maior que o valor limite, em seguida, mais alto, em seguida, essa cor é definida.   

Cada conjunto limiar tem um limite com um valor de **predefinido**.  Esta é a cor definir se sem outros valores são excedidos.  Pode adicionar ou remover limiares, clicando no **+** ou botão **x** .

A tabela seguinte descreve as definições de limiares.

| Definição | Descrição |
|:--|:--|
| Ativar limiares | Selecione esta opção para apresentar um ícone de cor para a esquerda de cada valor que indica o seu estado de funcionamento em relação limiares especificados. |
| Nome | Nome para identificar o valor limite. |
| Limiar | Valor para o limiar de.  A cor do Estado de funcionamento para cada item de lista está definida para a cor do valor mais alto-limite excedido por valor do item.  Existe um limiar predefinido que é a cor se sem valores do limiar são excedidos. |
| Cor | Cor para o valor limite. |


## <a name="next-steps"></a>Próximos passos

- Saiba mais sobre [as pesquisas de registo](log-analytics-log-searches.md) suportar as consultas em partes de visualização.