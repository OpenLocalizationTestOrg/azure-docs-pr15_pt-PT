<properties
   pageTitle="Utilizar a análise da cadeia Azure com armazém de dados SQL | Microsoft Azure"
   description="Sugestões para utilizar a análise da cadeia de Azure com armazém de dados do SQL Azure para desenvolver soluções."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="kevinvngo"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="08/16/2016"
   ms.author="kevin;barbkess;sonyama"/>

# <a name="use-azure-stream-analytics-with-sql-data-warehouse"></a>Utilizar a análise da cadeia Azure com armazém de dados SQL

Azure a análise da cadeia é um serviço totalmente gerido fornecer processamento de baixa latência, altamente disponível, dimensionáveis eventos complexas ao longo de transmissão de dados na nuvem. Pode saber as noções básicas ao ler o artigo [Introdução ao Azure da cadeia Analytics][]. Pode, em seguida, saiba como criar uma solução de ponto a ponto com a análise da cadeia ao seguir o tutorial [começar a utilizar a análise da cadeia de Azure][] .

Este artigo vai aprender a utilizar a base de dados do armazém de dados do SQL Azure como um receptor de saída para as tarefas de análise de vapor.

## <a name="prerequisites"></a>Pré-requisitos

Em primeiro lugar, execute através dos seguintes passos no tutorial [começar a utilizar a análise da cadeia de Azure][] .  

1. Criar uma entrada de concentrador de evento
2. Configurar e iniciar a aplicação criador de evento
3. Aprovisionar uma tarefa de análise da cadeia
4. Especifique a entrada de tarefa e de consulta

Em seguida, crie uma base de dados do armazém de dados do SQL Azure

## <a name="specify-job-output-azure-sql-data-warehouse-database"></a>Especificar o resultado da tarefa: base de dados do armazém de dados SQL Azure

### <a name="step-1"></a>Passo 1

No seu trabalho de análise da cadeia clique em **saída** situado na parte superior da página e, em seguida, clique em **Adicionar saída**.

### <a name="step-2"></a>Passo 2

Selecione a base de dados SQL e clique em seguinte.

![][add-output]

### <a name="step-3"></a>Passo 3
Introduza os seguinte valores na página seguinte:

- *Alias de saída*: introduza um nome amigável para este resultado de tarefa.
- *Subscrição*:
    - Se a base de dados do armazém de dados SQL for na mesma subscrição do que a tarefa de análise da cadeia, selecione a base de dados de SQL de utilização da subscrição atual.
    - Se a base de dados estiver numa subscrição diferente, selecione utilizar base de dados do SQL a partir de outra subscrição.
- *Base de dados*: Especifique o nome de uma base de dados de destino.
- *Nome do servidor*: Especifique o nome do servidor da base de dados que especificou apenas. Pode utilizar o Portal clássica Azure para localizar isto.

![][server-name]

- *Nome de utilizador*: Especifique o nome de utilizador de uma conta que possua permissões de escrita para a base de dados.
- *Palavra-passe*: fornecer a palavra-passe para a conta de utilizador especificado.
- *Tabela*: Especifique o nome da tabela de destino na base de dados.

![][add-database]

### <a name="step-4"></a>Passo 4

Clique no botão de verificação para adicionar este resultado de tarefa e para verificar se a análise da cadeia com êxito pode ligar à base de dados.

![][test-connection]

Quando a ligação à base de dados ser bem sucedida, irá ver uma notificação na parte inferior do portal. Pode clicar em Testar ligação na parte inferior para testar a ligação à base de dados.

## <a name="next-steps"></a>Próximos passos

Para obter uma descrição geral da integração, consulte o artigo [Descrição geral da integração do armazém de dados SQL][].

Para obter mais sugestões de desenvolvimento, consulte o artigo [Descrição geral do desenvolvimento armazém de dados SQL][].

<!--Image references-->

[add-output]: ./media/sql-data-warehouse-integrate-azure-stream-analytics/add-output.png
[server-name]: ./media/sql-data-warehouse-integrate-azure-stream-analytics/dw-server-name.png
[add-database]: ./media/sql-data-warehouse-integrate-azure-stream-analytics/add-database.png
[test-connection]: ./media/sql-data-warehouse-integrate-azure-stream-analytics/test-connection.png

<!--Article references-->

[Introdução ao Azure sequência Analytics]: ../stream-analytics/stream-analytics-introduction.md
[Começar a utilizar a análise da cadeia de Azure]: ../stream-analytics/stream-analytics-get-started.md
[Descrição geral do desenvolvimento armazém de dados SQL]:  ./sql-data-warehouse-overview-develop.md
[Descrição geral da integração do armazém de dados SQL]:  ./sql-data-warehouse-overview-integrate.md

<!--MSDN references-->

<!--Other Web references-->
[Azure Stream Analytics documentation]: http://azure.microsoft.com/documentation/services/stream-analytics/
