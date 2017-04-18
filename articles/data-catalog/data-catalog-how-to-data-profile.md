<properties
    pageTitle="Como origens de dados de perfil de dados"
    description="Artigo sobre como utilizar realce como incluir os perfis de dados de tabela e coluna nível quando registar origens de dados no catálogo de dados do Azure e como utilizar os perfis de dados para compreender as origens de dados."
    services="data-catalog"
    documentationCenter=""
    authors="spelluru"
    manager="NA"
    editor=""
    tags=""/>
<tags
    ms.service="data-catalog"
    ms.devlang="NA"
    ms.topic="article"
    ms.tgt_pltfrm="NA"
    ms.workload="data-catalog"
    ms.date="09/13/2016"
    ms.author="spelluru"/>

# <a name="data-profile-data-sources"></a>Origens de dados de perfil de dados

## <a name="introduction"></a>Introdução

**Catálogo de dados do Microsoft Azure** é um serviço de nuvem totalmente geridas que serve de um sistema de registo e sistema de deteção para origens de dados da empresa. Por outras palavras, o **Catálogo de dados do Azure** é tudo sobre ajudando às pessoas descobrir, compreender e utilizar origens de dados e organizações de ajuda para obter maior valor a partir dos seus dados existentes. Quando uma origem de dados está registada com o **Catálogo de dados do Azure**, respectivos metadados são copiado e indexado pelo serviço, mas a história não existem termina.

A funcionalidade de **Criação de perfis de dados** do **Catálogo de dados do Azure** examina os dados a partir de origens de dados suportadas no seu catálogo e recolhe informações sobre as estatísticas e informações sobre esses dados. É fácil incluir um perfil dos seus ativos de dados. Quando registar um elementos de dados, selecione **Incluir perfil de dados** na ferramenta de registo da origem de dados.

## <a name="what-is-data-profiling"></a>O que é a criação de perfis de dados

Criação de perfis de dados examina os dados na origem de dados sejam registado e recolhe informações sobre as estatísticas e informações sobre esses dados. Durante a identificação de origem de dados, estas estatísticas podem ajudar a determinar a conformidade dos dados a resolver o problema de empresas.

<!-- In [How to discover data sources](data-catalog-how-to-discover.md), you learn about **Azure Data Catalog's** extensive search capabilities including searching for data assets that have a profile. See [How to include a data profile when registering a data source](#howto). -->

Seguintes origens de dados suportam a criação de perfis de dados:

- Tabelas do SQL Server (incluindo Azure SQL DB e armazém de dados do SQL Azure) e vistas
- Oracle tabelas e vistas
- Teradata tabelas e vistas
- Ramo de tabelas

Incluindo os perfis de dados quando registar elementos de dados ajuda os utilizadores a respondam a perguntas sobre origens de dados, incluindo:

-   Pode ser utilizada para resolver o problema da minha empresa?
-   Os dados em conformidade com a normas específicas ou padrões?
-   Quais são algumas das anomalias da origem de dados?
-   Quais são os desafios possíveis de integração destes dados para a minha aplicação?

> [AZURE.NOTE] Também pode adicionar a documentação para um ativo para descrever como poderiam integrados dados para uma aplicação. Veja [como origens de dados do documento](data-catalog-how-to-documentation.md).


<a name="howto"/>
## <a name="how-to-include-a-data-profile-when-registering-a-data-source"></a>Como incluir um perfil de dados quando registar uma origem de dados

É fácil incluir um perfil da sua origem de dados. Quando registar uma origem de dados, no painel de **objetos sejam registados** a ferramenta de registo da origem de dados, selecione **Incluir perfil de dados**.

![](media\data-catalog-data-profile\data-catalog-register-profile.png)

Para saber mais sobre como registar origens de dados, consulte o artigo [como registar origens de dados](data-catalog-how-to-register.md) e [comece a trabalhar com o catálogo de dados do Azure](data-catalog-get-started.md).


## <a name="filtering-on-data-assets-that-include-data-profiles"></a>Filtragem de elementos de dados que incluem os perfis de dados
Para detetar elementos de dados que incluam um perfil de dados, pode incluir `has:tableDataProfiles` ou `has:columnsDataProfiles` como um dos termos de pesquisa.

> [AZURE.NOTE] Selecionar o **Perfil de dados incluir** na ferramenta de registo da origem de dados inclui uma tabela e informações de perfil de nível de coluna. No entanto, a API do catálogo de dados permite que os elementos de dados sejam registados com apenas um conjunto de informações de perfil incluídas.

## <a name="viewing-data-profile-information"></a>Ver informações de perfil de dados

Quando encontrar uma origem de dados adequado com um perfil, pode ver os detalhes do perfil de dados. Para ver o perfil de dados, selecione um recurso de dados e escolha o **Perfil de dados** na janela de portal do catálogo de dados.

![](media\data-catalog-data-profile\data-catalog-view.png)

Um perfil de dados no **Catálogo de dados do Azure** mostra a tabela e coluna perfil informações, incluindo:

### <a name="object-data-profile"></a>Perfil de dados de objeto

-   Número de linhas
-   Tamanho da tabela
-   Quando o objeto foi última atualização

### <a name="column-data-profile"></a>Perfil de dados da coluna

- Tipo de dados da coluna
- Número de valores distintos
- Número de linhas com os valores nulos
- Mínimo, máximo, média e desvio-padrão para valores de coluna

## <a name="summary"></a>Resumo
Dados de criação de perfis fornecem estatísticas e informações sobre os elementos de dados registadas para ajudar a determinar a conformidade dos dados para resolver problemas de empresas. Juntamente com anotar e origens de dados a documentar, os perfis de dados podem dar aos utilizadores uma mais aprofundada compreensão dos seus dados.


## <a name="see-also"></a>Consulte também
-   [Como registar origens de dados](data-catalog-how-to-register.md)
-   [Introdução ao catálogo de dados do Azure](data-catalog-get-started.md)
