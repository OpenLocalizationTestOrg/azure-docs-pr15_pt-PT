<properties
   pageTitle="Ligações de origem de dados | Microsoft Azure"
   description="Descreve as ligações de origem de dados para modelos de dados no Azure Analysis Services."
   services="analysis-services"
   documentationCenter=""
   authors="minewiskan"
   manager="erikre"
   editor=""
   tags=""/>
<tags
   ms.service="analysis-services"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="na"
   ms.date="10/25/2016"
   ms.author="owend"/>

# <a name="datasource-connections"></a>Ligações de origem de dados

Modelos de dados no Azure Analysis Services precisem de fornecedores de dados diferente ao ligar a determinadas origens de dados. Em alguns casos, modelos em tabela ligar a origens de dados com fornecedores nativos como SQL Server Native Client (SQLNCLI11) podem devolver um erro.

Por exemplo; Se tiver na memória ou consulta direta do modelo de dados que se liga a uma origem de dados na nuvem como base de dados SQL Azure, se utilizar nativos fornecedores que não SQLOLEDB, poderá ver a mensagem de erro: **"O fornecedor de 'SQLNCLI11.1' não está registado"**.

Ou, se tiver um modelo de DirectQuery ligar a origens de dados no local, se utilizar fornecedores nativos poderá ver a mensagem de erro: **"Erro ao criar o conjunto de linha OLE DB. Sintaxe incorreta perto 'Limite' "**.

## <a name="data-source-providers"></a>Fornecedores de origem de dados

Os seguintes fornecedores de origem de dados são suportados para na memória ou direcionar modelos de dados de consulta quando se liga no local ou na nuvem origens de dados:

|               | **Origem de dados**                     | **Na memória**                            |  **Consulta direta**                                           |
|---------------------------|-------------------------------|---------------------------------------------|---------------------------------------------|
| **Nuvem**                     | Armazém de dados do Azure SQL      | Fornecedor de dados do .NET framework para SQL Server | Fornecedor de dados do .NET framework para SQL Server |
|                           | Base de dados Azure SQL            | Fornecedor de dados do .NET framework para SQL Server | Fornecedor de dados do .NET framework para SQL Server |
| **No local** (através do Gateway) | Do SQL Server                    | SQL Server Native Client 11.0               | Fornecedor de dados do .NET framework para SQL Server |
|                           |  Do SQL Server                             | Fornecedor de Microsoft OLE DB para SQL Server    |   Fornecedor de dados do .NET framework para SQL Server                                          |
|                           |  Do SQL Server                             | Fornecedor de dados do .NET framework para SQL Server |  Fornecedor de dados do .NET framework para SQL Server                                           |
|                           | Oracle                        | Fornecedor OLE DB da Microsoft para Oracle        | Fornecedor de dados Oracle para .NET               |
|                           |  Oracle                             | Fornecedor de dados Oracle para .NET               | Fornecedor de dados Oracle para .NET                                            |
|                           | Teradata                      | Fornecedor OLE DB para Teradata                | Fornecedor de dados do Teradata para .NET             |
|                           |  Teradata                             | Fornecedor de dados do Teradata para .NET             |  Fornecedor de dados do Teradata para .NET                                            |
|                           | Sistema de plataforma de análise | Fornecedor de dados do .NET framework para SQL Server | Fornecedor de dados do .NET framework para SQL Server |


> [AZURE.NOTE] Certifique-se de que os fornecedores de 64 bits instalados quando utilizar o Gateway no local.

Quando migrar um modelo de tabela do SQL Server Analysis Services no local para o Azure Analysis Services, poderá ser necessário alterar o fornecedor.

**Para especificar um fornecedor de origem de dados**

1. No SSDT > **Explorador de modelos tabulares** > **Origens de dados**, uma ligação de origem de dados com o botão direito e, em seguida, clique em **Editar origem de dados**.

2. Em **Editar ligação**, clique em **Avançadas** para abrir a janela de propriedades avançadas.

3. Em **Definir propriedades avançadas** > **fornecedores**, em seguida, selecione o fornecedor de apropriado.

## <a name="impersonation"></a>Representação
Em alguns casos, poderá ser necessário especificar uma conta diferente de representação. Pode especificar a conta de representação SSDT ou SSMS.

Para origens de dados no local:

- Se utilizar a autenticação de SQL, representação deve ser conta de serviço.
- Se utilizar a autenticação do Windows, defina o Windows utilizador/palavra-passe. Para o SQL Server, autenticação do Windows com uma conta de representação específicas é suportada apenas na memória para modelos de dados.

Para origens de dados da nuvem:

- Se utilizar a autenticação de SQL, representação deve ser conta de serviço.


## <a name="next-steps"></a>Próximos passos

Se tiver de origens de dados no local, certifique-se de que instala o [gateway no local](analysis-services-gateway.md). Para saber mais sobre como gerir o seu servidor SSDT ou SSMS, consulte o artigo [Gerir o seu servidor](analysis-services-manage.md).
