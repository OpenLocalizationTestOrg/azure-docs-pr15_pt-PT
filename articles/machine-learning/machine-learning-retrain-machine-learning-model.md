<properties
    pageTitle="Reciclagem de uma modelo de aprendizagem automática | Microsoft Azure"
    description="Saiba como um modelo de retransmissão e atualizar o serviço Web para utilizar o modelo recentemente qualificado no Azure máquina aprendizagem."
    services="machine-learning"
    documentationCenter=""
    authors="vDonGlover"
    manager="raymondl"
    editor=""/>

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/10/2016"
    ms.author="v-donglo"/>

# <a name="retrain-a-machine-learning-model"></a>Reciclagem de uma modelo de aprendizagem automática

Como parte do processo de operationalization de modelos de aprendizagem automática no Azure máquina de formação, o seu modelo está formação e guardado. Em seguida, utilizá-lo para criar um serviço Web predicative. O serviço Web, em seguida, pode ser consumido na web sites, dashboards e aplicações móveis. 

Modelos de que cria utilizando máquina aprendizagem não estão normalmente estáticos. Novos dados fica disponíveis ou quando o consumidor da API do tem os seus próprios dados ao modelo tem de ser retrained. 

Reciclagem poderá ocorrer frequentemente. Com a funcionalidade de programação API Reciclagem, pode Reciclagem o modelo com as APIs da Reciclagem e atualizar o serviço Web com o modelo recentemente qualificado através de programação. 

Este documento descreve o processo de readaptação e mostra-lhe como utilizar as APIs da Reciclagem.

## <a name="why-retrain-defining-the-problem"></a>Por que motivo Reciclagem: que define o problema  

Como parte da processo de formação de aprendizagem automática, um modelo é formação utilizando um conjunto de dados. Modelos de que cria utilizando máquina aprendizagem não estão normalmente estáticos. Novos dados fica disponíveis ou quando o consumidor da API do tem os seus próprios dados ao modelo tem de ser retrained.

Nestes cenários, uma API programação fornece uma forma conveniente para permitir ou o consumidor da sua APIs criar um cliente que pode, numa base única ou regular, reciclagem o modelo com os seus próprios dados. Em seguida, podem avaliar os resultados da Reciclagem e atualizar a API do serviço Web para utilizar o modelo de formação recentemente.

>[AZURE.NOTE] Se tiver uma experiência de formação existente e o serviço do novo Web site, poderá querer finalizar reciclagem de um serviço Web aspeto do Office existente em vez de seguir a instruções passo a passo mencionada na secção seguinte.

## <a name="end-to-end-workflow"></a>Fluxo de trabalho de ponto a ponto 

O processo envolve os seguintes componentes: A experiência de formação e uma experiência de aspeto do Office publicado como um serviço Web. Para ativar a Reciclagem de um modelo de formação, tem de ser publicada como um serviço Web com a saída de um modelo de formação a experiência de formação. Permite que o acesso à API para o modelo para a Reciclagem. 

Os passos seguintes aplicam-se a serviços de novo e clássico Web:

Crie o serviço de aspeto do Office Web inicial:

* Criar uma experiência de formação
* Criar uma experiência de aspeto do Office web
* Implementar um serviço web aspeto do Office

Reciclagem o serviço Web:

* Atualizar a experiência de formação para permitir a Reciclagem
* Implementar o serviço web readaptação
* Utilizar o código de serviço de execução do lote para o modelo de retransmissão

Para obter instruções sobre os passos anteriores, consulte o artigo [modelos de formação de máquina de retransmissão através de programação](machine-learning-retrain-models-programmatically.md).

Caso implementado um serviço Web clássico:

* Criar um novo ponto final no serviço Web aspeto do Office
* Obter o URL de correcção e o código
* Utilizar o URL de correcção para aponte para o novo ponto final de modelo de retrained 

Para obter instruções sobre os passos anteriores, consulte o artigo [Reciclagem de um serviço Web clássica](machine-learning-retrain-a-classic-web-service.md).

Se ocorrer dificuldades reciclagem de um serviço Web clássico, consulte o artigo [resolução de problemas a Reciclagem de um serviço Web clássica do Azure máquina aprendizagem](machine-learning-troubleshooting-retraining-models.md).

Caso implementado um serviço Web novo:

* Iniciar sessão na sua conta do Gestor de recursos do Azure
* Obter a definição do serviço Web
* Exportar a definição do serviço Web como JSON
* Atualizar a referência para a `ilearner` blob no JSON
* Importar o JSON para uma definição de serviço Web
* Atualizar o serviço Web com a nova definição do serviço Web

Para obter instruções sobre os passos anteriores, consulte o artigo [Reciclagem de um serviço de novo Web site utilizando os cmdlets do PowerShell de gestão de aprendizagem automática](machine-learning-retrain-new-web-service-using-powershell.md).

O processo de configuração de reciclagem de um serviço Web clássica envolve os seguintes passos:

![Descrição geral do processo de reciclagem][1]

O processo de configuração de reciclagem de um serviço Web novo envolve os seguintes passos:

![Descrição geral do processo de reciclagem][7]

## <a name="other-resources"></a>Outros recursos

- [Reciclagem e atualizar o Azure máquina aprendizagem modelos com fábrica de dados do Azure](https://azure.microsoft.com/blog/retraining-and-updating-azure-machine-learning-models-with-azure-data-factory/)
- [Criar muitos modelos de aprendizagem automática e web pontos finais de serviço a partir de uma experiência através do PowerShell](machine-learning-create-models-and-endpoints-with-powershell.md)
- O vídeo [AML Reciclagem modelos utilizando APIs](https://www.youtube.com/watch?v=wwjglA8xllg) mostra-lhe como modelos de aprendizagem máquina criados no Azure máquina formação de retransmissão utilizando a Reciclagem APIs e PowerShell.

<!--image links-->
[1]: ./media/machine-learning-retrain-machine-learning-model/machine-learning-retrain-models-programmatically-IMAGE01.png
[7]: ./media/machine-learning-retrain-machine-learning-model/machine-learning-retrain-models-programmatically-IMAGE07.png

