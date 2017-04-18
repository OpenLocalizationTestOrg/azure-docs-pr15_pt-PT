
1. Visite o [Azure Portal]. Clique em **Procurar tudo** > **Aplicações Mobile** > back-end que acabou de criar. Nas definições de aplicação móvel, clique em **Guia de introdução** > **Cordova**. Em **Configurar a aplicação de cliente**, selecione **criar uma nova aplicação**, em seguida, clique em **Transferir**. Isto transfere um projecto Cordova completo para uma aplicação pré-configurada para ligar à sua back-end.

2. Descompactar o ficheiro transferido do postal para um diretório no seu disco rígido, navegue para o ficheiro da solução (.sln) e abri-lo utilizando o Visual Studio.

5. No Visual Studio, selecionar a plataforma de solução (Android, iOS ou Windows) a partir do menu pendente junto a seta de início, em seguida, selecione um dispositivo de implementação específico ou emulador ao clicar em pendente na seta verde. Tenha em atenção que pode utilizar a plataforma Android predefinido e ondulação emulador. Tutoriais mais avançadas necessitam que selecionar um dispositivo suportado ou emulador. 

6. Prima F5 ou clique na seta verde para criar e e executar a sua aplicação Cordova. Se vir uma caixa de diálogo de segurança no emulador pedido de acesso à rede, aceite-lo.   

7. Depois da a aplicação é iniciada no dispositivo ou emulador, escreva o texto relevante no **texto novo Enter**, tal como _concluída a iniciação_ e, em seguida, clique no botão **Adicionar** .  
Isto envia um pedido de mensagem para o Azure back-end que implementado anterior. Os dados de inserções back-end a partir do pedido são na tabela TodoItem na base de dados SQL e devolve informações sobre os itens recentemente armazenados novamente para a aplicação móvel. A aplicação móvel apresenta estes dados na lista.

    ![](./media/app-service-mobile-cordova-quickstart/quickstart-startup.png)
    
8. Repita os três passos anterior para cada plataforma do dispositivo que planeia de suporte.

[Portal do Azure]: https://portal.azure.com/
