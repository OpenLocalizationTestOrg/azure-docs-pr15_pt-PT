<properties 
    pageTitle="Funções de dados da fábrica e as variáveis do sistema | Microsoft Azure" 
    description="Fornece uma lista de funções Azure dados fábrica e as variáveis do sistema" 
    documentationCenter="" 
    authors="sharonlo101" 
    manager="jhubbard" 
    editor="monicar"
    services="data-factory"
/>

<tags 
    ms.service="data-factory" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/11/2016" 
    ms.author="shlo"/>

# <a name="azure-data-factory---functions-and-system-variables"></a>Dados Azure Factory - funções e variáveis do sistema
Este artigo fornece informações sobre funções e variáveis suportadas pelo Azure fábrica de dados.
  
## <a name="data-factory-system-variables"></a>Variáveis do sistema de fábrica do mesmo de dados

Nome da variável | Descrição | Âmbito de objeto | Âmbito JSON e casos de utilização
------------- | ----------- | ------------ | ------------------------
WindowStart | Início do intervalo de tempo para atividade atual executar janela | atividade | <ol><li>Especifique as consultas de seleção de dados. Consulte os artigos de conexão referenciados no artigo [Atividades de movimento de dados](data-factory-data-movement-activities.md) .</li><li>Passe parâmetros para ramo de script (exemplo mostrado acima).</li>
WindowEnd | Fim do intervalo de tempo para atividade atual executar janela | atividade | mesmo que acima
SliceStart | Início do intervalo de tempo para setor dados endereçados produzido | atividade<br/>conjunto de dados | <ol><li>Especifique os caminhos de pasta dinâmicos e nomes de ficheiro enquanto trabalha com [BLOBs do Azure](data-factory-azure-blob-connector.md) e [conjuntos de dados do sistema de ficheiros](data-factory-onprem-file-system-connector.md).</li><li>Especificar dependências de entrada com funções de fábrica do mesmo na coleção de entradas do tipo de atividade de dados.</li></ol>
SliceEnd | Fim do intervalo de tempo para setor de dados atual endereçados produzido | atividade<br/>conjunto de dados | mesmo que acima. 

> [AZURE.NOTE] Atualmente a fábrica de dados requer que a agenda especificada na atividade de exatamente correspondem a agenda especificada na disponibilidade do conjunto de dados de saída. Isto significa que WindowStart, WindowEnd e SliceStart e SliceEnd sempre mapeiam para o mesmo período de tempo e um setor individual de saída.
 
## <a name="data-factory-functions"></a>Funções de dados da fábrica 

Pode utilizar funções numa fábrica de dados ao longo com acima variáveis do sistema mencionadas para seguintes efeitos:

