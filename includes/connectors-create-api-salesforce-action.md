Agora que adicionou uma condição, o seu tempo para fazer algo empatia com os dados que são gerados pelo accionador. Siga estes passos para adicionar a ação de **Salesforce - obter objeto** . Esta ação irá obter os dados de cada vez, que é criada uma nova oportunidade potencial. Também irá adicionar uma segunda acção que irá utilizar os dados a partir do Salesforce - obter uma ação do objeto para enviar uma mensagem de e-mail utilizando o conector do Office 365.  

Para configurar esta ação, terá de fornecer as informações seguintes. Notará que é fácil de utilizar dados gerados pelo accionador como entrada para algumas das propriedades para o novo ficheiro:

|Criar propriedade de ficheiro|Descrição|
|---|---|
|Tipo de objeto|Este é o tipo de objeto do Salesforce que lhe interessam. Exemplos são oportunidade potencial, conta, etc.|
|ID de objecto|Isto representa um identificador para o objeto.|


1. Selecione a ligação **Adicionar uma ação** . Esta abre-se a caixa de pesquisa onde pode procurar por qualquer ação pretende utilizar para tirar. Neste exemplo, Salesforce ações são de interesse.      
![Imagem de acção Salesforce 1](./media/connectors-create-api-salesforce/action-1.png)  
- Introduza a *equipa de vendas* para procurar acções relacionadas com a equipa de vendas.
- Selecione **Salesforce - obter objeto** como a ação a tomar.   **Nota**: serão ser-lhe autorizar a sua aplicação de lógica para aceder à sua conta do Salesforce, se ainda não feito anteriormente.    
![Imagem de acção Salesforce 2](./media/connectors-create-api-salesforce/action-2.png)    
- O controlo de **obter o objeto** é aberta.  
- Selecione *conduzir* como o tipo de objeto.
- Selecione o controlo de **ID do objeto** .
- Selecione **…** para expandir a lista de tokens que podem ser utilizados como entrada para ações.       
![Imagem de acção Salesforce 3](./media/connectors-create-api-salesforce/action-3.png)    
- Selecione **Conduzir ID** controlo abre.   
![Imagem de acção Salesforce 4](./media/connectors-create-api-salesforce/action-4.png)     
- Repare que o token de oportunidade potencial ID é agora no controlo de ID do objeto, que indica que a ação do objeto Get irá procurar uma oportunidade potencial com um ID que é igual ao ID de oportunidade potencial do potencial cliente que o acionou esta aplicação lógica.  
![Imagem de acção Salesforce 5](./media/connectors-create-api-salesforce/action-5.png)  
- Guarde o seu trabalho. Já está, que adicionou a ação do objeto obter para a sua aplicação de lógica. O controlo do objeto Get deve ter este aspeto:    
![Imagem de acção Salesforce 6](./media/connectors-create-api-salesforce/action-6.png)  

Agora que tiver adicionado uma ação para obter uma oportunidade potencial, poderá querer fazer algo interessante com a oportunidade potencial recentemente criada. Numa empresa, poderá querer enviar um e-mail para notificar uma lista de distribuição que foi criada uma nova oportunidade potencial. Vamos utilizar o conector do Office 365 para enviar um e-mail com algumas das informações relevantes a partir do novo objecto de oportunidade potencial no Salesforce.  

1. Selecione **Adicionar uma ação** , em seguida, introduza o *e-mail* no controlo de pesquisa. Este filtra as ações para aqueles que estão relacionados com a enviar e receber e-mails.  
- Selecione o item de lista do **Office 365 Outlook - enviar uma mensagem de e-mail** . Se já tiver criado uma *ligação* à sua conta do Office 365, ser-lhe-á pedido para introduzir as credenciais do Office 365 criá-lo agora. Depois de terminar, abre o controlo de **Enviar um e-mail** .        
![Imagem de acção Salesforce 7](./media/connectors-create-api-salesforce/action-7.png)  
- Introduza o endereço de e-mail gostaria de enviar o e-mail no controlo **para** .
-  No controlo de **Assunto** , introduza a *nova oportunidade potencial criado* - em seguida, selecione o token de *empresa* . Isto vai mostrar o campo *empresa* da nova oportunidade potencial criado no Salesforce.  
-  No controlo de **corpo** , pode selecionar qualquer uma dos tokens a partir do novo objeto de oportunidade potencial e também pode introduzir o texto que pretende apresentar no corpo da mensagem de correio electrónico. Eis um exemplo:  
![Imagem de acção Salesforce 8](./media/connectors-create-api-salesforce/action-8.png)   
- Guarde o seu fluxo de trabalho.  

Já está. A aplicação de lógica agora está concluída.  

Agora, pode testar a sua aplicação de lógica: no Salesforce, crie uma nova oportunidade potencial que satisfaça a condição que criou.  Se seguiu totalmente este guia passo a passo, crie uma oportunidade potencial com um endereço de e-mail que contenha *amazon.com* no mesmo. Depois de aguardar alguns segundos deve ser acionou sua aplicação de lógica e os resultados podem ter um aspeto semelhantes ao seguinte:  
![Imagem de acção Salesforce 9](./media/connectors-create-api-salesforce/action-9.png)  

