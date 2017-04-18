<properties 
    pageTitle="O que são lógica aplicações?" 
    description="Saiba mais sobre aplicações de lógica de serviço de aplicação" 
    authors="kevinlam1" 
    manager="dwrede" 
    editor="" 
    services="logic-apps" 
    documentationCenter=""/>

<tags
    ms.service="logic-apps"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="hero-article" 
    ms.date="10/12/2016"
    ms.author="klam"/>

# <a name="what-are-logic-apps"></a>O que são lógica aplicações?

Aplicações de lógica fornecem uma forma para simplificar e implementar integrações dimensionáveis e fluxos de trabalho na nuvem. Fornece um estruturador visual do modelo e automatizar o processo de uma série de passos conhecido como um fluxo de trabalho.  Existem [muitas conexões](../connectors/apis-list.md) através da nuvem e no local para integrar rapidamente através de serviços e protocolos.  Uma aplicação de lógica começa com um accionador (como 'quando uma conta é adicionada ao Dynamics CRM') e depois de cozedura pode começar a várias acções de combinações, conversões e lógica de condição.

As vantagens da utilização de aplicações de lógica incluem o seguinte:  

- Poupando tempo através da criação de processos complexos utilizando o easy para compreender as ferramentas de estrutura
- Implementar padrões e fluxos de trabalho de forma totalmente integrada, que caso contrário, seria difícil implementar o no código
- Introdução ao rapidamente a partir de modelos
- Personalizar a sua aplicação de lógica com as suas APIs, código e ações personalizadas
- Ligar e sincronisação sistemas distintos ao longo no local e na nuvem
- Construir fora BizTalk server, gestão de API, funções Azure e Azure Service Bus com suporte de integração primeira classe

Lógica de aplicações é um iPaaS totalmente geridas (integração plataforma como um serviço) permitindo que os programadores não tem de se preocupar sobre como construir alojamento, escalabilidade, disponibilidade e gestão.  Lógica aplicações irá dimensionar automaticamente para cima para cumprir o pedido.

![Estruturador de fluxos de fluxo de aplicação](./media/app-service-logic-what-are-logic-apps/LogicAppCapture2.png)

Tal como mencionado, com as aplicações de lógica, pode automatizar processos de negócio. Eis alguns exemplos:  
 
* Mover os ficheiros que carregou para um servidor de FTP para o armazenamento do Windows Azure
* Processo e rota encomendas ao longo no local e na nuvem sistemas
* Monitorizar tweets tudo sobre um determinado tópico, analisar o sentimento e criar alertas e tarefas para itens que necessitam de seguimento.

Cenários, como estes podem ser configurados tudo a partir do designer visual e sem escrever uma única linha de código. Obter a introdução [construir a sua aplicação de lógica agora][create].  Assim que escritos - uma aplicação de lógica pode ser [implementada e reconfigurar rapidamente](app-service-logic-create-deploy-template.md) em múltiplas ambientes e regiões.

## <a name="why-logic-apps"></a>Por que motivo lógica aplicações?

Lógica aplicações reunindo a velocidade e escalabilidade o espaço de integração de empresa.  A facilidade de utilização do estruturador de fluxos, variedade de accionadores disponíveis e ações e ferramentas de gestão eficientes certifique centralizando seu APIs mais simples que nunca.  Como mover empresas no sentido digitalization, lógica de aplicações permite-lhe ligar sistemas legados e vanguardos em conjunto.

Para além disso, com os nossos [Conta integração da empresa] [ biztalk] pode dimensionar a maturar cenários de integração com potência de um [serviço de mensagens XML][xml], [Gestão de parceiros de negociação][tpm]e muito mais.

- **Ferramentas de estrutura de fácil utilização** - lógica aplicações pode ser concebido fim-a-ponto no browser ou com ferramentas do Visual Studio. Comece com um accionador - a partir de uma agenda simple para quando é criado um problema de GitHub. Em seguida, orquestrar qualquer número de ações através da Galeria de conectores avançada.

