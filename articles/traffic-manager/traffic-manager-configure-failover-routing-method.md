<properties
   pageTitle="Configurar o método de encaminhamento de tráfego de Gestor de tráfego activação pós-falha | Microsoft Azure"
   description="Este artigo irá ajudá-lo a configurar o método de encaminhamento de tráfego de activação pós-falha no Gestor de tráfego"
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

# <a name="configure-failover-routing-method"></a>Configurar o método de encaminhamento de activação pós-falha

Muitas vezes uma organização pretende fornecer fiabilidade para os seus serviços. Faz isto ao fornecer serviços de cópia de segurança no caso dos seus serviços primário correr para baixo. É um padrão comum para activação pós-falha do serviço fornecer um conjunto de serviços idênticos e enviar tráfego para um serviço principal, mantendo uma lista configurada de um ou mais serviços de cópia de segurança. Pode configurar este tipo de cópia de segurança com serviços em nuvem Azure e Web sites ao seguir os procedimentos abaixo.

Tenha em atenção que sites públicos do Azure fornece já encaminhamento da funcionalidade método para Web sites dentro de um centro de dados (também conhecido como região), o tráfego activação pós-falha independentemente o modo de Web site. Gestor de tráfego permite-lhe especificar o método de encaminhamento de tráfego de activação pós-falha dos sites públicos na centros de dados diferentes.

## <a name="to-configure-failover-traffic-routing-method"></a>Para configurar o método de encaminhamento de tráfego de activação pós-falha:

1. No portal clássico Azure, no painel esquerdo, clique no ícone de **Gestor de tráfego** para abrir o painel de tráfego gestor. Se ainda não tiver criado o seu perfil do Gestor de tráfego, consulte o artigo [Gerir perfis de Gestor de tráfego](traffic-manager-manage-profiles.md) para obter os passos para criar um perfil do Gestor de tráfego básica.
2. No painel Gestor de tráfego no portal do Azure clássico, localize o perfil do Gestor de tráfego que contém as definições que pretende modificar e, em seguida, clique na seta à direita do nome do perfil. Isto irá abrir a página de definições do perfil.
3. Na sua página de perfil, clique em **pontos finais** em parte superior da página e certifique-se de que a ambos serviços em nuvem e Web sites (pontos finais) que pretende incluir na sua configuração estão presentes. Para obter os passos adicionar ou remover os pontos finais, consulte o artigo [Gerir os pontos finais no Gestor de tráfego](traffic-manager-endpoints.md).
4. Na sua página de perfil, clique em **Configurar** na parte superior para abrir a página de configuração.
5. Para **definições do método encaminhar tráfego**, verifique se o método de encaminhamento de tráfego é **activação pós-falha**. Se não for, clique em **activação pós-falha** na lista pendente.
6. Para obter a **Lista de prioridade activação pós-falha**, ajuste a ordem de activação pós-falha para os pontos finais. Quando selecionar o método de encaminhamento de tráfego de **activação pós-falha** , é importante a ordem dos pontos finais selecionados. O ponto final principal está na parte superior. Utilize a seta para cima e para baixo nas setas para alterar a ordem, conforme necessário. Para obter informações sobre como definir a prioridade activação pós-falha ao utilizar o Windows PowerShell, consulte o artigo [AzureTrafficManagerProfile conjunto](http://go.microsoft.com/fwlink/p/?LinkId=400880).
7. Certifique-se de que as **Definições de monitorização** estão corretamente configurados. Monitorização assegura que os pontos finais que estão offline não lhe forem enviados tráfego. Para poder monitorizar os pontos finais, tem de especificar um caminho e o nome de ficheiro. Tenha em atenção que uma barra "/" é uma entrada válida para o caminho relativa e implica que o ficheiro está no diretório de raiz (predefinição). Para obter mais informações sobre como monitorizar, consulte [Gestor de tráfego de monitorização](traffic-manager-monitoring.md).
8. Depois de concluir as alterações à configuração, clique em **Guardar** na parte inferior da página.
9. Teste as alterações na configuração. Para mais informações, consulte [Definições do Gestor de tráfego de testes](traffic-manager-testing-settings.md) .
10. Assim que o seu perfil do Gestor de tráfego é configuração e como trabalhar, edite o registo DNS no servidor DNS que aponte o seu nome de domínio de empresa para o nome de domínio do Gestor de tráfego autoritativos. Para mais informações sobre como fazer isto, consulte o [ponto de um domínio da Internet da empresa para um domínio do Gestor de tráfego](traffic-manager-point-internet-domain.md).

## <a name="next-steps"></a>Próximos passos

[Aponte um domínio da Internet da empresa para um domínio do Gestor de tráfego](traffic-manager-point-internet-domain.md)

[Gestor de tráfego métodos de encaminhamento](traffic-manager-routing-methods.md)

[Configurar o método de encaminhamento de round robin](traffic-manager-configure-round-robin-routing-method.md)

[Configurar o método de encaminhamento de desempenho](traffic-manager-configure-performance-routing-method.md)

[Resolução de problemas de tráfego de Gestor degradado Estado](traffic-manager-troubleshooting-degraded.md)

[Gestor de-desativar, ativar o tráfego ou eliminar um perfil](disable-enable-or-delete-a-profile.md)

[Gestor de tráfego - desativar ou ativar um ponto final](disable-or-enable-an-endpoint.md)

