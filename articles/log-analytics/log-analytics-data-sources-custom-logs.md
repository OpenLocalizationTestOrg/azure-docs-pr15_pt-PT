<properties 
   pageTitle="Registos de personalizadas na análise de registo | Microsoft Azure"
   description="Análise de registo pode recolher eventos de ficheiros de texto em computadores com Windows e Linux.  Este artigo descreve como definir um novo registo personalizado e detalhes de registos criam no repositório de OMS."
   services="log-analytics"
   documentationCenter=""
   authors="bwren"
   manager="jwhit"
   editor="tysonn" />
<tags 
   ms.service="log-analytics"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/18/2016"
   ms.author="bwren" />

# <a name="custom-logs-in-log-analytics"></a>Registos personalizados no registo de análise

A origem de dados personalizada registos no registo de análise permite-lhe recolher eventos de ficheiros de texto em computadores com Windows e Linux. Muitas aplicações registar informações para ficheiros de texto em vez de padrão registo serviços como o registo de eventos do Windows ou do sistema.  Assim que recolhidos, pode analisar cada registo no registo de para campos individuais utilizando a funcionalidade de [Campos personalizados](log-analytics-custom-fields.md) do registo de análise.

![Colecção de registo personalizados](media/log-analytics-data-sources-custom-logs/overview.png)

Os ficheiros de registo ser recolhidos tem de corresponder os seguintes critérios.

- O registo de tem de ter uma única entrada por linha ou utilizar um carimbo de data/hora correspondência de um dos seguintes formatos no início de cada entrada.

    AAAA-MM-DD SS <br>
  M/AAAA HH: MM: SS AM/PM <br>
  Seg DD, aaaa ss
    
- O ficheiro de registo tem não permitir atualizações circulares onde o ficheiro é substituído por novas entradas. 

## <a name="defining-a-custom-log"></a>Definir um registo personalizado

Utilize o seguinte procedimento para definir um ficheiro de registo personalizados.  Desloque-se para o fim deste artigo para obter instruções sobre um exemplo de adicionar um registo personalizado.

### <a name="step-1-open-the-custom-log-wizard"></a>Passo 1. Abrir o Assistente de registo personalizados

O Assistente de registo personalizados é executado no portal do OMS e permite-lhe definir um novo registo personalizado para recolher.

1.  No portal do OMS, aceda a **Definições**.
2.  Clique em **dados** e, em seguida, **registos de personalizada**.
3.  Por predefinição, todas as alterações à configuração automaticamente são enviadas para todos os agentes.  Para Linux agentes, um ficheiro de configuração é enviado para a recolha Fluentd.  Se pretender modificar este ficheiro manualmente em cada agente Linux, em seguida, desmarque a caixa *Aplicar abaixo configuração para minha máquinas Linux*.
4.  Clique em **Add +** para abrir o Assistente de registo personalizados.

### <a name="step-2-upload-and-parse-a-sample-log"></a>Passo 2. Carregar e analisar um exemplo de registo

Comece por ao carregar uma amostra de registo de personalizado.  O assistente irá analisar e apresentar as entradas neste ficheiro para si validar.  Registo a análise utilizará o delimitador que especificar para identificar cada registo.

**Nova linha** é o delimitador predefinido e é utilizado para ficheiros de registo que têm uma única entrada por linha.  Se a linha for iniciado com uma data e hora dos formatos disponíveis, em seguida, pode especificar delimitador **carimbo** que suporta entradas que aparecem em mais de uma linha. 

Se for utilizado um delimitador de data/hora, em seguida, a propriedade TimeGenerated de cada registo armazenado na OMS será preenchida com a data/hora especificada para essa entrada no ficheiro de registo.  Se for utilizado um novo delimitador de linha, em seguida, TimeGenerated é povoada com a data e hora em que o registo de análise recolhidos a entrada. 

>[AZURE.NOTE]Análise de registo trata atualmente a data/hora recolhida a partir de um registo a utilizar o delimitador carimbo como UTC.  Mais cedo vai ser alterado para utilizar o fuso horário no agente do. 
 
1.  Clique em **Procurar** e navegue para um ficheiro de exemplo.  Tenha em atenção que este poderá botão poderá rotulada **Escolher ficheiro** em alguns browsers.
2.  Clique em **seguinte**. 
3.  O Assistente de registo personalizados irá carregar o ficheiro e os registos que identifica da lista.
4.  Altere o delimitador que é utilizado para identificar um novo registo e selecione o delimitador que melhor identifique os registos no seu ficheiro de registo.
5.  Clique em **seguinte**.

### <a name="step-3-add-log-collection-paths"></a>Passo 3. Adicionar registo caminhos de coleções de sites

