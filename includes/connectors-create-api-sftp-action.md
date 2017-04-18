Agora que adicionou um accionador, empatia respectiva hora alguma coisa com os dados que são gerados pelo accionador. Siga estes passos para adicionar um a ação **SFTP - extrair pasta** . Esta ação irá extrair os conteúdos de um ficheiro, se as condições definidas forem cumpridas. 

Para configurar esta ação, terá de fornecer as informações seguintes. Notará que é fácil de utilizar dados gerados pelo accionador como entrada para algumas das propriedades para o novo ficheiro:

|SFTP - extrair pasta propriedade|Descrição|
|---|---|
|Caminho do ficheiro de arquivo de origem|Este é o caminho para o ficheiro a ser extraído. Pode Selecione uma dos tokens a partir de uma ação anterior ou procure o servidor SFTP para encontrar o caminho do ficheiro.|
|Caminho da pasta de destino|Este é o caminho onde serão colocados os ficheiros extraídos. Selecione uma dos tokens a partir de uma ação anterior como o caminho de destino ou procure o servidor SFTP e selecione um caminho.|
|Substituir?|Indica se um ficheiro com o mesmo nome que o ficheiro extraído está localizado no caminho da pasta de destino se deve ser substituído o ficheiro existente ou não.|

Vamos começar a adicionar a ação para extrair os ficheiros se a condição que definiu anteriormente é avaliada como *verdadeira*. 

1. Selecione **Adicionar uma ação**.        
![Imagem de condição de acção SFTP 6](./media/connectors-create-api-sftp/condition-6.png)   
- Selecione a ação **SFTP - extrair pasta**      
![Imagem de condição de acção SFTP 7](./media/connectors-create-api-sftp/condition-7.png)   
- Selecione o **caminho do ficheiro de arquivo de origem**              
![Imagem de condição de acção SFTP 9](./media/connectors-create-api-sftp/condition-9.png)   
- Selecione o token de **caminho do ficheiro** . Isto indica que irá utilizar o caminho do ficheiro do ficheiro que o accionador encontrado como o caminho de ficheiro de arquivo de origem.           
![Imagem de condição de acção SFTP 10](./media/connectors-create-api-sftp/condition-10.png)   
- Selecione o **caminho da pasta de destino**           
![Imagem de condição de acção SFTP 11](./media/connectors-create-api-sftp/condition-11.png)   
- Selecione o token de **caminho do ficheiro** . Isto indica que irá utilizar o caminho do ficheiro do ficheiro que o accionador encontrado como o caminho de destino para os ficheiros extraídos.   
- Introduza *\ExtractedFile* no controlo **caminho da pasta de destino** . Faça isto apenas depois do token de caminho do ficheiro no controlo de caminho da pasta de destino.         
![Imagem de condição de acção SFTP 12](./media/connectors-create-api-sftp/condition-12.png)   
- Introduza *Verdadeiro* no **Substituir?* controlo para indicar que devem ser substituídos ficheiros existentes, caso tenham o mesmo nome como os ficheiros extraídos.      
![Imagem de condição de acção SFTP 13](./media/connectors-create-api-sftp/condition-13.png)   
- Guardar as alterações ao seu fluxo de trabalho  
