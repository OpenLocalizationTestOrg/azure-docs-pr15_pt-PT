<properties 
    pageTitle="Como adicionar codificação unidades" 
    description="Saiba como adicionar unidades codificação com .NET"  
    services="media-services" 
    documentationCenter="" 
    authors="juliako" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/01/2016"
    ms.author="juliako;milangada;gtrifonov"/>


#<a name="how-to-scale-encoding-with-net-sdk"></a>Como dimensionar codificação com .NET SDK

> [AZURE.SELECTOR]
- [Portal](media-services-portal-scale-media-processing.md )
- [.NET](media-services-dotnet-encoding-units.md)
- [RESTO](https://msdn.microsoft.com/library/azure/dn859236.aspx)
- [Java](https://github.com/southworkscom/azure-sdk-for-media-services-java-samples)
- [PHP](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices)

##<a name="overview"></a>Descrição geral

>[AZURE.IMPORTANT] Certifique-se rever o tópico [Descrição geral](media-services-scale-media-processing-overview.md) para obter mais informações sobre dimensionamento tópico de processamento de multimédia.
 
Para alterar o tipo de unidade reservadas e o número de codificação de unidades reservadas utilizando .NET SDK, faça o seguinte:

    IEncodingReservedUnit encodingS1ReservedUnit = _context.EncodingReservedUnits.FirstOrDefault();
    encodingS1ReservedUnit.ReservedUnitType = ReservedUnitType.Basic; // Corresponds to S1
    encodingS1ReservedUnit.Update();
    Console.WriteLine("Reserved Unit Type: {0}", encodingS1ReservedUnit.ReservedUnitType);
    
    encodingS1ReservedUnit.CurrentReservedUnits = 2;
    encodingS1ReservedUnit.Update();
    
    Console.WriteLine("Number of reserved units: {0}", encodingS1ReservedUnit.CurrentReservedUnits);

##<a name="opening-a-support-ticket"></a>Abrir um bilhetes de suporte

Por predefinição todas as contas dos serviços de multimédia podem Dimensionar para até 25 codificação e 5 a pedido transmissão reservadas unidades. Pode pedir um limite superior ao abrir um bilhetes de suporte.

###<a name="open-a-support-ticket"></a>Abrir um bilhetes de suporte

Para abrir um suporte bilhetes, faça o seguinte:

1. Clique em [obter suporte](https://manage.windowsazure.com/?getsupport=true). Se não tiver sessão iniciada, vai ser-lhe para introduzir as suas credenciais.

1. Selecione a sua subscrição.

1. Em tipo de suporte, selecione "Técnicos".

1. Clique em "Criar bilhetes".

1. Selecione "Azure dos serviços de multimédia" na lista de produtos apresentado na página seguinte.

1. Selecione "tipo de problema" que altura é adequado para o problema.

1. Clique em Continue.

1. Siga as instruções na página seguinte e, em seguida, introduza os detalhes sobre o problema.

1. Clique em Submeter para abrir a permissão.



##<a name="media-services-learning-paths"></a>Caminhos de aprendizagem dos serviços de multimédia

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fornecer comentários

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
