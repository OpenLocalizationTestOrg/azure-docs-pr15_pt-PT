
1. No seu Mac, visite o [Azure Portal]. Clique em **Procurar tudo** > **Aplicações Mobile** > back-end que acabou de criar. Nas definições de aplicação móvel, clique em **Guia de introdução** > **iOS (objectivo-C)**. Se preferir Swift, clique em **Guia de introdução** > **iOS (Swift)** em vez disso. Em **Transferir e executa o iOS projeto**, clique em **Transferir**. Isto transfere um projecto Xcode completo para uma aplicação pré-configurada para ligar à sua back-end. Abra o projecto utilizando Xcode.

2. Prima o botão **Executar** para criar o projecto e inicie a aplicação no simulator iOS.

3. Na aplicação, escreva o texto relevante, tal como _concluída a iniciação_ e, em seguida, clique no sinal de adição (**+**) ícone. Isto envia um pedido de mensagem para o Azure back-end que implementado anterior. Os dados de inserções back-end a partir do pedido são na tabela TodoItem SQL e devolve informações sobre os itens recentemente armazenados novamente para a aplicação móvel. A aplicação móvel apresenta estes dados na lista. 

    ![](./media/app-service-mobile-ios-quickstart/mobile-quickstart-startup-ios.png)

[Portal do Azure]: https://portal.azure.com/
