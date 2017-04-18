<properties
   pageTitle="Carga de trabalho de armazém de dados"
   description="Elasticidade SQL Data Warehouse permite-lhe crescem em sequência, encolher ou colocar em pausa cluster power utilizando uma escala de correr armazém das unidades de dados (DWUs). Este artigo explica as métricas de armazém de dados e como se relacionam DWUs. "
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="barbkess"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="07/25/2016"
   ms.author="barbkess;mausher;jrj;sonyama"/>


# <a name="data-warehouse-workload"></a>Carga de trabalho de armazém de dados
Uma carga de trabalho do armazém de dados que se refere a todas as operações que transpire contra um armazém de dados. A carga de trabalho do armazém de dados engloba todo o processo de carregamento de dados no armazém, análises e criar relatórios sobre o armazenamento de dados, gerir dados no armazém de dados e exportação de dados a partir do armazém de dados. A profundidade e boca destes componentes são geralmente proporcionais com o nível de vencimento do armazém de dados.


## <a name="new-to-data-warehousing"></a>Nunca armazenamento de dados?
Um armazém de dados é um conjunto de dados que são carregados a partir de uma ou mais origens de dados e são utilizados para executar tarefas de business intelligence como análise de dados e relatórios.

Armazenamento de dados é caracterizado por consultas que analisar números maiores de linhas, grandes intervalos de dados e podem devolver resultados relativamente grande para efeitos de análise e relatórios. Armazenamento de dados também é caracterizado por dados relativamente grande cargas versus pequenas ao nível da transação insere/atualizações/elimina.

- Um armazém de dados tem um melhor desempenho quando os dados são armazenados de uma forma que optimiza consultas que precisa para analisar grandes quantidades de linhas ou grandes intervalos de dados. Este tipo de detecção funciona melhor quando os dados são armazenados e procurados por colunas, em vez de por linhas.

>[AZURE.NOTE] Índice columnstore na memória, que utiliza o armazenamento de coluna, fornece até 10 x ganhos de compressão e 100 x ganhos de desempenho da consulta sobre tradicionais árvores binários para elaboração de relatórios e análises de consultas. Vamos considere columnstore índices como padrão para armazenar e analisar dados grandes no armazém de dados.

- Um armazém de dados possui requisitos diferentes que um sistema que optimiza para transação online processamento (OLTP). O sistema OLTP tem muitas inserir, atualizar e eliminar operações. Estas operações procuram determinadas linhas na tabela. Destina-se tabela desempenhar melhor quando os dados são armazenados de uma forma de linha por linha. Os dados podem ser ordenados e rapidamente procurados com uma divisão e conquiste abordagem denominada uma pesquisa de árvore ou btree binária.


## <a name="data-loading"></a>Carregamento de dados
Carregamento de dados é uma grande parte da carga de trabalho do armazém de dados. Empresas normalmente têm um sistema OLTP ocupado que controla alterações ao longo do dia quando os clientes estão a gerar as transações de empresas. Periodicamente, muitas vezes durante a noite durante uma janela de manutenção, as transações são movidas ou copiadas para o armazém de dados. Assim que os dados estiverem no armazém de dados, analistas podem efetuar uma análise e tomar decisões empresariais nos dados.

- Normalmente, o processo de carregamento chamado ETL extrair, transformação e carregar. Dados, normalmente, tem de ser transformados para que fique consistente com outros dados no armazém de dados. Empresas utilizado anteriormente, os servidores de ETL dedicados para executar as transformações. Agora, com essa processamento rapidamente previstos excederem consideravelmente paralelo pode carregar os dados para armazém de dados SQL pela primeira vez e, em seguida, efetue as transformações. Este processo é denominado extrair, carregamento e transformar (ELT) e está a ficar um novo padrão para a carga de trabalho do armazém de dados.

