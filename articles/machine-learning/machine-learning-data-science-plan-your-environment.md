<properties
    pageTitle="Como identificar cenários e planear avançadas processamento de dados de análise | Microsoft Azure"
    description="Plano de análise avançadas ponderando uma série de perguntas chave."
    services="machine-learning"
    documentationCenter=""
    authors="bradsev"
    manager="jhubbard"
    editor="cgronlun" />

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/19/2016"
    ms.author="bradsev" />


# <a name="how-to-identify-scenarios-and-plan-for-advanced-analytics-data-processing"></a>Como identificar cenários e plano para processamento de dados de análise avançadas

Quais os recursos que deverá planear a incluir quando configurar um ambiente para fazer um conjunto de dados de processamento de análise avançadas? Este artigo sugere uma série de perguntas para pedir que o irá ajudar a identificar as tarefas e recursos relevantes seu cenário. A ordem dos passos de alto nível para análise aspeto do Office está destacada [o que é o processo de ciências de dados de equipa (TDSP)?](data-science-process-overview.md). Cada um destes passos irão necessitar recursos específicos para as tarefas relevantes para o seu cenário em particular. As perguntas chaves para identificar o seu cenário dizem respeito logística de dados, características, a qualidade dos conjuntos de dados e as ferramentas e idiomas que preferir para fazer a análise.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## <a name="logistic-questions-data-locations-and-movement"></a>Perguntas logísticos: localizações dos dados e de movimento
As perguntas logísticos dizem respeito a localização da **origem de dados**, o **destino de destino** no Azure e requisitos para mover os dados, incluindo a agenda, quantidade e recursos envolvidos. Os dados poderão ter de ser movida várias vezes durante o processo de análise. É um cenário comum mover dados locais para alguns formulário de armazenamento no Azure e, em seguida, na máquina aprendizagem Studio.

1. **O que é a sua origem de dados?** É local ou na nuvem? Por exemplo:
    - Os dados são publicamente disponíveis em endereços HTTP.
    - Os dados residem numa localização de ficheiro/rede local.
    - Os dados são numa base de dados do SQL Server.
    - Os dados são armazenados num contentor de armazenamento Azure

2. **O que é o destino Azure?** Onde-necessitam de ser para processamento ou modelação? Por exemplo:
    - Armazenamento de Blobs do Azure
    - Bases de dados do SQL Azure
    - SQL Server no Azure VM
    - HDInsight (Hadoop no Azure) ou tabelas de ramo
    - Formação de máquina Azure
    - Montável Azure rígido discos virtuais.

3. **Como vai mover os dados?** Os tópicos seguintes são destacados os procedimentos e recursos disponíveis para ingerir esta última ou carregar os dados para uma variedade de armazenamento diferente e ambientes de processamento.

    -  [Carregar os dados para ambientes de armazenamento para análise](machine-learning-data-science-ingest-data.md)
    -  [Importar dados formação para o Azure máquina aprendizagem Studio a partir de várias origens de dados](machine-learning-data-science-import-data.md).

4. **Os dados necessitam de ser movida uma agenda normal ou modificadas durante a migração?** Considere utilizar Azure dados fábrica (ADF), quando precisa de dados a ser continuamente migrado, sobretudo, se um cenário de híbrido que acede ambas no local e na nuvem recursos está envolvido ou quando os dados são efectuados ou necessita de ser modificadas ou ter adicionada ao mesmo durante a ser migrada a lógica de negócio. Para obter mais informações, consulte o artigo [mover os dados de um servidor SQL no local para SQL Azure com fábrica de dados do Azure](machine-learning-data-science-move-sql-azure-adf.md)

5. **Quantidade dos dados que está a ser movido para Azure?** Conjuntos de dados que são muito grandes podem exceder a capacidade de armazenamento de determinados ambientes. Por exemplo, consulte o artigo o debate de limites de tamanho para máquina aprendizagem Studio na secção seguinte. Nestes casos uma amostra dos dados pode ser utilizada durante a análise. Para obter detalhes sobre como exemplo de seta para baixo um conjunto de dados em vários ambientes Azure, consulte o artigo [dados de exemplo no processo de ciências de dados de equipa](machine-learning-data-science-sample-data.md).


## <a name="data-characteristics-questions-type-format-and-size"></a>Perguntas sobre características de dados: tipo, formato e tamanho
Estas questões são chave para planear o armazenamento e processamento ambientes, cada um dos quais são adequadas para vários tipos de dados e cada um dos quais têm determinadas restrições.