- **APIs ligar facilmente** -as tarefas de composição par que são fáceis descrever são difíceis de implementar código. Lógica aplicações torna mais fácil ligar sistemas distintos. Pretende ligar a sua solução para o seu local de marketing de nuvem faturação sistema? Pretende centralizar mensagens através de APIs e sistemas com um Bus de serviço de empresa? Aplicações de lógica são a forma mais rápida e mais fiável a soluções para estes problemas.

- **Começar rapidamente a partir de modelos** - para o ajudar a começar a utilizar fornecemos uma [Galeria de modelos de] [ templates] que permitem-lhe criar rapidamente algumas soluções comuns. A partir do avançadas B2B soluções para conectividade SaaS simples e até alguns que destinam-se apenas ' diversão' - a Galeria de é a forma mais rápida de introdução ao power de lógica de aplicações.

- **Expansão alimentos-in** - não vir o conector de que precisa? Lógica aplicações foi concebida para funcionar com o seu próprio APIs e código; Pode criar facilmente a sua própria aplicação API para utilizar como uma conexão personalizada ou ligar para uma [Função Azure](https://functions.azure.com) executar fragmentos de código a pedido. 

- **Potência integração real** - iniciar fácil e crescem em sequência que precisar. Lógica aplicações pode facilmente tirar partido do BizTalk, solução de integração de líder de setor da Microsoft para ativar a integração com os profissionais de TI criar soluções que necessitam. Saiba mais sobre o [Pacote de integração de empresa](./app-service-logic-enterprise-integration-overview.md).

## <a name="logic-app-concepts"></a>Conceitos de aplicação de lógica

Seguem-se algumas das principais partes que compõem a experiência de lógica aplicações. 

- **Fluxo de trabalho** - lógica aplicações fornece uma forma de gráfica para modelar os processos de negócio como uma série de passos ou um fluxo de trabalho.
- **Conectores gerido** - as suas aplicações de lógica precisam de aceder aos dados e serviços. Conectores geridos são criados especificamente para que o ajude a quando estiver a estabelecer ligação ao e a trabalhar com os seus dados. Ver a lista de conectores agora disponíveis no [gerido conectores][managedapis].
- **Accionadores** - alguns conectores gerido também podem agir como um accionador. Um accionador inicia uma nova instância de um fluxo de trabalho com base num evento específico, como a chegada de mensagem de correio electrónico ou uma alteração na sua conta de armazenamento do Windows Azure.
-  **Ações** - cada passo depois do accionador num fluxo de trabalho chama-se uma ação. Cada ação, normalmente, liga a uma operação no seu conexão gerido ou aplicações API personalizadas.
- **Enterprise Integration Pack** - cenários de integração de mais avançadas, aplicações de lógica inclui capacidades de BizTalk. BizTalk é plataforma de integração à esquerda da indústria da Microsoft. As conexões Enterprise Integration Pack permitem-lhe incluir facilmente validação, transformação e muito mais para os seus fluxos de trabalho de aplicação de lógica.

## <a name="getting-started"></a>Introdução  

- Para começar a trabalhar com aplicações de lógica, siga para [criar uma aplicação de lógica] [ create] tutorial.  
- [Ver exemplos e cenários comuns](app-service-logic-examples-and-scenarios.md)
- [Pode automatizar processos de negócio com aplicações de lógica](http://channel9.msdn.com/Events/Build/2016/T694) 
- [Saiba como integrar o seu sistemas com aplicações de lógica](http://channel9.msdn.com/Events/Build/2016/P462)

[biztalk]: app-service-logic-enterprise-integration-accounts.md
[appservice]: ../app-service/app-service-value-prop-what-is.md
[create]: app-service-logic-create-a-logic-app.md
[managedapis]: ../connectors/apis-list.md
[tpm]: app-service-logic-enterprise-integration-accounts.md
[xml]: app-service-logic-enterprise-integration-b2b.md
[templates]: app-service-logic-use-logic-app-templates.md
