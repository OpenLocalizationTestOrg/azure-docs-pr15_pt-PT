<properties
   pageTitle="Utilizando o conector SQL nas aplicações de lógica | Aplicação de serviço do Microsoft Azure"
   description="Como criar e configurar a aplicação de conector de SQL ou API e utilizá-la numa aplicação lógica na aplicação de serviço do Azure"
   services="logic-apps"
   documentationCenter=".net,nodejs,java"
   authors="anuragdalmia"
   manager="erikre"
   editor=""/>

<tags
   ms.service="logic-apps"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="09/01/2016"
   ms.author="sameerch"/>


# <a name="get-started-with-the-microsoft-sql-connector-and-add-it-to-your-logic-app"></a>Introdução ao Microsoft SQL Connector e adicioná-lo para a sua aplicação de lógica
>[AZURE.NOTE] Esta versão do artigo aplica-se a versão do esquema de 2014-12-01-pré-visualização de aplicações de lógica. Para a versão do Azure SQL 2015-08-01-pré-visualizar esquema, clique em [API do SQL Azure](../connectors/connectors-create-api-sqlazure.md).

Ligar a no local do SQL Server ou uma base de dados do SQL Azure para criar e alterar as informações ou dados. Conectores podem ser utilizados nas aplicações de lógica para recuperar, processo, ou emissão dados como uma parte de um "fluxo de trabalho". Quando utiliza o conector de SQL no seu fluxo de trabalho, é possível alcançar uma variedade de cenários. Por exemplo, pode:

- Expor uma secção de dados que residem na base de dados SQL utilizando uma web ou aplicação móvel.
- Inserir uma tabela de base de dados SQL para o armazenamento de dados. Por exemplo, pode introduzir registos de empregado, atualizar encomendas de venda e assim sucessivamente.
- Obter dados do SQL e utilizá-lo um processo empresarial. Por exemplo, pode obter registos de cliente e colocar esses mesmos registos de cliente no SalesForce.

Pode adicionar o conector de SQL para os seus dados de fluxo de trabalho e o processo de negócio como parte de uma aplicação de lógica este fluxo de trabalho. 

## <a name="triggers-and-actions"></a>Accionadores e acções
*Accionadores* são eventos que ocorrem. Por exemplo, quando uma ordem é atualizada ou quando um novo cliente é adicionado. Uma *ação* é o resultado do accionador. Por exemplo, quando uma ordem é atualizada, envie um alerta para o vendedor. Em alternativa, quando é adicionado um cliente novo, enviar mensagens de e-mail bem-vindo ao novo cliente.

O conector de SQL pode ser utilizado como um accionador ou uma ação num dados de aplicação e suporta lógica nos formatos JSON e XML. Para todas as tabelas incluídas no seu pacote definições (mais informações sobre que mais adiante), não existe um conjunto de ações JSON e um conjunto de ações de XML.

O conector de SQL tem os seguintes accionadores e ações disponíveis:

Accionadores | Ações
--- | ---
Dados de inquérito | <ul><li>Inserir tabela</li><li>Actualizar índice</li><li>Selecione a partir da tabela</li><li>Eliminar da tabela</li><li>Chamar procedimento armazenado</li></ul>

## <a name="create-the-sql-connector"></a>Criar o conector SQL

Uma conexão pode ser criada dentro de uma aplicação de lógica ou ser criada diretamente a partir do Azure Marketplace. Para criar uma conexão a partir de mercado:  

1. Na startboard Azure, selecione **Marketplace**.
2. Procurar "SQL conexão", selecione-o e selecione **Criar**.
3. Introduza o nome, o plano de serviço de aplicação e outras propriedades.
4. Introduza as seguintes definições de pacote:

    Nome | Obrigatório |  Descrição
