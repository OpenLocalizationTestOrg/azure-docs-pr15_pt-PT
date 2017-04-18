### <a name="prerequisites"></a>Pré-requisitos

- Uma conta [SFTP](https://en.wikipedia.org/wiki/SSH_File_Transfer_Protocol)  


Antes de poder utilizar a sua conta SFTP numa aplicação lógica, tem de autorizar a aplicação de lógica para ligar à sua conta SFTP. Felizmente, pode fazê-lo facilmente a partir do seu app lógica no Portal do Azure.  

Eis os passos para autorizar a sua aplicação de lógica para ligar à sua conta SFTP:  
1. Para criar uma ligação ao SFTP, no estruturador de aplicação de lógica, selecione **Mostrar Microsoft gerido APIs** na lista pendente, em seguida, introduza *SFTP* na caixa de pesquisa. Selecione o accionador **SFTP - quando um ficheiro é adicionado ou modificado** :  
![Imagem de ligação online SFTP 1](./media/connectors-create-api-sftp/sftp-1.png)  
2. Se não tiver criado SFTP antes de todas as ligações, irá obter lhe for pedido para fornecer as suas credenciais SFTP. Estas credenciais serão utilizadas para autorizar para ligar à sua aplicação de lógica e aceder aos dados da sua conta SFTP:  
![Imagem de ligação online SFTP 2](./media/connectors-create-api-sftp/sftp-2.png)  
3. Repare a ligação foi criada e está agora gratuito continuar com os outros passos na sua aplicação de lógica:   
 ![Imagem de ligação online SFTP 3](./media/connectors-create-api-sftp/sftp-3.png) 
