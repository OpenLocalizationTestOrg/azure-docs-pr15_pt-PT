<properties
   pageTitle="Introdução ao arquivo de Lake de dados utilizando a interface de linha de comandos em diferentes plataformas | Microsoft Azure"
   description="Utilize o Azure linha de comandos em diferentes plataformas para criar uma conta do arquivo de Lake de dados e executar operações básicas"
   services="data-lake-store"
   documentationCenter=""
   authors="nitinme"
   manager="jhubbard"
   editor="cgronlun"/>

<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="09/27/2016"
   ms.author="nitinme"/>

# <a name="get-started-with-azure-data-lake-store-using-azure-command-line"></a>Introdução ao Azure dados Lake loja utilizando linha de comandos do Azure

> [AZURE.SELECTOR]
- [Portal](data-lake-store-get-started-portal.md)
- [PowerShell](data-lake-store-get-started-powershell.md)
- [.NET SDK](data-lake-store-get-started-net-sdk.md)
- [Java SDK](data-lake-store-get-started-java-sdk.md)
- [REST API](data-lake-store-get-started-rest-api.md)
- [Clip Azure](data-lake-store-get-started-cli.md)
- [NODE.js](data-lake-store-manage-use-nodejs.md)

Saiba como utilizar Azure interface de linha de comandos para criar uma conta do arquivo de Lake de dados do Azure e executar operações básicas, tais como criar pastas, carregar e transferir ficheiros de dados, eliminar a sua conta, etc. Para mais informações sobre o arquivo de dados de Lake, consulte o artigo [Descrição geral dos dados Lake loja](data-lake-store-overview.md).

O clip do Azure está implementada Node.js. Pode ser utilizada em nenhuma plataforma que suporte Node.js, incluindo o Windows, Mac e Linux. O clip do Azure é abrir origem. O código de origem é gerido no GitHub na <a href= "https://github.com/azure/azure-xplat-cli">https://github.com/azure/azure-xplat-cli</a>. Este artigo aborda a utilizar apenas o clip do Azure com o arquivo de dados de Lake. Para um guia Geral sobre como utilizar o Azure clip, consulte o artigo [como utilizar o clip do Azure] [azure-command-line-tools].


##<a name="prerequisites"></a>Pré-requisitos

Antes de começar este artigo, tem de ter o seguinte procedimento:

- **Azure uma subscrição**. Consulte o artigo [obter Azure versão de avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/).

- **Clip do azure** - consulte [instalar e configurar o clip do Azure](../xplat-cli-install.md) para obter informações de instalação e configuração. Certifique-se de que reiniciar o computador depois de instalar o clip.

## <a name="authentication"></a>Autenticação

Este artigo utiliza uma abordagem de autenticação mais simples com o arquivo de dados de Lake onde inicia a sessão como um utilizador do utilizador final. O nível de acesso ao arquivo de dados de Lake sistema de ficheiros de conta e, em seguida, é regulado pelo nível de acesso do utilizador com sessão iniciada. No entanto, existem outras abordagens também para autenticar com dados Lake loja, que são **autenticação de utilizador final** ou do **serviço de serviço**. Para obter instruções e mais informações sobre como autenticar, consulte o artigo [autenticar com o arquivo de dados de Lake utilizando o Azure Active Directory](data-lake-store-authenticate-using-active-directory.md).

##<a name="login-to-your-azure-subscription"></a>Início de sessão à sua subscrição do Azure

1. Siga os passos em [ligar a uma subscrição do Azure a partir da linha de comandos Interface o Azure (Azure CLI)](../xplat-cli-connect.md) -se documentados e ligar a sua subscrição através de `azure login` método.

2. Lista as subscrições que estão associadas a sua conta utilizando o `azure account list` comando.

        info:    Executing command account list
        data:    Name              Id                                    Current
        data:    ----------------  ------------------------------------  -------
        data:    Azure-sub-1       ####################################  true
        data:    Azure-sub-2       ####################################  false

    A partir de saída acima, **Azure-sub-1** atualmente está ativado e a outra subscrição é **Azure-sub-2**. 

3. Selecione a subscrição que pretende trabalhar em. Se pretende trabalhar na subscrição do Azure-sub-2, utilize o `azure account set` comando.

        azure account set Azure-sub-2


## <a name="create-an-azure-data-lake-store-account"></a>Criar uma conta do arquivo de Lake de dados do Azure

Abra uma linha de comandos, shell ou uma sessão de terminal e execute os seguintes comandos.

2. Mudar para o modo de Gestor de recursos do Azure utilizando o seguinte comando:

        azure config mode arm


5. Crie um novo grupo de recursos. No seguinte comando, forneça os valores de parâmetro que pretende utilizar.

        azure group create <resourceGroup> <location>

    Se o nome da localização contiver espaços, coloque-o na propostas. Por exemplo "Leste dos EUA 2".

5. Crie a conta de arquivo de Lake de dados.

        azure datalake store account create <dataLakeStoreAccountName> <location> <resourceGroup>

## <a name="create-folders-in-your-data-lake-store"></a>Criar pastas no seu arquivo de dados de Lake

