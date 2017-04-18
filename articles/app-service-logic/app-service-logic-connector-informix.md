<properties
   pageTitle="Utilizando o conector de Informix num serviço de aplicação do Microsoft Azure | Microsoft Azure"
   description="Como utilizar o conector de Informix com accionadores da aplicação de lógica e acções"
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

# <a name="informix-connector"></a>Conector Informix
>[AZURE.NOTE] Esta versão do artigo aplica-se a versão do esquema de 2014-12-01-pré-visualização de aplicações de lógica.

Conector da Microsoft para Informix é uma aplicação de API para ligar a aplicações através da aplicação de serviço de Azure a recursos armazenados numa base de dados IBM Informix. Conector inclui um cliente da Microsoft para ligar a computadores de servidor remotos Informix através de uma ligação de rede TCP/IP, incluindo ligações a Azure híbrido no local Informix servidores utilizando o Azure Service Bus reencaminhamento. Conector suporta as seguintes operações de base de dados:

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


## <a name="create-the-informix-connector"></a>Criar o conector de Informix
Pode definir uma conexão a partir de uma aplicação de lógica ou do Azure Marketplace, tal como no exemplo seguinte:  

1. Na startboard Azure, selecione **Marketplace**.
2. Na pá **Tudo** , escreva **informix** na caixa **Procurar tudo** e, em seguida, clique na tecla enter.
3. Na pesquisa tudo o painel de resultados, selecione **Informix conexão**.
4. No pá de descrição de conexão Informix, selecione **Criar**.
5. Na pá de pacote de conexão Informix, introduza o nome (por exemplo, "InformixConnectorNewOrders"), o plano de serviço de aplicação e outras propriedades.
6. Selecione **definições do pacote**e introduza as seguintes definições de pacote.

    Nome | Obrigatório |  Descrição
--- | --- | ---
ConnectionString | Sim | Cadeia de ligação de cliente Informix (por exemplo, "endereço de rede = nomedoservidor; Porta de rede = 9089; ID de utilizador = NomeUtilizador; Palavra-passe = palavra-passe; catálogo inicial = nwind; esquema predefinido = informix ").
Tabelas | Sim | Lista de tabela separados por vírgulas, ver e alias nomes necessários para operações de OData e para gerar swagger documentação com exemplos (por exemplo, "NOVASENCOM").
Procedimentos | Sim | Separados por ponto e lista de nomes do procedimento e função (por exemplo, "SPORDERID").
OnPremise | N | Implemente no local com o Azure Service Bus reencaminhamento.
ServiceBusConnectionString | N | Cadeia de ligação do Azure Service Bus reencaminhamento.
PollToCheckData | N | SELECIONE CONTAGEM declaração para utilizar com um acionador de aplicação lógica (por exemplo, "SELECIONAR CONTAGEM (\*) a partir do NOVASENCOM onde DATADEENVIO IS NULL").
PollToReadData | N | Instrução SELECT para utilizar com um acionador de aplicação lógica (por exemplo, "SELECIONE \* a partir do NOVASENCOM onde DATAENVIO é nulo para ATUALIZAR").
PollToAlterData | N | ATUALIZAR ou instrução DELETE para utilizar com um acionador de aplicação lógica (por exemplo, "ATUALIZAÇÃO NOVASENCOM definir DATADEENVIO = atual data onde atual da &lt;CURSOR&gt;").

7. Selecione **OK**e, em seguida, selecione **Criar**.
8. Quando estiver concluído, as definições de pacote têm um aspeto semelhantes ao seguinte:  
![][1]


## <a name="logic-app-with-informix-connector-action-to-add-data"></a>Aplicação de lógica com Informix ação de conexão para adicionar dados ##
Pode definir uma ação de aplicação de lógica para adicionar dados a uma tabela de Informix utilizando uma mensagem ou inserir API a operação de OData entidade. Por exemplo, pode inserir um novo registo de encomenda de cliente, através do processamento de uma instrução SQL INSERT relativamente a uma tabela definida com uma coluna de identidade devolver o valor de identidade ou as linhas afetadas para a aplicação de lógica (SELECIONAR ORDID da tabela FINAL (Inserir para NOVASENCOM (CUSTID, NOMEDOENVIO, SHIPADDR, CIDADEDEENVIO, SHIPREG, SHIPZIP) valores (?,?,?,?,?,?))).

