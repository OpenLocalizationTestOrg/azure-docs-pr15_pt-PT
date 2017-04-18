<properties
   pageTitle="Configurar o método de encaminhamento de tráfego de desempenho | Microsoft Azure"
   description="Este artigo irá ajudá-lo a configurar o método de encaminhamento de tráfego de desempenho no Gestor de tráfego"
   services="traffic-manager"
   documentationCenter=""
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

# <a name="configure-performance-traffic-routing-method"></a>Configurar o método de encaminhamento de tráfego de desempenho

Para encaminhar o tráfego para serviços em nuvem e Web sites (pontos finais) que estão localizados no centros de dados diferentes em todo o mundo (também conhecido como regiões), pode encaminhá para o ponto final de tráfego de entrada com a latência mais baixa do cliente a pedido. Normalmente, o Centro de dados com a latência mais baixo corresponde ao mais próximo da distância geográficos. O método de encaminhamento de tráfego de desempenho permite-lhe distribuir com base em latência mais baixa, mas não pode ter em conta as alterações em tempo real na configuração de rede ou para carregar. Para mais informações sobre os métodos de encaminhamento de tráfego diferentes o Gestor de tráfego Azure fornece, consulte o artigo [Encaminhamento de tráfego de sobre o Gestor de tráfego métodos](traffic-manager-routing-methods.md).

## <a name="route-traffic-based-on-lowest-latency-across-a-set-of-endpoints"></a>Encaminhar o tráfego com base em latência mais baixa ao longo de um conjunto de pontos finais:

1. No portal clássico Azure, no painel esquerdo, clique no ícone de **Gestor de tráfego** para abrir o painel de tráfego gestor. Se ainda não tiver criado o seu perfil do Gestor de tráfego, consulte o artigo [Gerir perfis de Gestor de tráfego](traffic-manager-manage-profiles.md) para obter os passos para criar um perfil do Gestor de tráfego básica.
2. No portal clássico Azure, no painel de tráfego Gestor, localize o perfil do Gestor de tráfego que contém as definições que pretende modificar e, em seguida, clique na seta à direita do nome do perfil. Isto irá abrir a página de definições do perfil.
3. Na página para o seu perfil, clique em **pontos finais** em parte superior da página e verifique se existem os pontos finais de serviço que pretende incluir na sua configuração. Para obter os passos adicionar ou remover os pontos finais do seu perfil, consulte o artigo [Gerir os pontos finais no Gestor de tráfego](traffic-manager-endpoints.md).
4. Na página para o seu perfil, clique em **Configurar** na parte superior para abrir a página de configuração.
5. Para **definições do método encaminhar tráfego**, verifique se o método de encaminhamento de tráfego é * *Desempenho*. Se não for, clique em * *Desempenho** na lista pendente.
6. Certifique-se de que as **Definições de monitorização** estão corretamente configurados. Monitorização assegura que os pontos finais que estão offline não lhe forem enviados tráfego. Para poder monitorizar os pontos finais, tem de especificar um caminho e o nome de ficheiro. Tenha em atenção que uma barra "/" é uma entrada válida para o caminho relativa e implica que o ficheiro está no diretório de raiz (predefinição). Para mais informações sobre como monitorizar, consulte o artigo [Sobre como Gestor de tráfego de monitorização](traffic-manager-monitoring.md).
7. Depois de concluir as alterações à configuração, clique em **Guardar** na parte inferior da página.
8. Teste as alterações na configuração. Para mais informações, consulte o artigo [Definições do Gestor de tráfego de testes](traffic-manager-testing-settings.md).
9. Assim que o seu perfil do Gestor de tráfego é configuração e como trabalhar, edite o registo DNS no servidor DNS que aponte o seu nome de domínio de empresa para o nome de domínio do Gestor de tráfego autoritativos. Para mais informações sobre como fazer isto, consulte o [ponto de um domínio da Internet da empresa para um domínio do Gestor de tráfego](traffic-manager-point-internet-domain.md).

## <a name="next-steps"></a>Próximos passos


[Aponte um domínio da Internet da empresa para um domínio do Gestor de tráfego](traffic-manager-point-internet-domain.md)

[Gestor de tráfego métodos de encaminhamento](traffic-manager-routing-methods.md)

[Configurar o método de encaminhamento de activação pós-falha](traffic-manager-configure-failover-routing-method.md)

[Configurar o método de encaminhamento de round robin](traffic-manager-configure-round-robin-routing-method.md)

[Resolução de problemas de tráfego de Gestor degradado Estado](traffic-manager-troubleshooting-degraded.md)

[Gestor de-desativar, ativar o tráfego ou eliminar um perfil](disable-enable-or-delete-a-profile.md)

[Gestor de tráfego - desativar ou ativar um ponto final](disable-or-enable-an-endpoint.md)