Pode criar pastas sob a sua conta Azure dados Lake arquivo para gerir e armazenar dados. Utilize o seguinte comando para criar uma pasta denominada "mynewfolder" na raiz do arquivo de Lake de dados.

    azure datalake store filesystem create <dataLakeStoreAccountName> <path> --folder

Por exemplo:

    azure datalake store filesystem create mynewdatalakestore /mynewfolder --folder

## <a name="upload-data-to-your-data-lake-store"></a>Carregar dados para o arquivo de dados de Lake

Pode carregar os seus dados ao arquivo de dados de Lake diretamente no nível de raiz ou para uma pasta que criou dentro da conta. Os fragmentos abaixo demonstram como carregar alguns dados de exemplo para a pasta (**mynewfolder**) que criou na secção anterior.

Se está a procurar alguns dados de exemplo carregar, pode obter a pasta **Ambulância dados** a partir do [Azure dados Lake Git repositório](https://github.com/MicrosoftBigData/usql/tree/master/Examples/Samples/Data/AmbulanceData). Transferir o ficheiro e armazená-lo num diretório local no seu computador, tal como C:\sampledata\.

    azure datalake store filesystem import <dataLakeStoreAccountName> "<source path>" "<destination path>"

Por exemplo:

    azure datalake store filesystem import mynewdatalakestore "C:\SampleData\AmbulanceData\vehicle1_09142014.csv" "/mynewfolder/vehicle1_09142014.csv"


## <a name="list-files-in-data-lake-store"></a>Lista ficheiros arquivo de dados Lake

Utilize o seguinte comando para listar os ficheiros numa conta dados Lake loja.

    azure datalake store filesystem list <dataLakeStoreAccountName> <path>

Por exemplo:

    azure datalake store filesystem list mynewdatalakestore /mynewfolder

O resultado desta deverá ser semelhante ao seguinte:

    info:    Executing command datalake store filesystem list
    data:    accessTime: 1446245025257
    data:    blockSize: 268435456
    data:    group: NotSupportYet
    data:    length: 1589881
    data:    modificationTime: 1446245105763
    data:    owner: NotSupportYet
    data:    pathSuffix: vehicle1_09142014.csv
    data:    permission: 777
    data:    replication: 0
    data:    type: FILE
    data:    ------------------------------------------------------------------------------------
    info:    datalake store filesystem list command OK

## <a name="rename-download-and-delete-data-from-your-data-lake-store"></a>Mudar o nome, transferir e eliminar os dados a partir do seu arquivo de dados de Lake

* **Para mudar o nome de um ficheiro**, utilize o seguinte comando:

        azure datalake store filesystem move <dataLakeStoreAccountName> <path/old_file_name> <path/new_file_name>

    Por exemplo:

        azure datalake store filesystem move mynewdatalakestore /mynewfolder/vehicle1_09142014.csv /mynewfolder/vehicle1_09142014_copy.csv

* **Para transferir um ficheiro**, utilize o seguinte comando. Certifique-se de que o caminho de destino que especificar já existe.

        azure datalake store filesystem export <dataLakeStoreAccountName> <source_path> <destination_path>

    Por exemplo:

        azure datalake store filesystem export mynewdatalakestore /mynewfolder/vehicle1_09142014_copy.csv "C:\mysampledata\vehicle1_09142014_copy.csv"

* **Para eliminar um ficheiro**, utilize o seguinte comando:

        azure datalake store filesystem delete <dataLakeStoreAccountName> <path>

    Por exemplo:

        azure datalake store filesystem delete mynewdatalakestore /mynewfolder/vehicle1_09142014_copy.csv

    Quando lhe for pedido, introduza **Y** para eliminar o item.

## <a name="view-the-access-control-list-for-a-folder-in-data-lake-store"></a>Ver a lista de controlo de acesso para uma pasta de arquivo de dados Lake

Utilize o seguinte comando para ver as ACL numa pasta de arquivo de Lake de dados. Na versão atual, podem ser definidas ACL apenas na raiz do arquivo de Lake de dados. Sim, o parâmetro do caminho será sempre raiz (/).

    azure datalake store permissions show <dataLakeStoreName> <path>

Por exemplo:

    azure datalake store permissions show mynewdatalakestore /


## <a name="delete-your-data-lake-store-account"></a>Eliminar a sua conta do arquivo de Lake de dados

Utilize o seguinte comando para eliminar uma conta do arquivo de Lake de dados.

    azure datalake store account delete <dataLakeStoreAccountName>

Por exemplo:

    azure datalake store account delete mynewdatalakestore

Quando lhe for pedido, introduza **Y** para eliminar a conta.


## <a name="next-steps"></a>Próximos passos

- [Proteger os dados no arquivo de dados de Lake](data-lake-store-secure-data.md)
- [Utilizar a análise de dados Azure Lake com o arquivo de dados de Lake](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
- [Utilizar o Azure HDInsight com o arquivo de dados de Lake](data-lake-store-hdinsight-hadoop-use-portal.md)


[azure-command-line-tools]: ../xplat-cli-install.md
