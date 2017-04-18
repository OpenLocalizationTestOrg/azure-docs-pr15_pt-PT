Clientes Azure podem desbloquear 25.000 e-mails gratuitos cada mês. Estes 25.000 e-mails mensais gratuitos irão dar-lhe acesso para elaboração de relatórios avançados e análise e [todos os APIs][] (Web, SMTP, eventos, análise e mais). Para obter informações sobre os serviços adicionais fornecida pela SendGrid, consulte a página [SendGrid funcionalidades][] .

### <a name="to-sign-up-for-a-sendgrid-account"></a>Para se inscrever para uma conta de SendGrid

1. Inicie a sessão [Portal de gestão Azure][].

2. No painel inferior do portal de gestão, clique em **Novo**.

    ![novo comando-barra][command-bar-new]

3. Clique em **Marketplace**.

    ![arquivo de sendgrid][sendgrid-store]

4. Na caixa de diálogo **Escolher uma aplicação e serviço** , selecione **SendGrid** e clique na seta à direita.

5. Na caixa de diálogo **Personalizar a aplicação e serviço** , selecione o plano de **SendGrid** que pretende para se inscrever no.

6. Introduza um nome para identificar o seu serviço de **SendGrid** nas definições do Azure, ou utilize o valor predefinido de **SendGridEmailDelivery.Simplified.SMTPWebAPI**. Nomes tem de estar entre 1 e 100 carateres de comprimento e contêm carateres alfanuméricos apenas, pontos, travessões e sublinhado. O nome deve ser exclusivo na sua lista de itens de arquivo de Azure subscritos.

    ![loja-ecrã-2][store-screen-2]

7. Escolher um valor para a região; Por exemplo, EUA Ocidental.

8. Clique na seta à direita.

9. No separador **Rever compra** , reveja o plano e informação de preços e, reveja os termos de legais. Se concordar com os termos, clique na marca de verificação. Depois de clicar na marca de verificação, a sua conta SendGrid vai começar o [processo de aprovisionamento SendGrid].

    ![loja-ecrã-3][store-screen-3]

10. Depois de confirmar a sua compra é redirecionado para o dashboard de suplementos e verá a mensagem **SendGrid comprar**.

    ![mensagem sendgrid comprar][sendgrid-purchasing-message]

    A conta SendGrid está aprovisionada imediatamente e verá a mensagem **com êxito comprado SendGrid do suplemento**. Agora são criadas a sua conta e as credenciais. Está pronto para enviar e-mails neste momento. 

    Para modificar o seu plano de subscrição ou consulte o artigo SendGrid definições de contactos, clique no nome do seu serviço de SendGrid para abrir o dashboard SendGrid Marketplace. 

    ![sendgrid-adicionar-em-dashboard][sendgrid-add-on-dashboard]

    Para enviar uma mensagem de e-mail utilizando SendGrid, tem de fornecer as credenciais da conta (nome de utilizador e palavra-passe).

### <a name="to-find-your-sendgrid-credentials"></a>Para localizar as suas credenciais SendGrid ###

1. Clique em **informações de ligação**.

    ![informações-botão de sendgrid-ligação][sendgrid-connection-info-button]

2. Na caixa de diálogo *informações de ligação* , copie o nome de utilizador e **palavra-passe** para utilizar posteriormente neste tutorial.

    ![informações da ligação sendgrid][sendgrid-connection-info]

    Para definir o seu e-mail definições deliverability, clique no botão **Gerir** . Isto irá redirecioná-se para o painel de controlo SendGrid. 

    ![Painel de controlo sendgrid][sendgrid-control-panel]

    Para mais informações sobre como começar com SendGrid, consulte o artigo [Introdução ao SendGrid][].

<!--images-->

[command-bar-new]: ./media/sendgrid-sign-up/sendgrid_BAR_NEW.PNG
[sendgrid-store]: ./media/sendgrid-sign-up/sendgrid_offerings_store.png
[store-screen-2]: ./media/sendgrid-sign-up/sendgrid_store_scrn2.png
[store-screen-3]: ./media/sendgrid-sign-up/sendgrid_store_scrn3.png
[sendgrid-purchasing-message]: ./media/sendgrid-sign-up/sendgrid_purchasing_message.png
[sendgrid-add-on-dashboard]: ./media/sendgrid-sign-up/sendgrid_add-on_dashboard.png
[sendgrid-connection-info]: ./media/sendgrid-sign-up/sendgrid_connection_info.png
[sendgrid-connection-info-button]: ./media/sendgrid-sign-up/sendgrid_connection_info_button.png
[sendgrid-control-panel]: ./media/sendgrid-sign-up/sendgrid_control_panel.png

<!--Links-->

[Funcionalidades de SendGrid]: http://sendgrid.com/features
[Portal de gestão do Azure]: https://manage.windowsazure.com
[SendGrid introdução]: http://sendgrid.com/docs
[Processo de aprovisionamento SendGrid]: https://support.sendgrid.com/hc/articles/200181628-Why-is-my-account-being-provisioned-
[todos os APIs]: https://sendgrid.com/docs/API_Reference/index.html

