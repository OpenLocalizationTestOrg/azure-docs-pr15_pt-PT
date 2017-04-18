<properties
   pageTitle="Utilizando o conector de DB2 num serviço de aplicação do Microsoft Azure | Microsoft Azure"
   description="Como utilizar o conector de DB2 com accionadores da aplicação de lógica e acções"
   services="logic-apps"
   documentationCenter=".net,nodejs,java"
   authors="gplarsen"
   manager="erikre"
   editor=""/>

<tags
   ms.service="logic-apps"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="05/31/2016"
   ms.author="plarsen"/>

# <a name="db2-connector"></a>Conector DB2
>[AZURE.NOTE] Esta versão do artigo aplica-se a versão do esquema de 2014-12-01-pré-visualização de aplicações de lógica.

Conector da Microsoft para DB2 é uma aplicação de API para ligar a aplicações através da aplicação de serviço de Azure a recursos armazenados numa base de dados IBM DB2. Conector inclui um cliente da Microsoft para ligar a computadores de servidor remotos DB2 através de uma ligação de rede TCP/IP, incluindo ligações a Azure híbrido no local DB2 servidores utilizando o reencaminhamento do Azure Service Bus conexão suporta as seguintes operações de base de dados:

- SELECIONE as linhas de leitura utilizando
- Inquérito para ler linhas utilizando SELECIONE CONTAGEM, seguido de SELECIONAR
- Adicionar uma linha ou em várias linhas de (em volume) utilizando a inserir
- Alterar uma linha ou em várias linhas de (em volume) utilizando UPDATE
- Remover uma linha ou em várias linhas de (em volume) ao utilizar eliminar
- Lido para alterar linhas utilizando o CURSOR SELECIONE seguido de ATUALIZAÇÃO onde atual do CURSOR
- Lido para remover linhas utilizando o CURSOR SELECIONE seguido de ATUALIZAÇÃO onde atual do CURSOR
- Executar o procedimento com parâmetros de entrada e saídos, devolve um valor, conjunto de resultados, utilizando chamada
- Comandos personalizados e operações compostas utilizando SELECIONAR, inserir, ATUALIZAR, eliminar

## <a name="triggers-and-actions"></a>Accionadores e acções
Conexão, suporta as seguintes accionadores de aplicação de lógica ações:

Accionadores | Ações
--- | ---
<ul><li>Dados de inquérito</li></ul> | <ul><li>Inserir em volume</li><li>Inserir</li><li>Atualização em massa</li><li>Actualização</li><li>Chamada</li><li>Eliminação em massa</li><li>Eliminar</li><li>Selecione</li><li>Atualização condicional</li><li>Publicar para EntitySet</li><li>Eliminar condicional</li><li>Selecione entidade única</li><li>Eliminar</li><li>Upsert para EntitySet</li><li>Comandos personalizados</li><li>Operações compostas</li></ul>


## <a name="create-the-db2-connector"></a>Criar o conector de DB2
Pode definir uma conexão a partir de uma aplicação de lógica ou do Azure Marketplace, tal como no exemplo seguinte:  

1. Na startboard Azure, selecione **Marketplace**.
2. Na pá **Tudo** , escreva **db2** na caixa **Procurar tudo** e, em seguida, clique na tecla enter.
3. Na pesquisa tudo o painel de resultados, selecione **DB2 conexão**.
4. No pá de descrição de conexão DB2, selecione **Criar**.
5. Na pá de pacote de conexão DB2, introduza o nome (por exemplo, "Db2ConnectorNewOrders"), o plano de serviço de aplicação e outras propriedades.
6. Selecione **definições do pacote**e introduza as seguintes definições de pacote:  

    Nome | Obrigatório |  Descrição
