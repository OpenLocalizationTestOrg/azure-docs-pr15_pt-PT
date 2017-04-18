#### <a name="prerequisites"></a>Pré-requisitos
- Uma conta Azure; Pode criar uma [conta gratuita](https://azure.microsoft.com/free)
- Uma conta do [Dynamics CRM Online](https://www.microsoft.com/en-us/dynamics/crm-free-trial-overview.aspx) 

Antes de utilizar a sua conta do Dynamics numa aplicação lógica, autorize a aplicação de lógica para ligar à sua conta CRM Online. Pode fazê-lo facilmente dentro da sua aplicação de lógica no portal do Azure. 

Autorize a sua aplicação de lógica para ligar à sua conta CRM Online através dos seguintes passos:

1. Crie uma aplicação de lógica. No estruturador de lógica aplicações, selecione **Mostrar Microsoft gerido APIs** na lista pendente e, em seguida, introduza "dynamics" na caixa de pesquisa. Selecione uma das accionadores ou ações:  
  ![](./media/connectors-create-api-crmonline/dynamics-triggers.png)
2. Se ainda não tiver criado quaisquer ligações ao Dynamics, lhe for pedido para iniciar sessão com as suas credenciais do Dynamics:  
  ![](./media/connectors-create-api-crmonline/dynamics-signin.png)
3. Selecione **Iniciar sessão**e introduza o seu nome de utilizador e palavra-passe. Selecione **Iniciar sessão**. 

    Estas credenciais são utilizadas para autorizar a sua aplicação de lógica para ligar e aceder aos dados na sua conta do Dynamics. 
4. Repare que a ligação foi criada. Agora, continue com os outros passos na sua aplicação de lógica:  
  ![](./media/connectors-create-api-crmonline/dynamics-properties.png)
