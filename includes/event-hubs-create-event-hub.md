## <a name="create-an-event-hub"></a>Criar um concentrador de evento

1. Inicie a sessão [portal do Azure][]e clique em **Novo** na parte superior esquerdo do ecrã.

2. Clique em **dados + Analytics**, em seguida, clique em **Evento concentradores**.

    ![](./media/event-hubs-create-event-hub/create-event-hub9.png)

3. Na pá **criar espaço de nomes** , introduza um nome de espaço de nomes. O sistema imediatamente verifica se o nome está disponível.

    ![](./media/event-hubs-create-event-hub/create-event-hub1.png)

4. Após certificar-se de que o nome do espaço de nomes estiver disponível, selecione a camada comparar (Basic ou padrão). Além disso, selecione uma subscrição do Azure, grupo de recursos e a localização na qual pretende criar o recurso. 

2. Clique em **Criar** para criar o espaço de nomes.

6. Na lista de espaço de nomes de concentradores de evento, clique no espaço de nomes recentemente criado.      

    ![](./media/event-hubs-create-event-hub/create-event-hub2.png)

7. Na pá espaço de nomes, clique em **Evento concentradores**.

    ![](./media/event-hubs-create-event-hub/create-event-hub3.png)

8. Na parte superior da pá, clique em **Adicionar concentrador de evento**.

    ![](./media/event-hubs-create-event-hub/create-event-hub4.png)

3. Escreva um nome para o seu centro de evento e, em seguida, clique em **Criar**.

    ![](./media/event-hubs-create-event-hub/create-event-hub5.png)

4. Na lista de concentradores de evento, clique no nome de evento concentrador recentemente criado. 

    ![](./media/event-hubs-create-event-hub/create-event-hub6.png)

5. Novamente na pá espaço de nomes (não a pá concentrador de evento específico), clique em **partilhado as políticas de acesso**e, em seguida, clique em **RootManageSharedAccessKey**.

    ![](./media/event-hubs-create-event-hub/create-event-hub7.png)

5. Clique no botão Copiar para copiar a cadeia de ligação **RootManageSharedAccessKey** para a área de transferência. Guarde esta cadeia de ligação para utilizar mais tarde no tutorial.

    ![](./media/event-hubs-create-event-hub/create-event-hub8.png)

Agora é criado o seu centro de evento e tiver as cadeias de ligação que precisa para enviar e receber eventos.

[Portal do Azure]: https://portal.azure.com/