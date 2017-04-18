<properties
   pageTitle="Implementar um novo serviço Web"
   description="O fluxo de trabalho de implementação de um processador com base no serviço web"
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
    ms.date="10/04/2016"
    ms.author="v-donglo"/>

# <a name="deploy-a-new-web-service"></a>Implementar um novo serviço web

Agora de formação do Microsoft Azure Machine fornece serviços web que são baseados no [Gestor de recursos do Azure](../azure-resource-manager/resource-group-overview.md) permitindo que para novas opções de plano de faturação e implementar o seu serviço web para várias regiões.

O fluxo de trabalho geral para implementar um serviço web utilizando os serviços de Web de formação do Microsoft Azure máquina é:

* Criar uma experiência de aspeto do Office
* implementá-lo
* configurar o seu nome
* plano de faturação
* testá-la
* consumi-lo.

O gráfico seguinte ilustra o fluxo de trabalho.

![Fluxo de trabalho do Web serviços implementação][1]
 
## <a name="deploy-web-service-from-studio"></a>Implementar o serviço web a partir do Studio 

Para implementar uma experiência como um novo serviço web. Inicie sessão na Studio de aprendizagem automática e criar um novo serviço web aspeto do Office. 

**Nota**: Se já tiver implementado uma experiência como um serviço web clássica não é implementá-lo como um novo serviço web.
 
Clique em **Executar** na parte inferior da tela experiência e, em seguida, clique em **Implementar o serviço Web** e **Implementar o serviço Web de [novo]**. A página de implementação do Gestor de serviço de Web de aprendizagem máquina, será aberto.

## <a name="machine-learning-web-service-manager-deploy-experiment-page"></a>Implementar o Gestor do serviço de máquina aprendizagem Web experiência página
Na página implementar experiência, introduza um nome para o serviço web.
Selecione um plano de preços. Se tiver um plano de preços existente, que pode selecionar a mesma, caso contrário, terá de criar um novo plano preço no serviço. 

1.  No **Plano de preço** de lista pendente, selecione um plano existente ou selecione a opção **Selecionar novo plano** .
2.  Em **Nome do plano**, escreva um nome que irá identificar o plano na sua fatura.
3.  Selecione uma das **Camadas plano mensal**. Tenha em atenção que a predefinição de camadas plano para os planos para sua região predefinido e o serviço web é implementada para essa região.

Clique em **Implementar** e página de guia de introdução para o seu abre de serviço web.

## <a name="quickstart-page"></a>Página de guia de introdução
A página de guia de introdução do serviço web dá-lhe acesso e orientações sobre as tarefas mais comuns que fará depois de criar um novo serviço web. A partir daqui, pode aceder facilmente a página de **teste** e a página de **consumir** .

## <a name="testing-your-web-service"></a>Testar o seu serviço web

Na página de guia de introdução, clique em serviço web de teste em tarefas comuns.   

Para testar o serviço web como um serviço de pedido de resposta (RRS):

* Na barra de menus, clique em **Testar** .
* Clique em **Pedido de resposta**.
* Introduza valores adequados para as colunas entradas da sua experiência.
* Clique em testar **Pedido de resposta**.

Resultados irá apresentar no lado direito da página.

Para testar a um serviço web de serviço de execução de lote (BES), irá utilizar um ficheiro CSV:

* Na barra de menus, clique em **Testar** .
* Clique em **lotes**.
* Em sua entrada, clique em Procurar e navegue para o ficheiro de dados de exemplo.
* Clique em **Testar**.

O estado da sua teste é apresentado em **Testar trabalhos em lotes**.

## <a name="consuming-your-web-service"></a>Consumir o serviço Web

Quando implementado como um serviço web, formação de máquina Azure experiências fornecem uma REST API que pode ser consumida por uma vasta gama de dispositivos e plataformas. Isto acontece porque simples REST API aceita e responde com JSON formatado mensagens. Portal do Azure máquina formação fornece código que pode ser utilizado para chamar o serviço web no R, c# e Python.
 
Na página Consuming pode encontrar:

* A chave de API e URI para consumir serviço web nas aplicações.
* Modelos de aplicação Excel e web para funcionam como ricochete iniciar o processo de consumo.
* Exemplos de código c#, python e R para começar a utilizar.

Para mais informações sobre utilizado por outros serviços web, consulte o artigo [como consumir um serviço web de aprendizagem do Azure máquina que tenha sido implementado a partir de uma experiência de aprendizagem automática](machine-learning-consume-web-services.md).

## <a name="next-steps"></a>Próximos passos

Para mais informações sobre utilizado por outros serviços web, consulte:

[Como consumir um serviço web de aprendizagem do Azure máquina que tenha sido implementado a partir de uma experiência de aprendizagem automática](machine-learning-consume-web-services.md)

[Formação de máquina Azure Web Services: Implementação e consumo](machine-learning-deploy-consume-web-service-guide.md)

<!--Image references-->
[1]: ./media/machine-learning-webservice-deploy-a-web-service/armdeploymentworkflow.png


<!--links-->
