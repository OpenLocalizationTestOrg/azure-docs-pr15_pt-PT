<properties
    pageTitle="Inicie sessão referência de mosaico estruturador de vista de análise | Microsoft Azure"
    description="Estruturador de vista no registo de análise permite-lhe criar vistas personalizadas na consola do OMS que contêm diferentes visualizações de dados no repositório de OMS. Este artigo fornece uma referência das definições para cada um dos mosaicos disponíveis para utilização no seu vistas personalizadas."
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
    ms.date="09/27/2016"
    ms.author="bwren"/>

# <a name="log-analytics-view-designer-tile-reference"></a>Referência de mosaico estruturador de vista de análise de registo
O estruturador de vista no registo de análise permite-lhe criar vistas personalizadas na consola do OMS que contêm diferentes visualizações de dados no repositório de OMS. Este artigo fornece uma referência das definições para cada um dos mosaicos disponíveis para utilização no seu vistas personalizadas.

Outros artigos disponíveis para o estruturador de vista são:

- [Estruturador de vista](log-analytics-view-designer.md) - descrição geral do estruturador de vista e procedimentos para criar e editar vistas personalizadas.
- [Referência de peça de visualização](log-analytics-view-designer-parts.md) - referência das definições para cada um dos mosaicos disponíveis para utilização no seu vistas personalizadas. 


A tabela seguinte lista os diferentes tipos de mosaicos disponíveis no estruturador de vista.  As secções abaixo descrevem cada tipo de mosaico detalhes e as respectivas propriedades.

