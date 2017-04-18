<properties
   pageTitle="Azure Máquina Virtual DotNet Core Tutorial 1 | Microsoft Azure"
   description="Tutorial do Azure Máquina Virtual DotNet Core"
   services="virtual-machines-windows"
   documentationCenter="virtual-machines"
   authors="neilpeterson"
   manager="timlt"
   editor="tysonn"
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-machines-windows"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="infrastructure-services"
   ms.date="10/21/2016"
   ms.author="nepeters"/>

# <a name="automating-application-deployments-to-azure-virtual-machines"></a>Automatizar implementações da aplicação para máquinas virtuais do Azure

Esta série de quatro partes detalhes implementar e configurar o recurso Azure e utilizar Azure recurso gerir modelos de aplicações. Nesta série, um modelo de exemplo é implementado e o modelo de implementação analisadas. O objetivo desta série é para informe sobre a relação entre recursos Azure e para fornecer mãos a experiência de implementar modelos de Gestor de recursos do Azure totalmente integrada. Este documento assume um nível de dados de conhecimento com o Gestor de recursos do Azure básico, antes de iniciar este tutorial familiarizar-se com conceitos básicos do Gestor de recursos do Azure.

## <a name="music-store-application"></a>Aplicação de música de arquivo

Amostra utilizada nesta série é um .net aplicação Core simulação de uma loja de música compras experiência. Pode ser implementada esta aplicação ao sistema virtual uma Linux ou o Windows, implementações do exemplo foram criadas para ambos. A aplicação inclui uma aplicação web e uma base de dados do SQL. Antes dos artigos nesta série de leitura, implemente a aplicação com o botão de implementação que se encontram nesta página. Quando implementado completamente, a arquitetura de aplicação / Azure aspeto o diagrama seguinte. 

O modelo de Gestor de recursos do arquivo de música pode ser encontrado aqui, [Música da loja Windows modelo](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-windows)

![Aplicação da loja de música](./media/virtual-machines-windows-dotnet-core/music-store.png)

Cada um destes componentes, incluindo o modelo associar JSON é examinar nos seguintes quatro artigos.

- [**Arquitetura de aplicação**](./virtual-machines-windows-dotnet-core-2-architecture.md) – componentes da aplicação como web sites e bases de dados tem de ser alojados em recursos de computador Azure, tais como máquinas virtuais e bases de dados Azure SQL. Este documento explica a necessidade de cluster de mapeamento, para recursos Azure e implementar estes recursos com um modelo de Gestor de recursos do Azure. 

- [**Segurança e acesso**](./virtual-machines-windows-dotnet-core-3-access-security.md) – quando alojamento aplicações no Azure, é necessário ter em consideração como a aplicação é acedida e acesso de componentes de aplicação como as diferentes entre si. Este documento detalhes fornecendo e proteger o acesso à internet para uma aplicação e acesso entre componentes da aplicação.

- [**Disponibilidade e a escala**](./virtual-machines-windows-dotnet-core-4-availability-scale.md) – disponibilidade e a escala referem-se para a capacidade de aplicações para se manter a ser executado durante o tempo de inatividade da infraestrutura e a capacidade de dimensionar cluster de recursos para cumprir o pedido da aplicação. Este detalhes do documento os componentes necessários para implementar uma balanceamento de carga e aplicação altamente disponível.

- [**Implementação da aplicação**](./virtual-machines-windows-dotnet-core-5-app-deployment.md) - quando implementar aplicações para máquinas virtuais do Azure, deve ser considerado o método pelo qual os binários da aplicação estão instalados no computador Virtual. Este documento apresenta detalhes sobre a instalação da aplicação automatização utilizar extensões de Script do Azure Máquina Virtual personalizada.

É o objetivo quando desenvolver o Gestor de recursos do Azure modelos automatizar a implementação do Azure infraestrutura e a instalação e configuração de todas as aplicações que está a ser alojados neste infraestrutura Azure. Trabalho através destes artigos fornece um exemplo desta experiência.

## <a name="deploy-the-music-store-application"></a>Implementar a aplicação da loja de música

A aplicação de arquivo de música pode ser implementada utilizar este botão.

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FMicrosoft%2Fdotnet-core-sample-templates%2Fmaster%2Fdotnet-core-music-windows%2Fazuredeploy.json" target="_blank">
    <img src="http://azuredeploy.net/deploybutton.png"/>
</a>

O modelo de Gestor de recursos do Azure requer os seguintes valores de parâmetro.

|Nome do parâmetro |Descrição   |
|---|---|
|ADMINUSERNAME   | Nome de utilizador de administração que é utilizado na máquina virtual e a base de dados do SQL Azure.  |
|ADMINPASSWORD | Palavra-passe que é utilizado na máquina de Virtual do Azure e base de dados SQL.  |
|NUMBEROFINSTANCES | O número de máquinas virtuais para ser criado. Cada um dos seguintes máquinas virtuais do anfitrião da aplicação web do arquivo de música e todo o tráfego é distribuído ao longo-los. |
|PUBLICIPADDRESSDNSNAME | Nome DNS globalmente exclusivo associado ao endereço IP público. |

Quando tiver concluído a implementação do modelo, navegue para o endereço IP público utilizando qualquer browser da internet. O .net Core música site será apresentada.

## <a name="next-steps"></a>Próximos passos

<hr>

[Passo 1 - arquitetura de aplicação com modelos de Gestor de recursos do Azure](./virtual-machines-windows-dotnet-core-2-architecture.md)

[Passo 2 - Access e a segurança em modelos de Gestor de recursos do Azure](./virtual-machines-windows-dotnet-core-3-access-security.md)

[Passo 3 - disponibilidade e a escala em modelos de Gestor de recursos do Azure](./virtual-machines-windows-dotnet-core-4-availability-scale.md)

[Passo 4 - implementação da aplicação com modelos de Gestor de recursos do Azure](./virtual-machines-windows-dotnet-core-5-app-deployment.md)


