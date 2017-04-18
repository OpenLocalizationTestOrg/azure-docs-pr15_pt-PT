<properties
    pageTitle="No dados públicos para Azure evento concentradores | Microsoft Azure"
    description="Descrição geral dos controladores de evento importar a partir de exemplo de web"
    services="event-hubs"
    documentationCenter="na"
    authors="spyrossak"
    manager="timlt"
    editor=""/>

<tags 
    ms.service="event-hubs"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="08/25/2016"
    ms.author="spyros;sethm" />

# <a name="pulling-public-data-into-azure-event-hubs"></a>No dados públicos concentradores evento Azure

Nos cenários típicos que Internet coisas (IoT), tem de dispositivos que pode programar a dados de push para Azure a um concentrador de evento Azure ou um concentrador de IoT. Ambos esses concentradores são pontos de entrada para Azure para armazenar, analisar e visualizar com uma vasta gama de ferramentas disponibilizados no Microsoft Azure. No entanto, ambos os necessitam que emissão dados aos mesmos, formatado como JSON e protegidos de formas específicas. Isto reúne a seguinte pergunta. O que fazer se pretende trazer dados de origens públicas ou privadas, onde os dados são expostos como um serviço web ou um feed das algum tipo, mas não tiver a capacidade para alterar a forma como os dados são publicados? Considere a meteorologia ou tráfego ou cotações - não consegue indicar NOAA, ou WSDOT ou NASDAQ para configurar uma push para o seu centro de evento. Para resolver este problema, escrita e origem abrir uma amostra de pequenas na nuvem que pode modificar e implementar o que irá separar os dados a partir de alguns essa origem e enviá-lo para o seu centro de evento. A partir daí, que pode fazer o que quiser com o mesmo, assunto, claro, para os termos de licenciamento do produtor. Pode localizar a aplicação [aqui](https://azure.microsoft.com/documentation/samples/event-hubs-dotnet-importfromweb/).

Note que o código neste exemplo mostra apenas como separar os feeds de dados a partir da web típicos e como escrever a um concentrador de evento Azure. Isto não se destina a ser uma aplicação de produção e não tentativas foram efetuadas para que seja adequado para utilização num ambiente - é strictfly um DIY, exemplo com foco nos programador. Além disso, a existência deste exemplo não é como uma recomendação que deve **solicitar** dados para o Azure em vez de **push** -lo. Deve rever segurança, desempenho, a funcionalidade e fatores de custos antes de liquidar numa arquitetura de fim para fim.

## <a name="application-structure"></a>Estrutura de aplicação

A aplicação escrita c# e a [Descrição de exemplo](https://azure.microsoft.com/documentation/samples/event-hubs-dotnet-importfromweb/) contém todas as informações que necessita para modificar, criar e publicar a aplicação. As secções seguintes fornecem uma descrição geral do que faz a aplicação.

Vamos começar com partem do princípio de que tem acesso a um feed de dados. Por exemplo, poderá pretender separar os dados de tráfego de Washington Estado departamento de transporte ou os dados meteorológicos a partir do NOAA, para apresentar relatórios personalizados ou para combinar os dados com outros dados na sua aplicação. Também terá da configurou um concentrador de evento Azure e saber a ligação cadeia necessária para aceder à mesma.

Quando a solução GenericWebToEH é iniciado, lê um ficheiro de configuração (App) para obter um número de coisas:

1. O URL ou uma lista de URLs, para o site de publicação os dados. Idealmente, este é um site que publica dados em JSON, como aqueles referenciado por WSDOT [aqui](http://www.wsdot.wa.gov/Traffic/api/). 
2. Credenciais para o URL, se necessário. Muitas origens públicas não necessitam de credenciais, ou pode colocar as credenciais na cadeia de URL. Outros requerem que forneceu separadamente. (Tenha em atenção que só pode especificar um conjunto de credenciais nesta aplicação, para que apenas irá funcionar se especificar apenas um URL, não uma lista de URLs.)
3. A cadeia de ligação e o nome do centro do evento no espaço de nomes evento concentradores, aos quais prime os dados. Pode encontrar estas informações no portal do Azure.
4. Um suspensão intervalo, em milissegundos, para o intervalo entre o site de dados públicos de consulta. Esta definição requer alguns pensamento. Se as inquérito demasiado com pouca frequência, pode perder dados; por outro lado, se as inquérito demasiado frequentemente, poderá obter muitos dados repetitivos ou pior ainda, pode estar bloqueado como um bot nefarious. Considere a frequência a origem de dados é atualizada - dados meteorologia ou tráfego poderão ser atualizados a cada 15 minutos, mas cotações talvez em poucos segundos, dependendo do local onde obtê-lo. 
5. Um sinalizador para indicar a aplicação se os dados está a chegar como JSON ou XML. Uma vez que necessita transmitir os dados a um concentrador de evento, a aplicação tem um módulo para converter XML JSON antes de enviar.

Depois de ler o ficheiro de configuração, a aplicação compõem um ciclo - aceder ao web site público, converter os dados se for necessário, ao escrevê-lo no seu centro de evento e, em seguida, a aguardar o intervalo de sono das antes de fazê-lo recomeçar novamente. Especificamente:

  * Ler o Web site público. Para receção pronto para enviar dados a instância de classe RawXMLWithHeaderToJsonReader é utilizada a partir do Azure/GenericWebToEH/ApiReaders/RawXMLWithHeaderToJsonReader.cs. Lê sequência de origem no método GetData() e, em seguida, divide-la para menores (ou seja, records) utilizando GetXmlFromOriginalText. 
  Este método vai ler XML assim como bem formadas JSON ou JSON de matriz. Em seguida, processamento é iniciado utilizando MergeToXML configuração a partir da App (predefinição = vazio).
  * Os dados de receção e envio são implementados como um ciclo no método Process() no Program.cs. 
  Depois de receber resultados de saída do GetData(), o método enqueues valores separados por ao concentrador de evento.

## <a name="next-steps"></a>Próximos passos

Para implementar a solução, clonar ou transferir a aplicação de [GenericWebToEH](https://azure.microsoft.com/documentation/samples/event-hubs-dotnet-importfromweb/) , edite o ficheiro App, construi-lo e finalmente publicá-lo. Assim que tiver publicado a aplicação, pode vê-lo em execução no portal do Azure clássico em serviços em nuvem e pode alterar algumas das definições de configuração (como o destino da concentrador de evento e o intervalo de sono das) na caixa de diálogo **Configurar** .

Consulte o artigo exemplos de evento concentradores mais na [Galeria de amostras Azure](https://azure.microsoft.com/documentation/samples/?service=event-hubs) e no [MSDN](https://code.msdn.microsoft.com/site/search?query=event%20hubs&f%5B0%5D.Value=event%20hubs&f%5B0%5D.Type=SearchText&ac=5).
