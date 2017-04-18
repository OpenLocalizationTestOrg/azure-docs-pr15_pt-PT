
1. Visite o [Azure Portal]. Clique em **Procurar tudo** > **Aplicações Mobile** > back-end que acabou de criar. Nas definições de aplicação móvel, clique em **Guia de introdução** > **Android)**. Em **Configurar a aplicação de cliente**, clique em **Transferir**. Isto transfere um projecto Android completo para uma aplicação pré-configurada para ligar à sua back-end. 

2. Abra o projeto utilizando o **Android Studio**, utilizando o **project importar (Eclipse TSA, Gradle, etc.)**. Certifique-se de que efetue esta seleção de importação para evitar erros JDK.

3. Prima o botão **Executar 'aplicação'** para criar o projecto e inicie a aplicação no simulator Android.

4. Na aplicação, escreva o texto relevante, tal como _concluída a iniciação_ e, em seguida, clique no botão de 'Adicionar'. Isto envia um pedido de mensagem para o Azure back-end que implementado anterior. Os dados de inserções back-end a partir do pedido são na tabela TodoItem SQL e devolve informações sobre os itens recentemente armazenados novamente para a aplicação móvel. A aplicação móvel apresenta estes dados na lista. 

    ![](./media/app-service-mobile-android-quickstart/mobile-quickstart-startup-android.png)

[Portal do Azure]: https://portal.azure.com/