Tem de definir um ou mais caminhos agente onde-pode localizar o registo personalizado.  Quer pode fornecer um caminho específico e um nome para o ficheiro de registo ou pode especificar um caminho com um carácter universal do nome.  Esta opção suporta as aplicações que cria um novo ficheiro de cada dia ou quando um ficheiro atinge um determinado tamanho.  Também pode fornecer caminhos múltiplos para um ficheiro de registo único.

Por exemplo, uma aplicação poderá criar um ficheiro de registo cada dia com a data incluída no nome do vistos log20100316.txt. Poderá ser um padrão para essa um registo *registo\*. txt* que pretende aplicar a qualquer ficheiro de registo após a aplicação do atribuindo um nome esquema.

A tabela seguinte fornece exemplos de padrões válidos para especificar os ficheiros de registo diferente. 

| Descrição | Caminho |
|:--|:--|
| Todos os ficheiros no *C:\Logs* com extensão. txt agente do Windows | C:\Logs\\\*. txt |
| Todos os ficheiros em *C:\Logs* com um nome começando registo e uma extensão. txt no agente do Windows | C:\Logs\log\*. txt |
| Todos os ficheiros em */var/log/audit* com extensão. txt no Linux agente | /var/log/audit/*.txt |
| Todos os ficheiros em */var/log/audit* com um nome começando registo e uma extensão. txt no Linux agente | /var/log/audit/log\*. txt |
  

1.  Selecione Windows ou Linux para especificar qual o formato de caminho que está a adicionar.
2.  Escreva o caminho e clique na **+** botão.
3.  Repita o processo para qualquer caminhos adicionais.

### <a name="step-4-provide-a-name-and-description-for-the-log"></a>Passo 4. Fornecer um nome e descrição para o registo

O nome que especificou será utilizado para o tipo de registo, tal como descrito acima.  Sempre terminará com _CL para distingui-lo como um registo personalizado.

1.  Escreva um nome para o registo.  O ** \_LC** sufixo é fornecido automaticamente.
2.  Adicione uma **Descrição**de opcional.
3.  Clique em **seguinte** para guardar a definição de registo personalizados.

### <a name="step-5-validate-that-the-custom-logs-are-being-collected"></a>Passo 5. Validar que estão a ser recolhidos os registos personalizados
Poderá demorar até a uma hora para os dados iniciais a partir de um novo registo personalizado que seja apresentado na análise de registo.  Irá iniciar a recolha de entradas a partir de registos que se encontram no caminho do ponto de que definiu no registo personalizado que especificou.  Não manterá as entradas que carregou durante a criação de registo personalizados, mas irá recolher o entradas já existentes nos ficheiros de registo que-localiza.

Assim que a análise de registo é iniciado recolher a partir do registo personalizado, os registos estarão disponíveis com um registo de pesquisa.  Utilize o nome que que lhe atribuiu o registo de personalizado como o **tipo** na sua consulta.

>[AZURE.NOTE] Se a propriedade RawData estiver em falta da procura, terá de fechar e reabrir o seu browser.

### <a name="step-6-parse-the-custom-log-entries"></a>Passo 6. Analisar as entradas de registo personalizados

A entrada de registo de toda será armazenada numa única propriedade denominada **RawData**.  Irá provavelmente pretende separar os diferentes tipos de informações em cada entrada em propriedades individuais armazenados no registo.  O que fazer este utilizando a funcionalidade de [Campos personalizados](log-analytics-custom-fields.md) do registo de análise.

Obter passos detalhados sobre análise a entrada de registo personalizados não são fornecidos aqui.  Consulte a documentação de [Campos personalizados](log-analytics-custom-fields.md) para estas informações.

## <a name="disabling-a-custom-log"></a>Desativar um registo personalizado

Não pode remover uma definição de registo personalizado depois de ter sido foi criada, mas pode desativá-lo ao remover todos os caminhos de coleções de sites.

1.  No portal do OMS, aceda a **Definições**.
2.  Clique em **dados** e, em seguida, **registos de personalizada**.
3.  Junto à definição para desativar o registo personalizado, clique em **Detalhes** .
4.  Remova cada um dos caminhos de coleções de sites para a definição de registo personalizados.


## <a name="data-collection"></a>Recolha de dados

Análise de registo será recolher novas entradas de cada registo personalizado aproximadamente em 5 minutos.  O agente registará o seu lugar cada ficheiro de registo que recolhe a partir de.  Se o agente de ficar offline para um período de tempo, em seguida, a análise de registo irá recolher entradas a partir de onde-última parou, mesmo se essas entradas foram criadas enquanto o agente estava offline.

Todo o conteúdo da entrada de log é escrito uma única propriedade denominada **RawData**.  Pode analisar isto para várias propriedades que podem ser analisadas e pesquisadas separadamente ao definir [Campos personalizados](log-analytics-custom-fields.md) depois de ter criado o registo de personalizado.


## <a name="custom-log-record-properties"></a>Propriedades do registo de registo personalizados

Registos de registo personalizados têm um tipo com o nome de registo que fornece e as propriedades da tabela seguinte.

