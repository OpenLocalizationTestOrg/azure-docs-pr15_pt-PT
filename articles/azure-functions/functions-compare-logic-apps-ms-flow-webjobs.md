<properties
    pageTitle="Escolher entre fluxo, aplicações de lógica, funções e WebJobs | Microsoft Azure"
    description="Compare e contraste de para a nuvem integração de serviços da Microsoft e decidir qual deve utilizar de serviço (s)."
    services="functions,app-service\logic"
    documentationCenter="na"
    authors="cephalin"
    manager="wpickett"
    tags=""
    keywords="Microsoft fluxo, fluxo, aplicações de lógica, funções azure, funções, azure webjobs, webjobs, processamento, cluster dinâmico, sem servidor arquitetura de eventos"/>

<tags
    ms.service="functions"
    ms.devlang="multiple"
    ms.topic="article"
    ms.tgt_pltfrm="multiple"
    ms.workload="na"
    ms.date="09/08/2016"
    ms.author="chrande; glenga"/>

# <a name="choose-between-flow-logic-apps-functions-and-webjobs"></a>Escolher entre fluxo, aplicações de lógica, funções e WebJobs

Este artigo compara e contrasta os seguintes serviços na nuvem Microsoft, que todos os resolver problemas de integração e automatização dos processos de negócio:

