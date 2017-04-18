<properties
    pageTitle="Gerir perfis de Gestor de tráfego Azure | Microsoft Azure"
    description="Este artigo ajuda-o a criar, desativar, ativar, eliminar e ver o histórico de um perfil do Gestor de tráfego Azure."
    services="traffic-manager"
    documentationCenter=""
    authors="sdwheeler"
    manager="carmonm"
    editor=""
/>
<tags
    ms.service="traffic-manager"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.tgt_pltfrm="na"
    ms.workload="infrastructure-services"
    ms.date="10/11/2016"
    ms.author="sewhee"
/>

# <a name="manage-an-azure-traffic-manager-profile"></a>Gerir um perfil do Gestor de tráfego do Azure

Perfis do Gestor de tráfego utilizam o encaminhamento de tráfego métodos para controlar a distribuição do tráfego para o seu serviços em nuvem ou pontos finais de Web site. Este artigo explica como criar e gerir perfis destes.

## <a name="create-a-traffic-manager-profile-using-quick-create"></a>Criar um perfil do Gestor de tráfego utilizando a criação rápida

Pode criar rapidamente um perfil do Gestor de tráfego ao utilizar a criação rápida no portal do Azure clássico. Criar rápida permite-lhe criar perfis com as definições de configuração básica. No entanto, é possível utilizar criação rápida para definições como o conjunto de pontos finais (serviços em nuvem e Web sites), a ordem de activação pós-falha para o método de encaminhamento de tráfego de activação pós-falha ou definições de monitorização. Depois de criar o seu perfil, pode configurar estas definições no portal do Azure clássico. Gestor de tráfego suporta pontos finais de até 200 por perfil. No entanto, a maior parte dos cenários de utilização necessitam de algumas apenas os pontos finais.

### <a name="to-create-a-traffic-manager-profile"></a>Para criar um perfil do Gestor de tráfego