--- | --- | ---
ConnectionString | Sim | Cadeia de ligação de cliente DB2 (por exemplo, "endereço de rede = nomedoservidor; Porta de rede = 50000; ID de utilizador = NomeUtilizador; Palavra-passe = palavra-passe; catálogo inicial = amostra; Compactar coleção = NWIND; predefinido esquema = NWIND ").
Tabelas | Sim | Lista de tabela separados por vírgulas, ver e alias nomes necessários para operações de OData e para gerar swagger documentação com exemplos (por exemplo, "*NOVASENCOM*").
Procedimentos | Sim | Separados por ponto e lista de nomes do procedimento e função (por exemplo, "SPORDERID").
OnPremise | N | Implemente no local com o Azure Service Bus reencaminhamento.
ServiceBusConnectionString | N | Cadeia de ligação do Azure Service Bus reencaminhamento.
PollToCheckData | N | SELECIONE CONTAGEM declaração para utilizar com um acionador de aplicação lógica (por exemplo, "SELECIONAR CONTAGEM (\*) a partir do NOVASENCOM onde DATADEENVIO IS NULL").
PollToReadData | N | Instrução SELECT para utilizar com um acionador de aplicação lógica (por exemplo, "SELECIONE \* a partir do NOVASENCOM onde DATAENVIO é nulo para ATUALIZAR").
PollToAlterData | N | ATUALIZAR ou instrução DELETE para utilizar com um acionador de aplicação lógica (por exemplo, "ATUALIZAÇÃO NOVASENCOM definir DATADEENVIO = atual data onde atual da &lt;CURSOR&gt;").

7. Selecione **OK**e, em seguida, selecione **Criar**.
8. Quando estiver concluído, as definições de pacote têm um aspeto semelhantes ao seguinte:  
![][1]


## <a name="logic-app-with-db2-connector-action-to-add-data"></a>Aplicação de lógica com DB2 ação de conexão para adicionar dados ##
Pode definir uma ação de aplicação de lógica para adicionar dados a uma tabela de DB2 utilizando uma mensagem ou inserir API a operação de OData entidade. Por exemplo, pode inserir um novo registo de encomenda de cliente, através do processamento de uma instrução SQL INSERT relativamente a uma tabela definida com uma coluna de identidade devolver o valor de identidade ou as linhas afetadas para a aplicação de lógica (SELECIONE ORDID da tabela FINAL (Inserir para NWIND. NOVASENCOM (CUSTID, NOMEDOENVIO, SHIPADDR, CIDADEDEENVIO, SHIPREG, SHIPZIP) VALORES (?,?,?,?,?,?))).

> [AZURE.TIP] Ligação DB2 "*publicar para EntitySet*" devolve o valor da coluna de identidade e "*API inserir*" devolve linhas afetadas

1. Na startboard Azure, selecione **+** (sinal de adição), **Web + Mobile**e, em seguida, **aplicação lógica**.
2. Introduza o nome (por exemplo, "NewOrdersDb2"), o plano de serviço de aplicação, a outras propriedades e, em seguida, selecione **Criar**.
3. Na startboard Azure, selecione a lógica aplicação que acabou de criar, **Definições**e, em seguida, **Accionadores e ações**.
4. Na pá accionadores e ações, selecione **criar de raiz** da aplicação de lógica modelos.
5. No painel de API aplicações, selecione **Periodicidade**, definir uma frequência e intervalo e, em seguida, **marca de verificação**.
6. No painel de API aplicações, selecione **DB2 conexão**, expanda a lista de operações para selecionar a **Inserir nas NEWORDER**.
7. Expanda a lista de parâmetros para introduzir os seguintes valores:  

    Nome | Valor
--- | --- 
CUSTID | 10042
NOMEDOENVIO | K preguiça Kountry arquivo 
SHIPADDR | Socalcos orquestra 12
CIDADEDEENVIO | Da Rainha 
SHIPREG | WA
SHIPZIP | 99362 

8. Selecione a **marca de verificação** para guardar as definições de ação e, em seguida, **Guardar**.
9. As definições de deverão ter o aspeto da seguinte forma:  
![][3]

10. Na lista **todos os é executado** em **operações**, selecione o item listado primeiro (Use mais recente). 
11. Na pá **aplicação lógica executar** , selecione a **ação** item **db2connectorneworders**.
12. Na pá **ação da aplicação de lógica** , selecione a **Ligação de entradas do tipo**. Conector DB2 utiliza as entradas para uma instrução INSERT parametrizada do processo.
13. Na pá **ação da aplicação de lógica** , selecione a **Ligação de resultados**. As entradas deverão ter o aspeto da seguinte forma:  
![][4]

#### <a name="what-you-need-to-know"></a>O que precisa de saber

