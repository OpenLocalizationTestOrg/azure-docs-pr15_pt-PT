<properties
   pageTitle="Dimensionamento serviço web | Microsoft Azure"
   description="Saiba como dimensionar um serviço web ao aumentar simultaneidade e adicione novos pontos finais."
   services="machine-learning"
   documentationCenter=""
   authors="neerajkh"
   manager="srikants"
   editor="cgronlun"
   keywords="Azure máquina de formação, serviços web, operationalization, dimensionamento, ponto final, simultaneidade"
   />
<tags
   ms.service="machine-learning"
   ms.devlang="NA"
   ms.workload="data-services"
   ms.tgt_pltfrm="na"
   ms.topic="article"
   ms.date="10/05/2016"
   ms.author="neerajkh"/>

# <a name="scaling-a-web-service"></a>Um serviço Web de dimensionamento

>[AZURE.NOTE] Este tópico descreve técnicas aplicáveis a um serviço Web de aprendizagem máquina clássica. 

Por predefinição, cada serviço Web publicado está configurado para suportar 20 pedidos em simultâneo e pode ser tão elevado como 200 pedidos em simultâneo. Enquanto o portal clássico Azure fornece uma forma de defina este valor, formação de máquina Azure optimiza automaticamente a definição para fornecer o melhor desempenho do seu serviço web e o valor de portal é ignorado. 

Se vai suportar o plano para chamar a API com uma carga mais elevada que um valor de chamadas em simultâneo máximo de 200, deverá criar vários pontos finais no mesmo serviço Web. Em seguida, pode distribuir aleatoriamente sua carga ao longo das mesmas.

## <a name="add-new-endpoints-for-same-web-service"></a>Adicionar novos pontos finais para o mesmo serviço web

A escala de um serviço Web é uma tarefa comuns. Alguns dos motivos para dimensionar são para suporta mais de 200 pedidos em simultâneo, aumentar a disponibilidade através de vários pontos finais ou fornecer separados pontos finais para o serviço web. Pode aumentar a escala, adicionando os pontos finais adicionais para o serviço Web mesmo através do [portal de clássica Azure](https://manage.windowsazure.com/) ou o portal de [Serviço Web do Azure máquina aprendizagem](https://services.azureml.net/) .

Para mais informações sobre como adicionar pontos finais de novos, consulte o artigo [Criar os pontos finais](machine-learning-create-endpoint.md).

Tenha em atenção que pode ser prejudicial se não está a telefonar a API com uma taxa de elevada correspondente utilizando uma contagem de simultaneidade alta. Poderá ver esporádica tempos limite e/ou picos a latência se colocar uma carga baixa uma API configurada para o caso de carga elevado.

As APIs síncronas são normalmente utilizadas em situações onde uma latência baixa é pretendida. Latência aqui implica o tempo demora até a API para concluir o uma pedido e não de conta para qualquer atrasos na rede. Digamos que tiver uma API com uma latência de 50 ms. Consumir totalmente a capacidade disponível com o nível de limitação alta e máximo em simultâneo chamadas = 20, precisa de telefonar esta API 20 * 1000 / 50 = 400 vezes por segundo. Expandir isto ainda mais, chamadas de em simultâneo um máximo de 200 permite-lhe ligar os tempos de API 4000 por segundo, partindo do princípio que uma latência de 50 ms.

<!--Image references-->
[1]: ./media/machine-learning-scaling-webservice/machlearn-1.png
[2]: ./media/machine-learning-scaling-webservice/machlearn-2.png
