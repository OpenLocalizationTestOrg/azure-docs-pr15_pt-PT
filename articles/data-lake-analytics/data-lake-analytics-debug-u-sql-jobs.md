<properties 
   pageTitle="Depurar tarefas U SQL | Microsoft Azure" 
   description="Saiba como depurar vértice falha U SQL utilizando o Visual Studio. " 
   services="data-lake-analytics" 
   documentationCenter="" 
   authors="mumian" 
   manager="jhubbard" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-analytics"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="09/02/2016"
   ms.author="jgao"/>



#<a name="debug-c-code-in-u-sql-for-data-lake-analytics-jobs"></a>Depurar c# código no U-SQL para trabalhos de dados Lake Analytics 

Saiba como utilizar ferramentas do Azure dados Lake Visual Studio para depurar tarefas U SQL falhadas devido a erros dentro de código do utilizador. 

A ferramenta de Visual Studio permite-lhe transferir código compilado e dados vértice necessárias a partir de computação para analisar e depurar tarefas falhadas.

Os sistemas de dados grande normalmente fornecem o modelo de expansão através de idiomas como Java, c#, Python, etc. Muitos estes sistemas fornecem runtime limitado depuração de informações, que o torna difíceis de depuração de erros de runtime no código personalizado. As ferramentas do Visual Studio mais recentes inclui uma funcionalidade denominada "Falhou depurar vértice". Utilizar esta funcionalidade, pode transferir os dados de tempo de execução do Azure local estação de trabalho para que pode depurar falhado c# código personalizado utilizando o tempo de execução do mesmo e dados de entrada exatos a partir da nuvem.  Depois dos problemas são corrigidos, pode voltar a executar o código revisto no Azure das ferramentas de.

Para uma apresentação vídeo desta funcionalidade, consulte o artigo [depurar o seu código personalizado em Azure dados Lake Analytics](https://mix.office.com/watch/1bt17ibztohcb).

>[AZURE.NOTE] Visual Studio pode deixar de responder ou se não tiver as actualizações de duas janelas seguintes uma falha de sistema: [Microsoft Visual C++ 2015 Redistributable Update 2](https://www.microsoft.com/download/details.aspx?id=51682), [Universal C Runtime para Windows](https://www.microsoft.com/download/details.aspx?id=50410&wa=wsignin1.0).


##<a name="prerequisites"></a>Pré-requisitos
-   Foram trocadas através do artigo [começar a trabalhar](data-lake-analytics-data-lake-tools-get-started.md) .

## <a name="create-and-configure-debug-projects"></a>Criar e configurar depuração de projetos

Quando abre uma falha na tarefa na ferramenta de dados Lake Visual Studio, irá obter um alerta. As informações de erro detalhada serão apresentadas no separador de erro e na barra amarela alerta na parte superior da janela. 

![Azure vértice de transferência do visual studio depuração dados Lake Analytics U-SQL](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-download-vertex.png)

**Para transferir o vértice e criar uma solução de depuração**

1.  Abra uma falha na tarefa de U SQL no Visual Studio.
2.  Clique em **Transferir** para transferir todos os recursos necessários e fluxos de entrada. Clique em **Repetir** se a transferência falhou.
3.  Clique em **Abrir** depois de concluída a transferência para criar um projeto de depuração local. Será criada uma nova solução do Visual Studio chamada **VertexDebug** com um projecto vazio denominado **LocalVertexHost** .

Se os operadores definidas pelo utilizador são utilizados no código U SQL atrás (Script.usql.cs), tem de criar um projeto escolar biblioteca c# com o código de operadores definidas pelo utilizador e incluir o projeto na solução VertexDebug.

Se ter registado. dll assemblagem para a base de dados de análise de Lake de dados, tem de adicionar o código fonte de conjuntos de para a solução VertexDebug.
 
Se tiver criado uma separada c# biblioteca de classes para o seu código U SQL e de fluidos. dll registados para a base de dados de análise de Lake de dados, tem de adicionar o projecto c# fonte de conjuntos da solução VertexDebug.

Em alguns casos raros, utilize os operadores de definidas pelo utilizador no código U SQL atrás de ficheiro (Script.usql.cs) da solução original. Se quiser torná-lo a trabalhar, tem de criar uma biblioteca de c# que contém o código de origem e altere o nome da assemblagem para aquele registado no cluster. Pode obter o nome da assemblagem registado no cluster verificando o script que tem a executar no cluster. Pode fazê-lo ao abrir a tarefa de U SQL e clique em "script" no painel de tarefas. 

**Para configurar a solução**

1.  A partir do Explorador de solução, com o botão direito do projecto c# que acabou de criar e, em seguida, clique em **Propriedades**.
2.  Defina o caminho de saída como projeto LocalVertexHost trabalhar caminho do diretório. Pode obter um caminho do directório de trabalho do projecto LocalVertexHost através das propriedades do LocalVertexHost.
3.  Criar o seu projecto c# para colocar o ficheiro. pdb no projeto LocalVertexHost directório de trabalho ou pode copiar o ficheiro. pdb para esta pasta manualmente.
4.  Nas **Definições de exceção**, verifique comuns idioma Runtime exceções:

![Definição do visual studio de depuração de dados Lake Analytics U-SQL Azure](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-clr-exception-setting.png)
 
##<a name="debug-the-job"></a>Depurar a tarefa

Depois de ter criado uma solução de depuração transferindo o vértice e tiver configurado o ambiente, pode começar a depuração do código U-SQL.

1.  A partir do Explorador de solução, com o botão direito do projecto **LocalVertexHost** que acabou de criar, aponte para **Depurar**e, em seguida, clique em **Iniciar a nova instância**. O LocalVertexHost tem de estar definida como o projeto de arranque. Poderá ver a seguinte mensagem pela primeira vez que pode ignorar. Pode demorar um minuto para aceder ao ecrã depuração.
 
    ![Aviso do visual studio de depuração de dados Lake Analytics U-SQL Azure](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-visual-studio-u-sql-debug-warning.png)

4.  Utilizar o Visual Studio com base experiência de depuração (monitorização, variáveis, etc.) para resolver o problema. 
5.  Depois de identificou um problema, corrigir o código e, em seguida, recriar o projeto c# antes de testar novamente, até que todos os problemas são resolvidos. Após a depuração foi concluída com êxito, na janela de saída que mostra a seguinte mensagem 

        The Program ‘LocalVertexHost.exe’ has exited with code 0 (0x0).
 
##<a name="resubmit-the-job"></a>Submeta novamente a tarefa

Depois de concluída a depuração do código U SQL, pode reenviar a tarefa de falha.

1. Registe-se assemblagem de. dll nova base de dados ADLA.

    1.  A partir do servidor Explorer/nuvem Explorer ferramenta dados de Lake Visual Studio, expanda o nó de **bases de dados** 
    2.  Botão direito do rato assemblagem aos conjuntos de registo. 
    3.  Registe-se a sua nova assemblagem de. dll à base de dados ADLA.
 
2.  Ou copie o código c# para script.usql.cs – c# código por trás do ficheiro.
3.  Submeta novamente a tarefa.

##<a name="next-steps"></a>Próximos passos

- [Tutorial: Introdução ao idioma do Azure dados Lake Analytics U-SQL](data-lake-analytics-u-sql-get-started.md)
- [Tutorial: desenvolver scripts de U SQL utilizando ferramentas de Lake de dados para o Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)
- [Desenvolver operadores definidas pelo utilizador de U SQL para trabalhos de análise de Lake de dados do Azure](data-lake-analytics-u-sql-develop-user-defined-operators.md)

