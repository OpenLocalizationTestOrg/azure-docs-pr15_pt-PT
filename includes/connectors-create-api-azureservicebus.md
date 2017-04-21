Agora que adicionou um accionador, empatia respectiva hora alguma coisa com os dados que são gerados pelo accionador. Siga estes passos para adicionar um a ação do **SharePoint Online - criar o ficheiro** . Esta ação irá criar um ficheiro no SharePoint Online sempre que o novo item accionador é acionada. 

Para configurar esta ação, terá de fornecer as informações seguintes. Notará que é fácil de utilizar dados gerados pelo accionador como entrada para algumas das propriedades para o novo ficheiro:

|Criar propriedade de ficheiro|Descrição|
|---|---|
|URL do site|Este é o URL do site do SharePoint Online, onde pretende criar o novo ficheiro. Selecione o site a partir da lista apresentada.|
|Caminho da pasta|Esta é a pasta (no URL do Site) onde será colocado o novo ficheiro. Procure e selecione a pasta.|
|Nome do ficheiro|Este é o nome do ficheiro a ser criado.|
|Conteúdo do ficheiro|O conteúdo que vai ser escrito para o ficheiro.|

1. Selecione **+ novo passo** para adicionar a ação.  
![](./media/connectors-create-api-sharepointonline/action-1.png)  
- Selecione a ligação **Adicionar uma ação** . Esta abre-se a caixa de pesquisa onde pode procurar por qualquer ação pretende utilizar para tirar. Neste exemplo, as ações do SharePoint são de interesse.    
![](./media/connectors-create-api-sharepointonline/action-2.png)    
- Introduza o *sharepoint* para procurar ações relacionados com o SharePoint.
- Selecione **SharePoint Online - criar o ficheiro** como a ação a tomar.   **Nota**: serão ser-lhe autorizar a sua aplicação de lógica para aceder à sua conta do SharePoint, se ainda não feito anteriormente.    
![](./media/connectors-create-api-sharepointonline/action-3.png)    
- O controlo de **criar ficheiros** é aberta.   
![](./media/connectors-create-api-sharepointonline/action-4.png)     
- Selecione o **URL do Site** e navegue para localizar o site onde pretende criar o ficheiro.     
![](./media/connectors-create-api-sharepointonline/action-5.png)  
- Selecione o **caminho da pasta** e procurar para localizar a pasta onde o novo ficheiro será colocado.  
![](./media/connectors-create-api-sharepointonline/action-6.png)  
- Selecione o controlo de **nome de ficheiro** e introduza o nome do ficheiro que pretende criar. Para o nome de ficheiro, tenha em atenção que pode utilizar qualquer uma das propriedades de accionador que criou anteriormente ao selecioná-lo simplesmente a partir da lista apresentada.     
![](./media/connectors-create-api-sharepointonline/action-7.png)  
- Selecione o controlo de **conteúdo do ficheiro** e introduza o conteúdo que vai ser escrito para o ficheiro que será criado. Para o conteúdo do ficheiro, tenha em atenção que pode utilizar qualquer uma das propriedades de accionador que criou anteriormente. Basta Selecione as propriedades a partir da lista apresentada. Em alternativa, pode introduzir o texto de **conteúdo do ficheiro** diretamente para o controlo. Neste exemplo, posso selecionada algumas propriedades e adicionar espaços e um hífen entre cada propriedade.        
![](./media/connectors-create-api-sharepointonline/action-8.png)  
- Guardar as alterações ao seu fluxo de trabalho  