> [AZURE.TIP] Ligação Informix "*publicar para EntitySet*" devolve o valor da coluna de identidade e "*API inserir*" devolve linhas afetadas

1. Na startboard Azure, selecione **+** (sinal de adição), **Web + Mobile**e, em seguida, **aplicação lógica**.
2. Introduza o nome (por exemplo, "NewOrdersInformix"), o plano de serviço de aplicação, a outras propriedades e, em seguida, selecione **Criar**.
3. Na startboard Azure, selecione a lógica aplicação que acabou de criar, **Definições**e, em seguida, **Accionadores e ações**.
4. Na pá accionadores e ações, selecione **criar de raiz** da aplicação de lógica modelos.
5. No painel de API aplicações, selecione **Periodicidade**, definir uma frequência e intervalo e, em seguida, **marca de verificação**.
6. No painel de API aplicações, selecione **Informix conexão**, expanda a lista de operações para selecionar a **Inserir nas NEWORDER**.
7. Expanda a lista de parâmetros para introduzir os seguintes valores:  

    Nome | Valor
--- | --- 
CUSTID | 10042
SHIPID | 10000
NOMEDOENVIO | K preguiça Kountry arquivo 
SHIPADDR | Socalcos orquestra 12
CIDADEDEENVIO | Da Rainha 
SHIPREG | WA
SHIPZIP | 99362 

8. Selecione a **marca de verificação** para guardar as definições de ação e, em seguida, **Guardar**.
9. As definições de deverão ter o aspeto da seguinte forma:  
![][3]  
10. Na lista **todos os é executado** em **operações**, selecione o item listado primeiro (Use mais recente). 
11. Na pá **aplicação lógica executar** , selecione a **ação** item **informixconnectorneworders**.
12. Na pá **ação da aplicação de lógica** , selecione a **Ligação de entradas do tipo**. Conector Informix utiliza as entradas para uma instrução INSERT parametrizada do processo.
13. Na pá **ação da aplicação de lógica** , selecione a **Ligação de resultados**. As entradas deverão ter o aspeto da seguinte forma:  
![][4]

#### <a name="what-you-need-to-know"></a>O que precisa de saber

- Conector trunca nomes de tabelas Informix quando formando nomes de ação de aplicação de lógica. Por exemplo, a operação de **Inserir NOVASENCOM** é truncada para **Inserir NEWORDER**.
- Depois de guardar a aplicação de lógica **Accionadores e acções**, a aplicação de lógica processa a operação. Poderão existir um atraso de um número de segundos (por exemplo, 3-5 segundos) antes de lógica aplicação processa a operação. Opcionalmente, pode clicar em **Executar agora** para processar a operação.
- Conector Informix define EntitySet os membros com atributos, incluindo se o membro corresponde a uma coluna de Informix com uma predefinida ou gerado colunas (por exemplo, identidade). Aplicação de lógica apresenta um asterisco vermelho junto ao nome de ID do membro do EntitySet, para designar Informix colunas que requerem valores. Não deve introduzir um valor para o membro ORDID, que corresponde à coluna de identidade Informix. Pode introduzir valores para os outros membros opcionais (itens, ORDDATE, REQDATE, SHIPID, frete, SHIPCTRY), que correspondem a colunas Informix com valores predefinidos. 
- Conector Informix devolve à aplicação de lógica a resposta na mensagem para EntitySet que inclui os valores para as colunas de identidade, que deriva da SQLDARD DRDA (dados de resposta área de dados do SQL) na instrução de SQL INSERT preparada. Servidor de Informix não devolver os valores introduzidos para colunas com valores predefinidos.  


