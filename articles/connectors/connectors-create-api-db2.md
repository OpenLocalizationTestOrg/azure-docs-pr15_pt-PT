<properties
    pageTitle="Adicionar o conector de DB2 nas suas aplicações de lógica | Microsoft Azure"
    description="Descrição geral de conexão DB2 com parâmetros REST API"
    services=""
    documentationCenter="" 
    authors="gplarsen"
    manager="erikre"
    editor=""
    tags="connectors"/>

<tags
   ms.service="logic-apps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration" 
   ms.date="09/26/2016"
   ms.author="plarsen"/>


# <a name="get-started-with-the-db2-connector"></a>Começar a trabalhar com o conector de DB2
Conector da Microsoft para DB2 liga-se a aplicações lógica para recursos armazenados numa base de dados IBM DB2. Este conector inclui um cliente da Microsoft para comunicar com computadores remotos de servidor DB2 através de uma rede TCP/IP. Isto inclui bases de dados de nuvem, como IBM Bluemix dashDB ou IBM DB2 para Windows em execução no Azure Virtualização e no local bases de dados utilizando o gateway de dados no local. Consulte o artigo [suportadas lista](connectors-create-api-db2.md#supported-db2-platforms-and-versions) do IBM DB2 plataformas e versões (neste tópico).

>[AZURE.NOTE] Esta versão do artigo aplica-se para disponibilidade geral lógica aplicações (das versões DG). 

A conexão DB2 suporta as seguintes operações de base de dados:

- Tabelas de base de dados de lista
- Ler uma linha, SELECIONE a utilizar
- Ler todas as linhas, SELECIONE a utilizar
- Adicionar uma linha utilizar Inserir
- Alterar uma linha através da ATUALIZAÇÃO
- Remover uma linha ao utilizar eliminar

Este tópico mostra-lhe como utilizar a ligação numa aplicação lógica para operações de base de dados do processo.

Para saber mais sobre aplicações de lógica, consulte o artigo [criar uma aplicação de lógica](../app-service-logic/app-service-logic-create-a-logic-app.md).

## <a name="available-actions"></a>Ações disponíveis
A conexão DB2 suporta as seguintes ações de aplicação de lógica:

- GetTables
- GetRow
- GetRows
- InsertRow
- UpdateRow
- Eliminarlinha


## <a name="list-tables"></a>Lista de tabelas
Criar uma aplicação de lógica para qualquer operação é composto por muitos passos executados através do portal do Microsoft Azure.

Pode adicionar uma ação a lista de tabelas numa base de dados DB2 da aplicação de lógica. A ação instrui a conexão para processar uma instrução de esquema DB2, tais como `CALL SYSIBM.SQLTABLES`.

### <a name="create-a-logic-app"></a>Criar uma aplicação de lógica
1.  Na **área de iniciar o Azure**, selecione **+** (sinal de adição), **Web + Mobile**e, em seguida, **Lógica de aplicação**.
2.  Introduza o **nome**, tais como `Db2getTables`, **subscrição**, **grupo de recursos**, **localização**e **Plano de serviço de aplicação**. Selecione **Afixar ao dashboard**e, em seguida, selecione **Criar**.

### <a name="add-a-trigger-and-action"></a>Adicione um acionador e uma ação
1.  No **Estruturador de fluxos de lógica de aplicações**, selecione **LogicApp em branco** na lista de **modelos** .
2.  Na lista de **Accionadores** , selecione **Periodicidade**. 
3.  No accionador **Periodicidade** , selecione **Editar**, selecione a **frequência** para baixo para selecionar o **dia**e, em seguida, defina o **intervalo** para escrever **7**.  
4.  Selecione a caixa **+ novo passo** e, em seguida, selecione **Adicionar uma ação**.
5.  Na lista de **ações** , escreva `db2` na **pesquisa de mais ações** caixa editar e, em seguida, selecione **DB2 - obter tabelas (pré-visualização)**.

    ![](./media/connectors-create-api-db2/Db2connectorActions.png)  

6.  No painel de configuração de **DB2 - obter tabelas** , selecione a **caixa de verificação** para ativar a **ligar através do gateway de dados no local**. Repare que as definições de alterar a partir da nuvem para no local.
    - Escreva o valor de **servidor**, sob a forma de número de porta de dois pontos endereço ou alias. Por exemplo, escreva `ibmserver01:50000`.
    - Escreva o valor para **base de dados**. Por exemplo, escreva `nwind`.
    - Selecione o valor para **autenticação**. Por exemplo, selecione **básica**.
    - Escreva o valor para **nome de utilizador**. Por exemplo, escreva `db2admin`.
    - Escreva o valor de **palavra-passe**. Por exemplo, escreva `Password1`.
    - Selecione o valor para o **Gateway**. Por exemplo, selecione **datagateway01**.
7. Selecione **Criar**e, em seguida, selecione **Guardar**. 

    ![](./media/connectors-create-api-db2/Db2connectorOnPremisesDataGatewayConnection.png)

8.  Na pá **Db2getTables** , dentro da lista de **todos os é executado** em **Resumo**, selecione o item listado primeiro (Use mais recente).
9.  Na pá **aplicação lógica executar** , selecione **Executar detalhes**. Dentro da lista de **ação** , selecione **Get_tables**. Consulte o artigo o valor de **Estado**, que devem ser **bem sucedida**. Selecione a **ligação de entradas** para ver as entradas. Selecionar a **ligação de resultados**e visualizar saídas; qual deve incluir uma lista de tabelas.

    ![](./media/connectors-create-api-db2/Db2connectorGetTablesLogicAppRunOutputs.png)

## <a name="create-the-connections"></a>Criar as ligações
Este conector suporta ligações para as bases de dados alojados no local e na nuvem utilizando as seguintes propriedades de ligação. 

Propriedade | Descrição
--- | ---
servidor | Obrigatório. Aceita um valor de cadeia que representa um endereço de TCP/IP ou alias, no formato IPv4 ou IPv6, seguido (delimitado por dois pontos) por um número de porta TCP/IP. 
base de dados | Obrigatório. Aceita um valor de cadeia que representa um nome DRDA da base de dados relacionais (RDBNAM). DB2 para z/SO aceita uma cadeia de 16 byte (base de dados é conhecido como um IBM DB2 para a localização do SO/z). DB2 para i5/SO aceita uma cadeia de 18 byte (base de dados é conhecido como um IBM DB2 para i relacional base de dados). DB2 para LUW aceita uma cadeia de 8 bytes.
autenticação | Opcional. Aceita um valor de item de lista, Basic ou Windows (kerberos). 
nome de utilizador | Obrigatório. Aceita um valor de cadeia. DB2 para z/SO aceita uma cadeia de 8 bytes. DB2 para i aceita uma cadeia de 10 byte. DB2 para Linux ou UNIX aceita uma cadeia de 8 bytes. DB2 para Windows aceita uma cadeia de 30 byte.
palavra-passe | Obrigatório. Aceita um valor de cadeia.
gateway | Obrigatório. Aceita um valor do item de lista, que representa o gateway de dados no local definido às aplicações de lógica dentro do grupo de armazenamento.  

## <a name="create-the-on-premises-gateway-connection"></a>Criar no local de ligação do gateway
Este conector pode aceder a uma base de dados no local DB2 utilização do gateway no local. Consulte os tópicos de gateway para obter mais informações. 

1. No painel de configuração de **Gateways** , selecione a **caixa de verificação** para ativar a **ligar através do gateway**. Repare que as definições de alterar a partir da nuvem para no local.
2. Escreva o valor de **servidor**, sob a forma de número de porta de dois pontos endereço ou alias. Por exemplo, escreva `ibmserver01:50000`.
3. Escreva o valor para **base de dados**. Por exemplo, escreva `nwind`.
4. Selecione o valor para **autenticação**. Por exemplo, selecione **básica**.
5. Escreva o valor para **nome de utilizador**. Por exemplo, escreva `db2admin`.
6. Escreva o valor de **palavra-passe**. Por exemplo, escreva `Password1`.
7. Selecione o valor para o **Gateway**. Por exemplo, selecione **datagateway01**.
8. Selecione **Criar** para continuar. 

    ![](./media/connectors-create-api-db2/Db2connectorOnPremisesDataGatewayConnection.png)

## <a name="create-the-cloud-connection"></a>Criar a ligação à nuvem
Este conector pode aceder a uma base de dados DB2 de nuvem. 

1. No painel de configuração de **Gateways** , deixe a **caixa de verificação** desativada (não clicadas) **ligar através do gateway**. 
2. Escreva o valor para **nome da ligação**. Por exemplo, escreva `hisdemo2`.
3. Escreva o valor para **nome de servidor DB2**, sob a forma de número de porta de dois pontos endereço ou alias. Por exemplo, escreva `hisdemo2.cloudapp.net:50000`.
3. Escreva o valor para **nome de base de dados DB2**. Por exemplo, escreva `nwind`.
4. Escreva o valor para **nome de utilizador**. Por exemplo, escreva `db2admin`.
5. Escreva o valor de **palavra-passe**. Por exemplo, escreva `Password1`.
6. Selecione **Criar** para continuar. 

    ![](./media/connectors-create-api-db2/Db2connectorCloudConnection.png)

## <a name="fetch-all-rows-using-select"></a>Obter todas as linhas, SELECIONE a utilizar
Pode definir uma ação de aplicação de lógica para obter todas as linhas numa tabela DB2. Isto indica a conexão para processar uma instrução DB2 SELECIONE, tais como `SELECT * FROM AREA`.

### <a name="create-a-logic-app"></a>Criar uma aplicação de lógica
1.  Na **área de iniciar o Azure**, selecione **+** (sinal de adição), **Web + Mobile**e, em seguida, **Lógica de aplicação**.
2.  Introduza o **nome**, tais como `Db2getRows`, **subscrição**, **grupo de recursos**, **localização**e **Plano de serviço de aplicação**. Selecione **Afixar ao dashboard**e, em seguida, selecione **Criar**.

### <a name="add-a-trigger-and-action"></a>Adicione um acionador e uma ação
1.  No **Estruturador de fluxos de lógica de aplicações**, selecione **LogicApp em branco** na lista de **modelos** .
2.  Na lista de **Accionadores** , selecione **Periodicidade**. 
3.  No accionador **Periodicidade** , selecione **Editar**, selecione a **frequência** para baixo para selecionar o **dia**e, em seguida, selecione o **intervalo** para escrever **7**. 
4.  Selecione a caixa **+ novo passo** e, em seguida, selecione **Adicionar uma ação**.
5.  Na lista de **ações** , escreva `db2` na **pesquisa de mais ações** caixa editar e, em seguida, selecione **DB2 - obter linhas (pré-visualização)**.
6. Na ação **obter linhas (pré-visualização)** , selecione a **ligação alterar**.
7. No painel de configuração de **ligações** , selecione **Criar novo**. 

    ![](./media/connectors-create-api-db2/Db2connectorNewConnection.png)
  
8. No painel de configuração de **Gateways** , deixe a **caixa de verificação** desativada (não clicadas) **ligar através do gateway**.
    - Escreva o valor para **nome da ligação**. Por exemplo, escreva `HISDEMO2`.
    - Escreva o valor para **nome de servidor DB2**, sob a forma de número de porta de dois pontos endereço ou alias. Por exemplo, escreva `HISDEMO2.cloudapp.net:50000`.
    - Escreva o valor para **nome de base de dados DB2**. Por exemplo, escreva `NWIND`.
    - Escreva o valor para **nome de utilizador**. Por exemplo, escreva `db2admin`.
    - Escreva o valor de **palavra-passe**. Por exemplo, escreva `Password1`.
9. Selecione **Criar** para continuar.

    ![](./media/connectors-create-api-db2/Db2connectorCloudConnection.png)

10. Na lista **nome da tabela** , selecione a **seta para baixo**e, em seguida, selecione **área**.
11. Em alternativa, selecione **Mostrar opções avançadas** para especificar as opções de consulta.
12. Selecione **Guardar**. 

    ![](./media/connectors-create-api-db2/Db2connectorGetRowsTableName.png)

13. Na pá **Db2getRows** , dentro da lista de **todos os é executado** em **Resumo**, selecione o item listado primeiro (Use mais recente).
14. Na pá **aplicação lógica executar** , selecione **Executar detalhes**. Dentro da lista de **ação** , selecione **Get_rows**. Consulte o artigo o valor de **Estado**, que devem ser **bem sucedida**. Selecione a **ligação de entradas** para ver as entradas. Selecionar a **ligação de resultados**e visualizar saídas; qual deve incluir uma lista de linhas.

    ![](./media/connectors-create-api-db2/Db2connectorGetRowsOutputs.png)

## <a name="add-one-row-using-insert"></a>Adicionar uma linha utilizar Inserir
Pode definir uma ação de aplicação de lógica para adicionar uma linha numa tabela DB2. Esta ação instrui a conexão para processar uma declaração inserir DB2, tais como `INSERT INTO AREA (AREAID, AREADESC, REGIONID) VALUES ('99999', 'Area 99999', 102)`.

### <a name="create-a-logic-app"></a>Criar uma aplicação de lógica
1.  Na **área de iniciar o Azure**, selecione **+** (sinal de adição), **Web + Mobile**e, em seguida, **Lógica de aplicação**.
2.  Introduza o **nome**, tais como `Db2insertRow`, **subscrição**, **grupo de recursos**, **localização**e **Plano de serviço de aplicação**. Selecione **Afixar ao dashboard**e, em seguida, selecione **Criar**.

### <a name="add-a-trigger-and-action"></a>Adicione um acionador e uma ação
1.  No **Estruturador de fluxos de lógica de aplicações**, selecione **LogicApp em branco** na lista de **modelos** .
2.  Na lista de **Accionadores** , selecione **Periodicidade**. 
3.  No accionador **Periodicidade** , selecione **Editar**, selecione a **frequência** para baixo para selecionar o **dia**e, em seguida, selecione o **intervalo** para escrever **7**. 
4.  Selecione a caixa **+ novo passo** e, em seguida, selecione **Adicionar uma ação**.
5.  Na lista de **ações** , escreva **db2** na caixa editar **Procurar mais ações** e, em seguida, selecione **DB2 - Inserir linha (pré-visualização)**.
6. Na ação **obter linhas (pré-visualização)** , selecione a **ligação alterar**. 
7. No painel de configuração de **ligações** , selecione uma ligação. Por exemplo, selecione **hisdemo2**.

    ![](./media/connectors-create-api-db2/Db2connectorChangeConnection.png)

8. Na lista **nome da tabela** , selecione a **seta para baixo**e, em seguida, selecione **área**.
9. Introduza valores para todos necessárias colunas (consulte o asterisco vermelho). Por exemplo, escreva `99999` **AREAID**, escreva `Area 99999`e o tipo de `102` para **REGIONID**. 
10. Selecione **Guardar**.

    ![](./media/connectors-create-api-db2/Db2connectorInsertRowValues.png)
 
11. Na pá **Db2insertRow** , dentro da lista de **todos os é executado** em **Resumo**, selecione o item listado primeiro (Use mais recente).
12. Na pá **aplicação lógica executar** , selecione **Executar detalhes**. Dentro da lista de **ação** , selecione **Get_rows**. Consulte o artigo o valor de **Estado**, que devem ser **bem sucedida**. Selecione a **ligação de entradas** para ver as entradas. Selecionar a **ligação de resultados**e visualizar saídas; qual deve incluir a nova linha.

    ![](./media/connectors-create-api-db2/Db2connectorInsertRowOutputs.png)

## <a name="fetch-one-row-using-select"></a>Obter uma linha, SELECIONE a utilizar
Pode definir uma ação de aplicação de lógica para obter uma linha numa tabela DB2. Esta ação instrui a conexão para processar uma instrução DB2 SELECIONE onde, tais como `SELECT FROM AREA WHERE AREAID = '99999'`.

### <a name="create-a-logic-app"></a>Criar uma aplicação de lógica
1.  Na **área de iniciar o Azure**, selecione **+** (sinal de adição), **Web + Mobile**e, em seguida, **Lógica de aplicação**.
2.  Introduza o **nome** (por exemplo, "**Db2getRow**"), **subscrição**, **grupo de recursos**, **localização**e **Plano de serviço de aplicação**. Selecione **Afixar ao dashboard**e, em seguida, selecione **Criar**.

### <a name="add-a-trigger-and-action"></a>Adicione um acionador e uma ação
1.  No **Estruturador de fluxos de lógica de aplicações**, selecione **LogicApp em branco** na lista de **modelos** . 
2.  Na lista de **Accionadores** , selecione **Periodicidade**. 
3.  No accionador **Periodicidade** , selecione **Editar**, selecione a **frequência** para baixo para selecionar o **dia**e, em seguida, selecione o **intervalo** para escrever **7**. 
4.  Selecione a caixa **+ novo passo** e, em seguida, selecione **Adicionar uma ação**.
5.  Na lista de **ações** , escreva **db2** na caixa editar **Procurar mais ações** e, em seguida, selecione **DB2 - obter linhas (pré-visualização)**.
6. Na ação **obter linhas (pré-visualização)** , selecione a **ligação alterar**. 
7. No painel de configurações de **ligações** , selecione uma ligação existente. Por exemplo, selecione **hisdemo2**.

    ![](./media/connectors-create-api-db2/Db2connectorChangeConnection.png)

8. Na lista **nome da tabela** , selecione a **seta para baixo**e, em seguida, selecione **área**.
9. Introduza valores para todos necessárias colunas (consulte o asterisco vermelho). Por exemplo, escreva `99999` para **AREAID**. 
10. Em alternativa, selecione **Mostrar opções avançadas** para especificar as opções de consulta.
11. Selecione **Guardar**. 

    ![](./media/connectors-create-api-db2/Db2connectorGetRowValues.png)

12. Na pá **Db2getRow** , dentro da lista de **todos os é executado** em **Resumo**, selecione o item listado primeiro (Use mais recente).
13. Na pá **aplicação lógica executar** , selecione **Executar detalhes**. Dentro da lista de **ação** , selecione **Get_rows**. Consulte o artigo o valor de **Estado**, que devem ser **bem sucedida**. Selecione a **ligação de entradas** para ver as entradas. Selecionar a **ligação de resultados**e visualizar saídas; qual deve incluir linha.

    ![](./media/connectors-create-api-db2/Db2connectorGetRowOutputs.png)

## <a name="change-one-row-using-update"></a>Alterar uma linha através da ATUALIZAÇÃO
Pode definir uma ação de aplicação de lógica para alterar uma linha numa tabela DB2. Esta ação instrui a conexão para processar uma instrução DB2 UPDATE, tais como `UPDATE AREA SET AREAID = '99999', AREADESC = 'Area 99999', REGIONID = 102)`.

### <a name="create-a-logic-app"></a>Criar uma aplicação de lógica
1.  Na **área de iniciar o Azure**, selecione **+** (sinal de adição), **Web + Mobile**e, em seguida, **Lógica de aplicação**.
2.  Introduza o **nome**, tais como `Db2updateRow`, **subscrição**, **grupo de recursos**, **localização**e **Plano de serviço de aplicação**. Selecione **Afixar ao dashboard**e, em seguida, selecione **Criar**.

### <a name="add-a-trigger-and-action"></a>Adicione um acionador e uma ação
1.  No **Estruturador de fluxos de lógica de aplicações**, selecione **LogicApp em branco** na lista de **modelos** .
2.  Na lista de **Accionadores** , selecione **Periodicidade**. 
3.  No accionador **Periodicidade** , selecione **Editar**, selecione a **frequência** para baixo para selecionar o **dia**e, em seguida, selecione o **intervalo** para escrever **7**. 
4.  Selecione a caixa **+ novo passo** e, em seguida, selecione **Adicionar uma ação**.
5.  Na lista de **ações** , escreva **db2** na caixa editar **Procurar mais ações** e, em seguida, selecione **DB2 - linha de atualização (pré-visualização)**.
6. Na ação **obter linhas (pré-visualização)** , selecione a **ligação alterar**. 
7. No painel de configurações de **ligações** , selecione para selecionar uma ligação existente. Por exemplo, selecione **hisdemo2**.

    ![](./media/connectors-create-api-db2/Db2connectorChangeConnection.png)

8. Na lista **nome da tabela** , selecione a **seta para baixo**e, em seguida, selecione **área**.
9. Introduza valores para todos necessárias colunas (consulte o asterisco vermelho). Por exemplo, escreva `99999` **AREAID**, escreva `Updated 99999`e o tipo de `102` para **REGIONID**. 
10. Selecione **Guardar**. 

    ![](./media/connectors-create-api-db2/Db2connectorUpdateRowValues.png)

11. Na pá **Db2updateRow** , dentro da lista de **todos os é executado** em **Resumo**, selecione o item listado primeiro (Use mais recente).
12. Na pá **aplicação lógica executar** , selecione **Executar detalhes**. Dentro da lista de **ação** , selecione **Get_rows**. Consulte o artigo o valor de **Estado**, que devem ser **bem sucedida**. Selecione a **ligação de entradas** para ver as entradas. Selecionar a **ligação de resultados**e visualizar saídas; qual deve incluir a nova linha.

    ![](./media/connectors-create-api-db2/Db2connectorUpdateRowOutputs.png)

## <a name="remove-one-row-using-delete"></a>Remover uma linha ao utilizar eliminar
Pode definir uma ação de aplicação de lógica para remover uma linha numa tabela DB2. Esta ação instrui a conexão para processar uma instrução DB2 eliminar, tais como `DELETE FROM AREA WHERE AREAID = '99999'`.

### <a name="create-a-logic-app"></a>Criar uma aplicação de lógica
1.  Na **área de iniciar o Azure**, selecione **+** (sinal de adição), **Web + Mobile**e, em seguida, **Lógica de aplicação**.
2.  Introduza o **nome**, tais como `Db2deleteRow`, **subscrição**, **grupo de recursos**, **localização**e **Plano de serviço de aplicação**. Selecione **Afixar ao dashboard**e, em seguida, selecione **Criar**.

### <a name="add-a-trigger-and-action"></a>Adicione um acionador e uma ação
1.  No **Estruturador de fluxos de lógica de aplicações**, selecione **LogicApp em branco** na lista de **modelos** . 
2.  Na lista de **Accionadores** , selecione **Periodicidade**. 
3.  No accionador **Periodicidade** , selecione **Editar**, selecione a **frequência** para baixo para selecionar o **dia**e, em seguida, selecione o **intervalo** para escrever **7**. 
4.  Selecione a caixa **+ novo passo** e, em seguida, selecione **Adicionar uma ação**.
5.  Na lista de **ações** , selecione **db2** na caixa editar **Procurar mais ações** e, em seguida, selecione **DB2 - Eliminar linha (pré-visualização)**.
6. Na ação **obter linhas (pré-visualização)** , selecione a **ligação alterar**. 
7. No painel de configurações de **ligações** , selecione uma ligação existente. Por exemplo, selecione **hisdemo2**.

    ![](./media/connectors-create-api-db2/Db2connectorChangeConnection.png)

8. Na lista **nome da tabela** , selecione a **seta para baixo**e, em seguida, selecione **área**.
9. Introduza valores para todos necessárias colunas (consulte o asterisco vermelho). Por exemplo, escreva `99999` para **AREAID**. 
10. Selecione **Guardar**. 

    ![](./media/connectors-create-api-db2/Db2connectorDeleteRowValues.png)

11. Na pá **Db2deleteRow** , dentro da lista de **todos os é executado** em **Resumo**, selecione o item listado primeiro (Use mais recente).
12. Na pá **aplicação lógica executar** , selecione **Executar detalhes**. Dentro da lista de **ação** , selecione **Get_rows**. Consulte o artigo o valor de **Estado**, que devem ser **bem sucedida**. Selecione a **ligação de entradas** para ver as entradas. Selecionar a **ligação de resultados**e visualizar saídas; qual deve incluir a linha eliminada.

    ![](./media/connectors-create-api-db2/Db2connectorDeleteRowOutputs.png)

## <a name="technical-details"></a>Detalhes técnicos

## <a name="actions"></a>Ações
Uma ação é uma operação realizada pelo fluxo de trabalho definido numa aplicação de lógica. O conector de base de dados DB2 inclui as seguintes ações. 

|Ação|Descrição|
|--- | ---|
|[GetRow](connectors-create-api-db2.md#get-row)|Obtém uma única linha a partir de uma tabela de DB2|
|[GetRows](connectors-create-api-db2.md#get-rows)|Obtém linhas de uma tabela de DB2|
|[InsertRow](connectors-create-api-db2.md#insert-row)|Insere uma nova linha numa tabela DB2|
|[Eliminarlinha](connectors-create-api-db2.md#delete-row)|Elimina uma linha de uma tabela de DB2|
|[GetTables](connectors-create-api-db2.md#get-tables)|Obtém tabelas a partir de uma base de dados DB2|
|[UpdateRow](connectors-create-api-db2.md#update-row)|Atualiza uma linha numa tabela DB2 existente|

### <a name="action-details"></a>Detalhes de ação

Nesta secção, consulte o artigo os detalhes específicos sobre cada ação, incluindo as propriedades de entrada opcionais ou obrigatórias e qualquer associados com o conector de saída correspondente.

#### <a name="get-row"></a>Obter linha 
Devolve uma única linha de uma tabela de DB2.  

| Nome da propriedade| Nome a apresentar |Descrição|
| ---|---|---|
|tabela * | Nome da tabela |Nome da tabela DB2|
|ID de * | Id de linha |Identificador exclusivo da linha para obter|

Um asterisco (*) significa que a propriedade é necessária.

##### <a name="output-details"></a>Detalhes de saída
Item

| Nome da propriedade | Tipo de dados |
|---|---|
|ItemInternalId|cadeia|


#### <a name="get-rows"></a>Obter linhas 
Obtém linhas de uma tabela de DB2.  

|Nome da propriedade| Nome a apresentar|Descrição|
| ---|---|---|
|tabela *|Nome da tabela|Nome da tabela DB2|
|$skip|Contagem de ignorar|Número de entradas para ignorar (predefinição = 0)|
|$top|Contagem de obter máximo|Número máximo de entradas para obter (predefinição = 256)|
|$filter|Consulta de filtro|Uma consulta de filtro ODATA para restringir o número de entradas|
|$orderby|Order By|Uma consulta de OrdenarPor ODATA para especificar a ordem das entradas|

Um asterisco (*) significa que a propriedade é necessária.

##### <a name="output-details"></a>Detalhes de saída
ItemsList

| Nome da propriedade | Tipo de dados |
|---|---|
|valor|matriz|


#### <a name="insert-row"></a>Inserir linha 
Insere uma nova linha numa tabela DB2.  

|Nome da propriedade| Nome a apresentar|Descrição|
| ---|---|---|
|tabela *|Nome da tabela|Nome da tabela DB2|
|item *|Linha|Linha para inserir na tabela especificada na DB2|

Um asterisco (*) significa que a propriedade é necessária.

##### <a name="output-details"></a>Detalhes de saída
Item

| Nome da propriedade | Tipo de dados |
|---|---|
|ItemInternalId|cadeia|


#### <a name="delete-row"></a>Eliminar linha 
Elimina uma linha de uma tabela de DB2.  

|Nome da propriedade| Nome a apresentar|Descrição|
| ---|---|---|
|tabela *|Nome da tabela|Nome da tabela DB2|
|ID de *|Id de linha|Identificador exclusivo da linha para eliminar|

Um asterisco (*) significa que a propriedade é necessária.

##### <a name="output-details"></a>Detalhes de saída
Nenhum.

#### <a name="get-tables"></a>Obter tabelas 
Obtém tabelas a partir de uma base de dados DB2.  

Não existem parâmetros para esta chamada. 

##### <a name="output-details"></a>Detalhes de saída 
TablesList

| Nome da propriedade | Tipo de dados |
|---|---|
|valor|matriz|

#### <a name="update-row"></a>Linha de actualização 
Atualiza uma linha numa tabela DB2 existente.  

|Nome da propriedade| Nome a apresentar|Descrição|
| ---|---|---|
|tabela *|Nome da tabela|Nome da tabela DB2|
|ID de *|Id de linha|Identificador exclusivo da linha para atualizar|
|item *|Linha|Linha com valores actualizados|

Um asterisco (*) significa que a propriedade é necessária.

##### <a name="output-details"></a>Detalhes de saída  
Item

| Nome da propriedade | Tipo de dados |
|---|---|
|ItemInternalId|cadeia|


### <a name="http-responses"></a>Respostas HTTP

Quando efetuar chamadas para as ações diferentes, poderá receber determinadas respostas. A tabela seguinte descreve as respostas e as respectivas descrições:  

|Nome|Descrição|
|---|---|
|200|OK|
|202|Aceites|
|400|Pedido incorrecto|
|401|Não autorizado|
|403|Proibido|
|404|Não foi encontrado|
|500|Erro de servidor interno. Ocorreu um erro desconhecido|
|predefinido|A operação falhou.|


## <a name="supported-db2-platforms-and-versions"></a>Suportadas DB2 plataformas e versões
Este conector suporta as seguintes IBM DB2 plataformas e versões, bem como IBM DB2 compatíveis produtos (por exemplo, dashDB IBM Bluemix) que suportam distribuído relacionais da base de dados arquitetura (DRDA) SQL acesso Gestor (SQLAM) versão 10 e 11:

- IBM DB2 para z/SO 11.1
- IBM DB2 para z/SO 10.1
- IBM DB2 para i 7.3
- IBM DB2 para i 7.2
- IBM DB2 para i 7.1
- IBM DB2 para LUW 11
- IBM DB2 para LUW 10.5


## <a name="next-steps"></a>Próximos passos

[Criar uma aplicação de lógica](../app-service-logic/app-service-logic-create-a-logic-app.md). Explore as outras conexões disponíveis nas aplicações de lógica na nossa [lista APIs](apis-list.md).

