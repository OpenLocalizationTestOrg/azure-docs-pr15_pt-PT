<properties
    pageTitle="Converter uma experiência de formação de aprendizagem automática para uma experiência de aspeto do Office | Microsoft Azure"
    description="Como converter uma experiência de formação de formação de máquina, utilizada para o seu modelo de análise de aspeto do Office, para uma experiência de aspeto do Office que pode ser implementada como um serviço web de formação."
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
    ms.date="08/19/2016"
    ms.author="garye"/>

# <a name="convert-a-machine-learning-training-experiment-to-a-predictive-experiment"></a>Converter uma experiência de formação de aprendizagem automática para uma experiência de aspeto do Office

Formação do Azure máquina permite-lhe construir, testar e implementar soluções de análise de aspeto do Office.

Assim que tenha criado e sujeita a iteração numa *formação experimentar* para formar o seu modelo de análise de aspeto do Office e estiver pronto para utilizá-la para pontuação dados novos, tem de preparar e simplificar a sua experiência de pontuação. Em seguida, pode implementar esta *experiência aspeto do Office* como um serviço Azure web, para que os utilizadores possam enviar dados para o seu modelo e receber previsões do seu modelo.

Ao converter uma experiência de aspeto do Office, que obtém o modelo com formação pronta a ser implementada como um serviço web. Os utilizadores do serviço web vão enviar dados de entrada ao seu modelo e o seu modelo irá enviar novamente os resultados de previsão. Por isso, tal como converter para uma experiência de aspeto do Office que irá pretende ter em conta, como o seu modelo para ser utilizado por outras pessoas que esperava.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

O processo de conversão de uma experiência de formação para uma experiência de aspeto do Office envolve os três passos:

1.  Guarde o modelo de aprendizagem máquina que tenha a formação e, em seguida, substitua a algoritmo e o [Modelo de comboio] de aprendizagem automática[ train-model] módulos com o seu modelo com formação guardado.
2.  Corte a experiência apenas aos módulos que são necessários para pontuação. Uma experiência de formação inclui um número de módulos que são necessários para formação, mas que não sejam necessários assim que o modelo estiver formação e pronto a utilizar para pontuação.
3.  Defina onde na sua experiência será aceitar dados a partir do utilizador do serviço web e os dados que serão devolvidos.

## <a name="set-up-web-service-button"></a>Botão do serviço Web

Depois de ter executado da experiência (botão**Executar** na parte inferior da tela experiência), o botão de **O serviço Web** (seleccionar a opção de **Serviço Web aspeto do Office** ) irá desempenhar por si os três passos de converter a sua experiência de formação para uma experiência de aspeto do Office:

1.  Guarda um modelo de formação como um módulo na secção **Modelos com formação** da paleta de módulo (à esquerda da tela experiência), em seguida, substitui a algoritmo e o [Modelo de comboio] de aprendizagem automática[ train-model] módulos com o modelo de formação guardado.
2.  Remove os módulos não claramente são necessários. No nosso exemplo, isto inclui os [Dados divididos][split], 2º [Pontuação modelo][score-model]e o [Modelo de avaliar] [ evaluate-model] módulos.
3.  Cria a entrada de serviço Web e módulos de saída e adiciona-os nas localizações predefinidas na sua experiência.

Por exemplo, a experiência seguinte prepara um modelo de árvore de classe duas aumentado decisão utilizando dados de contagem de exemplo:

![Experiência de formação][figure1]

Os módulos esta experiência funções que mostra basicamente quatro diferente:

![Funções de módulo][figure2]

Quando converte esta experiência de formação para uma experiência de aspeto do Office, algumas destes módulos já não forem necessárias ou que tenham um objetivo diferente:

- **Dados** - os dados neste conjunto de dados de exemplo não é utilizado durante a pontuação - o utilizador do serviço web irá fornecer os dados para ser classificados. No entanto, os metadados a partir deste conjunto de dados, tais como tipos de dados, são utilizado pelo modelo de formação. Por isso, tem de manter o conjunto de dados na experiência aspeto do Office para que pode conferir este metadados.

- **Preparação** - consoante os dados que serão submetidas para pontuação, estes módulos podem ou não podem ser necessários para processar os dados de entrada.

    Por exemplo, neste exemplo, o conjunto de dados de exemplo poderá ter valores em falta e inclui colunas que não são necessários para formar o modelo. Por isso, de [Dados em falta LIMPARB] [ clean-missing-data] módulo foi fornecido para negócio com valores em falta e uma [Selecionar colunas no conjunto de dados] [ select-columns] módulo foi fornecido para excluir essas colunas adicionais a partir do fluxo de dados. Se souber que os dados que serão submetidos para pontuação através do serviço web não terão valores em falta, em seguida, pode remover os [Dados em falta LIMPARB] [ clean-missing-data] módulo. No entanto, uma vez que as [Selecionar colunas no conjunto de dados] [ select-columns] módulo ajuda-o a definir o conjunto de funcionalidades a ser classificados, esse módulo tem de permanecer.

- **Comboio** - assim que o modelo foi preparado com êxito, pode guardá-lo como um módulo de modelo com formação único. Em seguida, substitua estes módulos individuais com o modelo de formação guardado.

- **Pontuação** - neste exemplo, o módulo de divisão é utilizada para dividir o fluxo de dados num conjunto de dados de teste e dados de formação. Na experiência aspeto do Office não é necessária e pode ser removida. Da mesma forma, 2º de [Modelo de pontuação] [ score-model] módulo e o [Modelo de avaliar] [ evaluate-model] módulo são utilizadas para comparar os resultados dos dados de teste, para que estes módulos são também não é necessário na experiência aspeto do Office. O restante de [Modelo de pontuação] [ score-model] módulo, no entanto, é necessário para devolver um resultado de pontuação através do serviço web.