| Propriedade | Descrição |
|:--|:--|
| TimeGenerated | Data e hora em que o registo foi recolhido pela análise de registo.  Se o registo de utiliza delimitador hora baseada em seguida, este é o tempo recolhido a partir da entrada. |
| SourceSystem  | Tipo de agente que o registo foi recolhido a partir do. <br> Ligar OpsManager – agente do Windows, quer directo ou SCOM <br> Linux – todos os agentes de Linux  |
| RawData             | Texto completo da entrada recolhido. |
| ManagementGroupName | Nome do grupo de gestão de agentes SCOM.  Para os outros agentes, esta é AOI -\<ID da área de trabalho\> |


## <a name="log-searches-with-custom-log-records"></a>Registo de pesquisas com os registos de registo personalizados

Registos de registos personalizados são armazenados no repositório OMS tal como registos a partir de outra origem de dados.  Terão um tipo de correspondência o nome que fornecer quando define o registo, para que possa utilizar a propriedade tipo na sua pesquisa para obter registos recolhidos a partir de um registo específico.

A tabela seguinte fornece exemplos diferentes de pesquisas de registo que obtêm registos a partir de registos personalizados.

| Consulta | Descrição |
|:--|:--|
| Tipo = MyApp_CL | Todos os eventos a partir de um personalizado registo MyApp_CL com nome. |
| Tipo = MyApp_CL Severity_CF = erro | Todos os eventos a partir de um registo personalizado com o nome MyApp_CL com um valor de *erro* num campo personalizado com o nome *Severity_CF*. |




## <a name="sample-walkthrough-of-adding-a-custom-log"></a>Tutorial de exemplo da ação de adicionar um registo personalizado

A secção seguinte explica um exemplo de criação de um registo personalizado.  O registo de exemplo a ser recolhido tem mostrada uma entrada em cada linha começar com uma data e hora e, em seguida, vírgulas delimitado por campos para código, estado e mensagem.  Várias entradas de amostra são apresentadas abaixo.

    2016-03-10 01:34:36 207,Success,Client 05a26a97-272a-4bc9-8f64-269d154b0e39 connected
    2016-03-10 01:33:33 208,Warning,Client ec53d95c-1c88-41ae-8174-92104212de5d disconnected
    2016-03-10 01:35:44 209,Success,Transaction 10d65890-b003-48f8-9cfc-9c74b51189c8 succeeded
    2016-03-10 01:38:22 302,Error,Application could not connect to database
    2016-03-10 01:31:34 303,Error,Application lost connection to database

### <a name="upload-and-parse-a-sample-log"></a>Carregar e analisar um exemplo de registo

Estamos fornecer um dos ficheiros de registo e pode ver os eventos que vai ser recolhê-lo.  Neste caso a nova linha é suficiente delimitador.  Se mostrada uma entrada no registo de poderia feiras Embora várias linhas, em seguida, delimitador carimbo seria necessário ser utilizado.

![Carregar e analisar um exemplo de registo](media/log-analytics-data-sources-custom-logs/delimiter.png)

### <a name="add-log-collection-paths"></a>Adicionar registo caminhos de coleções de sites

Os ficheiros de registo irão estar localizados no *C:\MyApp\Logs*.  Será criado um novo ficheiro de cada dia com um nome que inclua a data no padrão *appYYYYMMDD.log*.  Um padrão suficiente para este registo seria *C:\MyApp\Logs\\\*. log*.

![Caminho de registo de coleções de sites](media/log-analytics-data-sources-custom-logs/collection-path.png)

### <a name="provide-a-name-and-description-for-the-log"></a>Fornecer um nome e descrição para o registo

Utilizamos um nome de *MyApp_CL* e escreva uma **Descrição**.

![Nome do registo](media/log-analytics-data-sources-custom-logs/log-name.png)


### <a name="validate-that-the-custom-logs-are-being-collected"></a>Validar que estão a ser recolhidos os registos personalizados

Utilizamos uma consulta de *tipo = MyApp_CL* para devolver os registos a partir do registo de recolhidos.

![Consulta de registo com sem campos personalizados](media/log-analytics-data-sources-custom-logs/query-01.png)

### <a name="parse-the-custom-log-entries"></a>Analisar as entradas de registo personalizados

Utilizamos campos personalizados para definir o *EventTime*, *código*, *Estado*e campos de *mensagem* e é possível ver a diferença nos registos que são devolvidos pela consulta.

![Consulta de registo com campos personalizados](media/log-analytics-data-sources-custom-logs/query-02.png)

## <a name="next-steps"></a>Próximos passos

- Utilize [Campos personalizados](log-analytics-custom-fields.md) para analisar as entradas no registo de personalizado para os campos individuais.
- Saiba mais sobre [as pesquisas de registo](log-analytics-log-searches.md) analisar os dados recolhidos a partir de origens de dados e soluções. 