<properties 
    pageTitle="Notas de lançamento de análise da cadeia | Microsoft Azure" 
    description="Notas de lançamento de análise da cadeia" 
    services="stream-analytics" 
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

#<a name="stream-analytics-release-notes"></a>Notas de lançamento de análise da cadeia

## <a name="notes-for-04152016-release-of-stream-analytics"></a>Notas de lançamento 04/15/2016 de análise da cadeia ##

Nesta versão contém a seguinte actualização.

Título | Descrição
---|---
Exporta disponibilidade geral do Power BI  | [Exporta do Power BI](stream-analytics-power-bi-dashboard.md) estão agora disponíveis em geral. A data de expiração de autorização de 90 dias para o Power BI foi removida. Para obter mais informações sobre cenários onde autorização tem de ser renovada consulte a secção [renovar autorização](stream-analytics-power-bi-dashboard.md#Renew-authorization) de criação de um dashboard do Power BI.

## <a name="notes-for-03032016-release-of-stream-analytics"></a>Notas de lançamento 03/03/2016 de análise da cadeia ##

Nesta versão contém a seguinte actualização.

Título | Descrição
---|---
Novos itens de linguagem de consulta de análise da cadeia  | SAQL tem agora [GetType](https://msdn.microsoft.com/library/azure/mt643890.aspx "GetType MSDN página"), [TRY_CAST](https://msdn.microsoft.com/library/azure/mt643735.aspx "TRY_CAST MSDN página") e [REGEXMATCH](https://msdn.microsoft.com/library/azure/mt643891.aspx "REGEXMATCH MSDN página").

## <a name="notes-for-12102015-release-of-stream-analytics"></a>Notas de lançamento de 10/12/2015 de análise da cadeia ##

Nesta versão contém a seguinte actualização.

Título | Descrição
---|---
Actualização de versão REST API | A versão de REST API foi atualizada para 2015-10-01. Encontrará detalhes no MSDN em [Sequência Analytics gestão REST API referência](https://msdn.microsoft.com/library/azure/dn835031.aspx) e [integração de máquina formação em sequência Analytics](stream-analytics-how-to-configure-azure-machine-learning-endpoints-in-stream-analytics.md).
Integração de aprendizagem máquina Azure | Com esta versão é fornecido suporte para o Azure máquina aprender funções definidas pelo utilizador. Consulte o [tutorial](stream-analytics-machine-learning-integration-tutorial.md) , para obter mais informações, bem como o [blogue gerais anúncio](http://blogs.technet.com/b/machinelearning/archive/2015/12/10/apply-azure-ml-as-a-function-in-azure-stream-analytics.aspx).

## <a name="notes-for-11122015-release-of-stream-analytics"></a>Notas de lançamento de 11/12/2015 de análise da cadeia ##

Nesta versão contém a seguinte actualização.

Título | Descrição
---|---
Novo comportamento dos SELECIONE | SELECIONE em sequência Analytics foi expandida para permitir * como um acessor de propriedade de um registo aninhado. Para obter mais informações, consulte [http://msdn.microsoft.com/library/mt622759.aspx](http://msdn.microsoft.com/library/mt622759.aspx "Tipos de dados complexos").

## <a name="notes-for-10222015-release-of-stream-analytics"></a>Notas de lançamento de 22/10/2015 de análise da cadeia ##

Nesta versão contém as seguintes atualizações.

Título | Descrição
---|---
Funcionalidades do idioma de consulta adicionais | Análise de sequência tem expandidos a linguagem de consulta por incluindo as seguintes funcionalidades: [ABS](https://msdn.microsoft.com/library/azure/mt574054.aspx), [Arred. excesso](https://msdn.microsoft.com/library/azure/mt605286.aspx), [EXP](https://msdn.microsoft.com/library/azure/mt605289.aspx), [Arred](https://msdn.microsoft.com/library/azure/mt605240.aspx), [POWER](https://msdn.microsoft.com/library/azure/mt605287.aspx), [início de sessão](https://msdn.microsoft.com/library/azure/mt605290.aspx), [QUADRADO](https://msdn.microsoft.com/library/azure/mt605288.aspx)e [RAIZQ](https://msdn.microsoft.com/library/azure/mt605238.aspx).
Limitações de agregação removidas  | Nesta versão remove a limitação de 15 agregados numa consulta. Agora, não existe limite para o número de agregados por consulta.
Funcionalidade de grupo por System.Timestamp adicional | A função [GROUP BY](https://msdn.microsoft.com/library/azure/dn835023.aspx) permite agora para window_type ou [System.Timestamp](https://msdn.microsoft.com/library/azure/mt598501.aspx).
DESLOCAMENTO adicionado para Tumbling e salto windows | Por predefinição, o windows [Tumbling](https://msdn.microsoft.com/library/azure/dn835055.aspx) e [Hopping](https://msdn.microsoft.com/library/azure/dn835041.aspx) são alinhados à contra um tempo de zero (1/1/0001 UTC 12:00:00 AM). O novo parâmetro (opcional) 'offsetsize' permite especificar um desvio personalizado (ou alinhamento).


## <a name="notes-for-09292015-release-of-stream-analytics"></a>Notas de lançamento 29/09/2015 de análise da cadeia ##

Nesta versão contém as seguintes atualizações.

Título | Descrição
---|---
Conjunto de aplicações Azure IoT público pré-visualização | Análise da cadeia é incluída na pré-visualização do conjunto de IoT Azure público.
Integração com o Portal Azure | Para além de continuação presença no portal do Azure gestão, análise da cadeia agora está integrado no [Portal do Azure](https://azure.microsoft.com/overview/preview-portal/). Nota que funcionalidade no portal do pré-visualização está atualmente a análise da cadeia um subconjunto da funcionalidade oferecida no portal de gestão do Azure, sem suporte para testar a consulta no browser, o Power BI saída e configuração de navegação para ou criar nova entrada e saída recursos no subscrições que tiver acesso a.
Suporte para DocumentDB saída | Tarefas de análise da cadeia agora podem exportar para [DocumentDB](https://azure.microsoft.com/services/documentdb/).
Suporte para IoT concentrador entrada | Tarefas de análise da cadeia podem agora ingerir esta última dados a partir de IoT concentradores.
CARIMBO por para eventos heterogéneos | Quando um fluxo de dados único contém vários tipos de evento tendo selos de tempo em campos diferentes, pode agora utilizar [Carimbo por](http://msdn.microsoft.com/library/mt573293.aspx) com expressões para especificar campos de data/hora diferente para cada um dos casos.

## <a name="notes-for-09102015-release-of-stream-analytics"></a>Notas de lançamento de 10/09/2015 de análise da cadeia ##

Nesta versão contém as seguintes atualizações.

Título|Descrição
---|---
Suporte para obter grupos|Para ativar a partilha de dados com outros utilizadores do Power BI, trabalhos de análise da cadeia agora podem escrever a [obter grupos](stream-analytics-define-outputs.md#power-bi) dentro da sua conta do Power BI.

## <a name="notes-for-08202015-release-of-stream-analytics"></a>Notas para 20/08/2015 lançamento de análise da cadeia ##

Nesta versão contém as seguintes atualizações.

Título|Descrição
---|---
Função última adicionada |A função [última](http://msdn.microsoft.com/library/mt421186.aspx) está agora disponível em projectos de análise da cadeia, permitindo-lhe obter o evento mais recente numa sequência de evento num determinado intervalo de tempo.
Novas funções de matriz|Funções de matriz [GetArrayElement](http://msdn.microsoft.com/library/mt270218.aspx), [GetArrayElements](http://msdn.microsoft.com/library/mt298451.aspx) e [GetArrayLength](http://msdn.microsoft.com/library/mt270226.aspx) estão agora disponíveis.
Novas funções de registo|Funções de registo [GetRecordProperties](http://msdn.microsoft.com/library/mt270221.aspx) e [GetRecordPropertyValue](http://msdn.microsoft.com/library/mt270220.aspx) estão agora disponíveis.

## <a name="notes-for-07302015-release-of-stream-analytics"></a>Notas de lançamento de 30/07/2015 de análise da cadeia ##

Nesta versão contém as seguintes atualizações.

Título|Descrição
---|---
Id de organização do Power BI desacoplado a partir do Azure Id|Esta funcionalidade permite [saída do Power BI](stream-analytics-power-bi-dashboard.md) para trabalhos ASA em qualquer tipo de conta Azure (Live Id ou Id da Org). Para além disso, pode ter um Id de organização para a sua conta Azure e utilize um diferente para a autorização de saída do Power BI.
Suporte para o serviço Bus filas saída|Resultados de [Serviço Bus filas](stream-analytics-define-outputs.md#service-bus-queues) estão agora disponíveis em tarefas de análise da cadeia.
Suporte para o serviço Bus tópicos saída|Resultados de [Serviço Bus tópicos](stream-analytics-define-outputs.md#service-bus-topics) estão agora disponíveis em tarefas de análise da cadeia.

## <a name="notes-for-07092015-release-of-stream-analytics"></a>Notas de lançamento 09/07/2015 de análise da cadeia ##

Nesta versão contém as seguintes atualizações.


Título|Descrição
---|---
Personalizada Blob a partições de saída|Resultados de armazenamento de BLOBs permitem agora uma opção para especificar a frequência esse resultado blobs estão escritos e a estrutura e o formato da estrutura de pastas de caminho de dados de saída. 

## <a name="notes-for-05032015-release-of-stream-analytics"></a>Notas de lançamento 05/03/2015 de análise da cadeia ##

Nesta versão contém as seguintes atualizações.


Título|Descrição
---|---
Valor máximo aumentado para fora de ordem tolerância a janela|O tamanho máximo para a janela de tolerância fora de ordem é agora 59:59 (mm: SS)
Formato de exportação JSON: Linha separados ou numa matriz|Agora existe uma opção quando exportar para o armazenamento de BLOBs ou concentrador de evento para exportar como uma matriz de objetos JSON ou separando objetos JSON com uma nova linha. 

## <a name="notes-for-04162015-release-of-stream-analytics"></a>Notas de lançamento de 16/04/2015 de análise da cadeia ##


Título|Descrição
---|---
Atraso na configuração da conta de armazenamento do Windows Azure|Ao criar uma tarefa de análise da cadeia numa região pela primeira vez, vai ser-lhe para criar uma nova conta de armazenamento ou especifique uma conta existente para monitorizar a análise da cadeia de tarefas na região. Devido a latência na configuração de monitorização, criar outra tarefa de análise da cadeia na mesma região dentro de 30 minutos irá pedir a especificar uma segunda conta de armazenamento em vez de a mostrar aquele recentemente configurado no menu pendente de conta de armazenamento de monitorização. Para evitar a criação de uma conta de armazenamento desnecessária, aguarde 30 minutos depois de criar uma tarefa numa região pela primeira vez antes de aprovisionar tarefas adicionais na região.
Atualização de tarefa|Neste momento, a análise da cadeia não suporta edições direto para a definição ou a configuração de uma tarefa em execução. Para alterar a entrada, saída, consulta, escala ou configuração de uma tarefa em execução, primeiro tem de terminar a tarefa.
Tipos de dados inferidos da origem de entrada|Se não for utilizada uma instrução CREATE TABLE, o tipo de entrada é derivado de formato de entrada, por exemplo todos os campos de CSV cadeia. Os campos têm de ser convertidas em explicitamente o tipo de direita utilizando a função CAST para evitar erros de tipo de erro de correspondência.
Campos em falta são debitar como valores nulos|Referenciar um campo que não está presente na origem de entrada irá resultar em valores nulos no evento de saída.
COM demonstrações tem de preceder instruções SELECT|Na sua consulta, as instruções SELECT tem de seguir subconsultas definidas com instruções.
Problema de memória esgotada|Reinicie o transmissão trabalhos de análise com uma tolerância grande para fora da ordem eventos e/ou consultas complexas manutenção de uma grande quantidade de estado pode fazer com que a tarefa a memória, que resulta numa tarefa. As operações de início e estarão visíveis nos registos de operação a tarefa. Para evitar este comportamento, dimensione a consulta saída ao longo de múltiplas partições. Num lançamento futuro, esta limitação será resolvida por degradar desempenho no afetada tarefas em vez de reiniciá-los.
Entradas de grandes dimensões blob sem carimbo de data/hora carga útil podem causar o problema de memória esgotada|Consumir ficheiros grandes a partir do armazenamento de BLOBs pode causar trabalhos de análise da cadeia falhar, se não for especificado um campo de data/hora através do carimbo por. Para evitar este problema, manter cada blob de 10MB de tamanho.
Limitação de volume do evento de base de dados SQL|Ao utilizar a base de dados SQL como um alvo de saída, muito alto volumes de dados de saída podem fazer com que a tarefa de análise da cadeia expirar. Para resolver este problema, reduzir o volume de saída utilizando agregados ou operadores de filtro ou selecione armazenamento de Blobs do Azure ou evento concentradores como um alvo de saída em vez disso.
Obter conjuntos de dados podem conter apenas uma tabela|Obter não suporta mais de uma tabela um determinado conjunto de dados.

## <a name="get-help"></a>Obter ajuda
Para obter mais assistência, experimente nosso [Fórum Azure da cadeia Analytics](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Próximos passos

- [Introdução ao Azure sequência Analytics](stream-analytics-introduction.md)
- [Começar a utilizar o Azure da cadeia Analytics](stream-analytics-get-started.md)
- [Escala Azure da cadeia Analytics tarefas](stream-analytics-scale-jobs.md)
- [Azure sequência Analytics referência da linguagem de consulta](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Referência da sequência Azure Analytics gestão REST API](https://msdn.microsoft.com/library/azure/dn835031.aspx)
 
