<properties
   pageTitle="Carregar os dados do SQL Server para Azure SQL dados armazém (SSIS) | Microsoft Azure"
   description="Mostra-lhe como criar um pacote SQL Server Integration Services (SSIS) para mover dados de uma grande variedade de origens de dados para armazém de dados SQL."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="lodipalm"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="08/08/2016"
   ms.author="lodipalm;sonyama;barbkess"/>

# <a name="load-data-from-sql-server-into-azure-sql-data-warehouse-ssis"></a>Carregar os dados do SQL Server para Azure SQL dados armazém (SSIS)

> [AZURE.SELECTOR]
- [SSIS](sql-data-warehouse-load-from-sql-server-with-integration-services.md)
- [PolyBase](sql-data-warehouse-load-from-sql-server-with-polybase.md)
- [BCP](sql-data-warehouse-load-from-sql-server-with-bcp.md)


Crie um pacote SQL Server Integration Services (SSIS) para carregar os dados do SQL Server para armazém de dados do SQL Azure. Pode, opcionalmente, reestruturar, transformar e limpar os dados tal como o fluxo de dados SSIS-atravessa.

Neste tutorial, irá:

- Crie um novo projeto de Integration Services no Visual Studio.
- Ligar a origens de dados, incluindo o SQL Server (como uma origem) e armazém de dados SQL (como um destino).
- Estruture um pacote SSIS que carrega dados a partir da origem para o destino.
- Execute o pacote SSIS para carregar os dados.

Neste tutorial utiliza do SQL Server como a origem de dados. Poderá ser executado do SQL Server no local ou numa máquina virtual Azure.

## <a name="basic-concepts"></a>Conceitos básicos

O pacote é a unidade de trabalho no SSIS. Pacotes relacionadas são agrupadas em projetos. Criar projetos e pacotes de estrutura no Visual Studio com ferramentas de dados do SQL Server. O processo de estruturação é um processo visual na qual que arraste e largue componentes da caixa de ferramentas para a superfície de desenho, ligá-los e definir as respectivas propriedades. Depois de concluir o seu pacote, pode, opcionalmente, implemente-ao SQL Server para gestão abrangente, monitorização e segurança.

## <a name="options-for-loading-data-with-ssis"></a>Opções para carregar os dados com SSIS

SQL Server Integration Services (SSIS) é um conjunto de ferramentas de flexível que fornece várias opções para ligar e carregar os dados para armazém de dados SQL.

1. Utilize um destino de líquido ADO para ligar ao armazenamento de dados do SQL. Neste tutorial utiliza um destino de líquido ADO porque tem as opções de configuração menos.
2. Utilize um destino de OLE DB para ligar ao armazenamento de dados SQL. Esta opção pode fornecer ligeiramente melhor desempenho do que o destino de líquido ADO.
3. Utilize a tarefa de carregar de Blobs do Azure para testar os dados de armazenamento de Blobs do Azure. Em seguida, utilize a tarefa de SSIS executar SQL para iniciar um script de Polybase carrega os dados para armazém de dados SQL. Esta opção fornece o melhor desempenho das três opções listadas aqui. Para obter a tarefa carregar de Blobs do Azure, transfira o [Microsoft SQL Server 2016 Integration Services Feature Pack para Azure][]. Para saber mais sobre Polybase, consulte o [Guia de PolyBase][].

## <a name="before-you-start"></a>Antes de começar

Para visualizar passo a passo neste tutorial, é necessário:

