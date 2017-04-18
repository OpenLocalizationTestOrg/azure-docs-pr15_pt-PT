<properties
    pageTitle="Configurar métodos de encaminhamento de Gestor de tráfego | Microsoft Azure"
    description="Este artigo explica como configura no Gestor de tráfego de vários métodos de encaminhamento"
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
   ms.date="10/18/2016"
   ms.author="sewhee" />
<!-- repub for nofollow -->

# <a name="configure-traffic-manager-routing-methods"></a>Configurar o Gestor de tráfego métodos de encaminhamento

Gestor de tráfego Azure fornece três métodos de encaminhamento que controlam o modo como o tráfego é encaminhado para os pontos finais de serviço disponível. O método de encaminhamento de tráfego é aplicado a cada consulta DNS recebida para determinar o ponto final deve ser devolvido na resposta DNS.

Existem três métodos de encaminhamento de tráfego disponíveis no Gestor de tráfego:

- **Prioridade:** Selecione 'Priority' quando pretender utilizar um ponto final de serviço principal e fornecer cópias de segurança, caso não esteja disponível a página principal.
- **Ponderada:** Selecionar 'ponderada' quando pretender distribuir o tráfego através de um conjunto de pontos finais, quer uniformemente ou de acordo com espessuras e que pode definir.
- **Desempenho:** Selecione 'Desempenho' quando tiver os pontos finais em diferentes localizações geográficas e pretende que os utilizadores finais para utilizar o ponto final "mais próximo" em termos de latência da rede mais baixo.

## <a name="configure-priority-routing-method"></a>Configurar o método de encaminhamento de prioridade

Independentemente do modo de Web site, sites públicos do Azure fornecem já activação pós-falha da funcionalidade para Web sites dentro de um centro de dados (também conhecido como uma região). Gestor de tráfego fornece activação pós-falha para sites públicos no centros de dados diferentes.

É um padrão comum para activação pós-falha do serviço enviar o tráfego para um serviço principal e fornecem um conjunto de serviços de cópia de segurança idênticos para activação pós-falha. Os passos seguintes explicam como configurar esta activação com prioridade alta pós-falha com serviços em nuvem Azure e Web sites:

