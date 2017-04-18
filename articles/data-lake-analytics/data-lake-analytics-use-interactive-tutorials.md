<properties 
   pageTitle="Obter informações sobre a análise de dados Lake e U-SQL com os tutoriais Azure Portal interativos | Azure" 
   description="Guia de introdução com a aprendizagem Lake a análise de dados e U-SQL. " 
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


# <a name="use-azure-data-lake-analytics-interactive-tutorials"></a>Utilizar a tutoriais interativos Azure dados Lake Analytics

Portal do Azure fornece um tutorial interativo que pode começar a trabalhar com dados Lake Analytics. Este artigo mostra-lhe como seguir o tutorial, para analisar os registos de Web site.


>[AZURE.NOTE]Se pretender seguir o mesmo tutorial utilizando o Visual Studio, consulte o artigo [analisar registos de Web site utilizando a análise de dados Lake](data-lake-analytics-analyze-weblogs.md).
>Tutoriais mais interativos a serem adicionados ao portal.


Para outros tutoriais, consulte:

- [Começar a trabalhar com dados Lake Analytics através do Portal do Azure](data-lake-analytics-get-started-portal.md)
- [Começar a trabalhar com dados Lake Analytics através do PowerShell do Azure](data-lake-analytics-get-started-powershell.md)
- [Introdução ao .NET SDK a utilizar a análise de dados Lake](data-lake-analytics-get-started-net-sdk.md)
- [Desenvolver scripts de U SQL utilizando ferramentas de Lake de dados para o Visual Studio](data-lake-analytics-data-lake-tools-get-started.md) 

**Pré-requisitos**

Antes de começar este tutorial, tem de ter o seguinte procedimento:

- **Análise de Lake de dados A conta**.  Consulte o artigo [Introdução ao Azure dados Lake Analytics através do Portal do Azure](data-lake-analytics-get-started-portal.md).

##<a name="create-data-lake-analytics-account"></a>Criar dados Lake Analytics conta 

Tem de ter uma conta de análise de Lake dados antes de poder executar quaisquer tarefas.

Cada conta dados Lake Analytics tem uma dependência de conta [Azure dados Lake loja](../data-lake-store/data-lake-store-overview.md) .  Esta conta é referida como conta predefinida dados Lake loja.  Pode criar a conta de arquivo de Lake dados prévia ou quando cria a sua conta de dados Lake Analytics. Neste tutorial, irá criar a conta de arquivo de Lake de dados com a conta de análise

**Para criar uma conta de dados Lake Analytics**

1. Ao iniciar sessão no [Portal do Azure](https://portal.azure.com/signin/index/?Microsoft_Azure_Kona=true&Microsoft_Azure_DataLake=true&hubsExtension_ItemHideKey=AzureDataLake_BigStorage%2cAzureKona_BigCompute).
2. Clique em **Microsoft Azure** no canto superior esquerdo para abrir o StartBoard.
3. Clique no mosaico **Marketplace** .  
3. Escreva **Azure dados Lake Analytics** na caixa de pesquisa no pá **Tudo** e, seguida, prima **ENTER**. Deve o **Azure dados Lake Analytics** na lista.
4. Clique em **Análise de Lake Azure dados** a partir da lista.
5. Clique em **Criar** na parte inferior da pá.
6. Escreva ou selecione o seguinte:

    ![Pá portal do Azure dados Lake Analytics](./media/data-lake-analytics-get-started-portal/data-lake-analytics-portal-create-adla.png)

    - **Nome**: nome da conta de análise.
    - **Arquivo de dados de Lake**: conta de cada dados Lake Analytics tem uma conta de arquivo de Lake dados dependente. A conta de análise de Lake de dados e a conta de arquivo de Lake dados dependente têm de estar localizados no Centro de dados Azure do mesmo. Siga as instruções para criar uma nova conta do arquivo de Lake dados ou selecione uma existente.
    - **Subscrição**: selecione a subscrição Azure utilizada para a conta de análise.
    - **Grupo de recursos**. Selecione um grupo de recursos do Azure existente ou crie um novo. Aplicações são normalmente constituídas por vários componentes, por exemplo uma aplicação web, base de dados, servidor de base de dados, armazenamento e 3º serviços de terceiros. Gestor de recursos Azure (processador) permite-lhe trabalhar com os recursos na sua aplicação como um grupo, designado de um grupo de recursos do Azure. Pode implementar, atualizar, monitorizar ou eliminar todos os recursos para a sua aplicação numa operação de única e coordenada. Utilizar um modelo para implementação e esse modelo pode ser útil ambientes diferentes, tais como teste, teste e de produção. Pode clarificar faturação da sua organização, veja os custos de agregadas para todo o grupo. Para mais informações, consulte o artigo [Descrição geral do Gestor de recursos do Azure](azure-resource-manager/resource-group-overview.md). 
    - **Localização**. Selecione um centro de dados Azure para a conta de análise de Lake de dados. 
7. Selecione **Afixar Startboard**. Isto é necessário para seguir este tutorial.
8. Clique em **Criar**. Leva-o para o portal StartBoard. Um novo mosaico é adicionado à Home page com a etiqueta que mostra "Implementar o Azure dados Lake Analytics". Bastam alguns minutos para criar uma conta de análise de Lake de dados. Quando a conta estiver criada, o portal abre-se na conta no pá novo.

    ![Pá portal do Azure dados Lake Analytics](./media/data-lake-analytics-get-started-portal/data-lake-analytics-portal-blade.png)

##<a name="run-website-log-analysis-interactive-tutorial"></a>Executar tutorial interativo de análise de registo do Web site

**Para abrir o tutorial interactivo de análise de registo do Web site**

1. A partir do Portal de, clique em **Microsoft Azure** a partir do menu da esquerda para abrir o StartBoard.
2. Clique no mosaico que está ligado à sua conta de análise de Lake de dados.
3. Clique em **explorar interativos tutoriais** a partir da barra de **Essentials** .

    ![Tutoriais interativos de análise de dados Lake](./media/data-lake-analytics-use-interactive-tutorials/data-lake-analytics-explore-interactive-tutorials.png)

4. Se vir um cor de laranja indicando de aviso "em não configurar, clique em … amostras", clique em **Copiar dados de exemplo** para copiar os dados de exemplo para a conta de dados Lake arquivo predefinida. O tutorial interactivo tem os dados para executar.
5. Pá **Tutoriais interativo** , clique em **Análise de registo do Web site**. O portal abre o tutorial numa nova pá portal.
5. Clique em **1 Introdução** e, em seguida, siga as instruções

##<a name="see-also"></a>Consulte também

- [Descrição geral da análise Lake de dados do Microsoft Azure](data-lake-analytics-overview.md)
- [Começar a trabalhar com dados Lake Analytics através do Portal do Azure](data-lake-analytics-get-started-portal.md)
- [Começar a trabalhar com dados Lake Analytics através do PowerShell do Azure](data-lake-analytics-get-started-powershell.md)
- [Desenvolver scripts de U SQL utilizando ferramentas de Lake de dados para o Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)
- [Analisar os registos de Web site utilizando a análise do Azure dados Lake](data-lake-analytics-analyze-weblogs.md)