Eis como o nosso exemplo aparece depois de clicar em **Configurar o serviço Web**:

![Convertidas em experiência aspeto do Office][figure3]

Isto pode ser suficiente para preparar a sua experiência para ser implementada como um serviço web. No entanto, poderá querer fazer algumas trabalho adicional específico para a sua experiência.

### <a name="adjust-input-and-output-modules"></a>Ajustar módulos de entrada e saídos

Na sua experiência de formação, utilizado um conjunto de dados de formação e, em seguida, fez algumas processamento para obter os dados num formulário que é necessário o algoritmo de aprendizagem automática. Se os dados que esperar para receber através do serviço web não irão precisar deste processamento, pode mover o **módulo de entrada do serviço Web** para um nó diferente na sua experiência.

Por exemplo, por predefinição **O serviço Web** coloca o módulo de **entrada de serviço Web** na parte superior do fluxo de dados, como na figura acima. No entanto, se os dados de entrada não irão precisar deste processamento, em seguida, pode posicionar manualmente a **entrada de serviço Web** anteriores os módulos de processamento de dados:

![Mover a entrada de serviço web][figure4]

Os dados de entrada fornecidos através do serviço web agora irão transmitir diretamente para o módulo de modelo de pontuação sem qualquer pré-processamento.

Da mesma forma, por predefinição **O serviço Web** coloca o módulo de saída do serviço Web na parte inferior do fluxo de dados. Neste exemplo, o serviço web irá devolver ao utilizador o resultado do [Modelo de pontuação] [ score-model] módulo que inclui o vector de dados de entrada completa, juntamente com a pontuação de resultados.

No entanto, se preferir devolver algo diferente - por exemplo, apenas os resultados de pontuação e não o vector inteiro de dados de entrada -, em seguida, que podem inserir um [Selecionar colunas no conjunto de dados] [ select-columns] módulo para excluir a todas as colunas exceto os resultados de pontuação. Em seguida, mova o módulo de **saída de serviço Web** para o resultado das [Selecionar colunas no conjunto de dados] [ select-columns] módulo:

![Mover o resultado de serviço web][figure5]

### <a name="add-or-remove-additional-data-processing-modules"></a>Adicionar ou remover módulos de processamento de dados adicionais

Se existirem mais módulos na sua experiência que sabe que não serão necessários durante pontuação, estes podem ser removidos. Por exemplo, uma vez que recomendamos ter passado o módulo de **entrada de serviço Web** para um ponto após os módulos de processamento de dados, podemos remover os [Dados em falta LIMPARB] [ clean-missing-data] módulo a partir da experiência de aspeto do Office.

Os nossos experiência aspeto do Office agora este aspeto:

![Remover o módulo adicional][figure6]

### <a name="add-optional-web-service-parameters"></a>Adicionar parâmetros de serviço Web opcionais

Em alguns casos, poderá querer permitir que o utilizador do seu serviço web alterar o comportamento da módulos quando o serviço é acedido. *Parâmetros de serviço Web* permitem-lhe para o fazer.

Um exemplo comum está a configurar a [Importar dados] [ import-data] módulo para que o utilizador do serviço web implementado pode especificar uma origem de dados diferente, quando o serviço web é acedido. Ou configurar a [Exportar dados] [ export-data] módulo para que pode ser especificado um destino diferente.

Pode definir parâmetros do serviço Web e associá-las com um ou mais parâmetros do módulo, e pode especificar se forem necessárias ou opcional. O utilizador do serviço web, em seguida, pode fornecer os valores para estes parâmetros quando o serviço é acedido e as ações de módulo serão modificadas em conformidade.

Para obter mais informações sobre os parâmetros do serviço Web, consulte o artigo [Utilizar parâmetros de serviço de Web do Azure máquina aprendizagem ] [ webserviceparameters].

[webserviceparameters]: machine-learning-web-service-parameters.md


## <a name="deploy-the-predictive-experiment-as-a-web-service"></a>Implementar a experiência de aspeto do Office como um serviço web

Agora que a experiência de aspeto do Office tiver sido suficientemente preparada, pode implementá-lo como um serviço Azure web. Utilizar o serviço web, os utilizadores podem enviar dados ao seu modelo e o modelo irá devolver o respetivos previsões.

Para mais informações sobre o processo de implementação concluída, consulte o artigo [Implementar um serviço web de aprendizagem de máquina do Azure][deploy]

[deploy]: machine-learning-publish-a-machine-learning-web-service.md


<!-- Images -->
[figure1]:./media/machine-learning-convert-training-experiment-to-scoring-experiment/figure1.png
[figure2]:./media/machine-learning-convert-training-experiment-to-scoring-experiment/figure2.png
[figure3]:./media/machine-learning-convert-training-experiment-to-scoring-experiment/figure3.png
[figure4]:./media/machine-learning-convert-training-experiment-to-scoring-experiment/figure4.png
[figure5]:./media/machine-learning-convert-training-experiment-to-scoring-experiment/figure5.png
[figure6]:./media/machine-learning-convert-training-experiment-to-scoring-experiment/figure6.png


<!-- Module References -->
[clean-missing-data]: https://msdn.microsoft.com/library/azure/d2c5ca2f-7323-41a3-9b7e-da917c99f0c4/
[evaluate-model]: https://msdn.microsoft.com/library/azure/927d65ac-3b50-4694-9903-20f6c1672089/
[select-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/
[export-data]: https://msdn.microsoft.com/library/azure/7a391181-b6a7-4ad4-b82d-e419c0d6522c/
