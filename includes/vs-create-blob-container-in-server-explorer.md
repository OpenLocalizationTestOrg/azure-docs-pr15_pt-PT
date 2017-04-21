Pode criar filas de armazenamento Azure utilizando o Visual Studio **Server Explorer**.

![Servidor Explorer Blobs][Image1]

1. No menu **Ver** , selecione **Explorador de servidor**.
2. No Explorador do servidor, expanda o nó do **Azure** para a sua subscrição, expanda o nó de **armazenamento** e o nó para a conta de armazenamento que especificou no serviço de armazenamento do Windows Azure ligado.
3. Selecione o nó **filas** e escolha **Criar fila** no menu de contexto.
4. Introduza um nome para o contentor e selecione **OK**.   

Por predefinição, o novo contentor for privado e tem de especificar a chave de acesso de armazenamento para transferir blobs neste contentor. Se pretender tornar os ficheiros no contentor público, selecione o contentor no **Explorador do servidor** e prima `F4` para apresentar a janela de **Propriedades** . Configurar o **acesso de leitura público** para **BLOBs**. Qualquer pessoa na Internet pode ver blobs num contentor público, mas pode modificar ou elimine-as apenas se tiver a tecla de acesso adequado.


[Image1]: ./media/vs-create-blob-container-in-server-explorer/vs-storage-create-blob-containers-in-Server-Explorer.png