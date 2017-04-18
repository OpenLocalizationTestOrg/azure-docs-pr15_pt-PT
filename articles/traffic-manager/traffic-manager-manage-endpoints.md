<properties
    pageTitle="Gerir os pontos finais no Gestor de tráfego do Azure | Microsoft Azure"
    description="Este artigo irá ajudá-lo a adicionar, remover, ativar e desativar os pontos finais a partir do Azure tráfego gestor."
    services="traffic-manager"
    documentationCenter=""
    authors="sdwheeler"
    manager="carmonm"
    editor=""
/>
<tags
    ms.service="traffic-manager"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.workload="infrastructure-services"
    ms.date="10/11/2016"
    ms.author="sewhee"
/>

# <a name="add-disable-enable-or-delete-endpoints"></a>Adicionar, desativar, ativar ou eliminar pontos finais

A funcionalidade de aplicações Web na aplicação de serviço de Azure já fornece activação e a funcionalidade de encaminhamento de tráfego de round robin para Web sites dentro de um centro de dados, independentemente do modo de Web site. Gestor de tráfego Azure permite-lhe especificar activação e encaminhamento de tráfego de round robin nos serviços de Web sites e nuvem do centros de dados diferentes. É o primeiro passo necessário para fornecer essa funcionalidade Adicionar o ponto final de serviço ou Web site na nuvem ao Gestor de tráfego.

>[AZURE.NOTE]  Este artigo explica como utilizar o portal clássico. Portal clássico do Azure apenas suporta a criação e a atribuição de serviços em nuvem e Web apps como pontos finais. O novo [Azure portal](https://portal.azure.com) é a interface preferida.

Também pode desativar os pontos finais individuais que fazem parte de um perfil do Gestor de tráfego. Desativar um ponto final irá deixá-la como parte do perfil, mas o perfil age como se o ponto final não está incluído no mesmo. Esta ação é útil para remover temporariamente a um ponto final que está no modo de manutenção ou novamente a ser implementada. Assim que o ponto final estiver a trabalhar novamente, pode ser ativado.

>[AZURE.NOTE] Desativar um ponto final nada de fazer com o seu estado de implementação no Azure. Um ponto final de Saudável mantém-se para cima e conseguir receber tráfego mesmo quando está desativada no Gestor de tráfego. Para além disso, desativar um ponto final de um perfil não afeta respetivo estado no outro perfil.

## <a name="to-add-a-cloud-service-or-website-endpoint"></a>Para adicionar um ponto final de serviço ou Web site na nuvem

1. No painel de Gestor de tráfego no portal do Azure clássico, localize o perfil do Gestor de tráfego que contém as definições de ponto final que pretende modificar. Para abrir a página de definições, clique na seta à direita do nome do perfil.
2. Na parte superior da página, clique em **pontos finais** para ver os pontos finais que já fazem parte da sua configuração.
3. Na parte inferior da página, clique em **Adicionar** para aceder à página **Adicionar pontos finais de serviço** . Por predefinição, a página apresenta os serviços em nuvem em **Pontos finais de serviço**.
4. Para serviços em nuvem, selecione serviços na nuvem na lista para adicioná-los como os pontos finais para este perfil. Limpar o nome do serviço na nuvem remove-a na lista de pontos finais.
5. Para Web sites, clique na lista pendente de **Tipo de serviço** e, em seguida, selecione **Web app**.
6. Selecione os sites públicos na lista para adicioná-los como os pontos finais para este perfil. Limpar o nome do Web site remove-a na lista de pontos finais. Pode selecionar apenas um Web site por Azure Centro de dados (também conhecido como uma região). Quando selecionar o Web site primeiro, os outros sites públicos o Centro de dados mesmo tornam-se indisponível para seleção. Tenha em atenção que a Web sites apenas padrão estão listados.
7. Depois de selecionar os pontos finais para este perfil, clique em marca de verificação no canto inferior direito para guardar as alterações.

>[AZURE.NOTE] Depois de adicionar ou remover um ponto final de um perfil utilizando o método de encaminhamento de tráfego de *activação pós-falha* , na lista de prioridade activação pós-falha podem não ser ordenados de forma que pretende. Pode ajustar a ordem da lista de prioridade de activação pós-falha na página configuração. Para mais informações, consulte o artigo [Encaminhamento de tráfego de configurar activação pós-falha](traffic-manager-configure-failover-routing-method.md).

## <a name="to-disable-an-endpoint"></a>Para desativar um ponto final

1. No painel de Gestor de tráfego no portal do Azure clássico, localize o perfil do Gestor de tráfego que contém as definições de ponto final que pretende modificar. Para abrir a página de definições, clique na seta à direita do nome do perfil.
2. Na parte superior da página, clique em **pontos finais** para ver os pontos finais que estão incluídos na configuração.
3. Clique o ponto final que pretende desativar e, em seguida, clique em **desativar** na parte inferior da página.
4. Os clientes continuar a enviar tráfego para o ponto final para a duração de tempo-para-Live (TTL). Pode alterar o valor TTL na página de perfil do Gestor de tráfego de configuração.

## <a name="to-enable-an-endpoint"></a>Para ativar um ponto final

1. No painel de Gestor de tráfego no portal do Azure clássico, localize o perfil do Gestor de tráfego que contém as definições de ponto final que pretende modificar. Para abrir a página de definições, clique na seta à direita do nome do perfil.
2. Na parte superior da página, clique em **pontos finais** para ver os pontos finais que estão incluídos na configuração.
3. Clique o ponto final que pretende ativar e, em seguida, clique em **Ativar** na parte inferior da página.
4. Os clientes são dirigidos para o ponto final ativado, tal como indicado pelo perfil.

## <a name="to-delete-a-cloud-service-or-website-endpoint"></a>Para eliminar um ponto final de serviço ou Web site na nuvem

1. No painel de Gestor de tráfego no portal do Azure clássico, localize o perfil do Gestor de tráfego que contém as definições de ponto final que pretende modificar. Para abrir a página de definições, clique na seta à direita do nome do perfil.
2. Na parte superior da página, clique em **pontos finais** para ver os pontos finais que já fazem parte da sua configuração.
3. Na página pontos finais, clique no nome do ponto final que pretende eliminar a partir do perfil.
4. Na parte inferior da página, clique em **Eliminar**.

## <a name="next-steps"></a>Próximos passos

* [Gerir perfis de Gestor de tráfego](traffic-manager-manage-profiles.md)
* [Configurar métodos de encaminhamento](traffic-manager-configure-routing-method.md)
* [Resolução de problemas de tráfego de Gestor degradado Estado](traffic-manager-troubleshooting-degraded.md)
* [Considerações sobre o desempenho tráfego Gestor](traffic-manager-performance-considerations.md)
* [Operações no Gestor de tráfego (REST API referência)](http://go.microsoft.com/fwlink/p/?LinkID=313584)
