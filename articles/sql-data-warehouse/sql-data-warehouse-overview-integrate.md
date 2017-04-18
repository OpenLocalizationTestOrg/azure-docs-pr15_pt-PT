<properties
   pageTitle="Criar soluções integradas com armazém de dados do SQL | Microsoft Azure"
   description="Ferramentas e parceiros com as soluções que se integram no armazém de dados SQL. "
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
   ms.date="05/31/2016"
   ms.author="lodipalm;barbkess;sonyama"/>

#<a name="leverage-other-services-with-sql-data-warehouse"></a>Tirar partido de outros serviços com armazém de dados SQL
Para além da sua funcionalidades principais, armazém de dados SQL permite aos utilizadores tirar partido muitos dos outros serviços no Azure juntamente com-lo.  Especificamente, podemos atualmente reencaminhado passos para fortemente integrar com o seguinte:

+ Power BI
+ Dados Azure fábrica
+ Formação de máquina Azure
+ Análise de sequência Azure

Estamos a trabalhar para se ligar a mais serviços ao longo do Azure ecossistema.

##<a name="power-bi"></a>Power BI
Integração do Power BI permite-lhe tirar partido potência cluster de armazém de dados SQL com a denúncia dinâmicos e de visualização do Power BI. Integração do Power BI atualmente inclui:

+ **Ligar direta**: mais avançadas ligação com propagação lógica contra armazém de dados SQL.  Este procedimento fornece análise mais rápido numa escala maior.
+ **Abrir no Power BI**: botão Abrir no Power BI transmite informações instância para do Power BI, permitindo para uma ligação mais contínua.

Consulte o artigo [integrar com o Power BI](./sql-data-warehouse-integrate-power-bi.md) ou a [documentação do Power BI](http://blogs.msdn.com/b/powerbi/archive/2015/06/24/exploring-azure-sql-data-warehouse-with-power-bi.aspx) para obter mais informações.

##<a name="azure-data-factory"></a>Dados Azure fábrica
Azure fábrica de dados permite aos utilizadores uma plataforma gerida para criar complexas tubagens de carga extrair.  Integração de SQL Data Warehouse com o Azure dados fábrica inclui o seguinte:

+ **Procedimentos armazenados**: orquestrar a execução de procedimentos armazenados no armazém de dados SQL.
+ **Copiar**: utilizar ADF para mover dados para armazém de dados SQL.  Esta operação pode utilizar o dispositivo de movimento de dados padrão do ADF, ou PolyBase nas folhas de rosto. 

Consulte o artigo [integrar com o Azure fábrica de dados](./sql-data-warehouse-integrate-azure-data-factory.md) ou a [fábrica de dados do Azure documentação](https://azure.microsoft.com/documentation/services/data-factory/) para obter mais informações.

##<a name="azure-machine-learning"></a>Formação de máquina Azure
Azure máquina formação é um serviço de análise totalmente geridas que permite aos utilizadores criar modelos de complexas tirar partido de um grande conjunto de ferramentas de aspeto do Office.  Armazém de dados SQL é suportado como uma origem e de destino para estes modelos com as seguintes funcionalidades:

+ **Ler dados:** Unidade de modelos no escala utilizando T-SQL contra armazém de dados SQL.
+ **Escrever dados:** Consolide alterações a partir de qualquer modelo regressar ao armazém de dados SQL.

Consulte o artigo [integrar com o Azure máquina formação](./sql-data-warehouse-integrate-azure-machine-learning.md) ou a [documentação do Azure máquina formação](https://azure.microsoft.com/services/machine-learning/) para obter mais informações.

##<a name="azure-stream-analytics"></a>Análise de sequência Azure
Azure a análise da cadeia é uma infraestrutura complexa, totalmente gerida para processar e utilizado por outros dados do evento gerados a partir do Hub do Azure evento.  Integração com o armazém de dados SQL permite a transmissão dados processados eficazmente e armazenados ao lado dos dados relacionais, permitindo-análise mais aprofundada, mais avançada.  

+ **Saída da tarefa:** Envie a saída de tarefas de análise da cadeia diretamente ao armazém de dados SQL.

Consulte o artigo [integrar com o Azure a análise da cadeia](./sql-data-warehouse-integrate-azure-stream-analytics.md) ou a [documentação do Azure da cadeia Analytics](https://azure.microsoft.com/documentation/services/stream-analytics/) para obter mais informações.

<!--Image references-->

<!--Article references-->
[development overview]: sql-data-warehouse-overview-develop/

[Azure Data Factory]: sql-data-warehouse-integrate-azure-data-factory.md
[Azure Machine Learning]: sql-data-warehouse-integrate-azure-machine-learning.md
[Azure Stream Analytics]: sql-data-warehouse-integrate-azure-stream-analytics.md
[Power BI]: sql-data-warehouse-integrate-power-bi.md
[Partners]: sql-data-warehouse-partner-business-intelligence.md

<!--MSDN references-->

<!--Other Web references-->
