<properties
   pageTitle="Descrição geral de funções do Azure | Microsoft Azure"
   description="Compreenda como utilizar funções de Azure para otimizar o assíncronas das cargas de trabalho em minutos."
   services="functions"
   documentationCenter="na"
   authors="mattchenderson"
   manager="erikre"
   editor=""
   tags=""
   keywords="Azure funções, funções, processamento de eventos, webhooks, cluster dinâmico, sem servidor arquitetura"/>

<tags
   ms.service="functions"
   ms.devlang="multiple"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="multiple"
   ms.workload="na"
   ms.date="08/29/2016"
   ms.author="cfowler;mahender;glenga"/>
   
   
# <a name="azure-functions-overview"></a>Descrição geral das funções Azure

Funções Azure é uma solução para executar facilmente pequenas partes de código ou "funções," na nuvem. Pode escrever apenas o código de que necessita para o problema à mão, sem se preocupar uma aplicação completa ou a infraestrutura de executá-la. Este facto pode tornar desenvolvimento ainda mais produtivo e pode utilizar o idioma de desenvolvimento da escolha, tal como c#, F #, Node.js, Python ou PHP. Paga apenas a hora que o código é executado e fidedignidade Azure para dimensionar, conforme necessário.

Este tópico fornece uma descrição geral das funções Azure. Se pretende ir diretamente no e introdução ao Azure funções, comece com [criar a primeira função Azure](functions-create-first-azure-function.md). Se estiver à procura para obter mais informações técnicas sobre funções, consulte a [referência para programadores](functions-reference.md).

## <a name="features"></a>Funcionalidades

Aqui estão algumas das principais funcionalidades do Azure funções:
    
