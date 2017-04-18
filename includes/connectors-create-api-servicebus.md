### <a name="prerequisites"></a>Pré-requisitos

Tem de ter uma conta de [Serviço Bus](https://azure.microsoft.com/services/service-bus/) .  

Antes de poder utilizar a sua conta Azure Service Bus numa aplicação lógica, tem de autorizar a aplicação de lógica para ligar à sua conta do serviço bus. Felizmente, pode fazê-lo facilmente a partir do seu app lógica no portal do Azure.  

Eis os passos para autorize a sua aplicação de lógica para ligar à sua conta de serviço Bus:  

1. Para criar uma ligação ao serviço Bus, no estruturador de aplicação de lógica, selecione **Mostrar Microsoft gerido APIs** na lista pendente. Em seguida, introduza **bus serviço** na caixa de pesquisa. Selecione o acionador ou a ação que pretende utilizar.  
    ![Imagem de ligação de serviço Bus 1](./media/connectors-create-api-servicebus/servicebus-1.png)  

2. Se não tiver criado quaisquer ligações ao serviço Bus antes, será solicitado para fornecer as suas credenciais Bus de serviço. Estas credenciais são utilizadas para autorizar a sua aplicação de lógica para ligar e aceder aos dados da sua conta de serviço Bus. O conector de serviço Bus necessita a cadeia de ligação para o espaço de nomes de serviço Bus. Requer também **Gerir** permissões. Uma boa forma de saber se está a cadeia de ligação para o espaço de nomes ou uma entidade específica está se contém o `EntityPath` parâmetro. Caso isso aconteça, não é a cadeia de ligação à direita para uma aplicação de lógica.  
    ![Cadeia de ligação Bus de serviço](./media/connectors-create-api-servicebus/connectionstring.png)

1. Depois de ter recebido a cadeia de ligação para o espaço de nomes, pode utilizá-lo para a ligação API nas aplicações de lógica.  
    ![Imagem de ligação de serviço Bus 2](./media/connectors-create-api-servicebus/servicebus-2.png)  

3. Repare que a ligação foi criada, e consoante esteja agora gratuito continuar com os outros passos na sua aplicação de lógica.  
    ![Imagem de ligação de serviço Bus 3](./media/connectors-create-api-servicebus/servicebus-3.png)   
