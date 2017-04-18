1. Inicie sessão no [Portal do Azure].

2. Clique em **+ Novo** > **Web + Mobile** > **Aplicação Mobile**, em seguida, forneça um nome para o aplicação Mobile do back-end.

3. Para o **Grupo de recursos**, selecione um grupo de recursos existente ou crie um novo (utilizando o mesmo nome como a sua aplicação.) 
 
    Pode selecionar outro plano de serviço de aplicação ou crie um novo. Para obter mais informações sobre os serviços de aplicação planos e como criar um novo plano num preços diferentes camada e na sua localização pretendida, consulte o artigo [Descrição geral de aprofundada de planos do serviço de aplicação do Azure](../articles/app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md).

4. Para o **plano de serviço de aplicação**, o esquema predefinido (na [camada padrão](https://azure.microsoft.com/pricing/details/app-service/)) está selecionado. Também pode selecionar um plano diferente ou [crie um novo](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md#create-an-app-service-plan). Definições do plano de serviço de aplicação determinam a [localização, funcionalidades, de custos e recursos de calcular](https://azure.microsoft.com/pricing/details/app-service/) associado com a sua aplicação. 

    Depois de decidir no plano do, clique em **Criar**. Esta ação cria o aplicação Mobile back-end. 
    
6. No pá **Definições** para o novo aplicação Mobile do back-end, clique em **Guia de introdução do** > sua plataforma de aplicação de cliente > **ligar uma base de dados**. 

    ![](./media/app-service-mobile-dotnet-backend-create-new-service/dotnet-backend-create-data-connection.png)

7. No pá **Adicionar ligação de dados** , clique em **Base de dados SQL** > **criar uma nova base de dados**, escreva o **nome**da base de dados, selecione uma camada comparar, em seguida, clique em **Server**.  Pode reutilizar esta nova base de dados. Se já tiver uma base de dados na mesma localização, em vez disso, pode escolher **utilizar uma base de dados existente**. A utilização de uma base de dados numa localização diferente, não é recomendada devido a custos de largura de banda e a latência superior.
 
    ![](./media/app-service-mobile-dotnet-backend-create-new-service/dotnet-backend-create-db.png)

8. No pá **novo servidor** , escreva um nome de servidor exclusivo no campo **nome do servidor** , forneça um ID de utilizador e palavra-passe, selecione **Permitir azure serviços para aceder ao servidor**e clique em **OK**. Esta ação cria a nova base de dados.

9. Novamente na pá **Adicionar ligação de dados** , clique em **cadeia de ligação**, introduza os valores de ID de utilizador e palavra-passe da sua base de dados e clique em **OK**. Aguarde alguns minutos para ser implementada com êxito antes de continuar a base de dados.

<!-- URLs. -->
[Portal do Azure]: https://portal.azure.com/