1. **Quais são os tipos de dados?** Por exemplo:
    - Numérica
    - Categorias
    - Cadeias
    - Binário

2. **Como são formatados os seus dados?** Por exemplo:
    - Ficheiros de plano separados por vírgulas (CSV) ou separado por tabulações (TSV)
    - Comprimido ou não comprimido
    - Blobs do Azure
    - Ramo de Hadoop tabelas
    - Tabelas do SQL Server

2. **Como grandes são os seus dados?**
    - Pequeno: menos de 2GB
    - Média: Maior do que 2GB e menor que 10GB
    - Grande: Maiores que 10GB

Tome o ambiente do Azure máquina aprendizagem Studio por exemplo:

- Para obter uma lista de formatos de dados e tipos de suportados pelo Studio de aprendizagem do Azure máquina, consulte a secção [tipos de dados e formatos de dados suportados](machine-learning-data-science-import-data.md#data-formats-and-data-types-supported) .
- Para obter informações sobre limitações de dados para o Azure máquina aprendizagem Studio, consulte o artigo o **grandes como pode o conjunto de dados ser para minha módulos?** secção [Importar e exportar dados para máquina aprendizagem](machine-learning-faq.md#machine-learning-studio-questions)

Para obter informações sobre as limitações de outros serviços Azure utilizados no processo de análise, consulte o artigo [Azure subscrição e limites de serviço, Quotas e as restrições](../azure-subscription-service-limits.md).

## <a name="data-quality-questions-exploration-and-pre-processing"></a>Perguntas de qualidade de dados: informações detalhadas e pré-processamento

1. **O que saber sobre os seus dados?** Explore dados quando precisar de obter um compreender as suas características básicas. O que padrões ou tendências-obras expostas, o que é o aberrantes tem ou valores que existem em falta. Este passo é importante para determinar a extensão de pré-processamento for necessário, para elaboração de hipóteses que podem sugerir as funcionalidades mais adequadas ou tipo de análise de e para elaboração planos para recolha de dados adicionais. Calcular Estatística descritiva e desenho de visualizações estão técnicas úteis para o controlo de dados. Para obter detalhes sobre como explorar um conjunto de dados em vários ambientes Azure, consulte o artigo [explorar dados no processo de ciências de dados de equipa](machine-learning-data-science-explore-data.md).

2. **Os dados exigir pré-processamento ou limpeza?**
Pré-processamento e limpeza de dados são tarefas importantes que devem ser normalmente realizadas antes de conjunto de dados pode ser utilizado eficazmente para formação de máquina. Dados não processados são frequentemente com ruído e não fiáveis e poderão estar em falta valores. Utilizar esses dados para modelação pode produzir resultados enganadoras. Para obter uma descrição, consulte o artigo [formação de tarefas para preparar dados para máquina melhorado](machine-learning-data-science-prepare-data.md).

## <a name="tools-and-languages-questions"></a>Perguntas de ferramentas e idiomas
Existem várias opções dependendo do que idiomas e ambientes de desenvolvimento ou ferramentas precisa ou que estão a utilizar mais conformable.

1. **Que idiomas prefere utilizar para uma análise?**  
    - R
    - Python
    - SQL

2. **Que ferramentas pode utilizar para análise de dados?**
    - [Microsoft Azure Powershell](powershell-install-configure.md) - um idioma de script utilizado para administrar a sua recursos Azure num idioma de script.
    - [Azure máquina aprendizagem Studio](machine-learning-what-is-ml-studio/)
    - [Análise de rotação](http://www.revolutionanalytics.com/revolution-r-open)
    - [RStudio](http://www.rstudio.com)
    - [Ferramentas de Python para Visual Studio](http://microsoft.github.io/PTVS/)
    - [Anaconda](https://www.continuum.io/why-anaconda)
    - [Jupyter blocos de notas](http://jupyter.org/)
    - [Microsoft Power BI](http://powerbi.microsoft.com)


## <a name="identify-your-advanced-analytics-scenario"></a>Identifique o seu cenário de análise avançadas
Assim que atendeu as perguntas na secção anterior, está pronto para determinar que cenário de melhor se adequa seu caso. Os cenários de exemplo são destacados [cenários de análise avançadas no Azure máquina aprendizagem](machine-learning-data-science-plan-sample-scenarios.md).