## <a name="logic-app-with-informix-connector-action-to-add-bulk-data"></a>Aplicação de lógica com Informix ação de conexão para adicionar dados em volume ##
Pode definir uma ação de aplicação de lógica para adicionar dados a uma tabela de Informix utilizando uma operação API em volume inserir. Por exemplo, pode inserir duas nova ordem registos de cliente, através do processamento de uma instrução SQL INSERT utilizando uma matriz de valores da linha relativamente a uma tabela definido com uma coluna de identidade devolver linhas afetadas para a aplicação de lógica (SELECIONAR ORDID da tabela FINAL (Inserir para NOVASENCOM (CUSTID, NOMEDOENVIO, SHIPADDR, CIDADEDEENVIO, SHIPREG, SHIPZIP) valores (?,?,?,?,?,?))).

1. Na startboard Azure, selecione **+** (sinal de adição), **Web + Mobile**e, em seguida, **aplicação lógica**.
2. Introduza o nome (por exemplo, "NewOrdersBulkInformix"), o plano de serviço de aplicação, a outras propriedades e, em seguida, selecione **Criar**.
3. Na startboard Azure, selecione a lógica aplicação que acabou de criar, **Definições**e, em seguida, **Accionadores e ações**.
4. Na pá accionadores e ações, selecione **criar de raiz** da aplicação de lógica modelos.
5. No painel de API aplicações, selecione **Periodicidade**, definir uma frequência e intervalo e, em seguida, **marca de verificação**.
6. No painel de API aplicações, selecione **Informix conexão**, expanda a lista de operações para selecionar **Em volume inserir no novo**.
7. Introduza o valor de **linhas** como uma matriz. Por exemplo, copie e cole o seguinte:  

    ```
    [{"custid":10081,"shipid":10000,"shipname":"Trail's Head Gourmet Provisioners","shipaddr":"722 DaVinci Blvd.","shipcity":"Kirkland","shipreg":"WA","shipzip":"98034"},{"custid":10088,"shipid":10000,"shipname":"White Clover Markets","shipaddr":"305 14th Ave. S. Suite 3B","shipcity":"Seattle","shipreg":"WA","shipzip":"98128","shipctry":"USA"}]
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

- Conector trunca nomes de tabelas Informix quando formando nomes de ação de aplicação de lógica. Por exemplo, a operação **Em volume inserir NOVASENCOM** é truncada para **Em volume inserir no novo**.
- Base de dados Informix poderá sensível a maiúsculas nomes de tabelas e colunas. Por exemplo, inserir em volume operação matriz nomes das colunas poderão ter de ser especificado em letra minúscula ("custid") em vez de maiúsculas ("CUSTID").
- Por omissão identidade colunas (por exemplo, ORDID), anuláveis colunas (por exemplo, DATADEENVIO) e colunas com valores predefinidos (por exemplo, ORDDATE, REQDATE, SHIPID, frete, SHIPCTRY), base de dados Informix gera valores.
- Ao especificar "hoje" e "amanhã", conexão Informix gera "Data atual" e "Data atual + 1 dia" funções (por exemplo, REQDATE). 


## <a name="logic-app-with-informix-connector-trigger-to-read-alter-or-delete-data"></a>Aplicação de lógica com Informix acionador de conexão para ler, alterar ou eliminar dados ##
Pode definir um accionador de aplicação de lógica para as inquérito e ler os dados a partir de uma tabela de Informix utilizando uma operação de composta API inquérito dados. Por exemplo, pode ler um ou mais nova ordem registos de cliente, devolver os registos para a aplicação de lógica. As definições de pacote/aplicação Informix ligação deverão ter o aspeto da seguinte forma:

    Definição de aplicação | Valor
--- | --- | ---
PollToCheckData | SELECIONAR CONTAGEM (\*) a partir do NOVASENCOM onde DATAENVIO é nulo
PollToReadData | SELECIONE \* a partir do NOVASENCOM onde DATAENVIO é nulo para actualização
PollToAlterData | <no value specified>


Além disso, pode definir um accionador de aplicação de lógica para as inquérito, leia e alterar dados numa tabela Informix utilizando uma operação de composta API inquérito dados. Por exemplo, pode ler um ou mais nova ordem registos de cliente, atualizar os valores da linha, devolver os registos selecionados (antes da atualização) para a aplicação de lógica. As definições de pacote/aplicação Informix ligação deverão ter o aspeto da seguinte forma:

    Definição de aplicação | Valor
--- | --- | ---
PollToCheckData | SELECIONAR CONTAGEM (\*) a partir do NOVASENCOM onde DATAENVIO é nulo
PollToReadData | SELECIONE \* a partir do NOVASENCOM onde DATAENVIO é nulo para actualização
PollToAlterData | ACTUALIZAÇÃO NOVASENCOM conjunto DATADEENVIO = a data atual onde OF atual &lt;CURSOR&gt;


Além disso, pode definir um accionador de aplicação lógica as inquérito, leia e remover dados de uma tabela de Informix utilizando uma operação de composta API inquérito dados. Por exemplo, pode ler um ou mais nova ordem registos de cliente, eliminar linhas, devolver os registos selecionados (antes de eliminar) para a aplicação de lógica. As definições de pacote/aplicação Informix ligação deverão ter o aspeto da seguinte forma:

    Definição de aplicação | Valor
--- | --- | ---
PollToCheckData | SELECIONAR CONTAGEM (\*) a partir do NOVASENCOM onde DATAENVIO é nulo
PollToReadData | SELECIONE \* a partir do NOVASENCOM onde DATAENVIO é nulo para actualização
PollToAlterData | Eliminar NOVASENCOM onde OF atual &lt;CURSOR&gt;

Neste exemplo, lógica aplicação irá as inquérito, ler, atualizar e, em seguida, voltar a ler dados na tabela Informix.

1. Na startboard Azure, selecione **+** (sinal de adição), **Web + Mobile**e, em seguida, **aplicação lógica**.
2. Introduza o nome (por exemplo, "ShipOrdersInformix"), o plano de serviço de aplicação, a outras propriedades e, em seguida, selecione **Criar**.
3. Na startboard Azure, selecione a lógica aplicação que acabou de criar, **Definições**e, em seguida, **Accionadores e ações**.
4. Na pá accionadores e ações, selecione **criar de raiz** da aplicação de lógica modelos.
5. No painel de API aplicações, selecione **Informix conexão**, definir uma frequência e intervalo e, em seguida, **marca de verificação**.
6. No painel de API aplicações, selecione **Informix conexão**, expanda a lista de operações para selecionar **Selecione NOVASENCOM**.
7. Selecione a **marca de verificação** para guardar as definições de ação e, em seguida, **Guardar**. As definições de deverão ter o aspeto da seguinte forma:  
![][10]
8. Clique em para fechar a pá **Accionadores e ações** e, em seguida, clique em para fechar a pá **Definições** .
9. Na lista **todos os é executado** em **operações**, clique no item listado primeiro (Use mais recente).
10. Na pá **aplicação lógica executar** , clique no item de **ação** .
11. No pá **ação da aplicação de lógica** , clique na **Ligação de resultados**. Saídas de deverão ter o aspeto da seguinte forma:  
![][11]


## <a name="logic-app-with-informix-connector-action-to-remove-data"></a>Aplicação de lógica com Informix ação de conexão para remover dados ##
Pode definir uma ação de aplicação de lógica para remover dados de uma tabela de Informix utilizando um API Delete ou a mensagem a operação de OData entidade. Por exemplo, pode inserir um novo registo de encomenda de cliente, através do processamento de uma instrução SQL INSERT relativamente a uma tabela definida com uma coluna de identidade devolver o valor de identidade ou as linhas afetadas para a aplicação de lógica (SELECIONAR ORDID da tabela FINAL (Inserir para NOVASENCOM (CUSTID, NOMEDOENVIO, SHIPADDR, CIDADEDEENVIO, SHIPREG, SHIPZIP) valores (?,?,?,?,?,?))).

## <a name="create-logic-app-using-informix-connector-to-remove-data"></a>Criar utilizar Informix conexão para remover dados de aplicação de lógica ##
Pode criar uma nova aplicação de lógica a partir do Azure Marketplace e, em seguida, utilize o conector de Informix como uma ação para remover encomendas de clientes. Por exemplo, pode utilizar a operação de eliminação condicional do Informix conexão para processar uma instrução de SQL DELETE (eliminar a partir do NOVASENCOM onde ORDID > = 10000).

1. No menu do hub do quadro Azure **Iniciar** , clique em **+** (sinal de adição), clique em **Web + Mobile**e, em seguida, clique em **aplicação de lógica**. 
2. Na pá **Criar lógica aplicação** , escreva um **nome**, por exemplo **RemoveOrdersInformix**.
3. Selecione ou definir valores para as outras definições (por exemplo, o plano de serviço, o grupo de recursos).
4. As definições de deverão ter o aspeto da seguinte forma. Clique em **Criar**:  
![][12]
5. Na pá **Definições** , clique em **Accionadores e ações**.
6. Na pá **Accionadores e acções** , na lista de **aplicações de lógica modelos** , clique em **criar de raiz**.
7. No pá **Accionadores e ações** , no painel de **API aplicações** , dentro do grupo de recursos, clique em **Periodicidade**.
8. Na superfície da lógica aplicação estrutura, clique no item de **Periodicidade** , definir uma **frequência** e de **intervalo**, por exemplo, **dias** e **1**e, em seguida, clique na **marca de verificação** para guardar as definições de item de periodicidade.
9. Na pá **Accionadores e ações** , no painel de **API aplicações** , dentro do grupo de recursos, clique em **conexão Informix**.
10. Na superfície da lógica aplicação estrutura, clique no item de ação **Informix conexão** , clique nas reticências (…****) para expandir a lista de operações e, em seguida, clique em **Eliminar condicional de N**.
11. No item de ação de conexão Informix, escreva **ordid página 10000** para uma **expressão que identifica um subconjunto de entradas**.
12. Clique a **marca de verificação** para guardar as definições de ação e, em seguida, clique em **Guardar**. As definições de deverão ter o aspeto da seguinte forma:  
![][13]
13. Clique em para fechar a pá **Accionadores e ações** e, em seguida, clique em para fechar a pá **Definições** .
14. Na lista **todos os é executado** em **operações**, clique no item listado primeiro (Use mais recente).
15. Na pá **aplicação lógica executar** , clique no item de **ação** .
16. No pá **ação da aplicação de lógica** , clique na **Ligação de resultados**. Saídas de deverão ter o aspeto da seguinte forma:  
![][14]

**Nota:** Estruturador de fluxos de lógica aplicação trunca nomes de tabelas. Por exemplo a operação **condicional eliminar de NOVASENCOM** é truncada para **Eliminar condicional de N**.


> [AZURE.TIP] Utilize as seguintes instruções SQL para criar a tabela de exemplo e procedimentos armazenados. 

Pode criar a tabela de NOVASENCOM de exemplo utilizando as seguintes instruções de linguagem DDL Informix SQL:
 
    create table neworders (  
        ordid serial(10000) unique ,  
        custid int not null ,  
        empid int not null default 10000 ,  
        orddate date not null default today ,  
        reqdate date default today ,  
        shipdate date ,  
        shipid int not null default 10000 ,  
        freight decimal (9,2) not null default 0.00 ,  
        shipname char (40) not null ,  
        shipaddr char (60) not null ,  
        shipcity char (20) not null ,  
        shipreg char (15) not null ,  
        shipzip char (10) not null ,  
        shipctry char (15) not null default ''USA'' 
        )


Pode criar o procedimento SPORDERID armazenado exemplo utilizando a seguinte instrução Informix DDL:
 
    create procedure sporderid ( ord_id int)  
        returning int, int, int, date, date, date, int, decimal (9,2), char (40), char (60), char (20), char (15), char (10), char (15)  
        define xordid, xcustid, xempid, xshipid int;  
        define xorddate, xreqdate, xshipdate date;  
        define xfreight decimal (9,2);  
        define xshipname char (40);  
        define xshipaddr char (60);  
        define xshipcity char (20);  
        define xshipreg, xshipctry char (15);  
        define xshipzip char (10);  
        select ordid, custid, empid, orddate, reqdate, shipdate, shipid, freight, shipname, shipaddr, shipcity, shipreg, shipzip, shipctry  
            into xordid, xcustid, xempid, xorddate, xreqdate, xshipdate, xshipid, xfreight, xshipname, xshipaddr, xshipcity, xshipreg, xshipzip, xshipctry  
            from neworders where ordid = ord_id;  
        return xordid, xcustid, xempid, xorddate, xreqdate, xshipdate, xshipid, xfreight, xshipname, xshipaddr, xshipcity, xshipreg, xshipzip, xshipctry;  
    end procedure; 


## <a name="hybrid-configuration-optional"></a>Configuração híbrida (opcional)

> [AZURE.NOTE] Este passo é necessário apenas se estiver a utilizar DB2 conexão no local atrás da firewall.

Aplicação de serviço utiliza o Gestor de configuração híbrida para estabelecer uma ligação segura ao seu sistema no local. Se a conexão utiliza no local IBM DB2 Server para Windows, o Gestor de ligação híbrido é necessário.

Consulte o artigo [utilizar o Gestor de ligação híbrido](app-service-logic-hybrid-connection-manager.md).


## <a name="do-more-with-your-connector"></a>Fazer mais com a conexão
Agora que a conexão for criada, pode adicioná-la para um fluxo de trabalho de empresas com uma aplicação de lógica. Consulte o artigo [quais são as aplicações de lógica?](app-service-logic-what-are-logic-apps.md).

Crie as aplicações de API REST APIs a utilizar. Consulte o artigo [referência da aplicações API e conectores](http://go.microsoft.com/fwlink/p/?LinkId=529766).

Também pode rever a segurança estatísticas e controlo de desempenho ao conector. Consulte o artigo [Gerir e monitorizar o seu incorporada aplicações API e conectores](app-service-logic-monitor-your-connectors.md).


<!--Image references-->
[1]: ./media/app-service-logic-connector-informix/ApiApp_InformixConnector_Create.png
[2]: ./media/app-service-logic-connector-informix/LogicApp_NewOrdersInformix_Create.png
[3]: ./media/app-service-logic-connector-informix/LogicApp_NewOrdersInformix_TriggersActions.png
[4]: ./media/app-service-logic-connector-informix/LogicApp_NewOrdersInformix_Outputs.png
[5]: ./media/app-service-logic-connector-informix/LogicApp_NewOrdersBulkInformix_Create.png
[6]: ./media/app-service-logic-connector-informix/LogicApp_NewOrdersBulkInformix_TriggersActions.png
[7]: ./media/app-service-logic-connector-informix/LogicApp_NewOrdersBulkInformix_Inputs.png
[8]: ./media/app-service-logic-connector-informix/LogicApp_NewOrdersBulkInformix_Outputs.png
[9]: ./media/app-service-logic-connector-informix/LogicApp_UpdateOrdersInformix_Create.png
[10]: ./media/app-service-logic-connector-informix/LogicApp_UpdateOrdersInformix_TriggersActions.png
[11]: ./media/app-service-logic-connector-informix/LogicApp_UpdateOrdersInformix_Outputs.png
[12]: ./media/app-service-logic-connector-informix/LogicApp_RemoveOrdersInformix_Create.png
[13]: ./media/app-service-logic-connector-informix/LogicApp_RemoveOrdersInformix_TriggersActions.png
[14]: ./media/app-service-logic-connector-informix/LogicApp_RemoveOrdersInformix_Outputs.png


