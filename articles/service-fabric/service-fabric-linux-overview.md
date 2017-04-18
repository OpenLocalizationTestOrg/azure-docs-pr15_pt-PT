<properties
   pageTitle="Ferro Azure Service no Linux | Microsoft Azure"
   description="Clusters de serviço ferro suportam Linux e Java, que significa que poderá implementar e aplicações de serviço ferro anfitriãs escritas Java e c# no Linux."
   services="service-fabric"
   documentationCenter=".net"
   authors="mani-ramaswamy"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="Java"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/26/2016"
   ms.author="SubramaR"/>

# <a name="service-fabric-on-linux"></a>Serviço ferro em Linux

Pré-visualização do serviço ferro no Linux permite-lhe construir, implementar e gerir as aplicações do altamente disponíveis, altamente dimensionáveis no Linux tal como faria no Windows. Os quadros ferro de serviço (serviços fiáveis e intervenientes fiável) estão disponíveis no Java no Linux além c# (.NET Core).  Também pode criar os [Serviços executável convidado](service-fabric-deploy-existing-app.md) com qualquer idioma ou framework. Além disso, a pré-visualização também suporta orquestrar contentores Docker. Podem executar contentores docker executáveis convidado ou serviços de serviço ferro nativos, utilizam os quadros ferro de serviço.

Serviço ferro no Linux equivale conceptual a ferro de serviço no Windows (exceto os detalhes do sistema operativo e com a linguagem de programação). Por conseguinte, a maior parte das nossa [documentação existente](http://aka.ms/servicefabricdocs) aplica-se para o ajudar a familiarizar-se com a tecnologia.

> [AZURE.VIDEO service-fabric-linux-preview]

## <a name="supported-operating-systems-and-programming-languages"></a>Sistemas operativos e linguagens de programação suportadas

Pré-visualização do limitado suporta a criação de clusters de desenvolvimento de uma caixa para além de múltiplos máquinas clusters no Azure executar Ubuntu servidor 16.04. Pré-visualização do suporta os intervenientes fiável e os quadros serviços sem estado fiáveis do Java e c# além das executáveis convidado e orquestrar contentores Docker.  

>[AZURE.NOTE] Fiáveis coleções de sites não são suportadas no Linux ainda. Não são suportados clusters sozinho de frutas quer - apenas uma caixa e Azure Linux múltiplos máquinas clusters são suportados na pré-visualização.

## <a name="supported-tooling"></a>Ferramentas suportadas

A pré-visualização suporta a interação com o cluster através do clip Azure. Para os programadores de Java, integração com o Eclipse e Yeoman é fornecida com Eclipse suportada à Linux e nos x. A integração dos x utiliza uma VM Linux em definições avançadas através do vagrant. Para c# os programadores, integração com o Yeoman é fornecida para gerar modelos da aplicação.

## <a name="next-steps"></a>Próximos passos


1. Familiarize-se com os quadros programação [Intervenientes fiável](service-fabric-reliable-actors-introduction.md) e [Serviços fiáveis](service-fabric-reliable-services-introduction.md) .

2. [Preparar o seu ambiente de desenvolvimento em Linux](service-fabric-get-started-linux.md)

3. [Preparar o seu ambiente de desenvolvimento nos x](service-fabric-get-started-mac.md)

4. [Criar uma aplicação de serviço ferro Java primeira no Linux](service-fabric-create-your-first-linux-application-with-java.md)