* **Escolha de idioma** - funções de escrita usando c#, F #, Node.js, Python, PHP, lote, festa, Java ou qualquer executável.
* **Modelo de preços de pagamento por utilização** - pagamento apenas para o tempo despendido a executar o seu código. Ver a opção dinâmico plano de serviço de aplicação nos [preços secção](#pricing) abaixo.  
* **Trazer o seus próprio dependências** - funções suporta NuGet e NPM, para que possa utilizar bibliotecas do favoritas.  
* **Segurança integrada** - acionou proteger HTTP funções com fornecedores de OAuth como o Azure Active Directory, Facebook, Google, Twitter e Account Microsoft.  
* **Integração de simplificado** - facilmente tirar partido dos serviços Azure e ofertas de software-como-a-service (SaaS). Consulte a [secção integrações](#integrations) abaixo para obter alguns exemplos.  
* **Desenvolvimento flexível** - o direito de funções no portal de código ou configurar integração contínua e implementar o seu código através de GitHub, serviços de equipa do Visual Studio e outros [suportadas ferramentas de desenvolvimento](../app-service-web/web-sites-deploy.md#deploy-using-an-ide).  
* **Abrir origem** - funções de runtime é abrir origem e [está disponível no GitHub](https://github.com/azure/azure-webjobs-sdk-script).  

## <a name="what-can-i-do-with-functions"></a>O que posso fazer com funções?

Funções Azure é uma excelente solução para processamento de dados, integrar sistemas, trabalhar com o internet-de-coisas (IoT) e de construção APIs simples e microservices. Considere funções para tarefas, como o processamento de imagem ou a ordem, manutenção de ficheiros, tarefas de execução longa que pretende executar no tópico fundo ou para quaisquer tarefas que pretende executar uma agenda. 

Funções fornece modelos para ajudar a começar com alguns dos cenários principais, incluindo o seguinte:

* **BlobTrigger** - blobs de armazenamento do Windows Azure processo quando forem adicionados a contentores. Pode utilizar este para redimensionamento de imagens.
* Entregue **EventHubTrigger** - responder a eventos a um concentrador de evento Azure. Particularmente útil no instrumentação de aplicação, processamento de experiência ou fluxo de trabalho de utilizador e cenários de Internet coisas (IoT).
* **Genérico webhook** - processo webhook HTTP pedidos a partir de qualquer serviço que suporta webhooks.
* **GitHub webhook** - responder a eventos que ocorram no seu repositórios GitHub. Por exemplo, consulte o artigo [criar um webhook ou função API](functions-create-a-web-hook-or-api-function.md).
* **HTTPTrigger** - accionador a execução do código ao utilizar um pedido de HTTP.
* **QueueTrigger** - responder a mensagens que chegam uma fila de armazenamento do Windows Azure. Por exemplo, consulte o artigo [criar uma função de Azure que se liga a um serviço Azure](functions-create-an-azure-connected-function.md).
* **ServiceBusQueueTrigger** - ligar o seu código a outros serviços Azure ou no local serviços por ouvir filas de mensagens. 
* **ServiceBusTopicTrigger** - ligar o seu código a outros serviços Azure ou no local serviços ao subscrever para tópicos. 
* **TimerTrigger** - Executar limpeza ou outras tarefas de lote numa agenda predefinida. Por exemplo, consulte o artigo [criar um evento a função de processamento](functions-create-an-event-processing-function.md).

Funções Azure suporta *Accionadores*, que são formas de começar a execução do código e *enlaces*, que são formas para simplificar a codificação para os dados de entrada e saídos. Para obter uma descrição detalhada dos accionadores e enlaces que fornece Azure funções, consulte [funções Azure accionadores e referência para programadores de enlaces](functions-triggers-bindings.md).


## <a name="integrations"></a>Integrações

Funções Azure integra-se com uma variedade de Azure e serviços de terceiros 3. Pode utilizar estes para acionar a função e comece a execução ou para servir de entrada e saída para o seu código. As seguinte integrações de serviço são suportadas pelo Azure funções. 

* Azure DocumentDB
* Evento Azure concentradores 
* Aplicações do Azure Mobile (tabelas)
* Notificação Azure concentradores
* Azure Bus de serviço (filas e tópicos)
* Armazenamento Azure (blob, filas e tabelas) 
* GitHub (webhooks)
* No local (utilizando o serviço Bus)

## <a name="pricing"></a>Quanto o funções custo?

Funções Azure tem dois tipos de preços planos, escolha o dispositivo que melhor se adequa às suas necessidades: 

* **Plano de alojamento dinâmico** - quando executa uma função, Azure fornece todos os recursos utilizaria necessários. Não tem de se preocupar com a gestão de recursos e paga apenas a hora em que o código é executado. Detalhes do preços completos estão disponíveis na [página de preços de funções](/pricing/details/functions). 

* **Plano de serviço de aplicação** - executar as funções tal como o seu web, mobile e API apps. Quando já estiver a utilizar o serviço de aplicação para as outras aplicações, pode executar as funções no plano do mesmo sem custos adicionais. Encontrará detalhes completos na [página de preços de serviço de aplicação](/pricing/details/app-service/).

Para mais informações sobre as funções de dimensionamento, consulte o artigo [como dimensionar Azure funções](functions-scale.md).

##<a name="next-steps"></a>Próximos passos

+ [Criar a sua primeira função Azure](functions-create-first-azure-function.md)  
Ir diretamente no e crie a sua função primeira utilizando o guia de introdução do Azure funções. 
+ [Referência para programadores do Azure funções](functions-reference.md)  
Fornece mais informações técnicas sobre o tempo de execução de funções do Azure e uma referência para funções de codificação e definir accionadores e enlaces.
+ [Testes funções Azure](functions-test-a-function.md)  
Descreve várias ferramentas e técnicas para testar as suas funções.
+ [Como dimensionar funções Azure](functions-scale.md)  
Descreve os planos do serviço disponíveis com as funções de Azure, incluindo o plano de serviço dinâmicos e como escolher o plano à direita. 
+ [Saiba mais sobre o serviço de aplicação do Azure](../app-service/app-service-value-prop-what-is.md)  
Funções Azure tira partido da plataforma de aplicação de serviço do Azure para funcionalidades principais como híbridas, variáveis de ambiente e diagnóstico. 