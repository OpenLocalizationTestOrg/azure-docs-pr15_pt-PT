<properties
    pageTitle="Lista nos serviços do BizTalk de tarefas de administração e desenvolvimento | Microsoft Azure"
    description="Planeamento e a tarefa de ajuda para implementar o Azure BizTalk Services."
    services="biztalk-services"
    documentationCenter=""
    authors="msftman"
    manager="erikre"
    editor=""/>

<tags
    ms.service="biztalk-services"
    ms.workload="integration"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/15/2016"
    ms.author="deonhe"/>

# <a name="administration-and-development-task-list-in-biztalk-services"></a>Administração e lista de tarefas do desenvolvimento nos serviços do BizTalk  

## <a name="getting-started"></a>Introdução
Quando trabalha com os serviços do Microsoft Azure BizTalk, existem vários no local e componentes baseado na nuvem a ter em conta. Para começar, tenha em consideração o fluxo de processo seguintes:  

|Passo|Quem é o responsável|Tarefa|Ligações relacionadas|
|----|----|----|----|
|1.|Administrador|Criar a subscrição do Microsoft Azure utilizando uma conta Microsoft ou uma conta institucional|[Azure portal clássico](http://go.microsoft.com/fwlink/p/?LinkID=213885)|
|2.|Administrador|Criar ou aprovisionar um serviço de BizTalk.|[Criar um serviço de BizTalk através do portal clássico Azure](http://go.microsoft.com/fwlink/p/?LinkID=302280)|
|3.|Administrador|Registe-se de que ou implementação de serviços de BizTalk da sua empresa|[Registar e atualizar uma implementação BizTalk serviço no Portal de serviços de BizTalk](https://msdn.microsoft.com/library/azure/hh689837.aspx)|
|4.|Administrador|Aplica-se que a aplicação utiliza BizTalk placa de serviço para se ligar a um sistema de linha de negócio (LOB) no local ou utiliza uma fila ou o destino de tópico.  Crie o espaço de nomes do serviço Azure Bus. Conceder esta espaço de nomes, nome do serviço Bus emissor e valores de chave do serviço Bus emissor ao programador.|[Como: criar ou modificar um espaço de nomes de serviço do serviço Bus](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md) e [obter o nome do emissor e valores de chave emissor](biztalk-issuer-name-issuer-key.md)|
|5.|Para programadores|Instalar o SDK e crie o projeto de serviço de BizTalk no Visual Studio.|[Instale os serviços de Azure BizTalk SDK](https://msdn.microsoft.com/library/azure/hh689760.aspx) e [Criar Rich pontos finais mensagens no Azure](https://msdn.microsoft.com/library/azure/hh689766.aspx)|
|6.|Para programadores|Implemente o seu serviço de BizTalk projeto no seu serviço de BizTalk alojado no Azure.|[Implementar e atualizar o projeto de serviços de BizTalk](https://msdn.microsoft.com/library/azure/hh689881.aspx)|
|7.|Administrador|Se aplica se estiver a utilizar o editar.  Pode adicionar parceiros e criar acordos no Portal de serviços do Microsoft Azure BizTalk. Quando cria um contrato, pode adicionar o bridge e/ou transformações criadas pelo programador às definições do contrato.|[Configurar o editar, AS2 e EDIFACT no Portal de serviços de BizTalk](https://msdn.microsoft.com/library/azure/hh689853.aspx)|
|8.|Administrador|Utilizando o Portal clássico Azure, monitorize o estado de funcionamento do serviço BizTalk, incluindo métricas de desempenho.|[BizTalk serviços: Separadores de Dashboard, Monitor e escala](http://go.microsoft.com/fwlink/p/?LinkID=302281)|
|9.|Administrador|No portal Microsoft Azure BizTalk serviços, gerir erros utilizados por BizTalk serviços e controlar mensagens à medida que são processadas pelos ficheiros bridge.|[Utilizando o Portal de serviços de BizTalk](https://msdn.microsoft.com/library/azure/dn874043.aspx)|
|10.|Administrador|Crie um plano de cópia de segurança para agregar o serviço de BizTalk.|[Continuidade de negócios e recuperação de falhas nos serviços do BizTalk](https://msdn.microsoft.com/library/azure/dn509557.aspx) |  
## <a name="next-steps"></a>Próximos passos
[Tutoriais e amostras](https://msdn.microsoft.com/library/azure/hh689895.aspx)

[Criar o projeto no Visual Studio](https://msdn.microsoft.com/library/azure/hh689811.aspx)

[Instale os serviços de Azure BizTalk SDK](https://msdn.microsoft.com/library/azure/hh689760.aspx)

## <a name="concepts"></a>Conceitos
[Criar o projeto no Visual Studio](https://msdn.microsoft.com/library/azure/hh689811.aspx)  
[Editar, AS2 e EDIFACT mensagens (para as empresas)](https://msdn.microsoft.com/library/azure/hh689898.aspx)  
## <a name="other-resources"></a>Outros recursos  
[Adicionar origem, destino e Bridge pontos finais de mensagens](https://msdn.microsoft.com/library/azure/hh689877.aspx)  
[Saiba e criar mapas de mensagem e transformações](https://msdn.microsoft.com/library/azure/hh689905.aspx)  
[Utilizar o serviço de placa BizTalk (BAS)](https://msdn.microsoft.com/library/azure/hh689889.aspx)  
[Serviços de Azure BizTalk](http://go.microsoft.com/fwlink/p/?LinkID=303664)
