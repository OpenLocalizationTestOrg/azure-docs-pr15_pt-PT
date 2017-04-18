<properties 
   pageTitle="Conhecimentos aprofundados do desempenho de consulta de base de dados do Azure SQL" 
   description="Monitorização de desempenho de consulta identifica a maior parte das consultas consumir CPU para uma base de dados do SQL Azure." 
   services="sql-database" 
   documentationCenter="" 
   authors="stevestein" 
   manager="jhubbard" 
   editor="monicar"/>

<tags
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management" 
   ms.date="08/09/2016"
   ms.author="sstein"/>

# <a name="azure-sql-database-query-performance-insight"></a>Conhecimentos aprofundados do desempenho de consulta de base de dados do Azure SQL


Gerir e Otimização do desempenho das bases de dados relacionais são uma tarefa um desafio que requer conhecimentos significativo e investimento de tempo. Conhecimentos aprofundados de desempenho de consulta permite-lhe perder menos tempo a resolução de problemas de desempenho de base de dados, fornecendo o seguinte:

- Mais aprofundada visão o consumo de recursos (DTU) de bases de dados. 
- Principais consultas por contar de duração/CPU/execução, que pode ser potencialmente optimizado para um desempenho melhorado.
- A capacidade de desagregar os detalhes de uma consulta, ver o seu texto e histórico da utilização de recursos. 
- Optimização anotações que mostram ações realizadas por [Classificação de base de dados do SQL Azure](sql-database-advisor.md) do desempenho  



## <a name="prerequisites"></a>Pré-requisitos

