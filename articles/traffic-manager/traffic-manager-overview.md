<properties
    pageTitle="O que é o Gestor de tráfego | Microsoft Azure"
    description="Este artigo irá ajudá-lo a perceber o que é o Gestor de tráfego e, se se trata da escolha de encaminhamento de tráfego direita para a sua aplicação"
    services="traffic-manager"
    documentationCenter=""
    authors="sdwheeler"
    manager="carmonm"
    editor=""
/>
<tags
    ms.service="traffic-manager"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="infrastructure-services"
    ms.date="10/11/2016"
    ms.author="sewhee"
/>

# <a name="overview-of-traffic-manager"></a>Descrição geral do Gestor de tráfego

Gestor de tráfego do Microsoft Azure permite-lhe controlar a distribuição de tráfego de utilizador para pontos finais de serviço no centros de dados diferentes. Pontos finais de serviço suportados pelo tráfego Manager incluem Azure VMs, aplicações Web e dos serviços em nuvem. Também pode utilizar o Gestor de tráfego com os pontos finais externos, não Azure.

Gestor de tráfego utiliza o sistema de nomes de domínio (DNS) para direcionar pedidos de clientes para o ponto final de mais adequado com base num [método de encaminhamento de tráfego](traffic-manager-routing-methods.md) e o estado de funcionamento dos pontos finais. Gestor de tráfego fornece um intervalo de encaminhamento de tráfego métodos para se adequar às necessidades da aplicação diferente, ponto final o estado de funcionamento de [monitorização](traffic-manager-monitoring.md)e activação pós-falha automática. Gestor de tráfego é flexível para a falha, incluindo a falha de uma região Azure completa.

## <a name="traffic-manager-benefits"></a>Benefícios do Gestor de tráfego

Gestor de tráfego pode ajudá-lo:

- **Melhorar a disponibilidade das aplicações críticas**

    Gestor de tráfego proporciona elevada disponibilidade para as suas aplicações ao seu os pontos finais de monitorização e fornecer activação pós-falha automática quando um ponto final vai para baixo.

- **Melhorar a capacidade de resposta para as aplicações de alto desempenho**

    Azure permite-lhe executar serviços em nuvem ou Web sites centros de dados localizados em todo o mundo. Gestor de tráfego melhora a resposta da aplicação ao direcionar tráfego para o ponto final com a latência mais baixa de rede para o cliente.

- **Executar a manutenção do serviço sem tempo de inatividade**

    Pode executar operações de manutenção planeada na suas aplicações sem tempo de inatividade. Gestor de tráfego encaminha o tráfego para os pontos finais alternativos enquanto a manutenção está em curso.

- **Combinar no local e aplicações baseadas na nuvem**

    O Gestor de tráfego suporta externo, permitindo-lhe ser utilizado com híbrido pontos finais de que não sejam Azure na nuvem e no local híbridas, incluindo a "rajada-para-nuvem," "migrar-para-nuvem," e "activação pós-falha a nuvem" cenários.

- **Distribuir o tráfego para implementações complexas, grandes**

    Utilizar [perfis do Gestor de tráfego aninhadas](traffic-manager-nested-profiles.md), encaminhamento de tráfego métodos podem ser combinados para criar regras sofisticadas e flexíveis para suportar as necessidades de implementações maiores e mais complexas.

[AZURE.INCLUDE [load-balancer-compare-tm-ag-lb-include.md](../../includes/load-balancer-compare-tm-ag-lb-include.md)]

## <a name="next-steps"></a>Próximos passos

- Saiba mais sobre [como funciona o Gestor de tráfego](traffic-manager-how-traffic-manager-works.md).

- Obter informações sobre como desenvolver aplicações de alta disponibilidade utilizando o [Gestor de tráfego de monitorização de ponto final](traffic-manager-monitoring.md).

- Saiba mais sobre o [Encaminhamento de tráfego métodos](traffic-manager-routing-methods.md) suportados pelo Gestor de tráfego.

- [Criar um perfil do Gestor de tráfego](traffic-manager-manage-profiles.md).

