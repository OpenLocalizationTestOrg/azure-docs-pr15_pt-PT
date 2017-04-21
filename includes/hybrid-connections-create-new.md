
1. No computador no local, inicie sessão no [Portal de gestão do Azure](http://manager.windowsazure.com) (este é o portal antigo).

2. Na parte inferior do painel de navegação, selecione **+ Novo** > **Serviços de aplicação** > **BizTalk serviço** > **Criar personalizado**.

3. Forneça um **Nome de serviço BizTalk** e selecione uma **Edition**. 

    Neste tutorial, utiliza **mobile1**. Terá de fornecer um nome exclusivo para o seu novo serviço BizTalk.

4. Assim que o serviço de BizTalk ter sido criado, selecione o separador **Híbrido ligações** , em seguida, clique em **Adicionar**.

    ![Adicionar ligação híbrido](./media/hybrid-connections-create-new/3.png)

    Esta ação cria uma nova ligação de híbrido.

5. Fornecer um **nome** e o **Nome do anfitrião** para a sua ligação de híbrido e defina **porta** para `1433`. 
  
    ![Configurar ligação híbrido](./media/hybrid-connections-create-new/4.png)

    O nome do anfitrião é o nome do servidor no local. Este procedimento configura a ligação híbrido para aceder ao SQL Server em execução no porta 1433. Se estiver a utilizar uma instância do SQL Server com nome, em vez disso, utilize a porta estática que definiu anteriormente.

6. Quando a nova ligação estiver criada, o estado da de nova ligação mostra **Configuração incompleta no local**.

7. Navegar de volta para o seu serviço móvel, clique em **Configurar**, desloque para baixo para **ligações de híbrido** e clique em **Adicionar ligação híbrido**, em seguida, selecione a ligação híbrido que acabou de criar e clique em **OK.**

    Permite que o seu serviço móvel utilizar a sua nova ligação de híbrido.

Em seguida, terá de instalar o Gestor de ligação híbrido no computador no local.