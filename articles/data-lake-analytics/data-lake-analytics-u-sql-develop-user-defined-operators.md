<properties 
   pageTitle="Desenvolver operadores definidas pelo utilizador de U SQL para trabalhos de análise do Azure dados Lake | Azure" 
   description="Obter informações sobre como desenvolver operadores definidas pelo utilizador para ser utilizado e reutilização em trabalhos de análise de Lake de dados. " 
   services="data-lake-analytics" 
   documentationCenter="" 
   authors="edmacauley" 
   manager="jhubbard" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-analytics"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="05/16/2016"
   ms.author="edmaca"/>


# <a name="develop-u-sql-user-defined-operators-for-azure-data-lake-analytics-jobs"></a>Desenvolver operadores definidas pelo utilizador de U SQL para trabalhos de análise de Lake de dados do Azure

Obter informações sobre como desenvolver operadores definidas pelo utilizador para ser utilizado e reutilização em trabalhos de análise de Lake de dados. Irá desenvolver um operador personalizado para converter os nomes de país/região.

##<a name="prerequisites"></a>Pré-requisitos

- Visual Studio 2015, Visual Studio 2013 actualizar 4 ou Visual Studio 2012http com Visual C++ instalado 
- Microsoft Azure SDK para .NET versão 2.5 ou acima.  Instalá-lo a utilizar o instalador do plataforma Web.
- Uma conta de análise de Lake de dados.  Consulte o artigo [Introdução ao Azure dados Lake Analytics através do Portal do Azure](data-lake-analytics-get-started-portal.md).
- Percorra o tutorial de [Introdução ao Azure dados Lake análise U SQL Studio](data-lake-analytics-u-sql-get-started.md) .
- Ligar a Azure, consulte o artigo [Introdução ao Azure dados Lake análise U SQL Studio](data-lake-analytics-u-sql-get-started.md#connect-to-azure). 
- Carregar a origem de dados, consulte o artigo [Introdução ao Azure dados Lake análise U SQL Studio](data-lake-analytics-u-sql-get-started.md#upload-source-data-files). 

## <a name="define-and-use-user-defined-operator-in-u-sql"></a>Definir e utilizar o operador definidas pelo utilizador no U SQL

**Para criar e submeter uma tarefa de U SQL** 

1. No menu **ficheiro** , clique em **Novo**e, em seguida, clique em **projeto**.
2. Selecione o tipo de **Projecto U-SQL** .

    ![novo projeto do Visual Studio de U SQL](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-new-project.png)

3. Clique em **OK**. Visual studio cria uma solução com um ficheiro de Script.usql.
4. A partir do **Explorador de soluções**, expanda Script.usql e, em seguida, faça duplo clique em **Script.usql.cs**.
5. Cole o código seguinte no ficheiro:

        using Microsoft.Analytics.Interfaces;
        using System.Collections.Generic;
        
        namespace USQL_UDO
        {
            public class CountryName : IProcessor
            {
                private static IDictionary<string, string> CountryTranslation = new Dictionary<string, string>
                {
                    {
                        "Deutschland", "Germany"
                    },
                    {
                        "Schwiiz", "Switzerland"
                    },
                    {
                        "UK", "United Kingdom"
                    },
                    {
                        "USA", "United States of America"
                    },
                    {
                        "中国", "PR China"
                    }
                };
        
                public override IRow Process(IRow input, IUpdatableRow output)
                {
        
                    string UserID = input.Get<string>("UserID");
                    string Name = input.Get<string>("Name");
                    string Address = input.Get<string>("Address");
                    string City = input.Get<string>("City");
                    string State = input.Get<string>("State");
                    string PostalCode = input.Get<string>("PostalCode");
                    string Country = input.Get<string>("Country");
                    string Phone = input.Get<string>("Phone");
        
                    if (CountryTranslation.Keys.Contains(Country))
                    {
                        Country = CountryTranslation[Country];
                    }
                    output.Set<string>(0, UserID);
                    output.Set<string>(1, Name);
                    output.Set<string>(2, Address);
                    output.Set<string>(3, City);
                    output.Set<string>(4, State);
                    output.Set<string>(5, PostalCode);
                    output.Set<string>(6, Country);
                    output.Set<string>(7, Phone);
        
                    return output.AsReadOnly();
                }
            }
        }

5. Abra o Script.usql e cole o seguinte script U SQL:

        @drivers =
            EXTRACT UserID      string,
                    Name        string,
                    Address     string,
                    City        string,
                    State       string,
                    PostalCode  string,
                    Country     string,
                    Phone       string
            FROM "/Samples/Data/AmbulanceData/Drivers.txt"
            USING Extractors.Tsv(Encoding.Unicode);
        
        @drivers_CountryName =
            PROCESS @drivers
            PRODUCE UserID string,
                    Name string,
                    Address string,
                    City string,
                    State string,
                    PostalCode string,
                    Country string,
                    Phone string
            USING new USQL_UDO.CountryName();    
        
        OUTPUT @drivers_CountryName
            TO "/Samples/Outputs/Drivers.csv"
            USING Outputters.Csv(Encoding.Unicode);

6. **Solução Explorer**, clique com o botão direito do rato em **Script.usql**e, em seguida, clique em **Criar Script**.
6. **Solução Explorer**, clique com o botão direito do rato em **Script.usql**e, em seguida, clique em **Submeter Script**.
7. Se ainda não ligar à sua subscrição do Azure, será pedido introduzir as credenciais de conta Azure.
7. Clique em **Submeter**. Resultados de submissão e ligação de tarefa estão disponíveis na janela resultados quando a apresentação seja concluída.
8. Tem de clicar no botão Atualizar para ver o estado da tarefa mais recente e atualizar o ecrã.

**Para ver o resultado da tarefa**

1. A partir do **Explorador do servidor**, expanda **Azure**, expanda **Lake a análise de dados**, expandir a sua conta de dados Lake Analytics, expandir **Contas de armazenamento**, com o botão direito do armazenamento predefinido e, em seguida, clique em **Explorador**. 
2. Expanda amostras, expanda saídas e, em seguida, faça duplo clique em **CSV**.


##<a name="see-also"></a>Consulte também

- [Começar a trabalhar com dados Lake Analytics através do PowerShell](data-lake-analytics-get-started-powershell.md)
- [Começar a trabalhar com dados Lake Analytics através do portal Azure](data-lake-analytics-get-started-portal.md)
- [Utilizar dados Lake ferramentas para o Visual Studio para desenvolver aplicações U SQL](data-lake-analytics-data-lake-tools-get-started.md)
