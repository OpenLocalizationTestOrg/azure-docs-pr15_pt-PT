#### <a name="prerequisites"></a>Pré-requisitos
- Uma conta Azure; Pode criar uma [conta gratuita](https://azure.microsoft.com/free)
- Uma conta do [OneDrive](https://www.microsoft.com/store/apps/onedrive/9wzdncrfj1p3) 

Antes de poder utilizar a sua conta do OneDrive numa aplicação lógica, autorize a aplicação de lógica para ligar à sua conta do OneDrive.  Pode fazê-lo facilmente dentro da sua aplicação de lógica no portal do Azure. 

Autorize a sua aplicação de lógica para ligar à sua conta do OneDrive através dos seguintes passos:

1. Crie uma aplicação de lógica. No estruturador de lógica aplicações, selecione **Mostrar Microsoft gerido APIs** na lista pendente e, em seguida, introduza "onedrive" na caixa de pesquisa. Selecione uma das accionadores ou ações:  
  ![](./media/connectors-create-api-onedrive/onedrive-1.png)
2. Se ainda não tiver criado quaisquer ligações para o OneDrive, lhe for pedido para iniciar sessão com as suas credenciais do OneDrive:  
  ![](./media/connectors-create-api-onedrive/onedrive-2.png)
3. Selecione **Iniciar sessão**e introduza o seu nome de utilizador e palavra-passe. Selecione **Iniciar sessão**:  
  ![](./media/connectors-create-api-onedrive/onedrive-3.png)   

    Estas credenciais são utilizadas para autorizar a sua aplicação de lógica para ligar e aceder aos dados na sua conta do OneDrive. 
4. Selecione **Sim** para autorizar a aplicação de lógica para utilizar a sua conta do OneDrive:  
  ![](./media/connectors-create-api-onedrive/onedrive-4.png)   
5. Repare que a ligação foi criada. Agora, continue com os outros passos na sua aplicação de lógica:  
  ![](./media/connectors-create-api-onedrive/onedrive-5.png)
