<properties
   pageTitle="Analisar dados no arquivo de dados de Lake utilizando o Power BI | Microsoft Azure"
   description="Utilizar o Power BI para analisar dados armazenados no arquivo de Lake de dados do Azure"
   services="data-lake-store" 
   documentationCenter=""
   authors="nitinme"
   manager="jhubbard"
   editor="cgronlun"/>

<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="10/05/2016"
   ms.author="nitinme"/>

# <a name="analyze-data-in-data-lake-store-by-using-power-bi"></a>Analisar dados no arquivo de dados de Lake utilizando o Power BI

Este artigo vai aprender a utilizar o Power BI Desktop para analisar e visualizar dados armazenados no Azure dados Lake arquivo.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar este tutorial, tem de ter o seguinte procedimento:

- **Azure uma subscrição**. Consulte o artigo [obter Azure versão de avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/).

- **Conta azure dados Lake loja**. Siga as instruções no [artigo Introdução ao arquivo de Lake Azure dados utilizando o Portal do Azure](data-lake-store-get-started-portal.md). Este artigo assume que já criou uma conta de arquivo de Lake de dados, denominada **mybidatalakestore**e carregar um ficheiro de dados de exemplo (**Drivers.txt**) à mesma. Este ficheiro de exemplo está disponível para transferência a partir do [Azure dados Lake Git repositório](https://github.com/Azure/usql/tree/master/Examples/Samples/Data/AmbulanceData/Drivers.txt).

- **Ambiente de trabalho do power BI**. Pode transferi-lo a partir do [Centro de transferências da Microsoft](https://www.microsoft.com/en-us/download/details.aspx?id=45331). 


## <a name="create-a-report-in-power-bi-desktop"></a>Criar um relatório no Power BI Desktop

1. Inicie o Power BI Desktop no seu computador.

2. No friso **base** , clique em **Obter dados**e, em seguida, clique em mais. Na caixa de diálogo **Obter dados** , clique em **Azure**, clique em **Loja do Azure dados Lake**e, em seguida, clique em **Ligar**.

    ![Ligar a dados Lake arquivo] (./media/data-lake-store-power-bi/get-data-lake-store-account.png "Ligar a dados Lake arquivo")

3. Se vir uma caixa de diálogo Acerca do connector a ser numa fase desenvolvimento, optar por permitir para continuar.

4. Na caixa de diálogo **Arquivo de Lake de dados do Microsoft Azure** , fornecer o URL para a sua conta do arquivo de Lake de dados e, em seguida, clique em **OK**.

    ![URL do arquivo de dados de Lake] (./media/data-lake-store-power-bi/get-data-lake-store-account-url.png "URL do arquivo de dados de Lake")

5. Na caixa de diálogo seguinte, clique em **Iniciar sessão** para inicie sessão na conta do arquivo de Lake de dados. Serão redirecionados para página Iniciar da sua organização sessão. Siga os pedidos para iniciar sessão na conta do.

    ![Inicie sessão no arquivo de dados de Lake] (./media/data-lake-store-power-bi/get-data-lake-store-account-signin.png "Inicie sessão no arquivo de dados de Lake")

6. Após ter com êxito sessão, clique em **Ligar**.

    ![Ligar a dados Lake arquivo] (./media/data-lake-store-power-bi/get-data-lake-store-account-connect.png "Ligar a dados Lake arquivo")

7. Caixa de diálogo seguinte mostra o ficheiro que carregou à sua conta do arquivo de Lake de dados. Verifique se as informações e, em seguida, clique em **carregar**.

    ![Carregar dados a partir do arquivo de dados de Lake] (./media/data-lake-store-power-bi/get-data-lake-store-account-load.png "Carregar dados a partir do arquivo de dados de Lake")

8. Depois de terem sido carregados, com êxito, os dados na Power BI, verá os seguintes campos no separador **campos** .

    ![Campos importadas] (./media/data-lake-store-power-bi/imported-fields.png "Campos importadas")

    No entanto, para visualizar e analisar os dados, podemos preferir os dados esteja disponível pelos seguintes campos

    ![Campos desejado] (./media/data-lake-store-power-bi/desired-fields.png "Campos desejado")

    Nos próximos passos, podemos irá atualizar a consulta para converter os dados importados no formato pretendido.

9. No friso **base** , clique em **Editar consultas**.

    ![Editar consultas] (./media/data-lake-store-power-bi/edit-queries.png "Editar consultas")

10. No Editor de consultas, sob a coluna **conteúdo** , clique em **binário**.

    ![Editar consultas] (./media/data-lake-store-power-bi/convert-query1.png "Editar consultas")

11. Irá ver um ícone de ficheiro, que representa o ficheiro de **Drivers.txt** que carregou. Com o botão direito no ficheiro e clique em **CSV**.  

    ![Editar consultas] (./media/data-lake-store-power-bi/convert-query2.png "Editar consultas")

12. Deverá visualizar um resultado conforme apresentado abaixo. Os seus dados estão agora disponíveis num formato que pode utilizar para criar visualizações.

    ![Editar consultas] (./media/data-lake-store-power-bi/convert-query3.png "Editar consultas")

13. No friso **base** , clique em **Fechar e aplicar**e, em seguida, clique em **Fechar e aplicar**.

    ![Editar consultas] (./media/data-lake-store-power-bi/load-edited-query.png "Editar consultas")

14. Assim que a consulta é atualizada, no separador **campos** mostrará nos novos campos disponíveis para visualização.

    ![Campos de atualização] (./media/data-lake-store-power-bi/updated-query-fields.png "Campos de atualização")

15. Diga-na criar um gráfico circular para representar os controladores em cada cidade para um determinado país. Para fazê-lo, selecione o seguinte.

    1. A partir do separador visualizações, clique no símbolo para um gráfico circular.

        ![Gráfico circular de criar] (./media/data-lake-store-power-bi/create-pie-chart.png "Gráfico circular de criar")

    2. As colunas que vamos utilizar são **coluna 4** (nome da cidade) e **coluna 7** (nome do país/região). Arraste estes colunas a partir do separador **campos** para o separador de **visualizações** conforme apresentado abaixo.

        ![Criar visualizações] (./media/data-lake-store-power-bi/create-visualizations.png "Criar visualizações")

    3. Gráfico circular agora deverá assemelhar-se como aquele apresentado abaixo.

        ![Gráfico circular] (./media/data-lake-store-power-bi/pie-chart.png "Criar visualizações")

16. Ao selecionar um país específico dos filtros ao nível de página, agora pode ver o número de controladores em cada cidade do país selecionado. Por exemplo, no separador **visualizações** , em **filtros ao nível da página**, selecione **Brasil**.

    ![Selecione um país/região] (./media/data-lake-store-power-bi/select-country.png "Selecione um país/região")

17. Gráfico circular é atualizado automaticamente para apresentar os controladores as cidades do Brasil.

    ![Controladores num país] (./media/data-lake-store-power-bi/driver-per-country.png "Controladores por país/região")

18. No menu **ficheiro** , clique em **Guardar** para guardar a visualização como um ficheiro do Power BI Desktop.

## <a name="publish-report-to-power-bi-service"></a>Publicar o relatório no serviço do Power BI

Depois de ter criado as visualizações no Power BI Desktop, pode partilhá-lo com outras pessoas ao publicá-lo ao serviço do Power BI. Para obter instruções sobre como fazê-lo, consulte [publicar a partir do Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-upload-desktop-files/).

## <a name="see-also"></a>Consulte também

* [Analisar dados no arquivo de dados de Lake utilizando a análise de dados Lake](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