- Conector trunca nomes de tabelas DB2 quando formando nomes de ação de aplicação de lógica. Por exemplo, a operação de **Inserir NOVASENCOM** é truncada para **Inserir NEWORDER**.
- Depois de guardar a aplicação de lógica **Accionadores e acções**, a aplicação de lógica processa a operação. Poderão existir um atraso de um número de segundos (por exemplo, 3-5 segundos) antes de lógica aplicação processa a operação. Opcionalmente, pode clicar em **Executar agora** para processar a operação.
- Conector DB2 define EntitySet os membros com atributos, incluindo se o membro corresponde a uma coluna de DB2 com uma predefinida ou gerado colunas (por exemplo, identidade). Aplicação de lógica apresenta um asterisco vermelho junto ao nome de ID do membro do EntitySet, para designar DB2 colunas que requerem valores. Não deve introduzir um valor para o membro ORDID, que corresponde à coluna de identidade DB2. Pode introduzir valores para os outros membros opcionais (itens, ORDDATE, REQDATE, SHIPID, frete, SHIPCTRY), que correspondem a colunas DB2 com valores predefinidos. 
- Conector DB2 devolve à aplicação de lógica a resposta na mensagem para EntitySet que inclui os valores para as colunas de identidade, que deriva da SQLDARD DRDA (dados de resposta área de dados do SQL) na instrução de SQL INSERT preparada. Servidor DB2 não devolver os valores introduzidos para colunas com valores predefinidos.  


## <a name="logic-app-with-db2-connector-action-to-add-bulk-data"></a>Aplicação de lógica com DB2 ação de conexão para adicionar dados em volume ##
Pode definir uma ação de aplicação de lógica para adicionar dados a uma tabela de DB2 utilizando uma operação API em volume inserir. Por exemplo, pode inserir duas nova ordem registos de cliente, através do processamento de uma instrução SQL INSERT utilizando uma matriz de valores da linha relativamente a uma tabela definido com uma coluna de identidade devolver linhas afetadas para a aplicação de lógica (SELECIONE ORDID da tabela FINAL (Inserir para NWIND. NOVASENCOM (CUSTID, NOMEDOENVIO, SHIPADDR, CIDADEDEENVIO, SHIPREG, SHIPZIP) VALORES (?,?,?,?,?,?))).

1. Na startboard Azure, selecione **+** (sinal de adição), **Web + Mobile**e, em seguida, **aplicação lógica**.
2. Introduza o nome (por exemplo, "NewOrdersBulkDb2"), o plano de serviço de aplicação, a outras propriedades e, em seguida, selecione **Criar**.
3. Na startboard Azure, selecione a lógica aplicação que acabou de criar, **Definições**e, em seguida, **Accionadores e ações**.
4. Na pá accionadores e ações, selecione **criar de raiz** da aplicação de lógica modelos.
5. No painel de API aplicações, selecione **Periodicidade**, definir uma frequência e intervalo e, em seguida, **marca de verificação**.
6. No painel de API aplicações, selecione **DB2 conexão**, expanda a lista de operações para selecionar **Em volume inserir no novo**.
7. Introduza o valor de **linhas** como uma matriz. Por exemplo, copie e cole o seguinte:

    ```
    [{"CUSTID":10081,"SHIPNAME":"Trail's Head Gourmet Provisioners","SHIPADDR":"722 DaVinci Blvd.","SHIPCITY":"Kirkland","SHIPREG":"WA","SHIPZIP":"98034"},{"CUSTID":10088,"SHIPNAME":"White Clover Markets","SHIPADDR":"305 14th Ave. S. Suite 3B","SHIPCITY":"Seattle","SHIPREG":"WA","SHIPZIP":"98128","SHIPCTRY":"USA"}]
    ```

8. Selecione a **marca de verificação** para guardar as definições de ação e, em seguida, **Guardar**. As definições de deverão ter o aspeto da seguinte forma:  
![][6]

9. Na lista **todos os é executado** em **operações**, clique no item listado primeiro (Use mais recente).
10. Na pá **aplicação lógica executar** , clique no item de **ação** .
11. No pá **ação da aplicação de lógica** , clique na **Ligação de entradas do tipo**. Saídas de deverão ter o aspeto da seguinte forma:  
[][7]
12. No pá **ação da aplicação de lógica** , clique na **Ligação de resultados**. Saídas de deverão ter o aspeto da seguinte forma:  
![][8]

#### <a name="what-you-need-to-know"></a>O que precisa de saber

