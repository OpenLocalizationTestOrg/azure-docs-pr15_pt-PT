Agora que adicionou um accionador, empatia respectiva hora alguma coisa com os dados que são gerados pelo accionador. Siga estes passos para adicionar a ação do **SharePoint Online - criar o ficheiro** . Esta ação irá criar um ficheiro no SharePoint Online sempre que o novo item accionador é acionada. 

Para configurar esta ação, terá de fornecer as informações seguintes. Como fornecer esta informação, irá reparar que é fácil de utilizar dados gerados pelo accionador como entrada para algumas das propriedades para o novo ficheiro:

|Criar propriedade de ficheiro|Descrição|
|---|---|
|URL do site|Este é o URL do site do SharePoint Online, onde pretende criar o novo ficheiro. Selecione o site a partir da lista apresentada.|
|Caminho da pasta|Esta é a pasta (no URL do Site selecionado no passo anterior) onde o novo ficheiro será colocado. Procure e selecione a pasta.|
|Nome do ficheiro|Este é o nome do ficheiro a ser criado.|
|Conteúdo do ficheiro|O conteúdo que vai ser escrito para o ficheiro.|

1. Selecione **+ novo passo** para adicionar a ação.  
![Imagem do SharePoint online ação 1](./media/connectors-create-api-sharepointonline/action-1.png)  
- Selecione a ligação **Adicionar uma ação** . Esta abre-se a caixa de pesquisa onde pode procurar por qualquer ação pretende utilizar para tirar. Neste exemplo, as ações do SharePoint são de interesse.    
![Imagem do SharePoint online ação 2](./media/connectors-create-api-sharepointonline/action-2.png)    
- Introduza o *sharepoint* para procurar ações relacionados com o SharePoint.
- Selecione **SharePoint Online - criar o ficheiro** como a ação a tomar.   **Nota**: serão ser-lhe autorizar a sua aplicação de lógica para aceder à sua conta do SharePoint, se não tiver criado anteriormente uma ligação ao SharePoint Online.    
![Imagem do SharePoint online ação 3](./media/connectors-create-api-sharepointonline/action-3.png)    
- O controlo de **criar ficheiros** é aberta.   
![Imagem do SharePoint online ação 4](./media/connectors-create-api-sharepointonline/action-4.png)     
- Selecione o **URL do Site** e navegue para localizar o site onde pretende criar o ficheiro.     
![Imagem do SharePoint online ação 5](./media/connectors-create-api-sharepointonline/action-5.png)  
- Selecione o **caminho da pasta** e procurar para localizar a pasta onde o novo ficheiro será colocado.  
![Imagem do SharePoint online ação 6](./media/connectors-create-api-sharepointonline/action-6.png)  
- Selecione o controlo de **nome de ficheiro** e introduza o nome do ficheiro que pretende criar. Aqui, pode introduzir o nome do ficheiro diretamente ou pode utilizar qualquer uma das propriedades de accionador que criou anteriormente. Pode fazê-lo ao selecionar as propriedades da lista de **resultados de quando é criado um novo item**. Esta lista é apenas visualização depois de selecionar o controlo de **nome de ficheiro** . Neste walkthough, posso seleccionado ID (ID do item da lista nova) como o nome do ficheiro a ser criado pela ação **SharePoint Online - criar o ficheiro** .    
![Imagem do SharePoint online ação 7](./media/connectors-create-api-sharepointonline/action-7.png)  
- Selecione o controlo de **conteúdo do ficheiro** e introduza o conteúdo que vai ser escrito para o ficheiro que será criado. Para o conteúdo do ficheiro, tenha em atenção que pode utilizar qualquer uma das propriedades de accionador que criou anteriormente. Basta Selecione as propriedades a partir da lista apresentada. Em alternativa, pode introduzir o texto de **conteúdo do ficheiro** diretamente para o controlo. Neste exemplo, posso selecionada algumas propriedades e adicionar espaços e um hífen entre cada propriedade.        
![Imagem do SharePoint online ação 8](./media/connectors-create-api-sharepointonline/action-8.png)  
- Guardar as alterações ao seu fluxo de trabalho  
- Parabéns, tem agora uma aplicação de lógica totalmente funcional é acionada quando um novo item é adicionado a uma lista do SharePoint Online. A aplicação, em seguida, irá criar um ficheiro, com algumas das propriedades do item de lista nova.  Agora pode testá-la ao criar um novo item na lista do SharePoint. 
