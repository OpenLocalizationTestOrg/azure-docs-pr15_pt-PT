<properties
    pageTitle="Exemplo infraestrutura guiadas | Microsoft Azure"
    description="Saiba mais sobre as diretrizes de estrutura e implementação chaves para implementar uma infraestrutura de exemplo no Azure."
    documentationCenter=""
    services="virtual-machines-linux"
    authors="iainfoulds"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/08/2016"
    ms.author="iainfou"/>

# <a name="example-azure-infrastructure-walkthrough"></a>Tutorial do Azure infraestrutura de exemplo

[AZURE.INCLUDE [virtual-machines-linux-infrastructure-guidelines-intro](../../includes/virtual-machines-linux-infrastructure-guidelines-intro.md)] 

Este artigo explica edifício saída uma infraestrutura da aplicação de exemplo. Vamos detalhe estruturar uma infraestrutura de uma loja online simple que conjuga todas as linhas de orientação e decisões à volta de convenções de nomenclatura, conjuntos de disponibilidade, redes virtuais e Balanceadores de carga e implementar realmente máquinas virtuais (VMs).


## <a name="example-workload"></a>Carga de trabalho de exemplo

Pretende da Adventure Works ciclos criar uma aplicação do arquivo online no Azure que consiste em:

- Dois nginx servidores a executar o cliente front-end de uma camada web
- Dois nginx servidores, processamento de dados e encomendas de uma camada de aplicação
- Parte de servidores de MongoDB duas de um cluster sharded para armazenar dados de produtos e encomendas de uma camada de base de dados
- Dois controladores de domínio do Active Directory para fornecedores de uma camada de autenticação e de contas de cliente
- Todos os servidores de estão localizados nas duas sub-redes:
    - uma sub-rede front-end para os servidores web 
    - uma sub-rede back-end para os servidores de aplicações, MongoDB cluster e controladores de domínio

![Diagrama de diferentes camadas para infraestrutura de aplicação](./media/virtual-machines-common-infrastructure-service-guidelines/example-tiers.png)

Receção segura tráfego da web deve ser balanceamento de carga entre os servidores web como clientes procurar a loja online. Ordem de processamento de tráfego sob a forma de HTTP pedidos a partir da web servidores devem ser balanceamento de carga entre os servidores de aplicações. Para além disso, a infraestrutura de deve ser concebida para elevada disponibilidade.

A estrutura resultante tem de incorporar:

- Uma subscrição do Azure e conta
- Um grupo de recursos único
- Contas de armazenamento
- Uma rede virtual com duas sub-redes
- Disponibilidade de conjuntos de VMs com uma função semelhante
- Máquinas virtuais

Todos os anteriores, siga estes convenções de nomenclatura:

- Adventure Works ciclos utilizações **[carga de trabalho]-[localização]-[recurso Azure]** como um prefixo
    - Neste exemplo, "**azos**da" (Azure Online Store) é o nome de carga de trabalho do IT e "**utilizar**" (Leste dos EUA 2) é a localização
- Armazenamento contas utilizar adventureazosusesa**[descrição]**
    - 'Aventura' foi adicionada ao prefixo para fornecer a exclusividade e nomes de conta de armazenamento não suportam a utilização de hífenes.
- As redes virtuais utilizam AZOS-utilizar-VN**[número]**
- Conjuntos de disponibilidade utilizam azos-utilizar-como-**[função]**
- Nomes de máquina virtual utilizam azos-utilizar-vm -**[vmname]**


## <a name="azure-subscriptions-and-accounts"></a>Subscrições do Azure e contas

Da Adventure Works ciclos está a utilizar os respetivos subscrição Enterprise, com o nome da Adventure Works subscrição Enterprise para fornecer faturação para este carga de trabalho.


## <a name="storage-accounts"></a>Contas de armazenamento

Da Adventure Works ciclos determinados que eles necessários duas contas de armazenamento:

- **adventureazosusesawebapp** para o armazenamento padrão dos servidores web, servidores de aplicações e controladores de domínio e discos os respetivos dados.
- **adventureazosusesadbclust** para o armazenamento de Premium dos servidores de sharded cluster MongoDB e os seus discos dados.


## <a name="virtual-network-and-subnets"></a>Rede virtual e sub-redes

Uma vez que a rede virtual não é necessário em curso conectividade à rede no local da Adventure ciclos de trabalho, eles decidiram numa rede virtual apenas na nuvem.

Uma rede virtual apenas na nuvem eles criarem com as seguintes definições através do portal Azure:

- Nome: AZOS-utilizar-VN01
- Localização: Leste dos EUA 2
- Espaço de endereços de rede virtual: 10.0.0.0/8
- Primeira sub-rede:
    - Nome: front-end
    - Espaço de endereços: 10.0.1.0/24
- Segunda sub-rede:
    - Nome: back-end
    - Espaço de endereços: 10.0.2.0/24


## <a name="availability-sets"></a>Conjuntos de disponibilidade

Para manter alta disponibilidade de todas as quatro camadas da sua arquivo online, da Adventure Works ciclos decidido nos conjuntos de disponibilidade quatro:

- **azos utilizar como web** para os servidores de web
- **azos utilize como aplicação** para os servidores de aplicações
- **azos utilizar como db** para os servidores do cluster sharded MongoDB
- **azos utilizar como CC** para os controladores de domínio


## <a name="virtual-machines"></a>Máquinas virtuais

Da Adventure Works ciclos decidido nos nomes das seguintes para os respetivos VMs Azure:

- **azos-utilizar vm web01** para o primeiro servidor web
- **azos-utilizar vm web02** para o segundo servidor web
- **azos-utilizar vm app01** para o primeiro servidor de aplicação
- **azos-utilizar vm app02** para o segundo servidor de aplicação
- **azos-utilizar vm db01** para o primeiro servidor de MongoDB no cluster
- **azos-utilizar vm db02** para o segundo servidor de MongoDB no cluster
- **azos-utilizar vm dc01** para o primeiro controlador de domínio
- **azos-utilizar vm dc02** do segundo controlador de domínio

Segue-se a configuração resultante.

![Infraestrutura de aplicação final implementada no Azure](./media/virtual-machines-common-infrastructure-service-guidelines/example-config.png)

Esta configuração incorpora:

- Uma rede virtual apenas na nuvem com duas sub-redes (front-end e back-end)
- Duas contas de armazenamento
- Quatro conjuntos de disponibilidade, uma para cada camada do arquivo online
- As máquinas virtuais para as quatro camadas
- Um conjunto de balanceamento de carga externa para o tráfego de web baseadas em HTTPS da Internet para os servidores web
- Conjunto de tráfego de web não encriptado dos servidores web para os servidores de aplicações de balanceamento de uma carga interna
- Um grupo de recursos único


## <a name="next-steps"></a>Próximos passos

[AZURE.INCLUDE [virtual-machines-linux-infrastructure-guidelines-next-steps](../../includes/virtual-machines-linux-infrastructure-guidelines-next-steps.md)] 