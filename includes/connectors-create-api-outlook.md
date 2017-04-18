## <a name="connect-to-outlookcom"></a>Ligar ao Outlook.com

### <a name="prerequisites"></a>Pré-requisitos
- Uma conta do Outlook.com

Antes de poder utilizar a sua conta do Outlook.com numa aplicação lógica, tem de autorizar a aplicação de lógica para ligar à sua conta do Outlook.com. Felizmente, pode fazê-lo facilmente a partir do seu app lógica no Portal do Azure. 

Eis os passos para autorizar a sua aplicação de lógica para ligar à sua conta do Outlook.com:

1. Todas as aplicações de lógica tem de ser iniciado por um accionador para após criar a aplicação de lógica, o designer abre e apresenta uma lista de accionadores que pode utilizar para iniciar a sua aplicação de lógica:

  ![](./media/connectors-create-api-outlook/office365-outlook-0.png)
2. Introduza "outlook" na caixa de pesquisa. Repare que a lista é filtrada para todos os accionadores com "Outlook" no nome da lista:![](./media/connectors-create-api-outlook/office365-outlook-0-5.png)
3. Selecione **Novo e-mail do Office 365 Outlook**.   
  Se não tiver criado quaisquer ligações para o Outlook antes, irá obter lhe for pedido para fornecer as suas credenciais do Outlook.com. Estas credenciais serão utilizadas para autorizar para ligar à sua aplicação de lógica e aceder aos dados da sua conta do Outlook.com:![](./media/connectors-create-api-outlook/office365-outlook-1.png)
4. Fornecer as suas credenciais para o Outlook e inicie sessão:![](./media/connectors-create-api-outlook/office365-outlook-2.png)  
  Já está. Agora que criou uma ligação para o Outlook. Esta ligação estarão disponível para utilização em qualquer aplicação de lógica que criar.