1. **SQL Server Integration Services (SSIS)**. SSIS é um componente do SQL Server e requer uma versão de avaliação ou uma versão licenciada do SQL Server. Para obter uma versão de avaliação do SQL Server 2016 Preview, consulte o artigo [Avaliações do SQL Server][].
2. **Visual Studio**. Para obter o gratuito Visual Studio 2015 Comunidade Edition, consulte o artigo [Visual Studio da Comunidade][].
3. **Ferramentas de dados do SQL Server para Visual Studio (SSDT)**. Para obter ferramentas de dados do SQL Server para Visual Studio 2015, consulte o artigo [Transferir SQL Server dados Tools (SSDT)][].
4. **Dados de exemplo**. Neste tutorial utiliza dados de exemplo armazenados no SQL Server na base de dados de exemplo AdventureWorks como origem de dados sejam carregados armazém de dados SQL. Para obter a base de dados de exemplo AdventureWorks, consulte o artigo [Bases de dados de exemplo AdventureWorks 2014][].
5. **Armazém de dados do SQL de uma base de dados e permissões**. Neste tutorial liga a uma instância do armazém de dados SQL e carrega dados para o mesmo. Tem de ter permissões para criar uma tabela e para carregar os dados.
6. **Uma regra de firewall**. Tem de criar uma regra de firewall no armazém de dados do SQL com o endereço IP do seu computador local antes de pode carregar dados para o armazenamento de dados SQL.

## <a name="step-1-create-a-new-integration-services-project"></a>Passo 1: Criar um novo projeto de Integration Services

1. Inicie o Visual Studio 2015.
2. No menu **ficheiro** , selecione novo **| Project**.
3. Navegue para o **instalado | Modelos | Business Intelligence | Serviços de integração de** tipos de projeto.
4. Selecione **projeto de Integration Services**. Fornecer valores para o **nome** e uma **localização**e, em seguida, clique **em OK**.

Visual Studio é aberto e cria um novo projeto de Integration Services (SSIS). Em seguida, Visual Studio abre o estruturador para o único pacote SSIS novo (pacote. dtsx) no projeto. Consulte as seguintes áreas de ecrã:

- À esquerda, a **caixa de ferramentas** de componentes SSIS.
- A meio, a superfície de desenho, com vários separadores. É normalmente utilizado pelo menos de **Fluxo de controlo** e os separadores de **Fluxo de dados** .
- À direita, o **Explorador de soluções** e os painéis de **Propriedades** .

    ![][01]

## <a name="step-2-create-the-basic-data-flow"></a>Passo 2: Criar o fluxo de dados básico

1. Arraste uma tarefa de fluxo de dados da caixa de ferramentas para o centro da superfície da estrutura (no separador **Fluxo de controlo** ).

    ![][02]

2. Faça duplo clique na tarefa de fluxo de dados para mudar para o separador fluxo de dados.
3. A partir da lista de outras origens, na caixa de ferramentas, arraste uma origem de ADO.NET para a superfície de desenho. Com a placa de origem ainda seleccionada, altere o nome para a **origem do SQL Server** no painel de **Propriedades** .
4. A partir da lista de outros destinos na caixa de ferramentas, arraste um destino ADO.NET para a superfície de desenho sob a origem de ADO.NET. Com a placa de destino ainda seleccionada, altere o nome para o **destino de SQL DW** no painel de **Propriedades** .

    ![][09]

## <a name="step-3-configure-the-source-adapter"></a>Passo 3: Configurar o adaptador de origem

1. Faça duplo clique sobre a placa de origem para abrir o **Editor de código ADO.NET**.

    ![][03]

2. No separador **Gestor de ligação** do **Editor de código ADO.NET**, clique no botão de **Novo** ao lado da lista de **Gestor de ligação de ADO.NET** para abrir a caixa de diálogo **Gestor de ligação de ADO.NET configurar** e criar definições de ligação para a base de dados do SQL Server a partir do qual este tutorial carrega dados.

    ![][04]

3. Na caixa de diálogo **Gestor de ligação de ADO.NET configurar** , clique no botão **Novo** para abrir a caixa de diálogo **Gestor de ligação** e criar uma nova ligação de dados.

    ![][05]

4. Na caixa de diálogo **Gestor de ligação** , efetue as seguintes coisas.

    1. Para o **fornecedor de**, selecione o fornecedor de dados SqlClient.
    2. **Nome do servidor**, introduza o nome do SQL Server.
    3. Na secção de **Iniciar sessão servidor** , selecione ou introduza informações de autenticação.
    4. Na secção **ligar a uma base de dados** , selecione a base de dados de exemplo AdventureWorks.
    5. Clique em **Testar ligação**.
    
        ![][06]
    
    6. Na caixa de diálogo relatórios os resultados de teste de ligação, clique em **OK** para regressar à caixa de diálogo **Gestor de ligação** .
    7. Na caixa de diálogo **Gestor de ligação** , clique em **OK** para regressar à caixa de diálogo **Gestor de ligação de ADO.NET configurar** .
 
