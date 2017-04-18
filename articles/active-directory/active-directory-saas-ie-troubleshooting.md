<properties
    pageTitle="A extensão de painel de acesso de resolução de problemas para o Internet Explorer | Microsoft Azure"
    description="Como utilizar a política de grupo para implementar o suplemento do Internet Explorer para o portal das minhas aplicações."
    services="active-directory"
    documentationCenter=""
    authors="MarkusVi"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity"
    ms.date="08/16/2016"
    ms.author="markvi"/>

#<a name="troubleshooting-the-access-panel-extension-for-internet-explorer"></a>A extensão de painel de acesso de resolução de problemas para o Internet Explorer

Este artigo irá ajudá-lo a resolver problemas que se seguem:

- Não é possível aceder a suas aplicações através do portal das minhas aplicações ao utilizar o Internet Explorer.
- Consulte a mensagem "Instalar Software" apesar do software já tenha instalado.

Se for um administrador, consulte também: [como implementar a extensão de painel de acesso para o Internet Explorer utilizando a política de grupo](active-directory-saas-ie-group-policy.md)

##<a name="run-the-diagnostic-tool"></a>Executar a ferramenta de diagnóstico

Pode diagnosticar problemas de instalação com a extensão de painel de acesso ao transferir e executar a ferramenta de diagnóstico do painel de acesso:

1. [Clique aqui para transferir a ferramenta de diagnóstico.](https://account.activedirectory.windowsazure.com/applications/AccessPanelExtensionDiagnosticTool/AccessPanelExtensionDiagnosticTool.zip)

2. Abra o ficheiro e, prima o botão **extrair todos** .

    ![Prima extrair todos](./media/active-directory-saas-ie-troubleshooting/extract1.png)

3. Em seguida, prima o botão **extrair** para continuar.

    ![Prima extrair](./media/active-directory-saas-ie-troubleshooting/extract2.png)

4. Para executar a ferramenta, com o botão direito no ficheiro denominado **AccessPanelExtensionDiagnosticTool**, em seguida, selecione **Abrir com o > Microsoft Windows com base Script Host**.

    ![Abrir com o > com base do Microsoft Windows Script Host](./media/active-directory-saas-ie-troubleshooting/open_tool.png)

5. Em seguida, irá ver a janela de diagnóstico seguinte, que descreve o que poderá ser enganadora com a instalação.

    ![Um exemplo da janela de diagnóstico](./media/active-directory-saas-ie-troubleshooting/tool_preview.png)

6. Clique em "**Sim**" para permitir que o programa corrigir os problemas que foram encontrados.

7. Para guardar estas alterações, feche todas as janelas do Internet Explorer e, em seguida, abra novamente o Internet Explorer.<br />Se não conseguir aceder as suas aplicações, experimente os passos abaixo.

##<a name="check-that-the-access-panel-extension-is-enabled"></a>Verifique que a extensão de painel de acesso é activada

Para verificar que é activada a extensão de painel de acesso no Internet Explorer:

1. No Internet Explorer, clique no **ícone de engrenagem** no canto superior direito da janela. Em seguida, selecione **Opções da Internet**.<br />(Em versões mais antigas do Internet Explorer pode encontrar esta em **Ferramentas > Opções da Internet**.

    ![Aceda a ferramentas > Opções da Internet](./media/active-directory-saas-ie-troubleshooting/internetoptions.png)

2. Clique no separador **programas** e, em seguida, clique no botão **Gerir suplementos** .

    ![Clique em Gerir suplementos](./media/active-directory-saas-ie-troubleshooting/internetoptions_programs.png)

3. Nesta caixa de diálogo, selecione **Extensão do painel de acesso** e, em seguida, clique no botão **Ativar** .

    ![Clique em ativar](./media/active-directory-saas-ie-troubleshooting/enableaddon.png)

4. Para guardar estas alterações, feche todas as janelas do Internet Explorer e, em seguida, abra novamente o Internet Explorer.

##<a name="enable-extensions-for-inprivate-browsing"></a>Ativar as extensões de para a Navegação InPrivate

Se estiver a utilizar o modo de Navegação InPrivate:

1. No Internet Explorer, clique no **ícone de engrenagem** no canto superior direito da janela. Em seguida, selecione **Opções da Internet**.<br />(Em versões mais antigas do Internet Explorer pode encontrar esta em **Ferramentas > Opções da Internet**.

    ![Um exemplo da janela de diagnóstico](./media/active-directory-saas-ie-troubleshooting/inprivateoptions.png)

2. Aceda ao separador **Privacidade** , em seguida, **desmarque** a caixa de verificação denominada **desativar as barras de ferramentas e extensões quando a Navegação InPrivate é iniciado**</p>

    ![Desmarque desativar barras de ferramentas e extensões quando a Navegação InPrivate é iniciado](./media/active-directory-saas-ie-troubleshooting/enabletoolbars.png)

3. Para guardar estas alterações, feche todas as janelas do Internet Explorer e, em seguida, abra novamente o Internet Explorer.

##<a name="uninstall-the-access-panel-extension"></a>Desinstalar a extensão de painel de acesso

Para desinstalar a extensão de painel de acesso do seu computador:

1. No seu teclado, prima a **tecla Windows** para abrir o menu Iniciar. Quando o menu está aberto, pode escrever alguma coisa para fazer uma pesquisa. Escreva "Painel de controlo" e, em seguida, abra o **Painel de controlo** quando este aparecer nos resultados da pesquisa.

    ![Pesquisa de painel de controlo](./media/active-directory-saas-ie-troubleshooting/search_sm.png)

2. No canto superior direito do painel de controlo, altere a opção de **Ver por** para **ícones grandes**. Em seguida, localize e clique no botão **programas e funcionalidades** .

    ![Alterar a vista para mostrar ícones grandes](./media/active-directory-saas-ie-troubleshooting/control_panel.png)

3. A partir da lista, selecione a **Extensão do painel de acesso**e clique no botão **desinstalar** .

    ![Clique em desinstalar](./media/active-directory-saas-ie-troubleshooting/uninstall.png)

4. Em seguida, pode tentar instalar a extensão novamente para verificar se o problema foi resolvido.

Se encontrar problemas de desinstalar a extensão, também pode removê-lo utilizando a ferramenta de [Microsoft corrigi-lo](https://go.microsoft.com/?linkid=9779673) .

## <a name="related-articles"></a>Artigos relacionados

- [Índice de artigo para gestão de aplicações no Azure Active Directory](active-directory-apps-index.md)
- [Acesso a aplicação e único início de sessão com o Azure Active Directory](active-directory-appssoaccess-whatis.md)
- [Como implementar a extensão de painel de acesso para o Internet Explorer utilizando a política de grupo](active-directory-saas-ie-group-policy.md)