- [Fluxo da Microsoft](https://flow.microsoft.com/)
- [Aplicações de lógica Azure](https://azure.microsoft.com/services/logic-apps/)
- [Funções Azure](https://azure.microsoft.com/services/functions/)
- [WebJobs Azure de aplicação de serviço](../app-service-web/web-sites-create-web-jobs.md)

Todos os estes serviços são úteis quando "colando" em conjunto sistemas distintos. Pode todos definem entrada, ações, condições e saída. Pode executar cada uma num agenda ou acionador. No entanto, cada serviço adiciona um conjunto único de valor e compará-las não é uma questão de "qual o serviço é a melhor?" mas um dos "qual o serviço é melhor adaptados para esta situação?" Muitas vezes, uma combinação de um destes serviços é a melhor forma de criar rapidamente uma solução completa, dimensionáveis integração em destaque.

<a name="flow"></a>
## <a name="flow-vs-logic-apps"></a>Aplicações de fluxo de vs. lógica

Pode discutimos Microsoft Flow e Apps de lógica do Azure em conjunto porque são ambos os serviços de integração de *configuração do primeiro* , que torna mais fácil criar processos e fluxos de trabalho e integrar com várias aplicações SaaS e enterprise. 

- Fluxo está incorporado na parte superior de aplicações de lógica
- Que tenham o mesmo estruturador do fluxo de trabalho
- [Conectores de](../connectors/apis-list.md) trabalho de uma também pode trabalhar no outro

Fluxos de confere qualquer trabalhador do office para efetuar integrações simples (por exemplo: obter SMS para e-mails importantes) sem percorrer os programadores ou IT. Por outro lado, aplicações de lógica pode ativar a avançadas ou críticos integrações (por exemplo, B2B processos) onde são necessárias práticas de DevOps e segurança de nível empresarial. É normal para um fluxo de trabalho de negócio aumentar o das horas extraordinárias de complexidade. Por conseguinte, pode começar com um fluxo de na primeira, em seguida, convertê-la para uma aplicação de lógica conforme necessário.

A tabela seguinte ajuda a determinar se é melhores para uma determinado integração fluxo ou aplicações de lógica.

|               | Fluxo                                                                             | Aplicações de lógica                                                                                          |
|---------------|----------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------|
| Audiência      | trabalhadores do Office, os utilizadores de negócio                                                   | Profissionais de TI, os programadores                                                                                 |
| Cenários     | Gestão personalizada                                                                     | Críticos                                                                                    |
| Ferramenta de criação   | No browser, apenas IU                                                              | No browser e o [Visual Studio](../app-service/logic/app-service-logic-deploy-from-vs.md), [da Vista código](../app-service-logic/app-service-logic-author-definitions.md) disponíveis |
| DevOps        | Ad-hoc, desenvolver-se de produção                                                    | controlo de origem, testes, suporte e automatização e capacidade de gestão na [Gestão de recursos do Azure](../app-service-logic/app-service-logic-arm-provision.md)|
| Experiência de administrador| [https://flow.microsoft.com](https://flow.microsoft.com)                       | [https://portal.Azure.com](https://portal.azure.com)                                                |
| Segurança      | Práticas padrão: [soberania de dados](https://wikipedia.org/wiki/Technological_Sovereignty), [encriptação em repouso](https://wikipedia.org/wiki/Data_at_rest#Encryption) para dados sensíveis, etc. | Garantia de segurança do Azure: [Segurança do Azure](https://www.microsoft.com/trustcenter/Security/AzureSecurity), [Centro de segurança](https://azure.microsoft.com/services/security-center/), [registos de auditoria](https://azure.microsoft.com/blog/azure-audit-logs-ux-refresh/)e mais. |

<a name="function"></a>
## <a name="functions-vs-webjobs"></a>Funções vs. WebJobs

Vamos podem debater funções do Azure e Azure aplicação serviço WebJobs em conjunto porque são ambos os serviços de integração de *código primeiro* e concebido para programadores. Permitem-lhe executar um script ou um elemento de código em resposta a vários eventos, tal como [Novo armazenamento de Blobs](functions-bindings-storage.md) ou [um pedido de WebHook](functions-bindings-http-webhook.md). Aqui estão os respetivos semelhanças: 

- Ambos são criadas as na [Aplicação de serviço de Azure](../app-service/app-service-value-prop-what-is.md) e desfrutar de funcionalidades, como o [controlo de origem](../app-service-web/app-service-continuous-deployment.md), [autenticação](../app-service/app-service-authentication-overview.md)e [monitorização](../app-service-web/web-sites-monitor.md).
- São ambos os serviços com foco nos programador.
- Suporte padrão scripting tanto linguagens de programação.
- Têm ambos NuGet e NPM de suporte.

Funções é a evolução natural das WebJobs que TOA melhor características WebJobs e melhora a eles. As melhorias incluem: 

- Dev Center simplificada, teste e execução de código, diretamente no browser.
- Integração incorporada com os serviços mais Azure e serviços de terceiros 3 como [GitHub WebHooks](https://developer.github.com/webhooks/creating/).
- Pagamento-por utilização, sem necessidade de pagar a uma [Aplicação de serviço de plano](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md).
- Automático, [dimensionamento dinâmicos](functions-scale.md).
- Para clientes existentes do serviço de aplicação, em execução no plano de serviço de aplicação continua a ser possível (tirar partido dos recursos ao abrigo utilizada).
- Integração com aplicações de lógica.

A tabela seguinte resume as diferenças entre funções e WebJobs:

|                        | Funções                                                                                                                                                                | WebJobs                            |
|------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------|
| Dimensionamento                | Dimensionamento configurationless                                                                                                                                                | Dimensionar com o plano de serviço de aplicação        |
| Preços                | Pagar por utilização ou parte do plano de serviço de aplicação                                                                                                                                  | Parte do plano de serviço de aplicação           |
| Tipo de executar               | acionou, agendadas (por accionador temporizador)                                                                                                                                  | acionadas, contínuo, agendada   |
| Eventos de accionador         | [temporizador](functions-bindings-timer.md), [Azure DocumentDB](functions-bindings-documentdb.md), [Azure evento concentradores](functions-bindings-event-hubs), [HTTP/WebHook (GitHub, folga)](functions-bindings-http-webhook.md), [Aplicações do Azure aplicação serviço Mobile](functions-bindings-mobile-apps.md), [Azure notificação concentradores](functions-bindings-notification-hubs.md), [Azure Service Bus](functions-bindings-service-bus.md), [Armazenamento do Windows Azure](articles/functions-bindings-storage.md) | [Armazenamento azure](websites-dotnet-webjobs-sdk-storage-blobs-how-to.md), [Bus serviço Azure](websites-dotnet-webjobs-sdk-service-bus.md)         |
| Desenvolvimento de no browser | x                                                                                                                                                                        |                                    |
| Janela scripting       | experimental                                                                                                                                                             | x                                  |
| PowerShell             | experimental                                                                                                                                                             | x                                  |
| C#                     | x                                                                                                                                                                        | x                                  |
| F#                     | x                                                                                                                                                                        |                                    |
| Festa                   | experimental                                                                                                                                                             | x                                  |
| PHP                    | experimental                                                                                                                                                             | x                                  |
| Python                 | experimental                                                                                                                                                             | x                                  |
| JavaScript             | x                                                                                                                                                                        | x                                  |
| Java                   | experimental                                                                                                                                                             | x                                  |

Se deve utilizar funções ou WebJobs finalmente depende do que está a já fazer com a aplicação de serviço. Se tiver uma aplicação de serviço de aplicação para o qual pretende executar fragmentos de código e pretende geri-las em conjunto no mesmo ambiente DevOps, deve utilizar WebJobs. Se pretender executar fragmentos de código para outros serviços Azure ou até mesmo 3 terceiros aplicações, ou se pretende gerir o seu fragmentos de código de integração separadamente a partir do seu aplicações de serviço de aplicação, ou se pretende ligar o seu fragmentos de código através de uma aplicação de lógica, deverá tira partido de todos os melhoramentos nas funções.  

<a name="together"></a>
## <a name="flow-logic-apps-and-functions-together"></a>Fluxo, funções e Apps lógica em conjunto

Tal como mencionado anteriormente, qual o serviço é adequado para depende da sua situação. 

- Para uma optimização empresas simples, em seguida, utilize fluxo.
- Se o cenário de integração de é demasiado avançado para o fluxo de ou precisa de DevOps capacidades e certificações de segurança, em seguida, utilize lógica de aplicações.
- Se necessitar de um passo no seu cenário de integração de transformação altamente personalizada ou código especializado, em seguida, escrever uma aplicação de função e, em seguida, acionar uma função como uma ação na sua aplicação de lógica.

Pode ligar a uma aplicação de lógica num fluxo. Também pode ligar a uma aplicação de lógica e uma aplicação de lógica uma função numa função. Continue a integração entre o fluxo, aplicações de lógica e funções para melhorar a das horas extraordinárias. Pode criar algo num serviço de um e utilizá-lo em outros serviços. Por conseguinte, qualquer investimento que efetuar estes três tecnologias é vantajoso que.

## <a name="next-steps"></a>Próximos passos

Introdução ao cada um dos serviços ao criar o seu primeiro fluxo, lógica aplicação, função aplicação ou WebJob. Clique em qualquer uma das ligações seguintes:

- [Introdução ao Microsoft Flow](https://flow.microsoft.com/en-us/documentation/getting-started/)
- [Criar uma aplicação de lógica](../app-service-logic/app-service-logic-create-a-logic-app.md)
- [Criar a sua primeira função Azure](../azure-functions/functions-create-first-azure-function.md)
- [Implementar WebJobs utilizando o Visual Studio](../app-service-web/websites-dotnet-deploy-webjobs.md)

Em alternativa, obtenha mais informações sobre estes serviços de integração com as ligações seguintes:

- [Tirar partido da Azure funções e a aplicação de serviço de Azure para cenários de integração por Christopher Almeida](http://www.biztalk360.com/integrate-2016-resources/leveraging-azure-functions-azure-app-service-integration-scenarios/)
- [Integrações efetuadas simples por Carlos Lamanna](http://www.biztalk360.com/integrate-2016-resources/integrations-made-simple/)
- [Aplicações de lógica Live emissão na Web](http://aka.ms/logicappslive)
- [Microsoft fluxo perguntas mais frequentes](https://flow.microsoft.com/documentation/frequently-asked-questions/)
- [Recursos de documentação WebJobs Azure](../app-service-web/websites-webjobs-resources.md)