| Mosaico | Descrição |
|:--|:--|
| [Número](#number-tile) | Número único a mostrar a contagem de registos de uma consulta. |
| [Dois números](#two-numbers-tile) | Dois números únicos que mostra as contagens de registos duas consultas diferentes. |
| [Anel](#donut-tile) | Gráfico de anel com base numa consulta com um valor no Centro de resumo. |
| [Gráfico de linhas e nota de aviso](#line-chart-amp-callout-tile) | Gráfico de linhas com base numa consulta e uma nota de aviso com um valor de resumo. |
| [Gráfico de linhas](#line-chart-tile) | Gráfico de linhas com base numa consulta. |
| [Duas linhas cronológicas](#two-timelines-tile) | Gráfico de colunas com duas séries de que cada uma com base numa consulta em separado. |



## <a name="number-tile"></a>Mosaico de número

O mosaico de **número** mostra um número único a mostrar a contagem de registos a partir de uma consulta de registo e uma etiqueta.

![Mosaico de número](media/log-analytics-view-designer/tile-number.png)

| Definição | Descrição |
|:--|:--|
| Nome        | Texto a apresentar na parte superior do mosaico. |
| Descrição | Texto a apresentar no nome do mosaico.    |
| **Mosaico** |
| Legenda | Texto a apresentar no valor. |
| Consulta | Para executar a consulta.  Será apresentada a contagem do número de registos devolvidos pela consulta. |
| **Avançadas** |  **> Verificação de fluxo de dados** |
| Ativado | Selecione se a verificação de fluxo de dados deve ser ativada para o mosaico.  Isto fornece uma mensagem alternativa se dados não estão disponíveis para o mosaico.  Isto é normalmente utilizado para fornecer uma mensagem durante o período temporário quando a vista é instalada e dados vem disponíveis. |
| Consulta | Para executar para verificar se os dados estão disponíveis para a vista de consulta.  Se a consulta não devolver resultados, em seguida, é apresentada uma mensagem em vez do valor a partir da consulta principal. |
| Mensagem | Mensagem a apresentar se a consulta de verificação de fluxo de dados devolve sem dados.  Não se fornecer nenhuma mensagem, *Efetuar avaliação* é apresentado. |
| **Intervalo de tempo** |
| Duração | Duração a partir da data atual a utilizar para o intervalo de tempo da consulta.  Por exemplo, se for especificado **7 dias** , em seguida, a consulta é limitada a registos criados a partir de 7 dias para a data atual. |
| Desvio de dados de fim | Desvio opcional dos dados atuais a utilizar para o intervalo de tempo da consulta principal.  Por exemplo, se for utilizado **-1 dia** para o **deslocamento de data de fim** e **7 dias** utilizada para a **duração**, em seguida, a consulta é limitada a registos criados a partir de 8 dias para ontem. |

## <a name="two-numbers-tile"></a>Mosaico de dois números

O **Número de dois** mosaico apresenta a mostrar a contagem de registos de duas consultas de registo diferente e uma etiqueta para cada um de dois números.

![Mosaico de dois números](media/log-analytics-view-designer/tile-two-numbers.png)

| Definição | Descrição |
|:--|:--|
| Nome        | Texto a apresentar na parte superior do mosaico. |
| Descrição | Texto a apresentar no nome do mosaico.    |
| **Primeiro mosaico** |
| Legenda | Texto a apresentar no valor. |
| Consulta | Para executar a consulta.  Será apresentada a contagem do número de registos devolvidos pela consulta. |
| **Mosaico segundo** |
| Legenda | Texto a apresentar no valor. |
| Consulta | Para executar a consulta.  Será apresentada a contagem do número de registos devolvidos pela consulta. |
| **Avançadas** | **> Verificação de fluxo de dados** |
| Ativado | Selecione se a verificação de fluxo de dados deve ser ativada para o mosaico.  Isto fornece uma mensagem alternativa se dados não estão disponíveis para o mosaico.  Isto é normalmente utilizado para fornecer uma mensagem durante o período temporário quando a vista é instalada e dados vem disponíveis. |
| Consulta | Para executar para verificar se os dados estão disponíveis para a vista de consulta.  Se a consulta não devolver resultados, em seguida, é apresentada uma mensagem em vez do valor a partir da consulta principal. |
| Mensagem | Mensagem a apresentar se a consulta de verificação de fluxo de dados devolve sem dados.  Não se fornecer nenhuma mensagem, *Efetuar avaliação* é apresentado. |
| **Intervalo de tempo** |
| Duração | Duração a partir da data atual a utilizar para o intervalo de tempo da consulta.  Por exemplo, se for especificado **7 dias** , em seguida, a consulta é limitada a registos criados a partir de 7 dias para a data atual. |
| Desvio de dados de fim | Desvio opcional dos dados atuais a utilizar para o intervalo de tempo da consulta principal.  Por exemplo, se for utilizado **-1 dia** para o **deslocamento de data de fim** e **7 dias** utilizada para a **duração**, em seguida, a consulta é limitada a registos criados a partir de 8 dias para ontem. |

## <a name="donut-tile"></a>Mosaico anel

O mosaico **anel** apresenta um único número resumido de uma coluna de valor numa consulta de registo.  O anel graficamente apresenta os resultados dos três registos superiores.

![Mosaico anel](media/log-analytics-view-designer/tile-donut.png)

| Definição | Descrição |
|:--|:--|
| Nome        | Texto a apresentar na parte superior do mosaico. |
| Descrição | Texto a apresentar no nome do mosaico.    |
| **Anel** |
| Consulta | Consulta para executar o para o anel.  A primeira propriedade deve ser um valor de texto e a propriedade segunda um valor numérico.  Isto é, normalmente, uma consulta que utiliza a palavra-chave de **medida** para resumir os resultados. |
| **Anel** | **> Centro de** |
| Texto | Texto a apresentar em valor dentro de anel. |
| Operação | A operação a executar na propriedade valor para resumir um único valor.<br><br>-Soma: Adicione os valores de todos os registos com o valor da propriedade.<br>-Percentagem: Percentagem dos valores somadas a partir dos registos com o valor da propriedade comparado com os valores somados de todos os registos. |
| Utilizada numa operação de centro de valores de resultados | Opcionalmente, clique no sinal de adição para adicionar um ou mais valores.  Os resultados da consulta serão limitados aos registos com os valores de propriedade que especificar.  Se forem adicionados sem valores, que todos os registos são incluídas na consulta. |
| **Anel** | **> Opções adicionais** |
| Cores | A cor para apresentar para cada uma das três propriedades superiores.  Se pretender especificar cores alternativas para valores de propriedade específicos, em seguida, utilize avançadas mapeamento de cor. |
| Mapeamento de cores avançadas | Apresenta uma cor para valores de propriedade específicos.  Se o valor que especificar estiver nos três principais, a cor alternativa é apresentada em vez da cor do padrão.  Se a propriedade não estiver a ser os três principais, a cor não é apresentada. |
| **Avançadas** | **> Verificação de fluxo de dados** |
| Ativado | Selecione se a verificação de fluxo de dados deve ser ativada para o mosaico.  Isto fornece uma mensagem alternativa se dados não estão disponíveis para o mosaico.  Isto é normalmente utilizado para fornecer uma mensagem durante o período temporário quando a vista é instalada e dados vem disponíveis. |
| Consulta | Para executar para verificar se os dados estão disponíveis para a vista de consulta.  Se a consulta não devolver resultados, em seguida, é apresentada uma mensagem em vez do valor a partir da consulta principal. |
| Mensagem | Mensagem a apresentar se a consulta de verificação de fluxo de dados devolve sem dados.  Não se fornecer nenhuma mensagem, *Efetuar avaliação* é apresentado. |
| **Intervalo de tempo** |
| Duração | Duração a partir da data atual a utilizar para o intervalo de tempo da consulta.  Por exemplo, se for especificado **7 dias** , em seguida, a consulta é limitada a registos criados a partir de 7 dias para a data atual. |
| Desvio de dados de fim | Desvio opcional dos dados atuais a utilizar para o intervalo de tempo da consulta principal.  Por exemplo, se for utilizado **-1 dia** para o **deslocamento de data de fim** e **7 dias** utilizada para a **duração**, em seguida, a consulta é limitada a registos criados a partir de 8 dias para ontem. |

## <a name="line-chart-tile"></a>É transferido

O mosaico do **gráfico de linhas** apresenta um gráfico de linhas com várias séries de uma consulta de registo ao longo do tempo.  

![Mosaico de gráfico de linhas e nota de aviso](media/log-analytics-view-designer/tile-line-chart.png)

| Definição | Descrição |
|:--|:--|
| Nome        | Texto a apresentar na parte superior do mosaico. |
| Descrição | Texto a apresentar no nome do mosaico.    |
| **Gráfico de linhas** |  
| Consulta | Consulta para executar o para o gráfico de linhas.  A primeira propriedade deve ser um valor de texto e a propriedade segunda um valor numérico.  Isto é, normalmente, uma consulta que utiliza a palavra-chave de **medida** para resumir os resultados.  Se a consulta utiliza a palavra-chave de **intervalo** , em seguida, o eixo x do gráfico irá utilizar este intervalo de tempo.  Se a consulta não incluir a palavra-chave do **intervalo de** intervalos de hora a hora são utilizados para o eixo x. |
| **Gráfico de linhas** | **> Eixo do Y** |
| Utilizar escala logarítmica | Selecione utilizar uma escala logarítmica para o eixo dos YY. |
| Unidades | Especificar as unidades para os valores devolvidos pela consulta.  Estas informações são utilizadas para apresentar as etiquetas no gráfico que indica os tipos de valor e, opcionalmente, para converter os valores.  O **Tipo de unidade** Especifica a categoria da unidade e define os valores de **Tipo de unidade atual** que estão disponíveis.  Se selecionar um valor no **Converter** os valores numéricos são convertidas na partir do tipo de **Unidade atual** com o tipo de **Converter** . |
| Etiqueta personalizada | Texto a apresentar para o eixo do Y junto a etiqueta para o tipo de unidade.  Se não for especificada nenhum etiqueta, apenas o tipo de unidade é apresentado. |
| **Avançadas** | **> Verificação de fluxo de dados** |
| Ativado | Selecione se a verificação de fluxo de dados deve ser ativada para o mosaico.  Isto fornece uma mensagem alternativa se dados não estão disponíveis para o mosaico.  Isto é normalmente utilizado para fornecer uma mensagem durante o período temporário quando a vista é instalada e dados vem disponíveis. |
| Consulta | Para executar para verificar se os dados estão disponíveis para a vista de consulta.  Se a consulta não devolver resultados, em seguida, é apresentada uma mensagem em vez do valor a partir da consulta principal. |
| Mensagem | Mensagem a apresentar se a consulta de verificação de fluxo de dados devolve sem dados.  Não se fornecer nenhuma mensagem, *Efetuar avaliação* é apresentado. |
| **Intervalo de tempo** |
| Duração | Duração a partir da data atual a utilizar para o intervalo de tempo da consulta.  Por exemplo, se for especificado **7 dias** , em seguida, a consulta é limitada a registos criados a partir de 7 dias para a data atual. |
| Desvio de dados de fim | Desvio opcional dos dados atuais a utilizar para o intervalo de tempo da consulta principal.  Por exemplo, se for utilizado **-1 dia** para o **deslocamento de data de fim** e **7 dias** utilizada para a **duração**, em seguida, a consulta é limitada a registos criados a partir de 8 dias para ontem. |


## <a name="line-chart--callout-tile"></a>Mosaico de nota de aviso & gráfico da linha

O mosaico **gráfico de linhas e nota de aviso** apresenta um gráfico de linhas com várias séries de uma consulta de registo ao longo do tempo e de uma nota de aviso com um valor resumido.  

![Mosaico gráfico de linhas e nota de aviso](media/log-analytics-view-designer/tile-line-chart-callout.png)

| Definição | Descrição |
|:--|:--|
| Nome        | Texto a apresentar na parte superior do mosaico. |
| Descrição | Texto a apresentar no nome do mosaico.    |
| **Gráfico de linhas** |  
| Consulta | Consulta para executar o para o gráfico de linhas.  A primeira propriedade deve ser um valor de texto e a propriedade segunda um valor numérico.  Isto é, normalmente, uma consulta que utiliza a palavra-chave de **medida** para resumir os resultados.  Se a consulta utiliza a palavra-chave de **intervalo** , em seguida, o eixo x do gráfico irá utilizar este intervalo de tempo.  Se a consulta não incluir a palavra-chave do **intervalo de** intervalos de hora a hora são utilizados para o eixo x. |
| **Gráfico de linhas** | **> Nota de aviso** |
| Nota de aviso | Texto de título a apresentar acima do valor de nota de aviso. |
| Nome da série | Valor da propriedade da série a utilizar para o valor de nota de aviso.  Não se for fornecido nenhum série, são utilizados todos os registos da consulta. |
| Operação | A operação a executar na propriedade valor para resumir um único valor para a nota de aviso.<br>-Média: Média do valor a partir de todos os registos.<br><br>-Contagem: Contagem de todos os registos devolvidos pela consulta.<br>-Exemplo último: Valor a partir do último intervalo incluído no gráfico.<br>-Máximo: Valor máximo entre os intervalos incluídos no gráfico.<br>-Min: Valor mínimo entre os intervalos incluídos no gráfico.<br>-Soma: Soma do valor a partir de todos os registos. |
| **Gráfico de linhas** | **> Eixo do Y** |
| Utilizar escala logarítmica | Selecione utilizar uma escala logarítmica para o eixo dos YY. |
| Unidades | Especificar as unidades para os valores devolvidos pela consulta.  Estas informações são utilizadas para apresentar as etiquetas no gráfico que indica os tipos de valor e, opcionalmente, para converter os valores.  O **Tipo de unidade** Especifica a categoria da unidade e define os valores de **Tipo de unidade atual** que estão disponíveis.  Se selecionar um valor no **Converter** os valores numéricos são convertidas na partir do tipo de **Unidade atual** com o tipo de **Converter** . |
| Etiqueta personalizada | Texto a apresentar para o eixo do Y junto a etiqueta para o tipo de unidade.  Se não for especificada nenhum etiqueta, apenas o tipo de unidade é apresentado. |
| **Avançadas** | **> Verificação de fluxo de dados** |
| Ativado | Selecione se a verificação de fluxo de dados deve ser ativada para o mosaico.  Isto fornece uma mensagem alternativa se dados não estão disponíveis para o mosaico.  Isto é normalmente utilizado para fornecer uma mensagem durante o período temporário quando a vista é instalada e dados vem disponíveis. |
| Consulta | Para executar para verificar se os dados estão disponíveis para a vista de consulta.  Se a consulta não devolver resultados, em seguida, é apresentada uma mensagem em vez do valor a partir da consulta principal. |
| Mensagem | Mensagem a apresentar se a consulta de verificação de fluxo de dados devolve sem dados.  Não se fornecer nenhuma mensagem, *Efetuar avaliação* é apresentado. |
| **Intervalo de tempo** |
| Duração | Duração a partir da data atual a utilizar para o intervalo de tempo da consulta.  Por exemplo, se for especificado **7 dias** , em seguida, a consulta é limitada a registos criados a partir de 7 dias para a data atual. |
| Desvio de dados de fim | Desvio opcional dos dados atuais a utilizar para o intervalo de tempo da consulta principal.  Por exemplo, se for utilizado **-1 dia** para o **deslocamento de data de fim** e **7 dias** utilizada para a **duração**, em seguida, a consulta é limitada a registos criados a partir de 8 dias para ontem. |

## <a name="two-timelines-tile"></a>Mosaico de duas linhas cronológicas

O mosaico de **duas linhas cronológicas** apresenta os resultados de duas consultas de registo ao longo do tempo, como os gráficos de colunas.  É apresentada uma nota de aviso para cada série.  

![Mosaico de duas linhas cronológicas](media/log-analytics-view-designer/tile-two-timelines.png)

| Definição | Descrição |
|:--|:--|
| Nome        | Texto a apresentar na parte superior do mosaico. |
| Descrição | Texto a apresentar no nome do mosaico.    |
| Primeiro gráfico   
| Legenda | Texto a apresentar em nota de aviso para a primeira série.
| Cor | Cor para utilizar para as colunas na primeira série.
| Consulta de gráfico | Consulta para executar o para a primeira série.  A contagem do número de registos através de cada intervalo de tempo vai ser representada pelas colunas do gráfico.
| Operação | A operação a executar na propriedade valor para resumir um único valor para a nota de aviso.<br><br>-Média: Média do valor a partir de todos os registos.<br>-Contagem: Contagem de todos os registos devolvidos pela consulta.<br>-Exemplo último: Valor a partir do último intervalo incluído no gráfico.<br>-Máximo: Valor máximo entre os intervalos incluídos no gráfico.
| **Segundo gráfico** |
| Legenda | Texto a apresentar em nota de aviso para a segunda série.
| Cor | Cor para utilizar para as colunas da segunda série.
| Consulta de gráfico | Consulta para executar o para a segunda série.  A contagem do número de registos através de cada intervalo de tempo vai ser representada pelas colunas do gráfico.
| Operação | A operação a executar na propriedade valor para resumir um único valor para a nota de aviso.<br><br>-Média: Média do valor a partir de todos os registos.<br>-Contagem: Contagem de todos os registos devolvidos pela consulta.<br>-Exemplo último: Valor a partir do último intervalo incluído no gráfico.<br>-Máximo: Valor máximo entre os intervalos incluídos no gráfico. |
| **Avançadas** | **> Verificação de fluxo de dados** |
| Ativado | Selecione se a verificação de fluxo de dados deve ser ativada para o mosaico.  Isto fornece uma mensagem alternativa se dados não estão disponíveis para o mosaico.  Isto é normalmente utilizado para fornecer uma mensagem durante o período temporário quando a vista é instalada e dados vem disponíveis. |
| Consulta | Para executar para verificar se os dados estão disponíveis para a vista de consulta.  Se a consulta não devolver resultados, em seguida, é apresentada uma mensagem em vez do valor a partir da consulta principal. |
| Mensagem | Mensagem a apresentar se a consulta de verificação de fluxo de dados devolve sem dados.  Não se fornecer nenhuma mensagem, *Efetuar avaliação* é apresentado. |
| **Intervalo de tempo** |
| Duração | Duração a partir da data atual a utilizar para o intervalo de tempo da consulta.  Por exemplo, se for especificado **7 dias** , em seguida, a consulta é limitada a registos criados a partir de 7 dias para a data atual. |
| Desvio de dados de fim | Desvio opcional dos dados atuais a utilizar para o intervalo de tempo da consulta principal.  Por exemplo, se for utilizado **-1 dia** para o **deslocamento de data de fim** e **7 dias** utilizada para a **duração**, em seguida, a consulta é limitada a registos criados a partir de 8 dias para ontem. |

## <a name="next-steps"></a>Próximos passos

- Saiba mais sobre [as pesquisas de registo](log-analytics-log-searches.md) suportar as consultas no mosaicos.
- Adicione [Peças de visualização](log-analytics-view-designer-parts.md) para a vista personalizada.