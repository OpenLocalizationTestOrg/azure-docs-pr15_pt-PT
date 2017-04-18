<properties
    pageTitle="Como utilizar o armazenamento de Blobs do Xamarin | Microsoft Azure"
    description="A biblioteca de cliente de armazenamento do Azure para Xamarin permite que os programadores criar iOS, Android e Windows loja de aplicações com respectivas interfaces de utilizador nativa. Este tutorial mostra como utilizar Xamarin para criar uma aplicação que utiliza o armazenamento de Blobs do Azure."
    services="storage"
    documentationCenter="xamarin"
    authors="micurd"
    manager="jahogg"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/08/2016"
    ms.author="micurd"/>

# <a name="how-to-use-blob-storage-from-xamarin"></a>Como utilizar o armazenamento de Blobs do Xamarin

[AZURE.INCLUDE [storage-selector-blob-include](../../includes/storage-selector-blob-include.md)]

## <a name="overview"></a>Descrição geral

Xamarin permite que os programadores para utilizar uma partilhada c# código base para criar iOS, Android e Windows loja de aplicações com respectivas interfaces de utilizador nativa. Este tutorial mostra-lhe como utilizar o armazenamento de Blobs do Azure com uma aplicação de Xamarin. Se pretender para mais informações sobre o armazenamento do Windows Azure, antes de consultar o código, consulte o artigo [Introdução ao Microsoft Azure armazenamento](storage-introduction.md).

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

[AZURE.INCLUDE [storage-mobile-authentication-guidance](../../includes/storage-mobile-authentication-guidance.md)]

## <a name="create-a-new-xamarin-application"></a>Criar uma nova aplicação Xamarin

Para este começar a trabalhar, vamos está a criar uma aplicação que destinos Windows, iOS e Android. Esta aplicação irá basta criar um contentor e carregar um blob para este contentor. Estamos a utilizar o Visual Studio no Windows para este introdução, mas podem ser aplicadas as learnings mesmo quando criar uma aplicação utilizando Xamarin Studio no Mac OS.

Siga estes passos para criar a sua aplicação:

