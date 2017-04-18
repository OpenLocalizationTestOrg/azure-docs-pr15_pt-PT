### <a name="prerequisites"></a>Pré-requisitos

- Uma conta de [SMTP](https://wikipedia.org/wiki/Simple_Mail_Transfer_Protocol)  


Antes de poder utilizar a sua conta SMTP numa aplicação lógica, tem de autorizar a aplicação de lógica para ligar à sua conta SMTP. Felizmente, pode fazê-lo facilmente a partir do seu app lógica no Portal do Azure.  

Eis os passos para autorizar a sua aplicação de lógica para ligar à sua conta SMTP:  
1. Para criar uma ligação ao SMTP, no estruturador de aplicação de lógica, selecione **Mostrar Microsoft gerido APIs** na lista pendente, em seguida, introduza *SMTP* na caixa de pesquisa. Selecione a ação que pretende utilizar ou accionador:  
![](./media/connectors-create-api-smtp/smtp-1.png)  
2. Se não tiver criado SMTP antes de todas as ligações, irá obter lhe for pedido para fornecer as suas credenciais SMTP. Estas credenciais serão utilizadas para autorizar para ligar à sua aplicação de lógica e aceder aos dados da sua conta SMTP:  
![](./media/connectors-create-api-smtp/smtp-2.png)  
3. Repare a ligação foi criada e está agora gratuito continuar com os outros passos na sua aplicação de lógica:  
 ![](./media/connectors-create-api-smtp/smtp-3.png)  

