Eis como utilizar o **Serviço Bus - quando for recebida uma mensagem na fila de uma** accionador para iniciar um fluxo de trabalho de aplicação de lógica quando um novo item é enviado para uma fila Bus de serviço.  

>[AZURE.NOTE]Vai ser-lhe para iniciar sessão com a cadeia de ligação de serviço Bus se ainda não tiver criado uma ligação ao serviço Bus.  

1. Na caixa de pesquisa no estruturador de aplicações de lógica, introduza **bus de serviço**. Em seguida, selecione o **Serviço Bus - quando for recebida uma mensagem numa fila** accionador.  
![Imagem de accionador serviço Bus 1](./media/connectors-create-api-servicebus/trigger-1.png)   
- É apresentada a caixa de diálogo **quando for recebida uma mensagem de uma fila** .  
![Imagem de accionador serviço Bus 2](./media/connectors-create-api-servicebus/trigger-2.png)   
- Introduza o nome da fila Bus de serviço que pretende utilizar o accionador para monitorizar a.   
![Imagem de accionador serviço Bus 3](./media/connectors-create-api-servicebus/trigger-3.png)   

Neste momento, a sua aplicação de lógica ter sido configurada com um accionador. Quando um novo item é recebido na fila de espera que selecionou, o accionador começará a execução dos outros accionadores e ações no fluxo de trabalho.    
