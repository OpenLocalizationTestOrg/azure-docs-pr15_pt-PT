<properties
   pageTitle="Utilizar dados Lake loja Java SDK para desenvolver aplicações | Microsoft Azure"
   description="Utilizar o Azure dados Lake loja Java SDK para desenvolver aplicações"
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
   ms.date="10/17/2016"
   ms.author="nitinme"/>

# <a name="get-started-with-azure-data-lake-store-using-java"></a>Introdução ao Azure dados Lake loja utilizando Java

> [AZURE.SELECTOR]
- [Portal](data-lake-store-get-started-portal.md)
- [PowerShell](data-lake-store-get-started-powershell.md)
- [.NET SDK](data-lake-store-get-started-net-sdk.md)
- [Java SDK](data-lake-store-get-started-java-sdk.md)
- [REST API](data-lake-store-get-started-rest-api.md)
- [Clip Azure](data-lake-store-get-started-cli.md)
- [NODE.js](data-lake-store-manage-use-nodejs.md)

Saiba como utilizar o Azure dados Lake loja Java SDK para efetuar operações básicas, tais como criar pastas, carregar e transferir ficheiros de dados, etc. Para mais informações sobre Lake de dados, consulte o artigo [Azure dados Lake loja](data-lake-store-overview.md).

Pode aceder a documentos Java SDK API para Azure dados Lake arquivo na [loja Java API do Azure dados Lake documentos](https://azure.github.io/azure-data-lake-store-java/javadoc/).

## <a name="prerequisites"></a>Pré-requisitos

* Java Development Kit (JDK 7 ou mais elevado, utilizando Java versão 1.7 ou superior)
* Conta Azure dados Lake loja. Siga as instruções no [artigo Introdução ao arquivo de Lake Azure dados utilizando o Portal do Azure](data-lake-store-get-started-portal.md).
* [Maven](https://maven.apache.org/install.html). Neste tutorial utiliza Maven para dependências de compilação e o project. Apesar de ser possível construir sem utilizar um sistema de compilação como Maven ou Gradle, tornar estes sistemas é muito mais fácil de gerir dependências.
* (Opcional) E IDE como [IntelliJ IDEIA](https://www.jetbrains.com/idea/download/) ou [Eclipse](https://www.eclipse.org/downloads/) ou semelhantes.

## <a name="how-do-i-authenticate-using-azure-active-directory"></a>Como autenticar utilizando o Azure Active Directory?

Neste tutorial utilizamos uma palavra-passe Azure AD aplicação cliente para obter um token de Azure Active Directory (autenticação do serviço a serviço). Utilizamos este token para criar um objeto de cliente do arquivo de Lake de dados para efetuar operações de ficheiro e diretório de operações. Para obter instruções sobre como autenticar com o arquivo de Lake Azure dados utilizando o segredo de cliente, vamos executar os seguintes passos de alto nível:

1. Criar uma aplicação web do Azure AD
2. Obter o ID de cliente, o segredo cliente e o token ponto final para a aplicação web do Azure AD.
3. Configure o acesso para a aplicação web do Azure AD na loja de Lake dados ficheiro/pasta à qual pretende aceder a partir da aplicação de Java que estiver a criar.

Para obter instruções sobre como executar estes passos, consulte o artigo [criar uma aplicação do Active Directory](data-lake-store-authenticate-using-active-directory.md#create-an-active-directory-application).

Azure Active Directory fornece outras opções também para obter um token. Pode selecionar a partir de um número de variados mecanismos de autenticação de acordo com o seu cenário, por exemplo, uma aplicação em execução num browser, uma aplicação distribuído como uma aplicação de ambiente de trabalho ou uma aplicação de servidor a executar no local ou numa máquina virtual Azure. Também pode selecionar a partir de diferentes tipos de credenciais, como palavras-passe, certificados, 2-factor de autenticação, etc. Além disso, o Azure Active Directory permite-lhe sincronizar os utilizadores do Active Directory no local com a nuvem. Para obter detalhes, consulte o artigo [Cenários de autenticação para o Azure Active Directory](../active-directory/active-directory-authentication-scenarios.md). 

## <a name="create-a-java-application"></a>Criar uma aplicação de Java

O código de exemplo descontraídos de início de [no GitHub](https://azure.microsoft.com/documentation/samples/data-lake-store-java-upload-download-get-started/) disponíveis que durante o processo de criação de ficheiros na loja, de concatenação de ficheiros, transferir um ficheiro e eliminar alguns ficheiros na loja. Esta secção do artigo orientá-lo nas partes principais do código.

1. Crie um projeto Maven utilizando [mvn archetype](https://maven.apache.org/guides/getting-started/maven-in-five-minutes.html) da linha de comandos ou utilizando um IDE. Para obter instruções sobre como criar um projecto de Java utilizando IntelliJ, consulte o artigo [aqui](https://www.jetbrains.com/help/idea/2016.1/creating-and-running-your-first-java-application.html). Para obter instruções sobre como criar um projecto utilizando Eclipse, consulte o artigo [aqui](http://help.eclipse.org/mars/index.jsp?topic=%2Forg.eclipse.jdt.doc.user%2FgettingStarted%2Fqs-3.htm). 

2. Adicione as seguintes dependências para o seu ficheiro de **pom.xml** Maven. Adicionar o seguinte fragmento de texto entre o ** \</version >** etiqueta e o ** \</projeto >** etiqueta:

        <dependencies>
          <dependency>
            <groupId>com.microsoft.azure</groupId>
            <artifactId>azure-data-lake-store-sdk</artifactId>
            <version>2.0.4-SNAPSHOT</version>
          </dependency>
          <dependency>
            <groupId>org.slf4j</groupId>
            <artifactId>slf4j-nop</artifactId>
            <version>1.7.21</version>
          </dependency>
        </dependencies>

    A primeira dependência é utilizar o SDK de arquivo de dados Lake (`azure-datalake-store`) a partir do repositório de maven. A segunda dependência (`slf4j-nop`) é especificar qual framework de registo para utilizar para esta aplicação. O SDK de arquivo de dados Lake utiliza fachada [slf4j](http://www.slf4j.org/) registo, que permite-lhe escolher a partir de um número de quadros registo populares, como log4j, Java registo, logback, etc., ou nenhum registo. Neste exemplo, vamos desativar o registo, por conseguinte, utilizamos enlace **slf4j nop** . Para utilizar outras opções de registo na sua aplicação, consulte [aqui](http://www.slf4j.org/manual.html#projectDep).

### <a name="add-the-application-code"></a>Adicionar o código da aplicação

Existem três partes principais para o código.

1. Obter o token do Azure Active Directory

2. Utilize o token para criar um cliente de arquivo de Lake de dados.

3. Utilize o cliente de arquivo de Lake de dados para efetuar operações.

#### <a name="step-1-obtain-an-azure-active-directory-token"></a>Passo 1: Obter um token de Azure Active Directory.

O SDK de arquivo de dados Lake fornece convenientes métodos que permitem-lhe obter os tokens de segurança, se necessário, para falar com a conta de arquivo de Lake de dados. No entanto, o SDK, não definiu que apenas estes métodos de ser utilizado. Pode utilizar qualquer meio de obtenção de token, assim, semelhante à utilização do [Azure Active Directory SDK](https://github.com/AzureAD/azure-activedirectory-library-for-java)ou o seu próprio código personalizado.

Para utilizar o SDK de arquivo de dados Lake para obter o token de para a aplicação Web do Active Directory que criou anteriormente, utilize os métodos estáticos no `AzureADAuthenticator` escolares. Substitua os valores reais para a aplicação Azure Active Directory Web **Preencher em aqui** .

    private static String clientId = "FILL-IN-HERE";
    private static String authTokenEndpoint = "FILL-IN-HERE";
    private static String clientKey = "FILL-IN-HERE";

    AzureADToken token = AzureADAuthenticator.getTokenUsingClientCreds(authTokenEndpoint, clientId, clientKey);

#### <a name="step-2-create-an-azure-data-lake-store-client-adlstoreclient-object"></a>Passo 2: Criar um objeto de cliente (ADLStoreClient) do arquivo de Lake de dados do Azure

Criar um objeto de [ADLStoreClient](https://azure.github.io/azure-data-lake-store-java/javadoc/) requer que especifique o nome de conta do arquivo de Lake de dados e o token de Azure Active Directory gerado no último passo. Nota que o arquivo de dados de Lake nome da conta tem de ser um nome de domínio completamente qualificado. Por exemplo, substitua **Preencher em aqui** algo parecido com **mydatalakestore.azuredatalakestore.net**.

    private static String accountFQDN = "FILL-IN-HERE";  // full account FQDN, not just the account name
    ADLStoreClient client = ADLStoreClient.createClient(accountFQDN, token);

### <a name="step-3-use-the-adlstoreclient-to-perform-file-and-directory-operations"></a>Passo 3: Utilizar o ADLStoreClient para efetuar operações de ficheiro e directório

O código abaixo contém fragmentos de exemplo de algumas operações comuns. Pode observe a completo de [documentos de dados Lake loja Java SDK API](https://azure.github.io/azure-data-lake-store-java/javadoc/) do objeto **ADLStoreClient** para ver outras operações.
 
Note que os ficheiros são ler a partir e escritos para utilizar sequências de Java padrão. Isto significa que pode camada qualquer uma das sequências Java na parte superior das sequências de arquivo de Lake de dados para beneficiar dos funcionalidade Java padrão (por exemplo, imprimir sequências para saída formatada ou em qualquer uma das sequências compressão ou encriptação para funcionalidades adicionais na parte superior, etc.).

    // set file permission
    client.setPermission(filename, "744");

    // append to file
    stream = client.getAppendStream(filename);
    stream.write(getSampleContent());
    stream.close();

    // Read File
    InputStream in = client.getReadStream(filename);
    byte[] b = new byte[64000];
    while (in.read(b) != -1) {
        System.out.write(b);
    }
    in.close();

    // concatenate the two files into one
    List<String> fileList = Arrays.asList("/a/b/c.txt", "/a/b/d.txt");
    client.concatenateFiles("/a/b/f.txt", fileList);

    //rename the file
    client.rename("/a/b/f.txt", "/a/b/g.txt");

    // list directory contents
    List<DirectoryEntry> list = client.enumerateDirectory("/a/b", 2000);
    System.out.println("Directory listing for directory /a/b:");
    for (DirectoryEntry entry : list) {
        printDirectoryInfo(entry);
    }

    // delete directory along with all the subdirectories and files in it
    client.deleteRecursive("/a");

#### <a name="step-4-build-and-run-the-application"></a>Passo 4: Criar e executar a aplicação

1. Para executar a partir de um IDE, localize e prima o botão **Executar** . Para executar a partir do Maven, utilize [execução: execução](http://www.mojohaus.org/exec-maven-plugin/exec-mojo.html).

2. Para produzir uma para autónomo caixa que pode executar a partir da linha de comandos construir para a caixa com todas as dependências incluídas, utilizando o [Plug-in assemblagem de Maven](http://maven.apache.org/plugins/maven-assembly-plugin/usage.html). Pom.xml no [código fonte de exemplo no github](https://github.com/Azure-Samples/data-lake-store-java-upload-download-get-started/blob/master/pom.xml) tem um exemplo de como o fazer.


## <a name="next-steps"></a>Próximos passos

- [Proteger os dados no arquivo de dados de Lake](data-lake-store-secure-data.md)
- [Utilizar a análise de dados Azure Lake com o arquivo de dados de Lake](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
- [Utilizar o Azure HDInsight com o arquivo de dados de Lake](data-lake-store-hdinsight-hadoop-use-portal.md)