- Conector trunca nomes de tabelas DB2 quando formando nomes de ação de aplicação de lógica. Por exemplo, a operação **Em volume inserir NOVASENCOM** é truncada para **Em volume inserir no novo**.
- Por omissão identidade colunas (por exemplo, ORDID), anuláveis colunas (por exemplo, DATADEENVIO) e colunas com valores predefinidos (por exemplo, ORDDATE, REQDATE, SHIPID, frete, SHIPCTRY), base de dados DB2 gera valores.
- Ao especificar "hoje" e "amanhã", conexão DB2 gera "Data atual" e "Data atual + 1 dia" funções (por exemplo, REQDATE). 


## <a name="logic-app-with-db2-connector-trigger-to-read-alter-or-delete-data"></a>Aplicação de lógica com DB2 acionador de conexão para ler, alterar ou eliminar dados ##
Pode definir um accionador de aplicação de lógica para as inquérito e ler os dados a partir de uma tabela de DB2 utilizando uma operação de composta API inquérito dados. Por exemplo, pode ler um ou mais nova ordem registos de cliente, devolver os registos para a aplicação de lógica. As definições de pacote/aplicação DB2 ligação deverão ter o aspeto da seguinte forma:

    Definição de aplicação | Valor
--- | --- | ---
PollToCheckData | SELECIONAR CONTAGEM (\*) a partir do NOVASENCOM onde DATAENVIO é nulo
PollToReadData | SELECIONE \* a partir do NOVASENCOM onde DATAENVIO é nulo para actualização
PollToAlterData | <no value specified>


Além disso, pode definir um accionador de aplicação de lógica para as inquérito, leia e alterar dados numa tabela de DB2 utilizando uma operação de composta API inquérito dados. Por exemplo, pode ler um ou mais nova ordem registos de cliente, atualizar os valores da linha, devolver os registos selecionados (antes da atualização) para a aplicação de lógica. As definições de pacote/aplicação DB2 ligação deverão ter o aspeto da seguinte forma:

    Definição de aplicação | Valor
--- | --- | ---
PollToCheckData | SELECIONAR CONTAGEM (\*) a partir do NOVASENCOM onde DATAENVIO é nulo
PollToReadData | SELECIONE \* a partir do NOVASENCOM onde DATAENVIO é nulo para actualização
PollToAlterData | ACTUALIZAÇÃO NOVASENCOM conjunto DATADEENVIO = a data atual onde OF atual &lt;CURSOR&gt;


Além disso, pode definir um accionador de aplicação de lógica para as inquérito, leia e remover dados de uma tabela de DB2 utilizando uma operação de composta API inquérito dados. Por exemplo, pode ler um ou mais nova ordem registos de cliente, eliminar linhas, devolver os registos selecionados (antes de eliminar) para a aplicação de lógica. As definições de pacote/aplicação DB2 ligação deverão ter o aspeto da seguinte forma:

    Definição de aplicação | Valor
--- | --- | ---
PollToCheckData | SELECIONAR CONTAGEM (\*) a partir do NOVASENCOM onde DATAENVIO é nulo
PollToReadData | SELECIONE \* a partir do NOVASENCOM onde DATAENVIO é nulo para actualização
PollToAlterData | Eliminar NOVASENCOM onde OF atual &lt;CURSOR&gt;

Neste exemplo, lógica aplicação irá as inquérito, ler, atualizar e, em seguida, voltar a ler dados na tabela DB2.

1. Na startboard Azure, selecione **+** (sinal de adição), **Web + Mobile**e, em seguida, **aplicação lógica**.
2. Introduza o nome (por exemplo, "ShipOrdersDb2"), o plano de serviço de aplicação, a outras propriedades e, em seguida, selecione **Criar**.
3. Na startboard Azure, selecione a lógica aplicação que acabou de criar, **Definições**e, em seguida, **Accionadores e ações**.
4. Na pá accionadores e ações, selecione **criar de raiz** da aplicação de lógica modelos.
5. No painel de API aplicações, selecione **DB2 conexão**, definir uma frequência e intervalo e, em seguida, **marca de verificação**.
6. No painel de API aplicações, selecione **DB2 conexão**, expanda a lista de operações para selecionar **Selecione NOVASENCOM**.
7. Selecione a **marca de verificação** para guardar as definições de ação e, em seguida, **Guardar**. As definições de deverão ter o aspeto da seguinte forma:  
![][10]  
8. Clique em para fechar a pá **Accionadores e ações** e, em seguida, clique em para fechar a pá **Definições** .
9. Na lista **todos os é executado** em **operações**, clique no item listado primeiro (Use mais recente).
10. Na pá **aplicação lógica executar** , clique no item de **ação** .
11. No pá **ação da aplicação de lógica** , clique na **Ligação de resultados**. Saídas de deverão ter o aspeto da seguinte forma:  
![][11]


