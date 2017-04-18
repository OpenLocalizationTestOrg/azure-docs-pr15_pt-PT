#### <a name="prerequisites"></a>Pré-requisitos
- Uma conta Azure; Pode criar uma [conta gratuita](https://azure.microsoft.com/free)
- Uma conta [Office 365](https://office365.com)  

Antes de utilizar a sua conta Office 365 numa aplicação lógica, autorize a aplicação de lógica para ligar à sua conta Office 365. Pode fazê-lo facilmente dentro da sua aplicação de lógica no portal do Azure.  

Autorize a sua aplicação de lógica para ligar à sua conta do Office 365 através dos seguintes passos:

1. Crie uma aplicação de lógica. No estruturador de lógica aplicações, selecione **Mostrar Microsoft gerido APIs** na lista pendente e, em seguida, introduza "office 365" na caixa de pesquisa. Selecione uma das accionadores ou ações:  
    ![Passo de criação de ligação do Office 365](./media/connectors-create-api-office365-outlook/office365-sendemail.png)  

2. Se ainda não tiver criado quaisquer ligações para o Office 365, lhe for pedido para iniciar sessão com as suas credenciais do Office 365:  
    ![Passo de criação de ligação do Office 365](./media/connectors-create-api-office365-outlook/office365-signin.png)  

3. Selecione **Iniciar sessão**e introduza o seu nome de utilizador e palavra-passe. Selecione **Iniciar sessão**:  
    ![Passo de criação de ligação do Office 365](./media/connectors-create-api-office365-outlook/office365-usernamepassword.png)

    Estas credenciais são utilizadas para autorizar a sua aplicação de lógica para ligar e aceder à sua conta do Office 365. 

4. Repare que a ligação foi criada. Agora, continue com os outros passos na sua aplicação de lógica:   
    ![Passo de criação de ligação do Office 365](./media/connectors-create-api-office365-outlook/office365-sendemailproperties.png)  
  