<properties 
    pageTitle="Introdução às ferramentas de base de dados flexível" 
    description="EXPLICAÇÃO básica da funcionalidade de ferramentas da base de dados flexível da base de dados do SQL Azure, incluindo mais facilmente para executar a aplicação de exemplo." 
    services="sql-database" 
    documentationCenter="" 
    manager="jhubbard" 
    authors="ddove" 
    editor="CarlRabeler"/>

<tags 
    ms.service="sql-database" 
    ms.workload="sql-database" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="05/27/2016" 
    ms.author="ddove"/>

# <a name="get-started-with-elastic-database-tools"></a>Introdução às ferramentas de base de dados flexível

Este documento apresenta a experiência de programador ao executar a aplicação de exemplo. A amostra cria uma aplicação de sharded simple e explora capacidades chave das ferramentas de base de dados flexível. O exemplo demonstra funções da [biblioteca de base de dados flexível do cliente](sql-database-elastic-database-client-library.md)

Para instalar a biblioteca, vá para [Microsoft.Azure.SqlDatabase.ElasticScale.Client](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/). Tenha em atenção que a biblioteca está instalada com a aplicação de exemplo descrita abaixo.

## <a name="prerequisites"></a>Pré-requisitos

1. É necessário o Visual Studio 2012 ou superior com c#. Transferir uma versão gratuita no [Visual Studio transferências](http://www.visualstudio.com/downloads/download-visual-studio-vs.aspx).
2. Nuget 2.7 ou superior. Para obter a versão mais recente, consulte [Instalar NuGet](http://docs.nuget.org/docs/start-here/installing-nuget)

## <a name="download-and-run-the-sample-app"></a>Transferir e executar a aplicação de exemplo

O **flexível base de dados com Azure SQL — introdução** aplicação de exemplo ilustra os aspetos mais importantes da experiência de desenvolvimento de aplicações sharded utilizando ferramentas de flexível da base de dados do Azure SQL. Foca-se no casos de utilização de chaves para [shard mapear gestão](sql-database-elastic-scale-shard-map-management.md), [dependentes encaminhamento de dados](sql-database-elastic-scale-data-dependent-routing.md) e [consultar com várias shard](sql-database-elastic-scale-multishard-querying.md). Para transferir e executar o exemplo, siga estes passos: 

1. Abrir o Visual Studio e selecione **ficheiro -> novo -> projeto**.
2. Na caixa de diálogo, clique em **Online**.

    ![Novo projeto > Online][2]
3. Em seguida, clique em **Visual c#** em **amostras**.

    ![Clique em Visual C#][3]
4. Na caixa de pesquisa, escreva **db flexível** para procurar a amostra. O título **Flexível ferramentas DB para SQL Azure - introdução ao** aparece.

    ![Caixa de pesquisa][1]
 
5. Selecione o exemplo, selecione um nome e uma localização para o novo projeto e prima **OK** para criar o projeto.
6. Abra o ficheiro de **App** da solução para o projeto de exemplo e siga as instruções no ficheiro para adicionar o seu nome de servidor de base de dados do Azure SQL e as informações de início de sessão (nome de utilizador e palavra-passe).
7. Criar e executar a aplicação. Quando lhe for pedido, permitir que Visual Studio restaurar os pacotes de NuGet da solução. Isto irá transferir a versão mais recente da biblioteca de cliente flexível da base de dados a partir do NuGet.
8. Reproduzir com as diferentes opções para saber mais sobre as capacidades de biblioteca de cliente. Tenha em atenção os passos que a aplicação leva na consola de saída e à vontade explorar o código nos bastidores.

    ![progresso][4]

Parabéns – tiver criado e executar a sua aplicação sharded primeira utilizando ferramentas de base de dados flexível na base de dados do SQL Azure com êxito. Tirar um breve olhar sobre as shards que a amostra criada ligando ao Visual Studio ou ao servidor Azure DB do SQL Server Management Studio. Vai notar novas bases de dados de shard de exemplo e uma base de dados manager shard do mapa que criou a amostra.

> [AZURE.IMPORTANT] É recomendado utilizar sempre a versão mais recente do Management Studio para permanecer sincronizados com atualizações para o Microsoft Azure e base de dados SQL. [Atualizar o SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).


### <a name="key-pieces-of-the-code-sample"></a>Chaves equipamentos o código de exemplo

1. **Gerir Shards e mapas de Shard**: O código ilustra como trabalhar com shards, intervalos, e mapeamentos no ficheiro **ShardMapManagerSample.cs**. Pode encontrar mais informações sobre este tópico aqui: [Gestão de mapa Shard](http://go.microsoft.com/?linkid=9862595).  
2. **Dados dependentes encaminhamento**: encaminhamento de transações para a shard direita é apresentado na **DataDependentRoutingSample.cs**. Para obter mais detalhes, consulte o artigo [Encaminhamento dependente de dados](http://go.microsoft.com/?linkid=9862596). 
3. **Consulta através de vários Shards**: consulta ao longo shards é ilustrada no ficheiro **MultiShardQuerySample.cs**. Para obter mais detalhes, consulte o artigo [Shard com várias consultar](http://go.microsoft.com/?linkid=9862597).
4. **Adicionar vazias shards**: Adicionar iterativo da novas shards vazias é executado pelo código no ficheiro **AddNewShardsSample.cs**. Detalhes deste tópico abrange aqui: [Gestão de mapa Shard](http://go.microsoft.com/?linkid=9862595).

### <a name="other-elastic-scale-operations"></a>Outras operações de escala flexível

1. **Dividir uma shard existente**: A funcionalidade para dividir shards é fornecida através da **ferramenta de divisão em série**. Pode encontrar mais informações sobre esta ferramenta aqui: [Descrição geral da ferramenta de divisão em série](sql-database-elastic-scale-overview-split-and-merge.md).
2. **Intercalar shards existentes**: intercala a Shard também é executadas utilizando a **ferramenta de divisão em série**. Para obter mais informações, consulte: [Descrição geral da ferramenta de divisão em série](sql-database-elastic-scale-overview-split-and-merge.md).   


## <a name="cost"></a>Custo

As ferramentas de base de dados flexível são gratuitamente. Ferramentas de base de dados flexível não impor taxas adicionais na parte superior de custo para a utilização do Azure. 

Por exemplo, a aplicação de exemplo cria novas bases de dados. O custo depende a edição de base de dados do Azure SQL DB que escolher e a utilização da sua aplicação Azure.

Para obter informações sobre os preços consulte [Detalhes de preços de base de dados do SQL](https://azure.microsoft.com/pricing/details/sql-database/).

## <a name="next-steps"></a>Próximos passos
Para obter mais informações sobre as ferramentas de base de dados flexível, consulte:

* [Mapa de documentação de ferramentas da base de dados flexível](https://azure.microsoft.com/documentation/learning-paths/sql-database-elastic-scale/) 
-    Exemplos de código: 
    -    [BD flexível com SQL Azure - introdução](http://code.msdn.microsoft.com/Elastic-Scale-with-Azure-a80d8dc6?SRC=VSIDE)
    -    [BD flexível com SQL Azure - integrar com entidade Framework](http://code.msdn.microsoft.com/Elastic-Scale-with-Azure-bae904ba?SRC=VSIDE)
    -    [Shard elasticidade no Centro de Script](https://gallery.technet.microsoft.com/scriptcenter/Elastic-Scale-Shard-c9530cbe)
-    Blogue: [anúncio de nascimento de escala flexível](https://azure.microsoft.com/blog/2014/10/02/introducing-elastic-scale-preview-for-azure-sql-database/)
-    Canal 9: [vídeo de descrição geral de escala flexível](http://channel9.msdn.com/Shows/Data-Exposed/Azure-SQL-Database-Elastic-Scale)
-    Fórum de debate: [Fórum de base de dados SQL Azure](http://social.msdn.microsoft.com/forums/azure/home?forum=ssdsgetstarted)
-    Para medir o desempenho: [contadores de desempenho para o Gestor de mapa shard](sql-database-elastic-database-client-library.md)


<!--Anchors-->
[The Elastic Scale Sample Application]: #The-Elastic-Scale-Sample-Application
[Download and Run the Sample App]: #Download-and-Run-the-Sample-App
[Cost]: #Cost
[Next steps]: #next-steps

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-get-started/newProject.png
[2]: ./media/sql-database-elastic-scale-get-started/click-online.png
[3]: ./media/sql-database-elastic-scale-get-started/click-CSharp.png
[4]: ./media/sql-database-elastic-scale-get-started/output2.png
 
