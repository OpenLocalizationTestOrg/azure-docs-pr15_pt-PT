Vamos adicionar um accionador.

1. Introduza *sftp* na caixa de pesquisa no estruturador de aplicações de lógica, em seguida, selecione o accionador **SFTP - quando um ficheiro é adicionado ou modificado**   
![Imagem de accionador SFTP 1](./media/connectors-create-api-sftp/trigger-1.png)  
- O controlo **Quando um ficheiro é adicionado ou modificado** abre-se para cima  
![Imagem de accionador SFTP 2](./media/connectors-create-api-sftp/trigger-2.png)  
- Selecione **…** localizada no lado direito do controlo. Esta ação abre o controlo de selecionador de pasta  
![Imagem de accionador SFTP 3](./media/connectors-create-api-sftp/action-1.png)  
- Selecione o **SFTP** para selecionar a pasta de raiz como a pasta para monitorizar a ficheiros novos ou modificados. Aviso da pasta raiz agora é apresentado no controlo de **pasta** .  
![Imagem de accionador SFTP 4](./media/connectors-create-api-sftp/action-2.png)   

Neste momento, a sua aplicação de lógica ter sido configurada com um accionador que vai começar uma execução dos outros accionadores e ações no fluxo de trabalho quando um ficheiro é modificado ou criado na pasta SFTP específica. 

>[AZURE.NOTE]Para uma aplicação de lógica para serão funcionais, tem de conter pelo menos um accionador e uma ação. Siga os passos na secção seguinte para adicionar uma ação.  