1. No portal clássico Azure, no painel esquerdo, clique no ícone de **Gestor de tráfego** para abrir o painel de tráfego gestor.
2. No painel de Gestor de tráfego no portal do Azure clássico, localize o perfil do Gestor de tráfego que contém as definições que pretende modificar. Para abrir a página de definições de perfil, clique na seta à direita do nome do perfil.
3. Na sua página de perfil, clique em **pontos finais** em parte superior da página. Certifique-se de que a serviços em nuvem e a Web sites que pretende incluir na sua configuração estão presentes.
4. Na parte superior para abrir a página de configuração, clique em **Configurar** .
5. Para **definições do método encaminhar tráfego**, verifique se o método de encaminhamento de tráfego é **activação pós-falha**. Se não for, clique em **activação pós-falha** na lista pendente.
6. Para obter a **Lista de prioridade activação pós-falha**, ajuste a ordem de activação pós-falha para os pontos finais. Quando selecionar o método de encaminhamento de tráfego de **activação pós-falha** , é importante a ordem dos pontos finais selecionados. O ponto final principal está na parte superior. Utilize a seta para cima e para baixo nas setas para alterar a ordem, conforme necessário. Para obter informações sobre como definir a prioridade activação pós-falha ao utilizar o Windows PowerShell, consulte o artigo [AzureTrafficManagerProfile conjunto](http://go.microsoft.com/fwlink/p/?LinkId=400880).
7. Certifique-se de que as **Definições de monitorização** estão corretamente configurados. Monitorização assegura que os pontos finais que estão offline não lhe forem enviados tráfego. Para monitorizar os pontos finais, tem de especificar um caminho e o nome de ficheiro. A barra "/" é uma entrada válida para o caminho relativa e implica que o ficheiro está no diretório de raiz (predefinição).
8. Depois de concluir as alterações à configuração, clique em **Guardar** na parte inferior da página.
9. Teste as alterações na configuração.
10. Assim que o seu perfil do Gestor de tráfego está a funcionar, edite o registo DNS no servidor DNS que aponte o seu nome de domínio de empresa para o nome de domínio do Gestor de tráfego autoritativos.

## <a name="configure-weighted-routing-method"></a>Configurar o método de encaminhamento ponderado

Um padrão comuns para o tráfego encaminhamento método é fornecem um conjunto de pontos finais idênticos, que incluem serviços em nuvem e sites públicos, e enviar o tráfego para cada uma num modo round robin. Os passos seguintes descrevem como configurar este tipo de método de encaminhamento de tráfego.

>[AZURE.NOTE] Sites públicos do Azure já fornecem round robin balanceamento de carga da funcionalidade para Web sites dentro de um centro de dados (também conhecido como uma região). Gestor de tráfego permite-lhe especificar o método de encaminhamento de tráfego de round robin dos sites públicos na centros de dados diferentes.

1. No portal clássico Azure, no painel esquerdo, clique no ícone de **Gestor de tráfego** para abrir o painel de tráfego gestor.
2. No painel de tráfego Gestor, localize o perfil do Gestor de tráfego que contém as definições que pretende modificar. Para abrir a página de definições de perfil, clique na seta à direita do nome do perfil.
3. Na página para o seu perfil, clique em **pontos finais** em parte superior da página e certifique-se de que os pontos finais de serviço que pretende incluir na sua configuração estão presentes.
4. Na sua página de perfil, clique em **Configurar** na parte superior para abrir a página de configuração.
5. Para o **método de encaminhamento de tráfego definições**, verifique se o método de encaminhamento de tráfego é **Round Robin**. Se não for, clique em **Round Robin** na lista pendente.
6. Certifique-se de que as **Definições de monitorização** estão corretamente configurados. Monitorização assegura que os pontos finais que estão offline não lhe forem enviados tráfego. Para monitorizar os pontos finais, tem de especificar um caminho e o nome de ficheiro. A barra "/" é uma entrada válida para o caminho relativa e implica que o ficheiro está no diretório de raiz (predefinição).
7. Depois de concluir as alterações à configuração, clique em **Guardar** na parte inferior da página.
8. Teste as alterações na configuração.
9. Assim que o seu perfil do Gestor de tráfego está a funcionar, edite o registo DNS no servidor DNS que aponte o seu nome de domínio de empresa para o nome de domínio do Gestor de tráfego autoritativos.

## <a name="configure-performance-traffic-routing-method"></a>Configurar o método de encaminhamento de tráfego de desempenho

O método de encaminhamento de tráfego de desempenho permite-lhe direcionar tráfego para o ponto final com a latência mais baixa da rede do cliente. Normalmente, o Centro de dados com a latência mais baixo está mais próximo da distância geográfica. Este método encaminhar o tráfego não pode conta em tempo real alterações na configuração de rede ou carregar.

1. No portal clássico Azure, no painel esquerdo, clique no ícone de **Gestor de tráfego** para abrir o painel de tráfego gestor.
2. No painel de tráfego Gestor, localize o perfil do Gestor de tráfego que contém as definições que pretende modificar. Para abrir a página de definições de perfil, clique na seta à direita do nome do perfil.
3. Na página para o seu perfil, clique em **pontos finais** em parte superior da página e certifique-se de que os pontos finais de serviço que pretende incluir na sua configuração estão presentes.
4. Na página para o seu perfil, clique em **Configurar** na parte superior para abrir a página de configuração.
5. Para **definições do método encaminhar tráfego**, verifique se o método de encaminhamento de tráfego é * *Desempenho*. Se não for, clique em * *Desempenho** na lista pendente.
6. Certifique-se de que as **Definições de monitorização** estão corretamente configurados. Monitorização assegura que os pontos finais que estão offline não lhe forem enviados tráfego. Para monitorizar os pontos finais, tem de especificar um caminho e o nome de ficheiro. A barra "/" é uma entrada válida para o caminho relativa e implica que o ficheiro está no diretório de raiz (predefinição).
7. Depois de concluir as alterações à configuração, clique em **Guardar** na parte inferior da página.
8. Teste as alterações na configuração.
9. Assim que o seu perfil do Gestor de tráfego está a funcionar, edite o registo DNS no servidor DNS que aponte o seu nome de domínio de empresa para o nome de domínio do Gestor de tráfego autoritativos.

## <a name="next-steps"></a>Próximos passos

* [Gerir perfis de Gestor de tráfego](traffic-manager-manage-profiles.md)
* [Gestor de tráfego métodos de encaminhamento](traffic-manager-routing-methods.md)
* [Definições do Gestor de tráfego de teste](traffic-manager-testing-settings.md)
* [Aponte um domínio da Internet da empresa para um domínio do Gestor de tráfego](traffic-manager-point-internet-domain.md)
* [Gerir os pontos finais de Gestor de tráfego](traffic-manager-manage-endpoints.md)
* [Resolução de problemas de tráfego de Gestor degradado Estado](traffic-manager-troubleshooting-degraded.md)
