<properties 
   pageTitle="Descrição geral da análise do Microsoft Azure dados Lake | Azure" 
   description="Lake a análise de dados é um serviço de cálculo do Azure Big Data que permite-lhe utilizar dados para orientar o seu negócio com informações adquirida com os seus dados na nuvem, independentemente de onde está e independentemente do respetivo tamanho. A análise de dados Lake permite isto no mais simples, maneira mais dimensionável e mais económica possível. " 
   services="data-lake-analytics" 
   documentationCenter="" 
   authors="edmacauley" 
   manager="jhubbard" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-analytics"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="05/16/2016"
   ms.author="edmaca"/>

# <a name="overview-of-microsoft-azure-data-lake-analytics"></a>Descrição geral da análise Lake de dados do Microsoft Azure

## <a name="what-is-azure-data-lake-analytics"></a>O que é o Azure dados Lake Analytics?

Azure dados Lake Analytics é um novo serviço, construído para facilitar a análise de dados grande. Serviço permite-focar escrever, executar e gerir tarefas em vez de operativo infraestrutura distribuída. Em vez de implementação, configuração e sintonização do hardware, escrever consultas para transformar os dados e extrair informações importantes. O serviço analytics pode processar tarefas de qualquer escala instantaneamente definindo simplesmente o teclado para o quantidade power precisar. Apenas pagar para o trabalho quando estiver em execução tornando rentável. O serviço de análise suporta Azure Active Directory, permitindo-lhe gerir simplesmente o acesso e funções, integradas com o seu sistema de identidade no local. Também inclui U-SQL, um idioma que une os benefícios da SQL com a potência modelar do código de utilizador. Runtime de distribuído dimensionável U-SQL permite-lhe eficazmente analisar os dados na loja e nos servidores de SQL do Azure, base de dados do SQL Azure e armazém de dados do SQL Azure.


## <a name="key-capabilities"></a>Capacidades de chaves

- **Dimensionamento dinâmicos** 

    Lake a análise de dados é concepção do terreno para a nuvem escala e o desempenho.  Dinamicamente disposições recursos e permite-lhe fazer a análise no terabytes ou até mesmo exabytes de dados. Quando a tarefa estiver concluída,-ventos para baixo recursos automaticamente e pagar apenas para o poder de processamento utilizado. À medida que aumente ou diminua o tamanho dos dados armazenados ou a quantidade de cluster utilizado, não tem de reescrever código. Este permite-lhe dedicar a lógica empresarial apenas e não como processar e armazenar grandes conjuntos de dados. 

- **Desenvolver mais rápido, depurar e otimizar forma inteligente com ferramentas familiares**

    A análise de dados Lake tem abrangente integração com o Visual Studio, para que possa utilizar ferramentas familiares para executar, depurar e ajustar o seu código. Visualizações de tarefas U SQL de permitem-lhe ver a forma como o seu código é executado em escala, para que possa identificar facilmente desempenho congestionamentos e otimizar os custos. 

- **U-SQL: simples e familiar, extensible e avançada**

    A análise de dados Lake inclui U-SQL, um idioma de consulta que se expande da natureza familiar, simple, declarativa do SQL com a potência modelar c#. Linguagem U SQL é criada com base em runtime distribuído mesmo que oferece os sistemas de dados grande dentro da Microsoft. Milhões de programadores SQL e .NET agora podem processar e analisar todos os seus dados com as competências que já tenham.

- **Integra forma totalmente integrada com o seu investimentos de TI**

    Lake a análise de dados pode utilizar o seu investimentos existentes em TI para identidade, gestão, segurança e armazenamento de dados. Isto simplifica governação de dados e torna mais fácil expandir as suas aplicações de dados atual. Lake a análise de dados está integrada com o Active Directory para gestão de utilizadores e permissões e vem com num monitorização e auditorias.

- **Acessível e rentável**

    Lake a análise de dados é uma solução rentável para executar o cargas de trabalho de dados grande. Pode pagar numa base por tarefa quando os dados são processados. Sem hardware, licenças ou acordos específico do serviço de suporte são necessários. O sistema automaticamente escalas para cima ou para baixo como a tarefa é iniciada e for concluída, que significa que nunca pagar para mais do que o que precisa. 

- **Funciona com todos os seus dados do Azure**

    A análise de dados Lake pode trabalhar com um número de origens de dados Azure: armazenamento de Blobs do Azure, base de dados Azure SQL e obviamente Lake a análise de dados é especialmente otimizado para funcionar com o Azure dados Lake Store - fornecer o nível mais alto de desempenho, débito e parallelization por si cargas de trabalho de dados grande.

## <a name="see-also"></a>Consulte também

- Introdução
    - [Começar a trabalhar com dados Lake Analytics através do Portal do Azure](data-lake-analytics-get-started-portal.md)
    - [Começar a trabalhar com dados Lake Analytics através do PowerShell do Azure](data-lake-analytics-get-started-powershell.md)
    - [Introdução ao Azure .NET SDK a utilizar a análise de dados Lake](data-lake-analytics-get-started-net-sdk.md)
    - [Desenvolver scripts de U SQL utilizando ferramentas de Lake de dados para o Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)
    - [Introdução ao idioma do Azure dados Lake Analytics U-SQL](data-lake-analytics-u-sql-get-started.md)
    
- U SQL e desenvolvimento
    - [Introdução ao idioma do Azure dados Lake Analytics U-SQL](data-lake-analytics-u-sql-get-started.md)
    - [Utilize funções de janela U-SQL para tarefas do Azure dados Lake Analytics](data-lake-analytics-use-window-functions.md)
    - [Desenvolver operadores de definidas pelo utilizador U SQL para trabalhos de dados Lake Analytics](data-lake-analytics-u-sql-develop-user-defined-operators.md)
    
- Gestão
    - [Gerir Azure dados Lake Analytics através do Portal do Azure](data-lake-analytics-manage-use-portal.md)
    - [Gerir Azure dados Lake Analytics através do PowerShell do Azure](data-lake-analytics-manage-use-powershell.md)
    - [Monitorizar e resolver problemas de tarefas do Azure dados Lake Analytics através do Portal do Azure](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)
    - [Aceder a registos de diagnóstico para análise do Azure dados Lake](data-lake-analytics-diagnostic-logs.md)

- Tutorial do ponto a ponto
    - [Utilizar a tutoriais interativos Azure dados Lake Analytics](data-lake-analytics-use-interactive-tutorials.md)
    - [Analisar os registos de Web site utilizando a análise do Azure dados Lake](data-lake-analytics-analyze-weblogs.md)

- Saber diga-na sua opinião
    - [Comentar nosso registo de tarefas pendentes documentação](data-lake-analytics-documentation-backlog.md)
    - [Submeter um pedido de funcionalidade](http://aka.ms/adlafeedback)
    - [Obter ajuda nos fóruns do](http://aka.ms/adlaforums)