--- | --- | ---
Nome do servidor | Sim | Introduza o nome do SQL Server. Por exemplo, introduza *SQLserver/sqlexpress* ou *SQLserver.mydomain.com*.
Porta | N | Predefinição é 1433.
Nome de utilizador | Sim | Introduza um nome de utilizador que pode iniciar sessão para o SQL Server. Se ligar a um SQL Server no local, introduza as credenciais de autenticação de SQL.
Palavra-passe | Sim | Introduza a palavra-passe de nome de utilizador.
Nome da base de dados | Sim | Introduza a base de dados que estiver a ligar. Por exemplo, pode introduzir *clientes* ou *dbo/encomendas*.
No local | Sim | A predefinição é falso. Introduza FALSO se ligar a uma base de dados do Azure SQL. Introduza True se ligar a um SQL Server no local.
Cadeia de ligação do serviço Bus | N | Se estiver a estabelecer ligação para o local, introduza a cadeia de ligação de reencaminhamento de Bus de serviço.<br/><br/>[Utilizar o Gestor de ligação híbrido](app-service-logic-hybrid-connection-manager.md)<br/>[Bus preços de serviço](https://azure.microsoft.com/pricing/details/service-bus/)
Nome de servidor do parceiro | N | Se o servidor primário não estiver disponível, pode introduzir um servidor parceiro como um servidor alternativo ou cópia de segurança.
Tabelas | N | As tabelas de base de dados que podem ser atualizadas campos da lista. Por exemplo, introduza *OrdersTable* ou *EmployeeTable*. Se forem introduzidas sem tabelas, todas as tabelas podem ser utilizadas. Tabelas válidas e/ou procedimentos armazenados são necessários para utilizar este conector como uma ação.
Procedimentos armazenados | N | Introduza um procedimento armazenado existente que pode ser chamado pelo conector. Por exemplo, introduza *sp_IsEmployeeEligible* ou *sp_CalculateOrderDiscount*. Tabelas válidas e/ou procedimentos armazenados são necessários para utilizar este conector como uma ação.
Consulta de dados disponíveis | Para obter suporte accionador | Instrução de SQL para determinar se quaisquer dados estão disponíveis para uma tabela de base de dados do SQL Server de consulta. Isto deve devolver um valor numérico que representa o número de linhas de dados disponíveis. Exemplo: SELECIONE COUNT(*) table_name.
Consulta de dados de inquérito | Para obter suporte accionador | A instrução de SQL para consultar a tabela de base de dados do SQL Server. Pode introduzir qualquer número de instruções SQL separados por ponto e vírgula. Esta declaração é executada de forma transaccional e consolidada apenas quando os dados são armazenados em segurança na sua aplicação de lógica. Exemplo: SELECIONE *table_name; ELIMINAR a partir de table_name. <br/>**Note**<br/>Tem de fornecer uma instrução de inquérito evita ciclo infinito ao eliminar, mover ou atualizar os dados selecionados para se certificar de que os mesmos dados não são consultados novamente.

5. Quando estiver concluído, as definições de pacote têm um aspeto semelhantes ao seguinte:  
![][1]  

6. Selecione **Criar**. 


## <a name="use-the-connector-as-a-trigger"></a>Utilizar o conector como um accionador
Vamos olhar uma aplicação de lógica simples que consulta dados a partir de uma tabela SQL, adiciona os dados noutra tabela e atualiza os dados.

Para utilizar o conector SQL como um accionador, introduza os valores de **Consulta disponíveis de dados** e **Consulta de dados de inquérito** . **Consulta de dados disponíveis** é executado na agenda introduza e determina se quaisquer dados estão disponíveis. Uma vez que esta consulta devolve apenas um número escalar, pode ser optimizada e otimizada para frequente execução.

**Consulta de dados de um inquérito** apenas é executada quando a consulta disponíveis dados indica que dados estão disponíveis. Esta declaração de executa dentro de uma transação e só está empenhada quando os dados extraídos durável são armazenados no seu fluxo de trabalho. É importante evitar infinitamente extrair novamente os mesmos dados. Da natureza transaccional desta execução pode ser utilizada para eliminar ou atualizar os dados para se certificar de que não está recolheu da próxima vez que está a ser consultados dados.

> [AZURE.NOTE] O esquema devolvido por esta declaração de identifica as propriedades disponíveis na conexão. Todas as colunas devem ter o nome.

#### <a name="data-available-query-example"></a>Exemplo de dados da consulta disponíveis

    SELECT COUNT(*) FROM [Order] WHERE OrderStatus = 'ProcessedForCollection'

#### <a name="poll-data-query-example"></a>Exemplo de consulta de dados de inquérito

    SELECT *, GetData() as 'PollTime' FROM [Order]
        WHERE OrderStatus = 'ProcessedForCollection'
        ORDER BY Id DESC;
    UPDATE [Order] SET OrderStatus = 'ProcessedForFrontDesk'
        WHERE Id =
        (SELECT Id FROM [Order] WHERE OrderStatus = 'ProcessedForCollection' ORDER BY Id DESC)

### <a name="add-the-trigger"></a>Adicionar o accionador
1. Quando criar ou editar uma aplicação de lógica, selecione a conexão de SQL que criou como o accionador. Esta lista os accionadores disponíveis: **Inquérito dados (JSON)** e **Dados de um inquérito (XML)**:  
![][5]

2. Selecione o accionador **Inquérito dados (JSON)** , introduza a frequência e clique na ✓:  
![][6]

3. O accionador aparece agora tal como está configurado na aplicação lógica. Output(s) do accionador é apresentados e pode ser utilizados como entradas nas ações subsequentes:  
![][7]

## <a name="use-the-connector-as-an-action"></a>Utilizar o conector como uma ação
Utilizar o nosso cenário de aplicação de lógica simples que consulta dados a partir de uma tabela SQL, adiciona os dados noutra tabela e atualiza os dados.

Para utilizar o conector de SQL como uma ação, introduza o nome das tabelas e/ou do procedimentos armazenados que introduziu quando criou o conector de SQL:

1. Depois do accionador (ou selecione 'executar esta lógica manualmente'), adicione o conector SQL que criou a partir da galeria. Selecione uma das ações inserir, como *Inserir para TempEmployeeDetails (JSON)*:  
![][8]

2. Introduza os valores de entrada do registo para ser inserido e clique na ✓:  
![][9]

3. A partir da galeria, selecione a conexão SQL mesmo que criou. Como uma ação, selecione a ação de atualização na mesma tabela, como *EmployeeDetails atualização*:  
![][11]

4. Introduza os valores de entrada para a ação de atualização e, clique na ✓:  
![][12]

Pode testar a aplicação de lógica ao adicionar um novo registo na tabela que está a ser consultada.

## <a name="what-you-can-and-cannot-do"></a>O que pode e não pode fazer

Consulta SQL | Suportada | Não é suportado
--- | --- | ---
Onde cláusula | <ul><li>Operadores: E, ou, = <>, <, < =, >, > = e LIKE</li><li>Várias condições de sub podem ser combinadas, '(' e')'</li><li>Cadeia literais, de data/hora (incluídas no plicas), números (deverá conter apenas caracteres numéricos)</li><li>Estritamente deve ser num formato binário expressão, tal como ((operand operator operand) e/ou (operando de operador operando)) *</li></ul> | <ul><li>Operadores: Entre, IN</li><li>Todas as funções incorporadas, como Add (), Count agora, POWER() e assim sucessivamente</li><li>Gostar de operadores matemáticos *, -, +, e assim sucessivamente</li><li>Concatenações em cadeia utilizando +.</li><li>Todas as associações</li><li>É nulo e não é nulo</li><li>Qualquer números com não numérico, como carateres hexadecimal em números</li></ul>
Campos (na consulta selecionar) | <ul><li>Nomes de coluna válida separados por vírgulas. Sem prefixos de nome de tabela permitida (conexão works numa tabela de cada vez).</li><li>Podem ser escape nomes com ' [' e ']'</li></ul> | <ul><li>Palavras-chave como início, DISTINCT e assim sucessivamente</li><li>Atribuir um alias, como rua, Localidade + Zip endereço como</li><li>Funções de todos os incorporadas, como Add (), Count agora, POWER() e assim sucessivamente</li><li>Gostar de operadores matemáticos, *, -, +, e assim sucessivamente</li><li>Concatenações em cadeia utilizando +</li></ul>

#### <a name="tips"></a>Sugestões

- Para consultas avançadas, sugerimos criar um procedimento armazenado e executar utilizando o procedimento de executar armazenado API.
- Ao utilizar consultas internas, utilizá-los no prazo de procedimentos armazenados.
- Para aderir a várias condições, pode utilizar 'AND' e operadores "Ou".

## <a name="hybrid-configuration-optional"></a>Configuração híbrida (opcional)

> [AZURE.NOTE] Este passo é necessário apenas se estiver a utilizar o SQL Server no local atrás da firewall.

Aplicação de serviço utiliza o Gestor de configuração híbrida para estabelecer uma ligação segura ao seu sistema no local. Se a conexão utiliza um SQL Server no local, o Gestor de ligação híbrido é necessário.

> [AZURE.NOTE] Se pretender começar com aplicações do Azure lógica antes de inscrever-se para uma conta do Azure, aceda à [Aplicação de lógica tente](https://tryappservice.azure.com/?appservice=logic), onde imediatamente pode criar uma aplicação de lógica starter curto na aplicação de serviço. Sem cartões de crédito necessários; sem compromissos.

Consulte o artigo [utilizar o Gestor de ligação híbrido](app-service-logic-hybrid-connection-manager.md).


## <a name="do-more-with-your-connector"></a>Fazer mais com a conexão
Agora que a conexão for criada, pode adicioná-la para um fluxo de trabalho de empresas com uma aplicação de lógica. Consulte o artigo [quais são as aplicações de lógica?](app-service-logic-what-are-logic-apps.md).

Ver a referência Swagger REST API [API aplicações referência e conectores](http://go.microsoft.com/fwlink/p/?LinkId=529766).

Também pode rever a segurança estatísticas e controlo de desempenho ao conector. Consulte o artigo [Gerir e monitorizar o seu incorporada aplicações API e conectores](app-service-logic-monitor-your-connectors.md).


<!--Image references-->
[1]: ./media/app-service-logic-connector-sql/Create.png
[5]: ./media/app-service-logic-connector-sql/LogicApp1.png
[6]: ./media/app-service-logic-connector-sql/LogicApp2.png
[7]: ./media/app-service-logic-connector-sql/LogicApp3.png
[8]: ./media/app-service-logic-connector-sql/LogicApp4.png
[9]: ./media/app-service-logic-connector-sql/LogicApp5.png
[10]: ./media/app-service-logic-connector-sql/LogicApp6.png
[11]: ./media/app-service-logic-connector-sql/LogicApp7.png
[12]: ./media/app-service-logic-connector-sql/LogicApp8.png