1. Se ainda não o fez, transfira e instale [Xamarin para Visual Studio](https://www.xamarin.com/download).
2. Abrir o Visual Studio e criar uma aplicação vazia (partilhado nativo): **ficheiro > novo > projeto > em diferentes plataformas > App(Native Shared) em branco**.
3. A solução no painel de solução Explorer com o botão direito e selecione **Gerir pacotes de NuGet para solução**. Procurar **WindowsAzure.Storage** e instale a versão mais recente estável para todos os projetos na sua solução.
4. Criar e executar o seu projeto.

Agora deverá ter uma aplicação que lhe permite clicar num botão que aumenta um contador.

> [AZURE.NOTE] A biblioteca de cliente de armazenamento do Azure para Xamarin atualmente suporta os seguintes tipos de projeto: nativas partilhados, Xamarin.Forms partilhados, Xamarin.Android e Xamarin.iOS.

## <a name="create-container-and-upload-blob"></a>Criar o contentor e carregue blob

Em seguida, terá de adicionar algum código para a classe partilhada `MyClass.cs` que cria um contentor e carrega um blob para este contentor. `MyClass.cs`deverá ter o aspeto semelhante ao seguinte:

    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;
    using System.Threading.Tasks;

    namespace XamarinApp
    {
        public class MyClass
        {
            public MyClass ()
            {
            }

            public static async Task createContainerAndUpload()
            {
                // Retrieve storage account from connection string.
                CloudStorageAccount storageAccount = CloudStorageAccount.Parse("DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here");

                // Create the blob client.
                CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

                // Retrieve reference to a previously created container.
                CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

                // Create the container if it doesn't already exist.
                await container.CreateIfNotExistsAsync();

                // Retrieve reference to a blob named "myblob".
                CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob");

                // Create the "myblob" blob with the text "Hello, world!"
                await blockBlob.UploadTextAsync("Hello, world!");
            }
        }
    }

Certifique-se substituir "your_account_name_here" e "your_account_key_here" com o seu nome de conta real e chave da conta. Em seguida, pode utilizar esta classe partilhado no seu iOS, Android e Windows Phone aplicação. Pode adicionar simplesmente `MyClass.createContainerAndUpload()` para cada projeto. Por exemplo:

### <a name="xamarinappdroid--mainactivitycs"></a>XamarinApp.Droid > MainActivity.cs

    using Android.App;
    using Android.Widget;
    using Android.OS;

    namespace XamarinApp.Droid
    {
        [Activity (Label = "XamarinApp.Droid", MainLauncher = true, Icon = "@drawable/icon")]
        public class MainActivity : Activity
        {
            int count = 1;

            protected override async void OnCreate (Bundle bundle)
            {
                base.OnCreate (bundle);

                // Set our view from the "main" layout resource
                SetContentView (Resource.Layout.Main);

                // Get our button from the layout resource,
                // and attach an event to it
                Button button = FindViewById<Button> (Resource.Id.myButton);

                button.Click += delegate {
                    button.Text = string.Format ("{0} clicks!", count++);
                };

              await MyClass.createContainerAndUpload();
            }
        }
    }

### <a name="xamarinappios--viewcontrollercs"></a>XamarinApp.iOS > ViewController.cs

    using System;
    using UIKit;

    namespace XamarinApp.iOS
    {
        public partial class ViewController : UIViewController
        {
            int count = 1;

            public ViewController (IntPtr handle) : base (handle)
            {
            }

            public override async void ViewDidLoad ()
            {
                base.ViewDidLoad ();
                // Perform any additional setup after loading the view, typically from a nib.
                Button.AccessibilityIdentifier = "myButton";
                Button.TouchUpInside += delegate {
                    var title = string.Format ("{0} clicks!", count++);
                    Button.SetTitle (title, UIControlState.Normal);
                };

                await MyClass.createContainerAndUpload();
            }

            public override void DidReceiveMemoryWarning ()
            {
                base.DidReceiveMemoryWarning ();
                // Release any cached data, images, etc that aren't in use.
            }
        }
    }

### <a name="xamarinappwinphone--mainpagexaml--mainpagexamlcs"></a>XamarinApp.WinPhone > MainPage.xaml > MainPage.xaml.cs

    using Windows.UI.Xaml.Controls;
    using Windows.UI.Xaml.Navigation;

    // The Blank Page item template is documented at http://go.microsoft.com/fwlink/?LinkId=391641

    namespace XamarinApp.WinPhone
    {
        /// <summary>
        /// An empty page that can be used on its own or navigated to within a Frame.
        /// </summary>
        public sealed partial class MainPage : Page
        {
            int count = 1;

            public MainPage()
            {
                this.InitializeComponent();

                this.NavigationCacheMode = NavigationCacheMode.Required;
            }

            /// <summary>
            /// Invoked when this page is about to be displayed in a Frame.
            /// </summary>
            /// <param name="e">Event data that describes how this page was reached.
            /// This parameter is typically used to configure the page.</param>
            protected override async void OnNavigatedTo(NavigationEventArgs e)
            {
                // TODO: Prepare page for display here.

                // TODO: If your application contains multiple pages, ensure that you are
                // handling the hardware Back button by registering for the
                // Windows.Phone.UI.Input.HardwareButtons.BackPressed event.
                // If you are using the NavigationHelper provided by some templates,
                // this event is handled for you.
                Button.Click += delegate {
                    var title = string.Format("{0} clicks!", count++);
                    Button.Content = title;
                };

                await MyClass.createContainerAndUpload();
            }
        }
    }


## <a name="run-the-application"></a>Executar a aplicação

Pode agora executar esta aplicação num emulador Android ou Windows Phone. Também pode executar esta aplicação num emulador iOS, mas este procedimento vai requerer num Mac. Para obter instruções específicas sobre como fazer isto, leia a documentação para [ligar o Visual Studio para Mac](https://developer.xamarin.com/guides/ios/getting_started/installation/windows/connecting-to-mac/)

Depois de executar a aplicação, criarão o contentor `mycontainer` na sua conta de armazenamento. Deve conter o blob `myblob`, que contém o texto, `Hello, world!`. Pode verificar esta utilizando o [Explorador de armazenamento do Microsoft Azure](http://storageexplorer.com/).

## <a name="next-steps"></a>Próximos passos

Este começar a trabalhar, o que aprendeu como criar uma aplicação em diferentes plataformas no Xamarin que utiliza o armazenamento do Windows Azure. Nesta Introdução especificamente focado num cenário no armazenamento Blob do. No entanto, que pode fazer muitas mais com, não só armazenamento Blob do, mas também com a tabela, o ficheiro e o armazenamento de fila de espera. Veja consulte os seguintes artigos para obter mais informações:
- [Introdução ao armazenamento de Blobs do Azure utilizando o .NET](storage-dotnet-how-to-use-blobs.md)
- [Introdução ao armazenamento de tabela do Azure através do .NET](storage-dotnet-how-to-use-tables.md)
- [Introdução ao armazenamento de fila de espera do Windows Azure utilizando o .NET](storage-dotnet-how-to-use-queues.md)
- [Introdução ao Azure armazenamento de ficheiros no Windows](storage-dotnet-how-to-use-files.md)

[AZURE.INCLUDE [storage-try-azure-tools-blobs](../../includes/storage-try-azure-tools-blobs.md)]
