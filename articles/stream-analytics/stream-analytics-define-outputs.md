<properties
    pageTitle="Resultados de análise de sequência: opções de armazenamento, análise | Microsoft Azure"
    description="Saiba mais sobre a filtragem de opções de resultados de dados de análise da cadeia incluindo Power BI para os resultados da análise."
    keywords="transformação de dados, os resultados da análise, opções de armazenamento de dados"
    services="stream-analytics,documentdb,sql-database,event-hubs,service-bus,storage"
    documentationCenter="" 
    authors="jeffstokes72"
    manager="jhubbard" 
    editor="cgronlun"/>

<tags
    ms.service="stream-analytics"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="data-services"
    ms.date="09/26/2016"
    ms.author="jeffstok"/>

# <a name="stream-analytics-outputs-options-for-storage-analysis"></a>Resultados de análise de sequência: opções de armazenamento, análise

Durante a criação de uma tarefa de análise da cadeia, considere a forma como os dados resultantes serão consumidos. Como irá ver os resultados da tarefa a análise da cadeia de e onde serão armazená-lo?

Para poder ativar uma variedade de padrões de aplicação, Azure da cadeia Analytics tem opções diferentes para armazenar a saída e visualizar os resultados da análise. Isto torna mais fácil ver resultados de tarefa e fornece-lhe flexibilidade na consumo e armazenamento do resultado da tarefa de armazenamento de dados e outros fins. Qualquer saída configurada na tarefa tem de existir antes da tarefa é iniciada e eventos de iniciar a fluir. Por exemplo, se utilizar o armazenamento de BLOBs como uma saída, a tarefa não criará uma conta de armazenamento automaticamente. Necessita de ser criados pelo utilizador antes da tarefa de ASA é iniciada.

## <a name="azure-data-lake-store"></a>Arquivo de Lake de dados do Azure

