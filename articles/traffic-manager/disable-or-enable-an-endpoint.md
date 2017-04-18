<properties
   pageTitle="Desativar ou ativar um ponto final de Gestor de tráfego | Microsoft Azure"
   description="Este artigo irá ajudá-desativar ou ativar o seu os pontos finais de perfil do Gestor de tráfego."
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

# <a name="disable-or-enable-a-traffic-manager-endpoint"></a>Desativar ou ativar um ponto final de Gestor de tráfego

Também pode desativar os pontos finais individuais que fazem parte de um perfil do Gestor de tráfego. Os pontos finais incluem serviços em nuvem e Web sites. Desativar um ponto final irá deixá-la como parte do perfil, mas o perfil age como se o ponto final não está incluído no mesmo. Esta ação é muito útil para remover temporariamente a um ponto final que está no modo de manutenção ou novamente a ser implementada. Assim que o ponto final estiver a trabalhar novamente, pode ser ativado

>[AZURE.NOTE] **Desativar um ponto final nada de fazer com o seu estado de implementação no Azure. Um ponto final de Saudável irá permanecer para cima e conseguir receber tráfego mesmo quando está desativada no Gestor de tráfego. Para além disso, desativar um ponto final de um perfil não afeta respetivo estado no outro perfil.**

## <a name="to-disable-an-endpoint"></a>Para desativar um ponto final

1. No painel Gestor de tráfego no portal do Azure clássico, localize o perfil do Gestor de tráfego que contém as definições de ponto final que pretende modificar e, em seguida, clique na seta à direita do nome do perfil. Isto irá abrir a página de definições do perfil.
1. Na parte superior da página, clique em **pontos finais** para ver os pontos finais que estão incluídos na configuração.
1. Clique o ponto final que pretende desativar e, em seguida, clique em **desativar** na parte inferior da página.
1. Tráfego irá interromper a fluir para o ponto final com base no DNS Time-para-Live (TTL) configurado para o nome de domínio do Gestor de tráfego. Pode alterar o valor TTL a partir da página de configuração de perfil do Gestor de tráfego de.

## <a name="to-enable-an-endpoint"></a>Para ativar um ponto final


1. No painel Gestor de tráfego no portal do Azure clássico, localize o perfil do Gestor de tráfego que contém as definições de ponto final que pretende modificar e, em seguida, clique na seta à direita do nome do perfil. Isto irá abrir a página de definições do perfil.
1. Na parte superior da página, clique em **pontos finais** para ver os pontos finais que estão incluídos na configuração.
1. Clique o ponto final que pretende ativar e, em seguida, clique em **Ativar** na parte inferior da página.
1. Tráfego começa a fluir para o serviço novamente como ditado pelo perfil.

## <a name="next-steps"></a>Próximos passos

[Gestor de-desativar, ativar o tráfego ou eliminar um perfil](disable-enable-or-delete-a-profile.md)

[Resolução de problemas de tráfego de Gestor degradado Estado](traffic-manager-troubleshooting-degraded.md)

[Considerações sobre o desempenho tráfego Gestor](traffic-manager-performance-considerations.md)