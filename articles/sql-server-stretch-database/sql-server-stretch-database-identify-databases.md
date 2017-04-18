<properties
    pageTitle="Identificar bases de dados e tabelas para esticar base de dados ao executar a classificação de base de dados do esticar | Microsoft Azure"
    description="Obter informações sobre como identificar bases de dados e tabelas que são candidatos para esticar base de dados."
    services="sql-server-stretch-database"
    documentationCenter=""
    authors="douglaslMS"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-server-stretch-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="06/14/2016"
    ms.author="douglasl"/>

# <a name="identify-databases-and-tables-for-stretch-database-by-running-stretch-database-advisor"></a>Identificar as bases de dados e as tabelas para esticar base de dados executando esticar Advisor de base de dados

Para identificar bases de dados e tabelas que são candidatos para esticar base de dados, transfira o SQL Server 2016 classificação de actualização do e executar a classificação de base de dados esticar do. Esticar Advisor de base de dados também identifica os problemas de bloqueio.

## <a name="download-and-install-upgrade-advisor"></a>Transferir e instalar o recomendações de actualização
Transfira e instale Advisor atualizar a partir de [aqui](http://go.microsoft.com/fwlink/?LinkID=613421). Esta ferramenta não está incluída no suporte de instalação do SQL Server.

## <a name="run-the-stretch-database-advisor"></a>Executar a classificação do esticar da base de dados

1.  Execute a classificação de atualização.

2.  Selecione **cenários**e, em seguida, selecione **Executar ADVISOR de base de dados de ESTICAR**.

3.  No pá **Executar Advisor de base de dados de opções esticar** , clique em **SELECIONAR bases de dados para analisar**.

4.  No pá **Selecione bases de dados** , introduza ou selecione o nome do servidor e as informações de autenticação. Clique em **Ligar**.

5.  É apresentada uma lista das bases de dados no servidor selecionado. Selecione as bases de dados que pretende analisar. Clique em **Selecionar**.

6.  No pá **Executar Advisor de base de dados de opções esticar** , clique em **Executar**.  A análise é executado.

## <a name="review-the-results"></a>Rever os resultados

1.  Quando a análise estiver concluída, no pá **Analyzed bases de dados** , selecione uma das bases de dados que analisados para apresentar o **resultados da análise** pá.

    O pá **os resultados da análise** lista tabelas recomendadas na base de dados seleccionado que correspondem aos critérios de recomendação predefinido.

2.  Na lista de tabelas no pá **resultados da análise** , selecione uma das tabelas de recomendado para apresentar o **resultados da tabela** pá.

    Se lá estejam a impedir problemas, o **resultados da tabela** pá listas os problemas de bloqueio para a tabela selecionada. Para obter informações sobre problemas de bloqueio detectado pelo esticar Advisor de base de dados, consulte [limitações para esticar base de dados](sql-server-stretch-database-limitations.md).

3.  Na lista de problemas de bloqueio pá os **resultados da tabela** , selecione um dos problemas para apresentar mais informações sobre o problema selecionado e propõe passos de mitigação. Implemente os passos de mitigação sugerido, se pretende configurar a tabela selecionada para esticar base de dados.

## <a name="next-step"></a>Passo seguinte
Ative a base de dados esticar.

-   Para ativar o esticar base de dados numa **base de dados**, consulte o artigo [Ativar esticar base de dados para uma base de dados](sql-server-stretch-database-enable-database.md).

-   Para ativar o esticar base de dados noutra **tabela**, quando esticar já está ativado na base de dados, consulte o artigo [Ativar esticar base de dados para uma tabela](sql-server-stretch-database-enable-table.md).

## <a name="see-also"></a>Consulte também

[Limitações para esticar base de dados](sql-server-stretch-database-limitations.md)

[Ativar opções esticar base de dados para uma base de dados](sql-server-stretch-database-enable-database.md)

[Ativar opções esticar base de dados para uma tabela](sql-server-stretch-database-enable-table.md)

[Todos os tópicos do serviço de base de dados do Azure SQL Server esticar](sql-server-stretch-database-index-all-articles.md)
