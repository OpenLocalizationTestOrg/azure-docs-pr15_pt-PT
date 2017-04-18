Neste exemplo, posso irá mostrar-lhe como utilizar o **SharePoint Online - quando é criado um novo item** accionador para iniciar um fluxo de trabalho de aplicação de lógica quando é criado um novo item numa lista do SharePoint Online.

>[AZURE.NOTE]Irá obter lhe for pedido para iniciar sessão na sua conta do SharePoint, se ainda não tiver criado uma *ligação* ao SharePoint Online.  

1. Introduza *sharepoint* na caixa de pesquisa no estruturador de aplicações de lógica, em seguida, selecione o accionador **SharePoint Online - quando é criado um novo item** .  
![Imagem do SharePoint online accionador](./media/connectors-create-api-sharepointonline/trigger-1.png)  
- É apresentado o controlo **quando é criado um novo item** .  
![Imagem do SharePoint online accionador 2](./media/connectors-create-api-sharepointonline/trigger-2.png)   
- Selecione um **URL do Site**. Este é o site SharePoint online que pretende para monitorizar a existência de novos itens acionar o fluxo de trabalho.  
![Imagem do SharePoint online acionador 3](./media/connectors-create-api-sharepointonline/trigger-3.png)   
- Selecione um **nome da lista**. Esta é a lista no site do SharePoint Online que pretende para monitorizar a existência de novos itens que irão acionar o fluxo de trabalho.  
![Imagem do SharePoint online acionador 4](./media/connectors-create-api-sharepointonline/trigger-4.png)   

Neste momento, a sua aplicação de lógica ter sido configurada com um accionador que começará a execução dos outros accionadores e ações no fluxo de trabalho. Esta será executada sempre que é criado um novo item na lista do SharePoint Online que selecionou.  