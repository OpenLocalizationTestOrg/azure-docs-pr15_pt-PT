<properties
    pageTitle="Introdução a funções de janela de análise da cadeia | Microsoft Azure"
    description="Saiba mais sobre as funções de janela três em sequência Analytics (tumbling, salto, deslizante)."
    keywords="tumbling janela, deslizar janela, salto de janela"
    documentationCenter=""
    services="stream-analytics"
    authors="jeffstokes72"
    manager="jhubbard"
    editor="cgronlun"
/>

<tags
    ms.service="stream-analytics"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="data-services"
    ms.date="09/26/2016"
    ms.author="jeffstok"
/>


# <a name="introduction-to-stream-analytics-window-functions"></a>Introdução a funções de janela de análise da cadeia

Em muitos tempo real a transmissão de cenários, é necessário efetuar operações apenas nos dados contidos no windows temporais. Suporte nativo para as funções de sistema baseado em janelas é uma funcionalidade de chave do Azure sequência de análise que move agulha no produtividade do Programador de criação da cadeia complexo processar tarefas. Análise da cadeia permite que os programadores utilizar o windows [**Tumbling**](https://msdn.microsoft.com/library/dn835055.aspx), [**Hopping**](https://msdn.microsoft.com/library/dn835041.aspx) e [**correr**](https://msdn.microsoft.com/library/dn835051.aspx) para efetuar operações temporais nos dados em tempo real. É de notar que todas as operações da [janela](https://msdn.microsoft.com/library/dn835019.aspx) de saída de resultados no **fim** da janela. O resultado da janela do será o único evento com base na função de agregação utilizada. O evento terão o carimbo de hora de fim da janela e todas as funções de janela são definidas com um comprimento fixo. Por fim é importante ter em atenção que todas as funções de janela devem ser utilizadas numa cláusula [**GROUP BY**](https://msdn.microsoft.com/library/dn835023.aspx) .

![Conceitos de funções de análise janela da cadeia](media/stream-analytics-window-functions/stream-analytics-window-functions-conceptual.png)

## <a name="tumbling-window"></a>Janela de tumbling

Tumbling janela funções são utilizadas para segmentar uma sequência de dados em segmentos de tempo distintos e executar uma função contra-las, tal como no exemplo abaixo. Os elementos de diferenciação principais de uma janela Tumbling são que eles repetir, não se sobrepõem e um evento não é possível pertencer a mais do que uma janela de tumbling.

![Funções de análise janela da cadeia tumbling aqui](media/stream-analytics-window-functions/stream-analytics-window-functions-tumbling-intro.png)

## <a name="hopping-window"></a>Janela salto

Salto janela funções salto frente no tempo por um período fixo. Pode ser mais fácil considerá-los como Tumbling windows que podem sobrepor, para que eventos podem pertencer a mais do que um conjunto de resultados de janela Hopping. Para criar uma janela de Hopping o mesmo que um Tumbling janela um seria especifique simplesmente o tamanho do salto para ser o mesmo que o tamanho da janela. 

![Funções de análise janela da cadeia salto de introdução](media/stream-analytics-window-functions/stream-analytics-window-functions-hopping-intro.png)

## <a name="sliding-window"></a>Janela deslizante

Funções de janela deslizante, ao contrário windows Tumbling ou Hopping, produzem uma saída **apenas** quando ocorre um evento. Todas as janelas terão pelo menos um evento e a janela continuamente avança por um € (épsilon). Como o Windows salto, eventos podem pertencer a mais do que uma janela deslizante.

![Funções de análise janela da cadeia deslizante aqui](media/stream-analytics-window-functions/stream-analytics-window-functions-sliding-intro.png)

## <a name="getting-help-with-window-functions"></a>Obter ajuda com funções de janela

Para obter mais assistência, experimente nosso [Fórum Azure da cadeia Analytics](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Próximos passos

- [Introdução ao Azure sequência Analytics](stream-analytics-introduction.md)
- [Começar a utilizar o Azure da cadeia Analytics](stream-analytics-get-started.md)
- [Escala Azure da cadeia Analytics tarefas](stream-analytics-scale-jobs.md)
- [Azure sequência Analytics referência da linguagem de consulta](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Referência da sequência Azure Analytics gestão REST API](https://msdn.microsoft.com/library/azure/dn835031.aspx)
