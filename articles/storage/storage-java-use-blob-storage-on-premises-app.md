<properties
    pageTitle="No local aplicação com o armazenamento de BLOBs (Java) | Microsoft Azure"
    description="Saiba como criar uma aplicação de consola que carrega uma imagem para Azure e, em seguida, apresenta a imagem no seu browser. Exemplos de código Java."
    services="storage"
    documentationCenter="java"
    authors="tamram"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="tamram"/>

# <a name="on-premises-application-with-blob-storage"></a>Aplicação no local com o armazenamento de BLOBs

## <a name="overview"></a>Descrição geral

O exemplo seguinte mostra-lhe como pode utilizar o armazenamento Azure para armazenar as imagens no Azure. O código neste artigo é para uma aplicação de consola que carrega uma imagem para Azure e, em seguida, cria um ficheiro HTML que apresenta a imagem no seu browser.

## <a name="prerequisites"></a>Pré-requisitos

- Um Java programador Kit (JDK), versão 1.6 ou posterior, está instalado.
- O SDK do Azure está instalado.
- Para a caixa para as bibliotecas do Azure para Java e qualquer jarros dependência aplicável, são instalados e estão no caminho de compilação utilizado pelo seu compilador Java. Para obter informações sobre como instalar as bibliotecas do Azure para Java, consulte o artigo [Transferir o SDK do Azure para Java](java-download-azure-sdk.md).
- Configurar uma conta de armazenamento Azure. O nome da conta e a chave de conta para a conta de armazenamento serão utilizadas pelo código neste artigo. Veja [como criar uma conta de armazenamento](storage-create-storage-account.md#create-a-storage-account) para obter informações sobre a criação de uma conta de armazenamento e [teclas de acesso de armazenamento de vista e cópia](storage-create-storage-account.md#view-and-copy-storage-access-keys) para obter informações sobre como obter a chave de conta.

- Ter criado um ficheiro de imagem local com o nome armazenado à c: caminho\\myimages\\image1.jpg. Em alternativa, modifique o construtor de   **FileInputStream** no exemplo para utilizar um nome de caminho e o ficheiro de imagem diferente.

[AZURE.INCLUDE [create-account-note](../../includes/create-account-note.md)]

## <a name="to-use-azure-blob-storage-to-upload-a-file"></a>Para utilizar o armazenamento de Blobs do Azure para carregar um ficheiro

Um procedimento passo a passo é apresentado aqui. Se pretender para passar para a frente, é apresentado o código inteiro neste artigo.

O código para começar, incluindo as importações para as classes de armazenamento Azure core, as classes de cliente de Blobs do Azure, as classes Java IO e a classe de **URISyntaxException** .

    import com.microsoft.azure.storage.*;
    import com.microsoft.azure.storage.blob.*;
    import java.io.*;
    import java.net.URISyntaxException;

Declarar uma classe denominada **StorageSample**e incluir o parêntese abrir, **{**.

    public class StorageSample {

Dentro de classe **StorageSample** , declare uma variável de cadeia que irá conter o protocolo de ponto final predefinido, o nome da sua conta de armazenamento e a chave de acesso de armazenamento, conforme especificado na sua conta de armazenamento Azure. Substitua os valores de marcador de posição **seu\_conta\_nome** e **seu\_conta\_chave** pelas suas próprias conta chave nome e a conta, respetivamente.

    public static final String storageConnectionString =
           "DefaultEndpointsProtocol=http;" +
               "AccountName=your_account_name;" +
               "AccountKey=your_account_name";

Adicionar na sua declaração para **principal**, incluir um bloco de **experimentar** e incluir os parênteses de abrir necessários **{**.

    public static void main(String[] args)
    {
        try
        {

Declare variáveis dos seguintes tipos de (as descrições são para como são utilizadas neste exemplo):

-   **CloudStorageAccount**: utilizadas para iniciar o objecto conta com o seu nome de conta de armazenamento Azure e chave de e para criar o objeto de cliente blob.
-   **CloudBlobClient**: utilizada para aceder ao serviço de Blobs.
-   **CloudBlobContainer**: utilizado para criar um contentor blob, blobs no contentor de lista e eliminar o contentor.
-   **CloudBlockBlob**: utilizado para carregar um ficheiro de imagem local para o contentor.

<!-- -->

    CloudStorageAccount account;
    CloudBlobClient serviceClient;
    CloudBlobContainer container;
    CloudBlockBlob blob;

Atribua um valor para a variável de **conta** .

    account = CloudStorageAccount.parse(storageConnectionString);

Atribua um valor para a variável de **serviceClient** .

    serviceClient = account.createCloudBlobClient();

Atribua um valor para a variável de **contentor** . Vamos irá obter uma referência a um contentor denominada **gettingstarted**.

    // Container name must be lower case.
    container = serviceClient.getContainerReference("gettingstarted");

Crie o contentor. Este método irá criar o contentor, caso ainda não existir (e devolver **Verdadeiro**). Se o contentor existir, irá devolver **Falso**. Uma alternativa a **createIfNotExists** é o método **Criar** (que irá devolver um erro se o contentor já existe).

    container.createIfNotExists();

Configurar o acesso anónimo para o contentor.

    // Set anonymous access on the container.
    BlobContainerPermissions containerPermissions;
    containerPermissions = new BlobContainerPermissions();
    containerPermissions.setPublicAccess(BlobContainerPublicAccessType.CONTAINER);
    container.uploadPermissions(containerPermissions);

Obter uma referência para o blob bloco, que irá representar o blob no Azure armazenamento.

    blob = container.getBlockBlobReference("image1.jpg");

Utilize o construtor de **ficheiro** para obter uma referência para o ficheiro localmente criado que irá carregar. Certifique-se de que criou com este ficheiro antes de executar o código.

    File fileReference = new File ("c:\\myimages\\image1.jpg");

Carregue o ficheiro local através de uma chamada para o método de **CloudBlockBlob.upload** . O primeiro parâmetro para o método **CloudBlockBlob.upload** é um objeto de **FileInputStream** que representa o ficheiro local que vai ser carregado para o armazenamento Azure. O segundo parâmetro é o tamanho, em bytes, do ficheiro.

    blob.upload(new FileInputStream(fileReference), fileReference.length());

Ligar para uma função de ajuda com o nome **MakeHTMLPage**, para tornar uma página básica do HTML que contém uma ** &lt;imagem&gt; ** elemento com a origem de definido para o blob que está agora na sua conta de armazenamento Azure. O código para **MakeHTMLPage** outros fabricantes referido neste artigo.

    MakeHTMLPage(container);

Imprima uma mensagem de estado e informações sobre a página HTML criada.

    System.out.println("Processing complete.");
    System.out.println("Open index.html to see the images stored in your storage account.");

Feche o bloco **tente** inserindo um parêntese Reto de fecho: **}**

Processe as seguintes exceções:

-   **FileNotFoundException**: pode ser iniciadas pelos construtores **FileInputStream** ou **FileOutputStream** .
-   **StorageException**: pode ser iniciadas pela biblioteca de armazenamento do cliente Azure.
-   **URISyntaxException**: pode ser iniciadas pelo método **ListBlobItem.getUri** .
-   **Exceção**: processamento de exceção genérico.

<!-- -->

    catch (FileNotFoundException fileNotFoundException)
    {
        System.out.print("FileNotFoundException encountered: ");
        System.out.println(fileNotFoundException.getMessage());
        System.exit(-1);
    }
    catch (StorageException storageException)
    {
        System.out.print("StorageException encountered: ");
        System.out.println(storageException.getMessage());
        System.exit(-1);
    }
    catch (URISyntaxException uriSyntaxException)
    {
        System.out.print("URISyntaxException encountered: ");
        System.out.println(uriSyntaxException.getMessage());
        System.exit(-1);
    }
    catch (Exception e)
    {
        System.out.print("Exception encountered: ");
        System.out.println(e.getMessage());
        System.exit(-1);
    }

Fechar **principal** inserindo um parêntese Reto de fecho: **}**

Crie um método **MakeHTMLPage** para criar uma página HTML básica com o nome. Este método tem um parâmetro do tipo **CloudBlobContainer**, que irão ser utilizados para iteramos através da lista de blobs carregados. Este método irá gerar exceções tipo **FileNotFoundException**, que pode ser iniciadas pelas **FileOutputStream** construtor e **URISyntaxException**, que pode ser iniciado pelo método **ListBlobItem.getUri** . Incluir o parêntese de abertura, **{**.

    public static void MakeHTMLPage(CloudBlobContainer container) throws FileNotFoundException, URISyntaxException
    {

Crie um ficheiro local chamado **Index**.

    PrintStream stream;
    stream = new PrintStream(new FileOutputStream("index.html"));

Escrever para o ficheiro local, adicionando o ** &lt;html&gt;**, ** &lt;cabeçalho&gt;**, e ** &lt;corpo&gt; ** elementos.

    stream.println("<html>");
    stream.println("<header/>");
    stream.println("<body>");

Iteração a lista de blobs carregados. Para cada blob, na página HTML crie um ** &lt;img&gt; ** elemento que tem o atributo **src** enviado para o URI do blob, tal como se encontra na sua conta de armazenamento Azure.
Apesar de adicionados apenas uma imagem neste exemplo, se tiver adicionado mais, este código seria iteramos todos eles.

Para simplificar, este exemplo assume que cada blob carregado é uma imagem. Se atualizou blobs diferente de imagens ou blobs de página em vez de blobs do bloco, ajuste o código conforme necessário.

    // Enumerate the uploaded blobs.
    for (ListBlobItem blobItem : container.listBlobs()) {
    // List each blob as an <img> element in the HTML body.
    stream.println("<img src='" + blobItem.getUri() + "'/><br/>");
    }

Fechar o ** &lt;corpo&gt; ** elemento e a ** &lt;html&gt; ** elemento.

    stream.println("</body>");
    stream.println("</html>");

Feche o ficheiro local.

    stream.close();

Fechar **MakeHTMLPage** inserindo um parêntese Reto de fecho: **}**

Fechar **StorageSample** inserindo um parêntese Reto de fecho: **}**

Segue-se o código para este exemplo completo. Não se esqueça de modificar os valores de marcador de posição **seu\_conta\_nome** e **seu\_conta\_chave** para utilizar o seu nome de conta e chave da conta, respetivamente.

    import com.microsoft.azure.storage.*;
    import com.microsoft.azure.storage.blob.*;
    import java.io.*;
    import java.net.URISyntaxException;

    // Create an image, c:\myimages\image1.jpg, prior to running this sample.
    // Alternatively, change the value used by the FileInputStream constructor
    // to use a different image path and file that you have already created.
    public class StorageSample {

        public static final String storageConnectionString =
                "DefaultEndpointsProtocol=http;" +
                       "AccountName=your_account_name;" +
                       "AccountKey=your_account_name";

        public static void main(String[] args) {
            try {
                CloudStorageAccount account;
                CloudBlobClient serviceClient;
                CloudBlobContainer container;
                CloudBlockBlob blob;

                account = CloudStorageAccount.parse(storageConnectionString);
                serviceClient = account.createCloudBlobClient();
                // Container name must be lower case.
                container = serviceClient.getContainerReference("gettingstarted");
                container.createIfNotExists();

                // Set anonymous access on the container.
                BlobContainerPermissions containerPermissions;
                containerPermissions = new BlobContainerPermissions();
                containerPermissions.setPublicAccess(BlobContainerPublicAccessType.CONTAINER);
                container.uploadPermissions(containerPermissions);

                // Upload an image file.
                blob = container.getBlockBlobReference("image1.jpg");

                File fileReference = new File("c:\\myimages\\image1.jpg");
                blob.upload(new FileInputStream(fileReference), fileReference.length());

                // At this point the image is uploaded.
                // Next, create an HTML page that lists all of the uploaded images.
                MakeHTMLPage(container);

                System.out.println("Processing complete.");
                System.out.println("Open index.html to see the images stored in your storage account.");

            } catch (FileNotFoundException fileNotFoundException) {
                System.out.print("FileNotFoundException encountered: ");
                System.out.println(fileNotFoundException.getMessage());
                System.exit(-1);
            } catch (StorageException storageException) {
                System.out.print("StorageException encountered: ");
                System.out.println(storageException.getMessage());
                System.exit(-1);
            } catch (URISyntaxException uriSyntaxException) {
                System.out.print("URISyntaxException encountered: ");
                System.out.println(uriSyntaxException.getMessage());
                System.exit(-1);
            } catch (Exception e) {
                System.out.print("Exception encountered: ");
                System.out.println(e.getMessage());
                System.exit(-1);
            }
        }

        // Create an HTML page that can be used to display the uploaded images.
        // This example assumes all of the blobs are for images.
        public static void MakeHTMLPage(CloudBlobContainer container) throws FileNotFoundException, URISyntaxException
        {
            PrintStream stream;
            stream = new PrintStream(new FileOutputStream("index.html"));

            // Create the opening <html>, <header>, and <body> elements.
            stream.println("<html>");
            stream.println("<header/>");
            stream.println("<body>");

            // Enumerate the uploaded blobs.
            for (ListBlobItem blobItem : container.listBlobs()) {
                // List each blob as an <img> element in the HTML body.
                stream.println("<img src='" + blobItem.getUri() + "'/><br/>");
            }

            stream.println("</body>");

            // Complete the <html> element and close the file.
            stream.println("</html>");
            stream.close();
        }
    }

Para além de carregar o ficheiro de imagem local ao armazenamento Azure, o código de exemplo cria um ficheiro local namedindex.html, que pode abrir no seu browser para ver a sua imagem carregada.

Uma vez que o código contém o nome da conta e a chave de conta, certifique-se de que o seu código fonte é seguro.

## <a name="to-delete-a-container"></a>Para eliminar um contentor

Uma vez que são cobrados armazenamento, poderá querer eliminar o contentor **gettingstarted** depois de terminar experiências com este exemplo. Para eliminar um contentor, utilize o método de **CloudBlobContainer.delete** .

    container = serviceClient.getContainerReference("gettingstarted");
    container.delete();

Para ligar para o método de **CloudBlobContainer.delete** , o processo de inicialização objectos **CloudStorageAccount**, **ClodBlobClient**e **CloudBlobContainer** é o mesmo conforme apresentado para o método de **createIfNotExist** . Segue-se um exemplo completo que elimina o contentor denominado **gettingstarted**.

    import com.microsoft.azure.storage.*;
    import com.microsoft.azure.storage.blob.*;

    public class DeleteContainer {

        public static final String storageConnectionString =
                "DefaultEndpointsProtocol=http;" +
                   "AccountName=your_account_name;" +
                   "AccountKey=your_account_key";

        public static void main(String[] args)
        {
            try
            {
                CloudStorageAccount account;
                CloudBlobClient serviceClient;
                CloudBlobContainer container;

                account = CloudStorageAccount.parse(storageConnectionString);
                serviceClient = account.createCloudBlobClient();
                // Container name must be lower case.
                container = serviceClient.getContainerReference("gettingstarted");
                container.delete();

                System.out.println("Container deleted.");

            }
            catch (StorageException storageException)
            {
                System.out.print("StorageException encountered: ");
                System.out.println(storageException.getMessage());
                System.exit(-1);
            }
            catch (Exception e)
            {
                System.out.print("Exception encountered: ");
                System.out.println(e.getMessage());
                System.exit(-1);
            }
        }
    }

Para obter uma descrição geral de outras classes de armazenamento de BLOBs e métodos, consulte o artigo [como utilizar o armazenamento de Blobs do Java](storage-java-how-to-use-blob-storage.md).

## <a name="next-steps"></a>Próximos passos

Siga estas ligações para mais informações sobre tarefas de armazenamento mais complexas.

- [Armazenamento Azure SDK para Java](https://github.com/azure/azure-storage-java)
- [Referência SDK do cliente de armazenamento Azure](http://dl.windowsazure.com/storage/javadoc/)
- [Serviços de armazenamento Azure REST API](https://msdn.microsoft.com/library/azure/dd179355.aspx)
- [Blogue da equipa do armazenamento Azure](http://blogs.msdn.com/b/windowsazurestorage/)
