<properties
   pageTitle="Registar dados a partir do arquivo de dados de Lake no catálogo de dados do Azure | Microsoft Azure"
   description="Registar dados a partir do arquivo de dados de Lake no catálogo de dados do Azure"
   services="data-lake-store,data-catalog" 
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
   ms.date="10/28/2016"
   ms.author="nitinme"/>

# <a name="register-data-from-data-lake-store-in-azure-data-catalog"></a>Registar dados a partir do arquivo de dados de Lake no catálogo de dados do Azure

Este artigo vai aprender integrar o arquivo de Lake Azure dados com o catálogo de dados do Azure para tornar os seus dados detetável dentro de uma organização por integrá-lo com o catálogo de dados. Para mais informações sobre dados catalogação, consulte o artigo [Azure catálogo de dados](../data-catalog/data-catalog-what-is-data-catalog.md). Para compreender cenários em que pode utilizar o catálogo de dados, consulte o artigo [cenários comuns de catálogo de dados do Azure](../data-catalog/data-catalog-common-scenarios.md).

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar este tutorial, tem de ter o seguinte procedimento:

- **Azure uma subscrição**. Consulte o artigo [obter Azure versão de avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/).

- **Ativar a sua subscrição do Azure** para dados Lake arquivo público pré-visualização. Consulte as [instruções](data-lake-store-get-started-portal.md#signup).

- **Conta azure dados Lake loja**. Siga as instruções no [artigo Introdução ao arquivo de Lake Azure dados utilizando o Portal do Azure](data-lake-store-get-started-portal.md). Para este tutorial, diga-nos crie uma conta de arquivo de Lake de dados denominada **datacatalogstore**. 

    Depois de ter criado a conta, carregar um conjunto de dados de exemplo-lo. Para este tutorial, diga-nos carregar todos os ficheiros. csv na pasta **AmbulanceData** no [Azure dados Lake Git repositório](https://github.com/Azure/usql/tree/master/Examples/Samples/Data/AmbulanceData/). Pode utilizar vários clientes, tal como [O Explorador de armazenamento do Azure](http://storageexplorer.com/), para carregar os dados para um contentor blob.

- **Catálogo de dados azure**. Sua organização já tem de ter um catálogo de dados do Azure criado para a sua organização. Catálogo de apenas uma é permitido para cada organização.

## <a name="register-data-lake-store-as-a-source-for-data-catalog"></a>Arquivo de Lake do registo de dados como uma origem de catálogo de dados

>[AZURE.VIDEO adcwithadl] 

1. Aceda a `https://azure.microsoft.com/services/data-catalog`e clique em **começar**.

2. Inicie sessão no portal do catálogo de dados do Azure e clique em **publicar dados**.

    ![Registar uma origem de dados] (./media/data-lake-store-with-data-catalog/register-data-source.png "Registar uma origem de dados")

3. Na página seguinte, clique em **Iniciar aplicação**. Isto irá transferir o ficheiro de manifesto de aplicação no seu computador. Faça duplo clique no ficheiro manifesto para iniciar a aplicação.

4. Na página de boas-vindas, clique em **Iniciar sessão**e introduza as suas credenciais.

    ![Ecrã de boas-vindas] (./media/data-lake-store-with-data-catalog/welcome.screen.png "Ecrã de boas-vindas")

5. Na página selecionar um origem de dados, selecione **Lake de dados do Azure**e, em seguida, clique em **seguinte**.

    ![Selecione a origem de dados] (./media/data-lake-store-with-data-catalog/select-source.png "Selecione a origem de dados")

6. Na página seguinte, fornece o nome de conta do arquivo de Lake de dados que pretende para se registar no catálogo de dados. Deixe as outras opções como sendo o predefinido e, em seguida, clique em **Ligar**.

    ![Ligar a origem de dados] (./media/data-lake-store-with-data-catalog/connect-to-source.png "Ligar a origem de dados")

7. A página seguinte pode ser dividida nos seguintes segmentos de.

    um. A caixa de **Hierarquia de servidor** representa a estrutura de pastas de conta do arquivo de Lake de dados. **$Root** representa a raiz de conta do arquivo de Lake de dados e **AmbulanceData** representa a pasta criada na raiz da conta de arquivo de Lake de dados.

    b. A caixa de **objetos disponíveis** lista os ficheiros e pastas na pasta **AmbulanceData** .

    c. **Objetos à caixa registada possível** lista os ficheiros e pastas que pretende para se registar no catálogo de dados do Azure.

    ![Ver a estrutura de dados] (./media/data-lake-store-with-data-catalog/view-data-structure.png "Ver a estrutura de dados")

8. Para este tutorial, deverá registar todos os ficheiros no diretório. Para que, clique no botão (![Mover objetos](./media/data-lake-store-with-data-catalog/move-objects.png "Mover objetos")) para mover todos os ficheiros para a caixa de **objetos sejam registados** . 

    Uma vez que os dados serão registados num catálogo de dados de toda a organização, é uma abordagem de recomendado para adicionar alguns metadados que pode utilizar mais tarde para localizar rapidamente os dados. Por exemplo, pode adicionar um endereço de e-mail para o proprietário de dados (por exemplo, um que está a carregar os dados) ou adicionar uma etiqueta para identificar os dados. A captura de ecrã abaixo mostra uma etiqueta que recomendamos adicionar aos dados.

    ![Ver a estrutura de dados] (./media/data-lake-store-with-data-catalog/view-selected-data-structure.png "Ver a estrutura de dados")

    Clique em **Registe-se**.

8. A seguinte captura de ecrã indica se os dados com êxito estão registados no catálogo de dados.

    ![Registo concluído] (./media/data-lake-store-with-data-catalog/registration-complete.png "Ver a estrutura de dados")

9. Clique em **Vista de Portal** para regressar ao portal do catálogo de dados e certifique-se de que já pode aceder os dados registadas a partir do portal. Para procurar os dados, pode utilizar a etiqueta que utilizou ao registar os dados.

    ![Dados de pesquisa no catálogo] (./media/data-lake-store-with-data-catalog/search-data-in-catalog.png "Dados de pesquisa no catálogo")

10. Pode agora executar operações como adicionar anotações e documentação aos dados. Para obter mais informações, consulte as ligações seguintes.
    * [Anotar origens de dados no catálogo de dados](../data-catalog/data-catalog-how-to-annotate.md)
    * [Origens de dados do documento no catálogo de dados](../data-catalog/data-catalog-how-to-documentation.md)

## <a name="see-also"></a>Consulte também

* [Anotar origens de dados no catálogo de dados](../data-catalog/data-catalog-how-to-annotate.md)
* [Origens de dados do documento no catálogo de dados](../data-catalog/data-catalog-how-to-documentation.md)
* [Integrar o arquivo de dados de Lake com outros serviços do Azure](data-lake-store-integrate-with-other-services.md)