1. **Implemente os serviços em nuvem e Web sites para o ambiente de produção.** Para mais informações sobre os serviços em nuvem, consulte o artigo [Serviços em nuvem](http://go.microsoft.com/fwlink/p/?LinkId=314074). Para mais informações sobre sites públicos, consulte o artigo [Web sites](http://go.microsoft.com/fwlink/p/?LinkId=393327).

2. **Inicie sessão no portal do Azure clássico.** Clique em **Novo** no canto inferior esquerdo do portal, clique em **Serviços de rede > Gestor de tráfego**e, em seguida, clique em **Criar rápido** para começar a configurar o seu perfil.
3. **Configure o prefixo DNS.** Dê ao seu perfil do Gestor de tráfego um DNS exclusivo nome prefixo. Pode especificar apenas o prefixo para um nome de domínio do Gestor de tráfego.
4. **Selecione a subscrição.** Selecione a subscrição Azure adequada. Cada perfil está associado uma única subscrição. Se apenas tiver uma subscrição, esta opção não é apresentado.
5. **Selecione o método de encaminhamento de tráfego.** Selecione o método de encaminhamento de tráfego na **tráfego encaminhamento de política**. Para mais informações sobre métodos de encaminhamento de tráfego, consulte o artigo [sobre o Gestor de tráfego de métodos de encaminhamento de tráfego](traffic-manager-routing-methods.md).
6. **Clique em "Criar" para criar o perfil**. Quando a configuração do perfil estiver concluída, pode localizar o seu perfil no painel de Gestor de tráfego no portal do Azure clássico.
7. **Configure os pontos finais, monitorização e definições adicionais no portal do Azure clássico.** Apenas utilizando a criação rápida configura as definições básicas. É necessário configurar definições adicionais, tal como a lista de pontos finais e a ordem de activação pós-falha de ponto final.


## <a name="disable-enable-or-delete-a-profile"></a>Desativar, ativar ou eliminar um perfil

Pode desativar um perfil existente, de modo que o Gestor de tráfego não refira pedidos de utilizador para os pontos finais configurados. Quando desativar um perfil do Gestor de tráfego, o perfil e as informações contidas no perfil permaneçam intactos e podem ser editados na interface do Gestor de tráfego.  Referências retomar quando voltar a ativar o perfil. Quando cria um perfil do Gestor de tráfego no portal do Azure clássico, está ativada automaticamente. Se decidir que já não é necessário um perfil, pode eliminá-lo.

### <a name="to-disable-a-profile"></a>Para desativar um perfil

1. Se estiver a utilizar um nome de domínio personalizado, altere o registo CNAME no servidor DNS da Internet para que já não aponta para o seu perfil do Gestor de tráfego.
2. Tráfego deixa de ser encaminhado para os pontos finais através das definições de perfil do Gestor de tráfego.
3. Selecione o perfil que pretende desativar. Na página Gestor de tráfego, realce o perfil clicando na coluna junto ao nome do perfil. Nota, clicando no nome do perfil ou na seta junto ao nome do abre a página de definições do perfil.
4. Após selecionar o perfil, clique em **desativar** na parte inferior da página.

### <a name="to-enable-a-profile"></a>Para ativar um perfil

1. Selecione o perfil que pretende desativar. Na página Gestor de tráfego, realce o perfil clicando na coluna junto ao nome do perfil. Nota, clicando no nome do perfil ou na seta junto ao nome do abre a página de definições do perfil.
2. Após selecionar o perfil, clique em **Ativar** na parte inferior da página.
3. Se estiver a utilizar um nome de domínio personalizado, crie um registo de recurso CNAME no servidor DNS da Internet para apontarem para o nome de domínio do seu perfil do Gestor de tráfego.
4. Tráfego é direcionado para os pontos finais novamente.

### <a name="to-delete-a-profile"></a>Para eliminar um perfil

1. Certifique-se de que o registo de recursos DNS no seu servidor DNS da Internet já não utiliza um registo de recurso CNAME que aponta para o nome de domínio do seu perfil do Gestor de tráfego.
2. Selecione o perfil que pretende desativar. Na página Gestor de tráfego, realce o perfil clicando na coluna junto ao nome do perfil. Nota, clicando no nome do perfil ou na seta junto ao nome do abre a página de definições do perfil.
3. Após selecionar o perfil, clique em **Eliminar** na parte inferior da página.

## <a name="view-traffic-manager-profile-change-history"></a>Histórico de alterações de perfil do Gestor de tráfego de vistas

Pode ver o histórico de alterações no seu perfil do Gestor de tráfego no portal do Azure clássico nos serviços de gestão.

### <a name="to-view-your-traffic-manager-change-history"></a>Para ver o seu Gestor de tráfego de histórico de alterações

1. No painel esquerdo do portal clássico Azure, clique em **Gestão de serviços**.
2. Na página de serviços de gestão, clique em **Registos de operação**.
3. Na página operação registos, pode filtrar para ver o histórico de alterações no seu perfil do Gestor de tráfego. Após selecionar as opções de filtragem, clique em marca de verificação para ver os resultados.

   - Para ver as alterações para todos os perfis, selecione a subscrição e o intervalo de tempo e, em seguida, selecione **O Gestor de tráfego** no **tipo de** menu de atalho.
   - Para filtrar por nome de perfil, escreva o nome do perfil no campo **Nome do serviço** ou selecione-a partir do menu de atalho.
   - Para ver os detalhes para cada alteração individual, selecione a linha com a alteração que pretende ver e, em seguida, clique em **Detalhes** na parte inferior da página. Na janela de **Detalhes da operação** , pode ver a representação de XML do objeto API que foi criado ou atualizado como parte da operação de.

## <a name="next-steps"></a>Próximos passos

- [Adicionar um ponto final](traffic-manager-endpoints.md)
- [Configurar o método de encaminhamento de activação pós-falha](traffic-manager-configure-failover-routing-method.md)
- [Configurar o método de encaminhamento de round robin](traffic-manager-configure-round-robin-routing-method.md)
- [Configurar o método de encaminhamento de desempenho](traffic-manager-configure-performance-routing-method.md)
- [Aponte um domínio da Internet da empresa para um nome de domínio do Gestor de tráfego](traffic-manager-point-internet-domain.md)
- [Resolução de problemas de tráfego de Gestor degradado Estado](traffic-manager-troubleshooting-degraded.md)