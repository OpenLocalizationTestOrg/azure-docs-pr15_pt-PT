<properties
   pageTitle="Configurar o Gestor de tráfego arredondar método de encaminhamento de tráfego de robin | Microsoft Azure"
   description="Este artigo irá ajudá-lo a configurar round robin balanceamento de carga para os pontos finais de tráfego gestor."
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

# <a name="configure-round-robin-routing-method"></a>Método de encaminhamento de configurar Round Robin

Um padrão comuns para o tráfego encaminhamento método é fornecem um conjunto de pontos finais idênticos, que incluem serviços em nuvem e sites públicos, e enviar o tráfego para cada uma num modo round robin. Os passos abaixo descrevem como configurar o Gestor de tráfego para realizar este tipo de método de encaminhamento de tráfego. Para mais informações sobre os métodos de encaminhamento de tráfego diferente, consulte o artigo [sobre o Gestor de tráfego de métodos de encaminhamento de tráfego](traffic-manager-routing-methods.md).

>[AZURE.NOTE] Sites públicos do Azure já fornece round robin balanceamento de carga da funcionalidade para Web sites dentro de um centro de dados (também conhecido como uma região). Gestor de tráfego permite-lhe especificar o método de encaminhamento de tráfego de round robin dos sites públicos na centros de dados diferentes.

## <a name="routing-traffic-equally-round-robin-across-a-set-of-endpoints"></a>Encaminhamento tráfego quatros (arredondar robin) através de um conjunto de pontos finais:

1. No portal clássico Azure, no painel esquerdo, clique no ícone de **Gestor de tráfego** para abrir o painel de tráfego gestor. Se ainda não tiver criado o seu perfil do Gestor de tráfego, consulte o artigo [Gerir perfis de Gestor de tráfego](traffic-manager-manage-profiles.md) para obter os passos para criar um perfil do Gestor de tráfego básica.
2. No portal clássico Azure, no painel de tráfego Gestor, localize o perfil do Gestor de tráfego que contém as definições que pretende modificar e, em seguida, clique na seta à direita do nome do perfil. Isto irá abrir a página de definições do perfil.
3. Na página para o seu perfil, clique em **pontos finais** em parte superior da página e verifique se existem os pontos finais de serviço que pretende incluir na sua configuração. Para obter os passos adicionar ou remover os pontos finais, consulte o artigo [Gerir os pontos finais no Gestor de tráfego](traffic-manager-endpoints.md).
4. Na sua página de perfil, clique em **Configurar** na parte superior para abrir a página de configuração.
5. Para o **método de encaminhamento de tráfego definições**, verifique se o método de encaminhamento de tráfego é **Round Robin**. Se não for, clique em **Round Robin** na lista pendente.
6. Certifique-se de que as **Definições de monitorização** estão corretamente configurados. Monitorização assegura que os pontos finais que estão offline não lhe forem enviados tráfego. Para poder monitorizar os pontos finais, tem de especificar um caminho e o nome de ficheiro. Tenha em atenção que uma barra "/" é uma entrada válida para o caminho relativa e implica que o ficheiro está no diretório de raiz (predefinição). Para mais informações sobre como monitorizar, consulte o artigo [Sobre como Gestor de tráfego de monitorização](traffic-manager-monitoring.md).
7. Depois de concluir as alterações à configuração, clique em **Guardar** na parte inferior da página.
8. Teste as alterações na configuração. Para mais informações, consulte o artigo [Definições do Gestor de tráfego de testes](traffic-manager-testing-settings.md).
9. Assim que o seu perfil do Gestor de tráfego é configuração e como trabalhar, edite o registo DNS no servidor DNS que aponte o seu nome de domínio de empresa para o nome de domínio do Gestor de tráfego autoritativos. Para mais informações sobre como fazer isto, consulte o [ponto de um domínio da Internet da empresa para um domínio do Gestor de tráfego](traffic-manager-point-internet-domain.md).

## <a name="next-steps"></a>Próximos passos


[Aponte um domínio da Internet da empresa para um domínio do Gestor de tráfego](traffic-manager-point-internet-domain.md)

[Gestor de tráfego métodos de encaminhamento](traffic-manager-routing-methods.md)

[Configurar o método de encaminhamento de activação pós-falha](traffic-manager-configure-failover-routing-method.md)

[Configurar o método de encaminhamento de desempenho](traffic-manager-configure-performance-routing-method.md)

[Resolução de problemas de tráfego de Gestor degradado Estado](traffic-manager-troubleshooting-degraded.md)

[Gestor de-desativar, ativar o tráfego ou eliminar um perfil](disable-enable-or-delete-a-profile.md)

[Gestor de tráfego - desativar ou ativar um ponto final](disable-or-enable-an-endpoint.md)