> [AZURE.NOTE] Com o SQL Server 2016, pode agora executar a análise em tempo real numa tabela OLTP. Isto não substitui a necessidade de um armazém de dados armazenar e analisar dados, mas ainda disponibiliza uma maneira para efetuar análise em tempo real.

### <a name="reporting-and-analysis-queries"></a>Consultas de relatórios e análise
Consultas de relatórios e análise frequentemente são categorizadas em pequeno, médio e grande com base num número de critérios, mas normalmente baseia-se na hora. Na maioria dos armazém de dados, não existe uma carga de trabalho de versões mista do fast em execução versus consultas de execução longa. Em cada um dos casos, é importante para determinar a mistura e para determinar a sua frequência (hora a hora, diariamente, mês-end, trimestre-end entre outros). É importante compreender que a carga de trabalho de consulta mistas associado ao forma simultaneidade, oportunidade potencial a capacidade inicial planeamento de um armazém de dados.

- Planeamento da capacidade pode ser uma tarefa complexa para uma carga de trabalho de consulta mistas, sobretudo quando necessita de um tempo de antecedência longo para adicionar a capacidade para o armazém de dados. Armazém de dados SQL remove a urgência do planeamento de capacidade desde que pode aumentar e diminuir cluster capacidade a qualquer momento e desde o armazenamento e capacidade de cluster estão dimensionadas separadamente.

### <a name="data-management"></a>Gestão de dados
Gerir dados é importante, especialmente quando sabe que pode ficar fora do espaço em disco no futuro próximo. Armazenamento de dados normalmente dividir os dados em intervalos significativos, que são armazenados como a partições numa tabela. Todos os produtos com base no SQL Server permitem-lhe mover a partições e terminar a tabela. Esta partição mudar permite-lhe mover os dados mais antigos ao armazenamento dispendioso menos e mantenha os dados mais recentes disponíveis armazenamento online.

- Índices de Columnstore suportam tabelas com partições. Para columnstore índices, tabelas com partições são utilizadas para gestão de dados e o arquivo. Para tabelas armazenadas linha por-linha, a partições têm uma função maior no desempenho da consulta.  

- PolyBase é reproduzido um papel importante na gestão de dados. Utilizar PolyBase, tem a opção para arquivar dados mais antigos ao armazenamento blob do Hadoop ou Azure.  Este procedimento fornece várias opções, uma vez que os dados ainda estiverem online.  Poderá demorar mais tempo a obter dados a partir do Hadoop, mas a variação de tempo de obtenção poderá compensar o custo de armazenamento.

### <a name="exporting-data"></a>Exportar dados
Uma forma para disponibilizar para relatórios e análise de dados é enviar dados a partir do armazém de dados para os servidores dedicado para executar relatórios e análise. Nestes servidores chamam marts de dados. Por exemplo, que previamente processar dados do relatório e, em seguida, exporte-o armazenamento de dados para muitos servidores em todo o mundo, a tornar disponível para clientes e analistas.

- Para gerar relatórios, cada uma das noites poderia povoar servidores de relatórios só de leitura com um instantâneo dos dados diários. Isto dá-largura de banda superior para clientes enquanto baixar as necessidades de recursos de cluster no armazém de dados. A partir de um aspecto de segurança, marts dados permitem-lhe reduzir o número de utilizadores que têm acesso ao armazém de dados.
- Para análise, pode criar um cubo de análise no armazém de dados e execute a análise contra no armazém de dados, ou previamente processar dados e exportá-lo para o servidor de análise para análise ainda mais.

## <a name="next-steps"></a>Próximos passos
Agora que já conhece um pouco sobre armazém de dados SQL, saiba como rapidamente [criar um armazém de dados SQL][] e [carregar os dados de exemplo][].

<!--Image references-->

<!--Article references-->
[carregar os dados de exemplo]: ./sql-data-warehouse-load-sample-databases.md
[criar um armazém de dados SQL]: ./sql-data-warehouse-get-started-provision.md

<!--MSDN references-->

<!--Other web references-->
