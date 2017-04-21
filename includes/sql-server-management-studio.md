
   * Inicie sessão conta Azure ao introduzir as suas credenciais.

     Este método é mais rápido e mais fácil, mas se utilizar este método não será possível ver a base de dados do SQL Azure ou serviços de telemóvel na janela do **Explorador de servidor** .

     No **Explorador do servidor**, clique no botão de **ligar ao Azure** . É uma alternativa clique com o botão direito do rato em **Azure** e, em seguida, clique em **ligar ao Azure** no menu de contexto.

   * Instale um certificado de gestão que ativa o acesso à sua conta.

     No **Explorador do servidor**, clique com o botão direito do rato em **Azure** e, em seguida, clique em **Gerir subscrições** no menu de contexto. Na caixa de diálogo **Gerir Azure subscrições** , clique no separador **certificados** e, em seguida, clique em **Importar**. Siga as instruções para transferir e, em seguida, importar um ficheiro de subscrição (também designado de um ficheiro de *.publishsettings* ) para a sua conta Azure.

     > [AZURE.NOTE] Transferir o ficheiro de subscrição para uma pasta fora da sua directórios de código de origem (por exemplo, na pasta transferências) e, em seguida, elimine-o quando a importação estiver concluída. Um utilizador malicioso que obtém acesso ao ficheiro de subscrição pode editar, criar e eliminar os seus serviços Azure.

    Para mais informações, consulte o artigo [como ligar ao Azure a partir do Visual Studio](http://go.microsoft.com/fwlink/?LinkId=324796).