- Conhecimentos aprofundados de desempenho de consulta só está disponível com V12 de base de dados do SQL Azure.
- Conhecimentos aprofundados de desempenho de consulta requer que a [Loja de consulta](https://msdn.microsoft.com/library/dn817826.aspx) está ativa na sua base de dados. Se não estiver em execução loja de consulta, o portal do pede-lhe a ativá-la.

 
## <a name="permissions"></a>Permissões

As permissões de [controlo de acesso baseado em funções](../active-directory/role-based-access-control-configure.md) seguintes são necessários para utilizar conhecimentos aprofundados de desempenho de consulta: 

- Permissões de **leitor**, **proprietário**, **Contribuinte**, **SQL DB contribuinte**ou **SQL Server contribuinte** são necessários para ver as consultas consome recursos superior e gráficos. 
- Permissões de **proprietário**, **Contribuinte**, **SQL DB contribuinte**ou **SQL Server contribuinte** são necessários para ver o texto da consulta.



## <a name="using-query-performance-insight"></a>Utilizar conhecimentos aprofundados de desempenho de consulta

É fácil de utilizar conhecimentos aprofundados de desempenho de consulta:

- Abra [o portal do Azure](https://portal.azure.com/) e localize a base de dados que pretende examinar. 
  - A partir do menu do lado esquerdo, em suporte e resolução de problemas, selecione "Consulta desempenho conhecimentos aprofundados".
- No separador primeiro, reveja a lista de principais consultas consumo de recursos.
- Selecione uma consulta individual para ver os detalhes.
- Abra [Advisor de base de dados do SQL Azure](sql-database-advisor.md) e verifique se as recomendações estão disponíveis.
- Utilize os controlos de deslize ou zoom ícones para alterar o intervalo observado.

    ![dashboard de desempenho](./media/sql-database-query-performance/performance.png)

> [AZURE.NOTE] Algumas horas de dados tem de ser capturado ao arquivo de consulta de base de dados do SQL fornecer informações de desempenho da consulta. Se a base de dados não tem nenhuma actividade ou arquivo de consulta não estava ativo durante um determinado período de tempo, os gráficos estará vazios quando apresentar esse período de tempo. Pode ativar o arquivo de consulta em qualquer altura se não estiver em execução.   



## <a name="review-top-cpu-consuming-queries"></a>Rever CPU superior consumir consultas

No [portal](http://portal.azure.com) faça o seguinte:

1. Navegue para uma base de dados do SQL e clique em **todas as definições de** > **suporte + resolução de problemas** > **conhecimentos aprofundados de desempenho de consulta**. 

    ![Conhecimentos aprofundados de desempenho de consulta][1]

    Abre a vista principais consultas e as principais consultas consome CPU são listadas.

1. Clique em à volta do gráfico para obter detalhes.<br>A linha superior mostra geral DTU %, para a base de dados, enquanto que as barras mostram CPU % média consumida pelas consultas selecionadas durante o intervalo selecionado (por exemplo, se estiver selecionada **semana passada** cada barra representa um dia).

    ![principais consultas][2]

    Grelha da parte inferior representa informação agregada consultas visível.

  - ID da consulta – Identificador exclusivo de consulta no interior da base de dados.
  - CPU por consulta durante observável intervalo (depende da função de agregação).
  - Duração por consulta (depende da função de agregação).
  - Número total de execuções para uma consulta específica.

    Selecione ou desmarque consultas individuais para incluir ou exclui-los a partir do gráfico utilizando caixas de verificação.

1. Se os seus dados ficam obsoletos, clique no botão **Atualizar** .
1. Pode utilizar os controlos de deslize e zoom nos botões para alterar o intervalo de observação e investigar desportiva:  ![definições](./media/sql-database-query-performance/zoom.png)
1. Opcionalmente, se pretender que uma vista diferente, pode selecionar o separador **personalizado** e definir:
  
  - Métrica (CPU, duração, contagem de execução)
  - Intervalo de tempo (passado semana, mês passado, horas do último 24). 
  - Número de consultas.
  - Função de agregação.

    ![definições](./media/sql-database-query-performance/custom-tab.png)

## <a name="viewing-individual-query-details"></a>Ver detalhes de consulta individuais

Para ver os detalhes de consulta:

1. Clique em qualquer consulta na lista de principais consultas.

    ![Detalhes](./media/sql-database-query-performance/details.png)

1. Abre a vista de detalhes e dividida a contagem de duração/consumo/execução CPU consultas ao longo do tempo.
1. Clique em à volta do gráfico para obter detalhes.
  - Gráfico superior mostra linhas com geral % DTU de base de dados e as barras são CPU % média consumida pela consulta selecionada.
  - Segundo gráfico mostra a duração total através da consulta selecionada.
  - Gráfico da parte inferior mostra o número total de execuções pela consulta selecionada.
    
    ![Detalhes da consulta][3]

1. Opcionalmente, utilize os controlos de deslize, botões de zoom ou clique em **Definições** para personalizar a forma como os dados de consultas são apresentados, ou para selecionar um período de tempo diferentes.

## <a name="review-top-queries-per-duration"></a>Rever as principais consultas por duração

Na atualização de conhecimentos aprofundados de desempenho de consulta recente, podemos introduzidas duas nova de métricas de que o pode ajudar a identificar possíveis gargalos: contagem de duração e execução.<br>

Consultas de execução longa têm o maior potencial para bloquear mais longo de recursos, bloquear outros utilizadores e limitar escalabilidade. Também são os melhores candidatos para otimização.<br>

Para identificar o tempo de execução de consultas:

1. Abrir o separador **Personalizar** no conhecimentos aprofundados de desempenho de consulta para base de dados selecionada
1. Alterar a métrica para ser **duração**
1. Selecione o número de consultas e intervalo de observação
1. Selecione a função de agregação
  - **Soma** adiciona o tempo de execução de consulta todos os durante o intervalo de observação todo.
  - **Máximo** localiza consultas tempo de execução do qual foi máximo no intervalo de observação todo.
  - **Avg** localiza média tempo de execução de todas as execuções de consulta e mostrar os fora destas médias. 

    ![duração de consulta][4]

## <a name="review-top-queries-per-execution-count"></a>Rever as principais consultas por contagem de execução

Número elevado de execuções poderá não ser que afetam os própria base de dados e a utilização de recursos pode ser baixa, mas poderá obter global da aplicação lenta.

Em alguns casos, contagem de execuções muito alta pode conduzir a aumentar de round viagens de rede. Arredondar viagens significativamente afetar o desempenho. São sujeitos a latência da rede e a latência descendentes servidor. 

Por exemplo, muitos Web sites condicionados por dados fortemente aceder a base de dados para todos os pedidos de utilizador. Enquanto a ligação ajuda-o a, o tráfego de rede um aumento de agrupamento e processamento de carga no servidor de base de dados adverso pode afetar o desempenho.  Conselhos geral são manter round viagens num mínimo de referências absoluto.

Para identificar frequentemente executada consultas ("chatty") de consultas:

1. Abrir o separador **Personalizar** no conhecimentos aprofundados de desempenho de consulta para base de dados selecionada
1. Alterar a métrica para ser **contagem de execução**
1. Selecione o número de consultas e intervalo de observação

    ![Contagem de execução de consulta][5]

## <a name="understanding-performance-tuning-annotations"></a>Noções sobre anotações de sintonização do desempenho 

Enquanto explorar sua carga de trabalho conhecimentos aprofundados de desempenho de consulta, poderá reparar em ícones com linha vertical na parte superior do gráfico.<br>

Estes ícones estiverem anotações; que representam desempenho afetar ações realizadas por [Classificação de base de dados do SQL Azure](sql-database-advisor.md). Ao apontar anotação, obter informação básica sobre a ação:

![anotação de consulta][6]

Se quiser saber mais ou aplique recomendação advisor, clique no ícone. Será aberto detalhes de ação. Se for uma active recomendação pode aplicá-lo diretamente ausente, utilizando o comando.

![detalhes de anotação de consulta][7]

### <a name="multiple-annotations"></a>Várias anotações. ###

É possível que, devido a nível de zoom, irão obter fechadas anotações que estão perto entre si numa só. Isto irá ser representado pelo ícone especial, clicar na mesma irá abrir pá novo onde a lista de agrupada anotações será apresentado.
Correlacionar consultas e optimização ações do desempenho pode ajudar a compreender melhor sua carga de trabalho. 


##  <a name="optimizing-the-query-store-configuration-for-query-performance-insight"></a>Otimizar a configuração de arquivo de consulta para conhecimentos aprofundados de desempenho de consulta

Durante a utilização dos conhecimentos aprofundados de desempenho de consulta, poderá encontrar as seguintes mensagens de arquivo de consulta:

- "Arquivo de consulta não está corretamente configurado nesta base de dados. Clique aqui para obter mais informações."
- "Arquivo de consulta não está corretamente configurado nesta base de dados. Clique aqui para alterar as definições." 

Estas mensagens normalmente aparecem quando o arquivo de consulta não é possível recolher novos dados. 

Primeiro caso acontece ao arquivo de consulta está num estado só de leitura e parâmetros são definidos optimizada. Para corrigir este problema, aumentar tamanho do arquivo de consulta ou desmarcando arquivo de consulta.

![botão qds][8]

Segundo caso acontece ao arquivo de consulta está desativada ou não são optimizada definir parâmetros. <br>Pode alterar a política de retenção e captura e ativar o arquivo de consulta através da execução comandos abaixo ou diretamente a partir do portal:

![botão qds][9]

### <a name="recommended-retention-and-capture-policy"></a>Política de retenção e captura recomendada

Existem dois tipos de políticas de retenção:

- Tamanho baseado – se definida como automático-lo será limpar os dados automaticamente quando é atingido junto ao tamanho máximo.
- Tempo com base - por predefinição podemos irá configurá-lo a 30 dias, que significa que, se vai executar a consulta loja espaço, irá eliminar informações sobre a consulta mais de 30 dias

Capturar política foi definida como:

- **Todos os** – para capturar a todas as consultas.
- **Automática** – consultas pouco frequentes e consultas com duração de compilar e execução significativo são ignoradas. Limiares de duração de contar, compilação e runtime execução internamente são determinados. Esta é a opção de predefinido.
- **Nenhum** – arquivo de consulta deixa de capturar novas consultas, no entanto, estado do runtime para consultas já capturados é ainda recolhidos.
    
Recomendamos que definir todas as políticas para automática e LIMPARB política para 30 dias:

    ALTER DATABASE [YourDB] 
    SET QUERY_STORE (SIZE_BASED_CLEANUP_MODE = AUTO);
        
    ALTER DATABASE [YourDB] 
    SET QUERY_STORE (CLEANUP_POLICY = (STALE_QUERY_THRESHOLD_DAYS = 30));
    
    ALTER DATABASE [YourDB] 
    SET QUERY_STORE (QUERY_CAPTURE_MODE = AUTO);

Aumente tamanho do arquivo de consulta. Isto poderá ser executado por estabelecer ligação a uma base de dados e emitir sequência de consulta:

    ALTER DATABASE [YourDB]
    SET QUERY_STORE (MAX_STORAGE_SIZE_MB = 1024);

Aplicar estas definições são eventualmente irá tornar o arquivo de consulta recolher novas consultas, no entanto, se não pretender esperar pode limpar arquivo de consulta. 
> [AZURE.NOTE] Executar a consulta seguinte irá eliminar todas as informações atuais no arquivo de consulta. 


    ALTER DATABASE [YourDB] SET QUERY_STORE CLEAR;


## <a name="summary"></a>Resumo

Conhecimentos aprofundados de desempenho de consulta ajuda-o a compreender o impacto da sua carga de trabalho de consulta e como se relaciona com consumo de recursos base de dados. Com esta funcionalidade, irá obter informações sobre a parte superior consumir consultas e identificar facilmente aqueles corrigir antes de estas fiquem com um problema.




## <a name="next-steps"></a>Próximos passos

Para obter recomendações adicionais sobre como melhorar o desempenho da sua base de dados do SQL, clique em [recomendações](sql-database-advisor.md) sobre a pá **Conhecimentos aprofundados de desempenho de consulta** .

![Advisor de desempenho](./media/sql-database-query-performance/ia.png)


<!--Image references-->
[1]: ./media/sql-database-query-performance/tile.png
[2]: ./media/sql-database-query-performance/top-queries.png
[3]: ./media/sql-database-query-performance/query-details.png
[4]: ./media/sql-database-query-performance/top-duration.png
[5]: ./media/sql-database-query-performance/top-execution.png
[6]: ./media/sql-database-query-performance/annotation.png
[7]: ./media/sql-database-query-performance/annotation-details.png
[8]: ./media/sql-database-query-performance/qds-off.png
[9]: ./media/sql-database-query-performance/qds-button.png

