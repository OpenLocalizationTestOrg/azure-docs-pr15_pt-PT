### <a name="prerequisites"></a>Pré-requisitos
- Uma conta de Twilio
- Um número de telefone Twilio verificado que possa receber SMS
- Um número de telefone Twilio verificado que pode enviar SMS

>[AZURE.NOTE] Se estiver a utilizar uma conta de avaliação Twilio, só pode enviar SMS para números de telefone **verificado** .  

Antes de poder utilizar a sua conta Twilio numa aplicação lógica, tem de autorizar a aplicação de lógica para ligar à sua conta Twilio. Felizmente, pode fazê-lo facilmente a partir do seu app lógica no Portal do Azure. 

Eis os passos para autorizar a sua aplicação de lógica para ligar à sua conta Twilio:

1. Para criar uma ligação ao Twilio, no estruturador de aplicação de lógica, selecione **Mostrar Microsoft gerido APIs** na lista pendente, em seguida, introduza *Twilio* na caixa de pesquisa. Selecione a ação que pretende utilizar ou accionador:  
  ![](./media/connectors-create-api-twilio/twilio-0.png)
2. Se não tiver criado Twilio antes de todas as ligações, irá obter lhe for pedido para fornecer as suas credenciais Twilio. Estas credenciais serão utilizadas para autorizar para ligar à sua aplicação de lógica e aceder aos dados da sua conta Twilio:  
  ![](./media/connectors-create-api-twilio/twilio-1.png)  
3. Terá do **id da conta Twilio** e **Twilio token de acesso** a partir do dashboard no Twilio, por isso, inicie sessão sua conta de Twilio agora de captar estes dois blocos de informações:  
  ![](./media/connectors-create-api-twilio/twilio-2.png)  
4. Aplicações Twilio e lógica utilizam nomes diferentes para identificar estes dois blocos de informações. Eis como tem de mapeá-las para a caixa de diálogo de aplicações de lógica:![](./media/connectors-create-api-twilio/twilio-3.png)  
5. Selecione o botão **Criar ligação** :  
  ![](./media/connectors-create-api-twilio/twilio-4.png)
6. Repare a ligação foi criada e está agora gratuito continuar com os outros passos na sua aplicação de lógica:  
  ![](./media/connectors-create-api-twilio/twilio-5.png)