## <a name="logic-app-with-db2-connector-action-to-remove-data"></a>Aplicação de lógica com DB2 ação de conexão para remover dados ##
Pode definir uma ação de aplicação de lógica para remover dados de uma tabela de DB2 utilizando uma mensagem ou eliminar API a operação de OData entidade. Por exemplo, pode inserir um novo registo de encomenda de cliente, através do processamento de uma instrução SQL INSERT relativamente a uma tabela definida com uma coluna de identidade devolver o valor de identidade ou as linhas afetadas para a aplicação de lógica (SELECIONE ORDID da tabela FINAL (Inserir para NWIND. NOVASENCOM (CUSTID, NOMEDOENVIO, SHIPADDR, CIDADEDEENVIO, SHIPREG, SHIPZIP) VALORES (?,?,?,?,?,?))).

## <a name="create-logic-app-using-db2-connector-to-remove-data"></a>Criar utilizar DB2 conexão para remover dados de aplicação de lógica ##
Pode criar uma nova aplicação de lógica a partir do Azure Marketplace e, em seguida, utilize o conector de DB2 como uma ação para remover encomendas de clientes. Por exemplo, pode utilizar a operação de eliminação condicional do DB2 conexão para processar uma instrução de SQL DELETE (eliminar a partir do NOVASENCOM onde ORDID > = 10000).

1. No menu do hub do quadro Azure **Iniciar** , clique em **+** (sinal de adição), clique em **Web + Mobile**e, em seguida, clique em **aplicação de lógica**. 
2. Na pá **Criar lógica aplicação** , escreva um **nome**, por exemplo **RemoveOrdersDb2**.
3. Selecione ou definir valores para as outras definições (por exemplo, o plano de serviço, o grupo de recursos).
4. As definições de deverão ter o aspeto da seguinte forma. Clique em **Criar**:  
![][12]  
5. Na pá **Definições** , clique em **Accionadores e ações**.
6. Na pá **Accionadores e acções** , na lista de **aplicações de lógica modelos** , clique em **criar de raiz**.
7. No pá **Accionadores e ações** , no painel de **API aplicações** , dentro do grupo de recursos, clique em **Periodicidade**.
8. Na superfície da lógica aplicação estrutura, clique no item de **Periodicidade** , definir uma **frequência** e de **intervalo**, por exemplo, **dias** e **1**e, em seguida, clique na **marca de verificação** para guardar as definições de item de periodicidade.
9. Na pá **Accionadores e ações** , no painel de **API aplicações** , dentro do grupo de recursos, clique em **conexão DB2**.
10. Na superfície da lógica aplicação estrutura, clique no item de ação **DB2 conexão** , clique nas reticências (…****) para expandir a lista de operações e, em seguida, clique em **Eliminar condicional de N**.
11. No item de ação de conexão DB2, escreva **ORDID página 10000** de uma **expressão que identifica um subconjunto de entradas**.
12. Clique a **marca de verificação** para guardar as definições de ação e, em seguida, clique em **Guardar**. As definições de deverão ter o aspeto da seguinte forma:  
![][13]  
13. Clique em para fechar a pá **Accionadores e ações** e, em seguida, clique em para fechar a pá **Definições** .
14. Na lista **todos os é executado** em **operações**, clique no item listado primeiro (Use mais recente).
15. Na pá **aplicação lógica executar** , clique no item de **ação** .
16. No pá **ação da aplicação de lógica** , clique na **Ligação de resultados**. Saídas de deverão ter o aspeto da seguinte forma:  
![][14]

**Nota:** Estruturador de fluxos de lógica aplicação trunca nomes de tabelas. Por exemplo, a operação **condicional eliminar de NOVASENCOM** é truncada para **Eliminar condicional de N**.


> [AZURE.TIP] Utilize as seguintes instruções SQL para criar a tabela de exemplo e procedimentos armazenados. 

