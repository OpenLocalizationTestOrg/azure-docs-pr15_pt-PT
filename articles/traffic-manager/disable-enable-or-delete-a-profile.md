<properties
   pageTitle="Desativar, ativar ou eliminar um perfil do Gestor de tráfego | Microsoft Azure"
   description="Este artigo irá ajudá-lo a trabalhar com os perfis de Gestor de tráfego."
   services="traffic-manager"
   documentationCenter="na"
   authors="sdwheeler"
   manager="carmonm"
   editor="tysonn" />
<tags
   ms.service="traffic-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/18/2016"
   ms.author="sewhee" />
<!-- repub for nofollow -->

# <a name="disable-enable-or-delete-a-profile"></a>Desativar, ativar ou eliminar um perfil


Pode desativar um perfil existente do Gestor de tráfego para que não-lo será referenciem pedidos de utilizador finais configurados. Quando desativar um perfil do Gestor de tráfego, o próprio perfil e as informações contidas no perfil irá permanecer intacto e pode ser editado na interface do Gestor de tráfego. Quando quiser voltar a ativar o perfil, facilmente pode fazê-lo no Azure serão retomada portal e referências. Quando cria um perfil do Gestor de tráfego no portal do Azure, está ativada automaticamente.

## <a name="to-disable-a-profile"></a>Para desativar um perfil

1. Modificar o registo de recursos DNS no seu servidor DNS da Internet para utilizar o tipo de registo apropriado e o ponteiro para outro nome ou o endereço IP de uma localização específica na Internet. Por outras palavras, altere o registo de recursos DNS no seu servidor DNS da Internet para que já não utiliza um registo de recurso CNAME que aponta para o nome de domínio do seu perfil do Gestor de tráfego.
1. Tráfego deixarão de ser encaminhado para os pontos finais através das definições de perfil do Gestor de tráfego.
1. Selecione o perfil que pretende desativar. Para selecionar o perfil, na página Gestor de tráfego, realce o perfil clicando na coluna junto ao nome do perfil. Não clique no nome do perfil ou na seta junto ao nome do como isto vai levá-lo para a página de definições do perfil.
1. Após selecionar o perfil, clique em Desativar na parte inferior da página.

## <a name="to-enable-a-profile"></a>Para ativar um perfil

1. Selecione o perfil que pretende ativar. Para selecionar o perfil, na página Gestor de tráfego, realce o perfil clicando na coluna junto ao nome do perfil. Não clique no nome do perfil ou na seta junto ao nome do como isto vai levá-lo para a página de definições do perfil.
1. Após selecionar o perfil, clique em ativar na parte inferior da página.
1. Modificar o registo de recursos DNS no seu servidor DNS da Internet para utilizar o tipo de registo CNAME, que mapas do seu nome de domínio de empresa para o nome de domínio do seu perfil do Gestor de tráfego. Para mais informações, consulte o artigo [ponto um domínio da Internet da empresa para um domínio do Gestor de tráfego](traffic-manager-point-internet-domain.md).
1. Tráfego começa a ser direcionado para os pontos finais novamente.

## <a name="delete-a-profile"></a>Eliminar um perfil


1. Certifique-se de que o registo de recursos DNS no seu servidor DNS da Internet já não utiliza um registo de recurso CNAME que aponta para o nome de domínio do seu perfil do Gestor de tráfego.
1. Selecione o perfil que pretende eliminar. Para selecionar o perfil, na página Gestor de tráfego, realçar o perfil por
1. clicar na coluna junto ao perfil. Não clique no nome do perfil ou na seta junto ao nome do como isto vai levá-lo para a página de definições do perfil.
1. Após selecionar o perfil, clique em eliminar na parte inferior da página.

## <a name="next-steps"></a>Próximos passos

[Gestor de tráfego - desativar ou ativar um ponto final](disable-or-enable-an-endpoint.md)

[Configurar o método de encaminhamento de activação pós-falha](traffic-manager-configure-failover-routing-method.md)

[Configurar o método de encaminhamento de round robin](traffic-manager-configure-round-robin-routing-method.md)

[Configurar o método de encaminhamento de desempenho](traffic-manager-configure-performance-routing-method.md)

[Resolução de problemas de tráfego de Gestor degradado Estado](traffic-manager-troubleshooting-degraded.md)