5. Na caixa de diálogo **Gestor de ligação de ADO.NET configurar** , clique em **OK** para voltar para o **Editor de código ADO.NET**.
6. No **Editor de código ADO.NET**, na lista **nome da tabela ou a vista** , selecione a tabela **Sales.SalesOrderDetail** .

    ![][07]

7. Clique em **Pré-visualizar** para ver as linhas de dados na tabela de origem na caixa de diálogo **Pré-visualizar resultados de consulta** pela primeira vez 200.

    ![][08]

8. Na caixa de diálogo **Pré-visualizar resultados de consulta** , clique em **Fechar** para voltar para o **Editor de código ADO.NET**.
9. No **Editor de código ADO.NET**, clique em **OK** para concluir a configuração da origem de dados.

## <a name="step-4-connect-the-source-adapter-to-the-destination-adapter"></a>Passo 4: Ligue o adaptador de origem à placa de destino

1. Selecione a placa de origem na superfície da estrutura.
2. Selecione a seta azul que expande a partir da placa de origem e arraste-o para o editor de destino até se encaixar no lugar.

    ![][10]

    Num pacote SSIS típico, utilize um número de outros componentes da caixa de ferramentas SSIS entre a origem e destino reestruturar, transformar e limpar os seus dados, tal como o fluxo de dados SSIS-atravessa. Para manter este exemplo tão simples quanto possível, podemos estiver a estabelecer ligação a origem diretamente para o destino.

## <a name="step-5-configure-the-destination-adapter"></a>Passo 5: Configurar o adaptador de destino

1. Faça duplo clique sobre a placa de destino para abrir o **Editor de destino ADO.NET**.

    ![][11]

2. No separador **Gestor de ligação** do **Editor de destino ADO.NET**, clique no botão de **Novo** ao lado da lista de **Gestor de ligação** para abrir a caixa de diálogo **Gestor de ligação de ADO.NET configurar** e criar definições de ligação para a base de dados do armazém de dados do SQL Azure no qual este tutorial carrega dados.
3. Na caixa de diálogo **Gestor de ligação de ADO.NET configurar** , clique no botão **Novo** para abrir a caixa de diálogo **Gestor de ligação** e criar uma nova ligação de dados.
4. Na caixa de diálogo **Gestor de ligação** , efetue as seguintes coisas.
    1. Para o **fornecedor de**, selecione o fornecedor de dados SqlClient.
    2. **Nome do servidor**, introduza o nome do armazém de dados SQL.
    3. Na secção de **Iniciar sessão servidor** , selecione **utilizar o SQL Server autenticação** e introduza as informações de autenticação.
    4. Na secção **ligar a uma base de dados** , selecione a base de dados SQL armazém de dados existente.
    5. Clique em **Testar ligação**.
    6. Na caixa de diálogo relatórios os resultados de teste de ligação, clique em **OK** para regressar à caixa de diálogo **Gestor de ligação** .
    7. Na caixa de diálogo **Gestor de ligação** , clique em **OK** para regressar à caixa de diálogo **Gestor de ligação de ADO.NET configurar** .
5. Na caixa de diálogo **Gestor de ligação de ADO.NET configurar** , clique em **OK** para voltar para o **Editor de destino ADO.NET**.
6. No **Editor de destino ADO.NET**, clique em **Novo** junto a **utilizar uma tabela ou vista** de lista para abrir a caixa de diálogo **Criar tabela** para criar uma nova tabela de destino com uma lista de coluna que corresponde à tabela de origem.

    ![][12a]