Pode criar a tabela de NOVASENCOM de exemplo utilizando as seguintes instruções de linguagem DDL SQL DB2:
 
    CREATE TABLE ORDERS (  
        ORDID INT NOT NULL GENERATED BY DEFAULT AS IDENTITY (START WITH 10000, INCREMENT BY 1) ,  
        CUSTID INT NOT NULL ,  
        EMPID INT NOT NULL DEFAULT 10000 ,  
        ORDDATE DATE NOT NULL DEFAULT CURRENT DATE ,  
        REQDATE DATE DEFAULT CURRENT DATE ,  
        SHIPDATE DATE ,  
        SHIPID INT NOT NULL DEFAULT 10000,  
        FREIGHT DECIMAL (9,2) NOT NULL DEFAULT 0.00 ,  
        SHIPNAME CHAR (40) NOT NULL ,  
        SHIPADDR CHAR (60) NOT NULL ,  
        SHIPCITY CHAR (20) NOT NULL ,  
        SHIPREG CHAR (15) NOT NULL ,  
        SHIPZIP CHAR (10) NOT NULL ,  
        SHIPCTRY CHAR (15) NOT NULL DEFAULT 'USA' ,  
        PRIMARY KEY(ORDID)  
        )  
 
    CREATE UNIQUE INDEX XORDID ON ORDERS (ORDID ASC)  



Pode criar o procedimento SPOERID armazenado exemplo utilizando a seguinte instrução DB2 DDL:
 
    CREATE OR REPLACE PROCEDURE NWIND.SPORDERID (IN ORDERID VARCHAR(128))  
        DYNAMIC RESULT SETS 1  
    P1: BEGIN  
        DECLARE CURSOR1 CURSOR WITH RETURN FOR  
            SELECT * FROM NWIND.NEWORDERS  
                WHERE ORDID = ORDERID;  
        OPEN CURSOR1;  
    END P1  
    ') 


## <a name="hybrid-configuration-optional"></a>Configuração híbrida (opcional)

> [AZURE.NOTE] Este passo é necessário apenas se estiver a utilizar DB2 conexão no local atrás da firewall.

Aplicação de serviço utiliza o Gestor de configuração híbrida para estabelecer uma ligação segura ao seu sistema no local. Se a conexão utiliza no local IBM DB2 Server para Windows, o Gestor de ligação híbrido é necessário.

Consulte o artigo [utilizar o Gestor de ligação híbrido](app-service-logic-hybrid-connection-manager.md).


## <a name="do-more-with-your-connector"></a>Fazer mais com a conexão
Agora que a conexão for criada, pode adicioná-la para um fluxo de trabalho de empresas com uma aplicação de lógica. Consulte o artigo [quais são as aplicações de lógica?](app-service-logic-what-are-logic-apps.md).

Crie as aplicações de API REST APIs a utilizar. Consulte o artigo [referência da aplicações API e conectores](http://go.microsoft.com/fwlink/p/?LinkId=529766).

Também pode rever a segurança estatísticas e controlo de desempenho ao conector. Consulte o artigo [Gerir e monitorizar o seu incorporada aplicações API e conectores](app-service-logic-monitor-your-connectors.md).


<!--Image references-->
[1]: ./media/app-service-logic-connector-db2/ApiApp_Db2Connector_Create.png
[2]: ./media/app-service-logic-connector-db2/LogicApp_NewOrdersDb2_Create.png
[3]: ./media/app-service-logic-connector-db2/LogicApp_NewOrdersDb2_TriggersActions.png
[4]: ./media/app-service-logic-connector-db2/LogicApp_NewOrdersDb2_Outputs.png
[5]: ./media/app-service-logic-connector-db2/LogicApp_NewOrdersBulkDb2_Create.png
[6]: ./media/app-service-logic-connector-db2/LogicApp_NewOrdersBulkDb2_TriggersActions.png
[7]: ./media/app-service-logic-connector-db2/LogicApp_NewOrdersBulkDb2_Inputs.png
[8]: ./media/app-service-logic-connector-db2/LogicApp_NewOrdersBulkDb2_Outputs.png
[9]: ./media/app-service-logic-connector-db2/LogicApp_UpdateOrdersDb2_Create.png
[10]: ./media/app-service-logic-connector-db2/LogicApp_UpdateOrdersDb2_TriggersActions.png
[11]: ./media/app-service-logic-connector-db2/LogicApp_UpdateOrdersDb2_Outputs.png
[12]: ./media/app-service-logic-connector-db2/LogicApp_RemoveOrdersDb2_Create.png
[13]: ./media/app-service-logic-connector-db2/LogicApp_RemoveOrdersDb2_TriggersActions.png
[14]: ./media/app-service-logic-connector-db2/LogicApp_RemoveOrdersDb2_Outputs.png

