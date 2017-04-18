<properties
    pageTitle="Introdução ao reencaminhamento híbrido ligações | Microsoft Azure"
    description="Como escrever uma aplicação de consola do c# para ligações de híbrido"
    services="service-bus"
    documentationCenter=".net"
    authors="jtaubensee"
    manager="timlt"
    editor=""/>

<tags
    ms.service="service-bus"
    ms.devlang="tbd"
    ms.topic="hero-article"
    ms.tgt_pltfrm="dotnet"
    ms.workload="na"
    ms.date="10/28/2016"
    ms.author="jotaub"/>

# <a name="get-started-with-relay-hybrid-connections"></a>Introdução ao reencaminhamento híbrido ligações

[AZURE.INCLUDE [relay-selector-hybrid-connections](../../includes/relay-selector-hybrid-connections.md)]

## <a name="what-will-be-accomplished"></a>O que vai ser feito

Uma vez que híbrido ligações requer um cliente e um componente de servidor, podemos irá criar aplicações de consola de duas neste tutorial. Eis os passos:

1. Crie um espaço de nomes de reencaminhamento, utilizando o portal Azure.

2. Crie uma ligação de híbrido através do portal Azure.

3. Escreva um servidor de aplicação de consola para receber mensagens.

4. Escreva um cliente de aplicação de consola para enviar mensagens.

## <a name="prerequisites"></a>Pré-requisitos

1. No [visual Studio 2013 ou o Visual Studio 2015](http://www.visualstudio.com). Os exemplos neste tutorial utilizam Visual Studio 2015.

2. Uma subscrição do Azure.

[AZURE.INCLUDE [create-account-note](../../includes/create-account-note.md)]

## <a name="1-create-a-namespace-using-the-azure-portal"></a>1. a criar um espaço de nomes utilizando o portal Azure

Se já tiver um espaço de nomes de reencaminhamento criado, ir para a secção [criar uma ligação de híbrido através do portal Azure](#2-create-a-hybrid-connection-using-the-azure-portal) .

[AZURE.INCLUDE [relay-create-namespace-portal](../../includes/relay-create-namespace-portal.md)]

## <a name="2-create-a-hybrid-connection-using-the-azure-portal"></a>2. a criar uma ligação de híbrido através do portal Azure

Se já tiver uma ligação de híbrido criada, ir para a secção [criar uma aplicação de servidor](#3-create-a-server-application-listener) .

[AZURE.INCLUDE [relay-create-hybrid-connection-portal](../../includes/relay-create-hybrid-connection-portal.md)]

## <a name="3-create-a-server-application-listener"></a>3. criar uma aplicação de servidor (escuta)

Para ouvir e receber mensagens do reencaminhamento, podemos irá escrever uma aplicação de consola c# utilizando o Visual Studio.

[AZURE.INCLUDE [relay-hybrid-connections-dotnet-get-started-server](../../includes/relay-hybrid-connections-dotnet-get-started-server.md)]

## <a name="4-create-a-client-application-sender"></a>4. criar uma aplicação de cliente (remetente)

Para enviar mensagens para o reencaminhamento, podemos irá escrever uma aplicação de consola c# utilizando o Visual Studio.

[AZURE.INCLUDE [relay-hybrid-connections-dotnet-get-started-client](../../includes/relay-hybrid-connections-dotnet-get-started-client.md)]

## <a name="5-run-the-applications"></a>5. executar as aplicações

1. Execute a aplicação de servidor.

2. Execute a aplicação de cliente e introduza algum texto.

3. Certifique-se de que a consola de aplicação do servidor exporta o texto que foi introduzido na aplicação cliente.

![aplicações em execução](./media/relay-hybrid-connections-dotnet-get-started/running-applications.png)

Parabéns, ter criado uma aplicação híbrida ligações de ponto a ponto.

## <a name="next-steps"></a>Passos seguintes:

- [Reencaminhamento perguntas mais frequentes](relay-faq.md)
- [Criar um espaço de nomes](relay-create-namespace-portal.md)
- [Introdução ao nó](relay-hybrid-connections-node-get-started.md)