1.  Especificação de consultas de seleção de dados (consulte os artigos de conexão referenciados pelo artigo de [Atividades de movimento de dados](data-factory-data-movement-activities.md) .

    A sintaxe para invocar uma função de fábrica do mesmo de dados é: ** $$ ** para consultas de seleção de dados e outras propriedades na atividade e conjuntos de dados.  
2. Especificar dependências de entrada com funções de fábrica do mesmo de dados na atividade as entradas coleção (consulte exemplo acima).

    $$ Não é necessária para especificar as expressões de dependência de entrada.   

No seguinte exemplo, a propriedade **sqlReaderQuery** num ficheiro JSON está atribuída a um valor devolvido pela função **Text.Format** . Este exemplo utiliza também uma variável de sistema com o nome **WindowStart**, que representa a hora de início de atividade executar janela.
    
    {
        "Type": "SqlSource",
        "sqlReaderQuery": "$$Text.Format('SELECT * FROM MyTable WHERE StartTime = \\'{0:yyyyMMdd-HH}\\'', WindowStart)"
    }

### <a name="functions"></a>Funções

As tabelas seguintes apresentam todas as funções no Azure fábrica de dados:

Categoria | Função | Parâmetros | Descrição
-------- | -------- | ---------- | ----------- 
Tempo | AddHours(X,Y) | X: DateTime <br/><br/>Y: int de | Adiciona Y horas à hora indicada X. <br/><br/>Exemplo: 9/5/2013 12:00:00 PM + 2 horas = 9/5/2013 2:00:00 PM
Tempo | AddMinutes(X,Y) | X: DateTime <br/><br/>Y: int de | Adiciona X minutos de Y.<br/><br/>Exemplo: 9/15/2013 12:00:00 PM + 15 minutos = 9/15/2013 12:15:00: 00
Tempo | StartOfHour(X) | X: Datetime | Obtém a hora de início para a hora representada pelo componente de hora de X. <br/><br/>Exemplo: StartOfHour de 9/15/2013 05:10:23 PM é 15/9/2013 05:00:00 PM
Data | AddDays(X,Y) | X: DateTime<br/><br/>Y: int de | Adiciona os dias de Y ao X.<br/><br/>Exemplo: 9/15/2013 12:00:00 PM + 2 dias = 9/17/2013 12:00:00 PM
Data | AddMonths(X,Y) | X: DateTime<br/><br/>Y: int de | Adiciona Y meses X.<br/><br/>Exemplo: 9/15/2013 12:00:00 PM + 1 mês = 10/15/2013 12:00:00 PM 
Data | AddQuarters(X,Y) | X: DateTime <br/><br/>Y: int de | Adiciona Y * 3 meses a X.<br/><br/>Exemplo: 9/15/2013 12:00:00 PM + 1 trimestre = 12/15/2013 12:00:00 PM
Data | AddWeeks(X,Y) | X: DateTime<br/><br/>Y: int de | Adiciona Y * 7 dias para X<br/><br/>Exemplo: 9/15/2013 12:00:00 PM + 1 semana = 9/22/2013 12:00:00 PM
Data | AddYears(X,Y) | X: DateTime<br/><br/>Y: int de | Soma anos de Y a X.<br/><br/>Exemplo: 9/15/2013 12:00:00 PM + 1 ano = 9/15/2014 12:00:00 PM
Data | Day(X) | X: DateTime | Obtém o componente do dia de X.<br/><br/>Exemplo: Dia de 9/15/2013 12:00:00 PM é 9. 
Data | DayOfWeek(X) | X: DateTime | Obtém o dia da semana do componente do X.<br/><br/>Exemplo: DayOfWeek de 9/15/2013 12:00:00 PM é Domingo.
Data | DayOfYear(X) | X: DateTime | Obtém o dia do ano representada pelo componente de ano de X.<br/><br/>Exemplos:<br/>1/12/2015: dia 335 de 2015<br/>31/12/2015: dia 365 de 2015<br/>31/12/2016: dia 366 da 2016 (bissexto ano)
Data | DaysInMonth(X) | X: DateTime | Obtém os dias no mês representado pelo componente de mês do parâmetro X.<br/><br/>Exemplo: DaysInMonth de 9/15/2013 são 30 uma vez que existem 30 dias no mês Setembro.
Data | EndOfDay(X) | X: DateTime | Obtém a data-hora em que representa o fim do dia (componente de dia) de X.<br/><br/>Exemplo: EndOfDay de 9/15/2013 05:10:23 PM é 15/9/2013 11:59:59 PM.
Data | EndOfMonth(X) | X: DateTime | Obtém o fim do mês representado por componente de mês do parâmetro X. <br/><br/>Exemplo: EndOfMonth de 9/15/2013 05:10:23 PM é 30/9/2013 11:59:59 PM (hora de data que representa o fim do mês de Setembro)
Data | StartOfDay(X) | X: DateTime | Obtém o início do dia representado pelo componente do dia do parâmetro X.<br/><br/>Exemplo: StartOfDay de 9/15/2013 05:10:23 PM é 15/9/2013 12:00:00 AM.
Data/hora | FROM(X) | X: cadeia de | Analisa a cadeia de X para uma data hora.
Data/hora | Ticks(X) | X: DateTime | Obtém a escala a propriedade do parâmetro X. Uma escala é igual a 100 nanossegundos. O valor desta propriedade representa o número de marcas de escala decorridos desde 12:00:00 meia-noite, 1 de Janeiro 0001. 
Texto | Format(X) | X: variável de cadeia | Formata o texto.

#### <a name="textformat-example"></a>Exemplo de Text.Format

    "defines": { 
        "Year" : "$$Text.Format('{0:yyyy}',WindowStart)",
        "Month" : "$$Text.Format('{0:MM}',WindowStart)",
        "Day" : "$$Text.Format('{0:dd}',WindowStart)",
        "Hour" : "$$Text.Format('{0:hh}',WindowStart)"
    }

Tópico de [Personalizar data e hora cadeias de formato](https://msdn.microsoft.com/library/8kb3ddd4.aspx) consulte o artigo que descreve diferentes opções de formatação pode utilizar (por exemplo: aa vs. aaaa). 

> [AZURE.NOTE] Ao utilizar uma função dentro de outra função, é necessário utilizar **$$** prefixo para a função interna. Por exemplo: $$Text.Format ('PartitionKey eq \\' my_pkey_filter_value\\' e página RowKey \\' {0:yyyy-MM-dd ss}\\', Time.AddHours (SliceStart -6)). Neste exemplo, repare que **$$** prefixo não é utilizado para a função **Time.AddHours** . 
  

