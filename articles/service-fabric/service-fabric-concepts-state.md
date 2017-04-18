<properties
   pageTitle="Definir e gerir o Estado | Microsoft Azure"
   description="Como definir e gerir o estado de serviço no ferro de serviço"
   services="service-fabric"
   documentationCenter=".net"
   authors="appi101"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="08/10/2016"
   ms.author="aprameyr"/>

# <a name="service-state"></a>Estado do serviço
**Estado do serviço** refere-se para os dados que o serviço requer para funcionar. Inclui as estruturas de dados e as variáveis que o serviço lê e escreve para trabalhar.

Considere um serviço de calculadora simples, por exemplo. Este serviço demora dois números e devolve os respetivos soma. Este é um serviço puramente sem estado que não tenha dados associados.

Agora, considere a Calculadora do mesmo, mas para além de informática soma, também tem um método para devolver a última soma que tem calculada. Este serviço está agora com estado – contém algumas Estado que escreve (quando-fórmula calcula uma soma nova) e lê a partir do (quando devolve a soma calculada última).

No Azure Service ferro, o primeiro serviço chama um serviço sem estado. O serviço do segundo chama um serviço de estado.

## <a name="storing-service-state"></a>Estado do serviço de armazenamento
Estado pode ser externalized ou localizado em conjunto com o código que é manipular o estado. Externalization do Estado de normalmente é efetuada ao utilizar um arquivo ou base de dados externa. No nosso exemplo Calculadora, este pode ser uma base de dados do SQL na qual está armazenado o resultado atual numa tabela. Todos os pedidos para calcular a soma executarem uma atualização nesta linha.

Também pode ser cocriação localizado com o código que manipula este código de estado. Serviços de estado no serviço ferro foram criados utilizando este modelo. Serviço ferro fornece a infraestrutura para se certificar de que este estado é altamente disponível e tolerância a em caso de falha falhas.

## <a name="next-steps"></a>Próximos passos

Para obter mais informações sobre os conceitos de ferro de serviço, consulte o seguinte:

- [Disponibilidade dos serviços de ferro de serviço](service-fabric-availability-services.md)

- [Escalabilidade dos serviços de ferro de serviço](service-fabric-concepts-scalability.md)

- [Criar a partições serviços ferro de serviço](service-fabric-concepts-partitioning.md)