Análise da cadeia suporta [Azure dados Lake loja](https://azure.microsoft.com/services/data-lake-store/). Este armazenamento permite-lhe armazenar dados de qualquer tamanho, tipo e ingestão velocidade para análise operacional e exploratória. Neste momento, a criação e de configuração de arquivo de Lake dados saídas é suportado apenas no Portal de clássica do Azure. Além disso, a análise da cadeia tem de ser autorizados a aceder ao arquivo de Lake de dados. Para obter detalhes sobre autorização e como inscrever-se para a pré-visualização do dados Lake loja (caso seja necessário) são abordados no [artigo de saída Lake de dados](stream-analytics-data-lake-output.md).

### <a name="authorize-an-azure-data-lake-store"></a>Autorize uma loja de Lake de dados do Azure

Quando o armazenamento de Lake de dados está seleccionado como uma saída no portal de gestão do Azure, vai ser-lhe para autorizar uma ligação para um arquivo de Lake de dados existente.  

![Autorize arquivo de dados de Lake](./media/stream-analytics-define-outputs/06-stream-analytics-define-outputs.png)  

Em seguida, preencha as propriedades para o resultado de arquivo de Lake dados conforme visto abaixo:

![Autorize arquivo de dados de Lake](./media/stream-analytics-define-outputs/07-stream-analytics-define-outputs.png)  

A tabela abaixo apresenta os nomes das propriedades e a respectiva descrição necessárias para criar uma saída de arquivo de Lake de dados.

<table>
<tbody>
<tr>
<td><B>NOME DA PROPRIEDADE</B></td>
<td><B>DESCRIÇÃO</B></td>
</tr>
<tr>
<td>Alias de saída</td>
<td>Este é um nome amigável utilizado nas consultas para direcionar o resultado da consulta para este Lake arquivo de dados.</td>
</tr>
<tr>
<td>Nome da conta</td>
<td>O nome da conta de armazenamento de Lake dados onde vai enviar a saída. Será apresentada uma lista de contas de arquivo de Lake dados à qual o utilizador com sessão iniciada portal do tem acesso a lista pendente.</td>
</tr>
<tr>
<td>Caminho do prefixo com padrão [<I>opcional</I>]</td>
<td>O caminho do ficheiro utilizado para escrever os seus ficheiros na conta de arquivo de dados Lake especificado. <BR>{date}, {tempo}<BR>Exemplo 1: pasta1/registos / {date} / {tempo}<BR>Exemplo 2: pasta1/registos / {date}</td>
</tr>
<tr>
<td>Formato de data [<I>opcionais</I>]</td>
<td>Se o token de data é utilizado no caminho do prefixo, pode selecionar o formato de data na qual os seus ficheiros estão organizados. Exemplo: Aaaa/MM/DD</td>
</tr>
<tr>
<td>Formato de hora [<I>opcional</I>]</td>
<td>Se for utilizado o token de tempo no caminho do prefixo, especifique o formato da hora em que os seus ficheiros estão organizados. Atualmente, o valor de suportado apenas é HH.</td>
</tr>
<tr>
<td>Formato de serialização de evento</td>
<td>Formato de serialização para dados de saída. JSON, CSV e Avro são suportadas.</td>
</tr>
<tr>
<td>Codificação</td>
<td>Se em formato CSV ou JSON, uma codificação tem de ser especificada. UTF-8 é o formato de codificação suportado apenas neste momento.</td>
</tr>
<tr>
<td>Delimitador</td>
<td>Aplicável apenas para serialização CSV. Análise da cadeia suporta um número de delimitadores comuns para serializar os dados CSV. Valores suportados são vírgula, ponto e vírgula, espaço, separador e barra vertical.</td>
</tr>
<tr>
<td>Formatar</td>
<td>Aplicável apenas para serialização JSON. Linha separada Especifica que será formatada a saída fazendo com que cada objeto JSON separado por uma nova linha. Matriz Especifica que será formatado o resultado como uma matriz de objetos JSON.</td>
</tr>
</tbody>
</table>

### <a name="renew-data-lake-store-authorization"></a>Renovar a autorização de arquivo de Lake de dados

Terá de autenticar novamente a sua conta do arquivo de Lake dados se a palavra-passe foi alterado desde que a tarefa foi criada ou última autenticada.

![Autorize arquivo de dados de Lake](./media/stream-analytics-define-outputs/08-stream-analytics-define-outputs.png)  


## <a name="sql-database"></a>Base de dados SQL

[Base de dados do SQL Azure](https://azure.microsoft.com/services/sql-database/) pode ser utilizado como uma saída para dados que estão relacionais natureza ou para as aplicações que dependem de conteúdo a ser alojado numa base de dados relacional. Tarefas de análise da cadeia gravará a uma tabela existente numa base de dados SQL Azure.  Tenha em atenção que o esquema da tabela deve corresponder exatamente aos campos e os tipos de que está a ser saída da sua tarefa. Também pode ser especificado um [Armazém de dados do SQL Azure](https://azure.microsoft.com/documentation/services/sql-data-warehouse/) como uma saída através da base de dados SQL saída opção também (isto é uma funcionalidade de pré-visualização). A tabela abaixo apresenta os nomes das propriedades e a respectiva descrição para a criação de uma base de dados SQL saída.

| Nome da propriedade | Descrição |
|---------------|-------------|
| Alias de saída | Este é um nome amigável utilizado nas consultas para direcionar o resultado da consulta para esta base de dados. |
| Base de dados | O nome da base de dados onde vai enviar a saída |
| Nome do servidor | O nome do servidor de base de dados SQL |
| Nome de utilizador | O nome de utilizador que tem acesso ao escrever à base de dados |
| Palavra-passe | A palavra-passe para ligar à base de dados |
| Tabela | O nome da tabela onde o resultado será escrito. O nome da tabela é sensível a maiúsculas e o esquema desta tabela deve corresponder exatamente para o número de campos e os tipos de que está a ser gerados pelo seu resultado da tarefa. |

> [AZURE.NOTE] Atualmente a oferta de base de dados do Azure SQL é suportada para uma tarefa de saída em sequência Analytics. No entanto, uma máquina de Virtual Azure com o SQL Server com uma base de dados anexado não é suportada. Este é o sujeito a alterações em versões futuras.

## <a name="blob-storage"></a>Armazenamento de BLOBs

Armazenamento de BLOBs oferece uma solução rentável e dimensionável para armazenar grandes quantidades de dados não estruturados na nuvem.  Para obter uma introdução sobre o armazenamento de Blobs do Azure e a sua utilização, consulte a documentação de [como utilizar Blobs](../storage/storage-dotnet-how-to-use-blobs.md).

A tabela abaixo apresenta os nomes das propriedades e a respectiva descrição para a criação de uma saída blob.

<table>
<tbody>
<tr>
<td>NOME DA PROPRIEDADE</td>
<td>DESCRIÇÃO</td>
</tr>
<tr>
<td>Alias de saída</td>
<td>Este é um nome amigável utilizado nas consultas para direcionar o resultado da consulta ao armazenamento blob do.</td>
</tr>
<tr>
<td>Conta de armazenamento</td>
<td>O nome da conta de armazenamento onde vai enviar a saída.</td>
</tr>
<tr>
<td>Chave de conta de armazenamento</td>
<td>A chave secreta associada à conta de armazenamento.</td>
</tr>
<tr>
<td>Contentor de armazenamento</td>
<td>Contentores fornecem um agrupamento lógico para blobs armazenados no serviço de Blobs do Microsoft Azure. Quando carregar um blob para o serviço de BLOBs, tem de especificar um contentor para esse blob.</td>
</tr>
<tr>
<td>Caminho do prefixo com padrão [opcional]</td>
<td>O caminho do ficheiro utilizado para escrever o seu blobs dentro do contentor especificado.<BR>Dentro do caminho, pode optar por utilizar um ou mais ocorrências de 2 variáveis que se seguem para especificar a frequência escritas blobs:<BR>{date}, {tempo}<BR>Exemplo 1: cluster1/registos / {date} / {tempo}<BR>Exemplo 2: cluster1/registos / {date}</td>
</tr>
<tr>
<td>Formato de data [opcional]</td>
<td>Se o token de data é utilizado no caminho do prefixo, pode selecionar o formato de data na qual os seus ficheiros estão organizados. Exemplo: Aaaa/MM/DD</td>
</tr>
<tr>
<td>Formato de hora [opcional]</td>
<td>Se for utilizado o token de tempo no caminho do prefixo, especifique o formato da hora em que os seus ficheiros estão organizados. Atualmente, o valor de suportado apenas é HH.</td>
</tr>
<tr>
<td>Formato de serialização de evento</td>
<td>Formato de serialização para dados de saída.  JSON, CSV e Avro são suportadas.</td>
</tr>
<tr>
<td>Codificação</td>
<td>Se em formato CSV ou JSON, uma codificação tem de ser especificada. UTF-8 é o formato de codificação suportado apenas neste momento.</td>
</tr>
<tr>
<td>Delimitador</td>
<td>Aplicável apenas para serialização CSV. Análise da cadeia suporta um número de delimitadores comuns para serializar os dados CSV. Valores suportados são vírgula, ponto e vírgula, espaço, separador e barra vertical.</td>
</tr>
<tr>
<td>Formatar</td>
<td>Aplicável apenas para serialização JSON. Linha separada Especifica que será formatada a saída fazendo com que cada objeto JSON separado por uma nova linha. Matriz Especifica que será formatado o resultado como uma matriz de objetos JSON.</td>
</tr>
</tbody>
</table>

## <a name="event-hub"></a>Concentrador de evento

[Evento concentradores](https://azure.microsoft.com/services/event-hubs/) é altamente dimensionáveis publicar-subscrever ingestor do evento. -Pode recolher milhões de eventos por segundo.  Uma utilização de um concentrador de evento como saída é quando o resultado de uma tarefa de análise da cadeia será a entrada de tarefa de transmissão de outra.

Existem alguns parâmetros que são necessários para configurar sequências de dados do evento concentrador como um resultado.

| Nome da propriedade | Descrição |
|---------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Alias de saída | Este é um nome amigável utilizado nas consultas para direcionar o resultado da consulta para este concentrador de evento. |
| Espaço de nomes do serviço Bus | Um espaço de nomes de serviço Bus é um contentor para um conjunto de mensagens entidades. Quando tiver criado um novo concentrador de evento, também criado um espaço de nomes de serviço Bus |
| Concentrador de evento | O nome do seu saída concentrador de evento |
| Nome da política concentrador evento | A política de acesso partilhado, que pode ser criada no separador evento concentrador configurar. Cada política de acesso partilhado irá ter um nome, as permissões que definir e as teclas de acesso |
| Chave de política de concentrador de evento | A chave de Shared Access utilizada para autenticar o acesso ao serviço Bus espaço de nomes |
| Coluna de chave partição [opcional] | Esta coluna contenha a chave de partição saída concentrador de evento. |
| Formato de serialização de evento | Formato de serialização para dados de saída.  JSON, CSV e Avro são suportadas. |
| Codificação | Para CSV e JSON, UTF-8 é o formato de codificação suportado apenas neste momento |
| Delimitador | Aplicável apenas para serialização CSV. Análise da cadeia suporta um número de delimitadores comuns para dados serialização no formato CSV. Valores suportados são vírgula, ponto e vírgula, espaço, separador e barra vertical. |
| Formatar | Aplicável apenas para o tipo JSON. Linha separada Especifica que será formatada a saída fazendo com que cada objeto JSON separado por uma nova linha. Matriz Especifica que será formatado o resultado como uma matriz de objetos JSON. |

## <a name="power-bi"></a>Power BI

[Power BI](https://powerbi.microsoft.com/) podem ser utilizados como uma saída para uma tarefa de análise da cadeia para fornecer para uma experiência de visualização avançada de resultados da análise. Esta funcionalidade pode ser utilizada para dashboards operacionais avançados, criação de relatórios e métrica pelo esforço elaboração de relatórios.

### <a name="authorize-a-power-bi-account"></a>Autorize uma conta do Power BI

1.  Quando está seleccionado do Power BI como uma saída no portal de gestão do Azure, vai ser-lhe para autorizar um utilizador existente do Power BI ou para criar uma nova conta do Power BI.  

    ![Autorize utilizador do Power BI](./media/stream-analytics-define-outputs/01-stream-analytics-define-outputs.png)  

2.  Crie uma nova conta se de que não ainda tiver um, em seguida, clique em autorizar agora.  É apresentado um ecrã de como o seguinte.  

    ![Conta Azure do Power BI](./media/stream-analytics-define-outputs/02-stream-analytics-define-outputs.png)  

3.  Neste passo, fornece a conta escolar ou profissional para a autorização a saída do Power BI. Se o não é já se inscreveu no Power BI, selecione iniciar agora. Pode dever diferente da conta de subscrição Azure que tem sessão iniciada com a conta escolar ou profissional que utiliza para o Power BI.

### <a name="configure-the-power-bi-output-properties"></a>Configurar as propriedades de saída do Power BI

Assim que tiver a conta do Power BI autenticada, pode configurar as propriedades para a saída do Power BI. A tabela abaixo é a lista de nomes de propriedade e a respectiva descrição para configurar a saída do Power BI.

| Nome da propriedade | Descrição |
|---------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Alias de saída | Este é um nome amigável utilizado nas consultas para direcionar o resultado da consulta para este resultado obter. |
| Grupo área de trabalho | Para ativar a partilha de dados com outros utilizadores do Power BI pode selecionar grupos dentro da sua conta do Power BI ou selecione "Meus da área de trabalho", se não pretender escrever a um grupo.  Atualizar um grupo existente requer renovar a autenticação do Power BI. | 
| Nome do conjunto de dados | Forneça um nome de conjunto de dados pretendido para o resultado do Power BI utilizar |
| Nome da tabela | Forneça um nome de tabela em conjunto de dados do resultado do Power BI. Atualmente, saída do Power BI a partir de tarefas de análise da cadeia só pode ter uma tabela num conjunto de dados |

Para uma guia passo a passo de configurar uma saída do Power BI e de dashboards, consulte o artigo [análise da cadeia de Azure e do Power BI](stream-analytics-power-bi-dashboard.md) .

> [AZURE.NOTE] Não explicitamente crie o conjunto de dados e a tabela no dashboard do Power BI. O conjunto de dados e a tabela serão povoados automaticamente quando a tarefa é iniciada e a tarefa é iniciada saída meios Power BI. Tenha em atenção que se a consulta de tarefa não gerar quaisquer resultados, o conjunto de dados e a tabela não serão criadas. Tenha também em atenção de que se Power BI já tinha um conjunto de dados e uma tabela com o mesmo nome como um fornecidas nesta tarefa de análise da cadeia, serão substituídos os dados existentes.

### <a name="renew-power-bi-authorization"></a>Renovar a autorização do Power BI

Terá de autenticar novamente a sua conta do Power BI se a palavra-passe foi alterado desde que a tarefa foi criada ou última autenticada. Se a autenticação Multifator (MFA) é configurado no seu inquilino do Azure Active Directory (AAD) também será necessário para renovar a autorização de Power BI em 2 semanas. Um sintoma deste problema é sem saída de tarefa e um "erro do utilizador de autenticar" nos registos do operação:

  ![Erro de token de atualização do Power BI](./media/stream-analytics-define-outputs/03-stream-analytics-define-outputs.png)  

Para resolver este problema, pare o seu trabalho em execução e aceda ao seu resultado do Power BI.  Clique na ligação "Renovar autorização" e reinicie o seu trabalho desde a última vez parado para evitar a perda de dados.

  ![Power BI renovar autorização](./media/stream-analytics-define-outputs/04-stream-analytics-define-outputs.png)  

## <a name="table-storage"></a>Armazenamento de tabela

[Armazenamento de tabela do Azure](../storage/storage-introduction.md) oferece armazenamento altamente disponível, previstos excederem consideravelmente dimensionável, para que uma aplicação pode dimensionar automaticamente para cumprir o pedido de utilizador. Armazenamento de tabela é arquivo de chave/atributo da Microsoft NoSQL qual pode tirar partido de dados estruturados com menos restrições no esquema. Armazenamento de tabela do Azure pode ser utilizado para armazenar dados para persistente e obtenção eficiente.

A tabela abaixo apresenta os nomes das propriedades e a respectiva descrição para a criação de uma saída de tabela.

| Nome da propriedade | Descrição |
|---------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Alias de saída | Este é um nome amigável utilizado nas consultas para direcionar o resultado da consulta para este armazenamento de tabela. |
| Conta de armazenamento | O nome da conta de armazenamento onde vai enviar a saída. |
| Chave de conta de armazenamento | A tecla de acesso associada à conta de armazenamento. |
| Nome da tabela | O nome da tabela. A tabela irá obter criada se não existir. |
| Tecla de partição | O nome da coluna que contém a chave de partição saída. A chave de partição é um identificador exclusivo para o partição dentro de uma tabela dada que a primeira parte da chave primária de uma entidade de formulários. É um valor de cadeia que pode ser até 1 KB de tamanho. |
| Tecla de linha | O nome da coluna resultado que contém a chave de linha. A chave de linha é um identificador exclusivo para uma entidade dentro de uma determinada partição. Faz a segunda parte da chave primária de uma entidade. A chave de linha é um valor de cadeia que pode ser até 1 KB de tamanho. |
| Tamanho de batch | O número de registos para um lote de operação. Normalmente, a predefinição é suficiente para a maioria das tarefas, referem-se para a [especificação de operação de Batch da tabela](https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.tablebatchoperation.aspx) para obter mais detalhes sobre como modificar esta definição. |

## <a name="service-bus-queues"></a>Serviço Bus filas

[Serviço Bus filas](https://msdn.microsoft.com/library/azure/hh367516.aspx) oferecer um primeiro In, entrega de mensagens do primeiro saída (FIFO) para um ou mais consumidores concorrentes. Normalmente, as mensagens são esperadas para ser recebido e processadas pelos destinatários pela ordem temporal na qual foram adicionadas à fila de espera e cada mensagem é recebida e processada pelos consumidores de apenas uma mensagem.

A tabela abaixo apresenta os nomes das propriedades e a respectiva descrição para a criação de uma saída fila de espera.

| Nome da propriedade | Descrição |
|----------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Alias de saída | Este é um nome amigável utilizado nas consultas para direcionar o resultado da consulta para fila de Bus este serviço. |
| Espaço de nomes do serviço Bus | Um espaço de nomes de serviço Bus é um contentor para um conjunto de mensagens entidades. |
| Nome da fila | O nome da fila de Bus de serviço. |
| Nome da política fila de espera | Quando cria uma fila, também pode criar políticas de acesso partilhado no separador Configurar fila de espera. Cada política de acesso partilhado vai ter um nome, as permissões que definir e as teclas de acesso. |
| Chave de política de fila de espera | A chave de Shared Access utilizada para autenticar o acesso ao serviço Bus espaço de nomes |
| Formato de serialização de evento | Formato de serialização para dados de saída.  JSON, CSV e Avro são suportadas. |
| Codificação | Para CSV e JSON, UTF-8 é o formato de codificação suportado apenas neste momento |
| Delimitador | Aplicável apenas para serialização CSV. Análise da cadeia suporta um número de delimitadores comuns para dados serialização no formato CSV. Valores suportados são vírgula, ponto e vírgula, espaço, separador e barra vertical. |
| Formatar | Aplicável apenas para o tipo JSON. Linha separada Especifica que será formatada a saída fazendo com que cada objeto JSON separado por uma nova linha. Matriz Especifica que será formatado o resultado como uma matriz de objetos JSON. |

## <a name="service-bus-topics"></a>Tópicos de Bus do serviço

Enquanto serviço Bus filas fornecer um método de comunicação de um-para-um partir do remetente para recetor, o [Serviço Bus tópicos](https://msdn.microsoft.com/library/azure/hh367516.aspx) fornecem um formulário um-para-muitos de comunicação.

A tabela abaixo apresenta os nomes das propriedades e a respectiva descrição para a criação de uma saída de tabela.

| Nome da propriedade | Descrição |
|----------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Alias de saída | Este é um nome amigável utilizado nas consultas para direcionar o resultado da consulta para este tópico Bus de serviço. |
| Espaço de nomes do serviço Bus | Um espaço de nomes de serviço Bus é um contentor para um conjunto de mensagens entidades. Quando tiver criado um novo concentrador de evento, também criado um espaço de nomes de serviço Bus |
| Nome do tópico | Tópicos são mensagens entidades, semelhantes às concentradores evento e filas. Está a foi concebidos para recolher sequências de evento a partir de um número de serviços e dispositivos diferentes. Quando é criado um tópico, é igualmente apresentado um nome específico. As mensagens enviadas para um tópico não estará disponíveis a menos que é criada uma subscrição, tanto Certifique-se de que existem um ou mais subscrições no tópico |
| Nome da política de tópico | Quando cria um tópico, também pode criar políticas de acesso partilhado no separador Configurar tópico. Cada política de acesso partilhado irá ter um nome, as permissões que definir e as teclas de acesso |
| Chave de política de tópico | A chave de Shared Access utilizada para autenticar o acesso ao serviço Bus espaço de nomes |
| Formato de serialização de evento | Formato de serialização para dados de saída.  JSON, CSV e Avro são suportadas. |
| Codificação | Se em formato CSV ou JSON, uma codificação tem de ser especificada. Neste momento, o formato de codificação suportado apenas é UTF-8 |
| Delimitador | Aplicável apenas para serialização CSV. Análise da cadeia suporta um número de delimitadores comuns para dados serialização no formato CSV. Valores suportados são vírgula, ponto e vírgula, espaço, separador e barra vertical. |

## <a name="documentdb"></a>DocumentDB

[Azure DocumentDB](https://azure.microsoft.com/services/documentdb/) é um completamente geridas NoSQL documento da base de dados serviço que oferece a consulta e transações ao longo do esquema de royalties dados, o desempenho previsível e fiável e desenvolvimento rápido.

A tabela abaixo apresenta os nomes das propriedades e a respectiva descrição para a criação de uma saída DocumentDB.

<table>
<tbody>
<tr>
<td>NOME DA PROPRIEDADE</td>
<td>DESCRIÇÃO</td>
</tr>
<tr>
<td>Nome da conta</td>
<td>O nome da conta DocumentDB.  Também pode ser o ponto final da conta.</td>
</tr>
<tr>
<td>Chave da conta</td>
<td>A tecla de acesso partilhado para a conta DocumentDB.</td>
</tr>
<tr>
<td>Base de dados</td>
<td>O nome da base de dados de DocumentDB.</td>
</tr>
<tr>
<td>Padrão de nome de coleções de sites</td>
<td>O padrão de nome de coleções de sites para as coleções de ficheiros ser utilizado. O formato de nome de coleções de sites pode ser construído utilizando o token de opcional {partição}, onde a partições iniciar a partir de 0.<BR>Por exemplo As seguintes são entradas válidas:<BR>Conjunto {partição}<BR>Conjunto<BR>Tenha em atenção que coleções de sites tem de existir antes da tarefa de análise da cadeia é iniciada e não será criada automaticamente.</td>
</tr>
<tr>
<td>Tecla de partição</td>
<td>O nome do campo em eventos de saída utilizado para especificar a chave para criar a partições saída ao longo de coleções de sites.</td>
</tr>
<tr>
<td>ID do documento</td>
<td>O nome do campo em eventos de saída utilizado para especificar a chave primária que inserir ou atualizar operações são baseados em.</td>
</tr>
</tbody>
</table>


## <a name="get-help"></a>Obter ajuda
Para obter mais assistência, experimente nosso [Fórum Azure da cadeia Analytics](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Próximos passos
Tenha de foram introduzidas para a análise da cadeia, um serviço gerido de transmissão de análise de dados a partir da Internet de coisas. Para saber mais sobre este serviço, consulte o artigo:

- [Começar a utilizar o Azure da cadeia Analytics](stream-analytics-get-started.md)
- [Escala Azure da cadeia Analytics tarefas](stream-analytics-scale-jobs.md)
- [Azure sequência Analytics referência da linguagem de consulta](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Referência da sequência Azure Analytics gestão REST API](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-get-started.md
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301