7. Na caixa de diálogo **Criar tabela** , efetue as seguintes coisas.

    1. Altere o nome da tabela de destino para **SalesOrderDetail**.
    2. Remova a coluna **rowguid** . O tipo de dados **uniqueidentifier** não é suportado no armazém de dados do SQL.
    3. Altere o tipo de dados da coluna **LineTotal** para **dinheiro**. O tipo de dados **decimal** não é suportado no armazém de dados do SQL. Para obter informações sobre tipos de dados suportados, consulte o artigo [Criar tabela (armazém de dados do SQL Azure, paralelas armazém de dados)][].
    
        ![][12b]
    
    4. Clique em **OK** para criar a tabela e voltar para o **Editor de destino ADO.NET**.

8. No **Editor de destino ADO.NET**, selecione o separador **mapeamentos** para ver como colunas na origem de são mapeadas para colunas no destino.

    ![][13]

9. Clique em **OK** para concluir a configuração da origem de dados.

## <a name="step-6-run-the-package-to-load-the-data"></a>Passo 6: Executar o pacote para carregar os dados

Execute o pacote de ao clicar no botão **começar** na barra de ferramentas ou ao selecionar uma das opções no menu **Depurar** **Executar** .

À medida o pacote começa a ser executado, verá amarelas a girar rodas para indicar atividade, bem como o número de linhas processadas até ao momento.

![][14]

Quando o pacote de ter sido concluído em execução, verá marcas de verificação verdes para indicar o sucesso, bem como o número total de linhas de dados carregados a partir da origem para o destino.

![][15]

Parabéns! Com êxito tenha utilizado o SQL Server Integration Services para carregar os dados para armazém de dados do SQL Azure.

## <a name="next-steps"></a>Próximos passos

- Saiba mais sobre o fluxo de dados SSIS. Comece aqui: [Fluxo de dados][].
- Saiba como depurar e resolver problemas seus diretamente de pacotes no ambiente de estrutura. Comece aqui: [Ferramentas de resolução de problemas para o desenvolvimento de pacote][].
- Saiba como implementar os projetos SSIS e pacotes ao servidor de serviços de integração de ou para outra localização de armazenamento. Comece aqui: [implementação de projetos e pacotes][].

<!-- Image references -->
[01]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/ssis-designer-01.png
[02]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/ssis-data-flow-task-02.png
[03]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/ado-net-source-03.png
[04]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/ado-net-connection-manager-04.png
[05]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/ado-net-connection-05.png
[06]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/test-connection-06.png
[07]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/ado-net-source-07.png
[08]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/preview-data-08.png
[09]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/source-destination-09.png
[10]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/connect-source-destination-10.png
[11]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/ado-net-destination-11.png
[12a]: ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/destination-query-before-12a.png
[12b]: ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/destination-query-after-12b.png
[13]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/column-mapping-13.png
[14]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/package-running-14.png
[15]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/package-success-15.png

<!-- Article references -->

<!-- MSDN references -->
[Guia de PolyBase]: https://msdn.microsoft.com/library/mt143171.aspx
[Transfira as ferramentas de dados do SQL Server (SSDT)]: https://msdn.microsoft.com/library/mt204009.aspx
[Criar tabela (armazém de dados do Azure SQL, paralelas dados armazém)]: https://msdn.microsoft.com/library/mt203953.aspx
[Fluxo de dados]: https://msdn.microsoft.com/library/ms140080.aspx
[Ferramentas de resolução de problemas para o desenvolvimento do pacote]: https://msdn.microsoft.com/library/ms137625.aspx
[Implementação de projetos e pacotes]: https://msdn.microsoft.com/library/hh213290.aspx

<!--Other Web references-->
[Microsoft SQL Server 2016 Integration Services Feature Pack do Azure]: http://go.microsoft.com/fwlink/?LinkID=626967
[Avaliações do SQL Server]: https://www.microsoft.com/en-us/evalcenter/evaluate-sql-server-2016
[Comunidade do Visual Studio]: https://www.visualstudio.com/en-us/products/visual-studio-community-vs.aspx
[Bases de dados de exemplo AdventureWorks 2014]: https://msftdbprodsamples.codeplex.com/releases/view/125550
