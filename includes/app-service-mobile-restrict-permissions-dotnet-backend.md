
Por predefinição, APIs na aplicação Mobile back-end pode ser chamados forma anónima. Em seguida, é necessário restringir o acesso a apenas os clientes autenticados.  

+ **Back-end de Node.js (através do portal)** :  
    
    Na sua aplicação Mobile **Definições**, clique em **Tabelas fácil** e selecione a tabela. Clique em **alterar permissões**, selecione **acesso autenticado apenas** para todas as permissões e, em seguida, clique em **Guardar**. 

+ **Back-end do .NET (c#)**:  

    No server project, navegue até à **controladores** > **TodoItemController.cs**. Adicionar o `[Authorize]` atributo para a classe de **TodoItemController** , da seguinte forma. Para restringir o acesso apenas aos métodos específicos, também pode aplicar este atributo apenas para esses métodos em vez da classe. Volte a publicar o projeto de servidor.


        [Authorize]
        public class TodoItemController : TableController<TodoItem>

+ **Back-end de Node.js (através do código de Node.js)** :  
    
    Para exigir autenticação de acesso de tabela, adicione a linha seguinte para o script de servidor Node.js:


        table.access = 'authenticated';

    Para obter mais detalhes, consulte [como: exigir autenticação para acesso a tabelas](../articles/app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md#howto-tables-auth). Para saber como transferir o projeto de código do guia de introdução do seu site, consulte o artigo [como: Transferir o projeto de código do guia de introdução do Node.js back-end utilizando Git](../articles/app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md#download-quickstart).

