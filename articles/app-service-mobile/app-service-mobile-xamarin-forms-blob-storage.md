<properties
    pageTitle="Ligar ao armazenamento Azure na sua aplicação Xamarin.Forms"
    description="Adicionar imagens para a aplicação móvel do todo lista Xamarin.Forms ligando-se ao armazenamento de Blobs do Azure"
    documentationCenter="xamarin"
    authors="adrianhall"
    manager="erikre"
    editor=""
    services="app-service\mobile"/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-xamarin-ios"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="10/01/2016"
    ms.author="adrianha"/>

#<a name="connect-to-azure-storage-in-your-xamarinforms-app"></a>Ligar ao armazenamento Azure na sua aplicação Xamarin.Forms

## <a name="overview"></a>Descrição geral

O Azure Mobile aplicações cliente e servidor SDK suportam a sincronização offline de dados estruturadas com operações CRUD contra o ponto final /tables. Geralmente estes dados são armazenados numa base de dados ou a loja semelhante e geralmente estes arquivos de dados não armazenam dados binários grandes eficazmente. Além disso, algumas aplicações tem relacionado com dados que estão armazenados noutros locais (por exemplo, armazenamento de BLOBs, partilhas de ficheiros) e é útil para poder criar associações entre o ponto final /tables registos e outros dados.

Este tópico mostra-lhe como adicionar suporte para imagens para o guia de introdução de lista de todo aplicações Mobile. Primeiro tem de concluir o tutorial [criar uma aplicação de Xamarin.Forms].

Neste tutorial, irá criar uma conta de armazenamento e adicionar uma cadeia de ligação ao seu back-end aplicação Mobile. Em seguida, irá adicionar um novo Herdar do novo tipo de aplicações Mobile `StorageController<T>` ao seu projeto do servidor.

>[AZURE.TIP] Neste tutorial tem um [exemplo de acompanha](https://azure.microsoft.com/documentation/samples/app-service-mobile-dotnet-todo-list-files/) disponível, que pode ser implementada à sua própria conta Azure. 

## <a name="prerequisites"></a>Pré-requisitos

* Conclua o tutorial de [criar uma aplicação do Xamarin.Forms] , que enumera outros pré-requisitos. Este artigo utiliza a aplicação a partir desse tutorial completa.

>[AZURE.NOTE] Se pretender começar com a aplicação de serviço de Azure antes de se inscrever para uma conta do Azure, aceda ao [Serviço de aplicação tente](https://tryappservice.azure.com/?appServiceName=mobile). Não existem, imediatamente pode criar uma aplicação móvel starter curto na aplicação de serviço — sem cartão de crédito obrigatório e sem compromissos.

## <a name="create-a-storage-account"></a>Criar uma conta de armazenamento

1. Crie uma conta de armazenamento ao seguir o tutorial [de criar uma conta de armazenamento do Azure]. 

2. No portal do Azure, navegue até à sua conta de armazenamento recentemente criado e clique no ícone de **teclas** . Copie a **cadeia de ligação principal**.

3. Navegue para a sua aplicação móvel do back-end. Em **Todas as definições de** -> **Definições da aplicação** -> **Cadeias de ligação**, crie uma nova chave denominada `MS_AzureStorageAccountConnectionString` e utilize o valor que copiou da sua conta de armazenamento. Utilize **personalizada** como o tipo de chave.

## <a name="add-a-storage-controller-to-the-server"></a>Adicionar um controlador de armazenamento no servidor

Tem de adicionar um novo controlador ao seu projeto do servidor que irá responder aos pedidos para obter um token de SA para armazenamento do Windows Azure, bem como devolver uma lista de ficheiros que correspondem a um registo:

- [Adicionar um controlador de armazenamento ao seu projeto do servidor](#add-controller-code)
- [Rotas registadas pelo controlador de armazenamento](#routes-registered)
- [Cliente e servidor de comunicações](#client-communication)

###<a name="add-controller-code"></a>Adicionar um controlador de armazenamento ao seu projeto do servidor

1. No Visual Studio, abra o projeto de servidor do .NET. Adicione o pacote de Nuget [Microsoft.Azure.Mobile.Server.Files]. Certifique-se de que selecione **incluir a versão de pré-lançamento**.

2. No Visual Studio, abra o projeto de servidor do .NET. Botão direito do rato na pasta de **controladores** e selecione **Adicionar** -> **controlador** -> **2-controlador de Web API - vazia**. Nome o controlador de `TodoItemStorageController`.

3. Adicione o seguinte utilizando instruções:

        using Microsoft.Azure.Mobile.Server.Files;
        using Microsoft.Azure.Mobile.Server.Files.Controllers;

4. Altere a classe de base para `StorageController`:
    
        public class TodoItemStorageController : StorageController<TodoItem>

5. Adicione os seguintes métodos para a classe de:

        [HttpPost]
        [Route("tables/TodoItem/{id}/StorageToken")]
        public async Task<HttpResponseMessage> PostStorageTokenRequest(string id, StorageTokenRequest value)
        {
            StorageToken token = await GetStorageTokenAsync(id, value);

            return Request.CreateResponse(token);
        }

        // Get the files associated with this record
        [HttpGet]
        [Route("tables/TodoItem/{id}/MobileServiceFiles")]
        public async Task<HttpResponseMessage> GetFiles(string id)
        {
            IEnumerable<MobileServiceFile> files = await GetRecordFilesAsync(id);

            return Request.CreateResponse(files);
        }

        [HttpDelete]
        [Route("tables/TodoItem/{id}/MobileServiceFiles/{name}")]
        public Task Delete(string id, string name)
        {
            return base.DeleteFileAsync(id, name);
        }

6. Atualize a configuração da Web API para configurar o encaminhamento de atributo. Na **Startup.MobileApp.cs**, adicione a linha seguinte para o `ConfigureMobileApp()` método, após a definição da `config` variável:

        config.MapHttpAttributeRoutes();

7. Publica o projeto de servidor para a sua aplicação móvel do back-end.

###<a name="routes-registered"></a>Rotas registadas pelo controlador de armazenamento

O novo `TodoItemStorageController` expõe dois recursos subendereço no registo que gere:

- StorageToken

    + MENSAGEM de HTTP: Cria um token de armazenamento
    
        `/tables/TodoItem/{id}/MobileServiceFiles`
    
- MobileServiceFiles

    + HTTP GET: Obtém uma lista de ficheiros associado ao registo
    
        `/tables/TodoItem/{id}/MobileServiceFiles`

    + Eliminar HTTP: Elimina o ficheiro especificado no identificador de recursos do ficheiro
    
        `/tables/TodoItem/{id}/MobileServiceFiles/{fileid}`

###<a name="client-communication"></a>Cliente e servidor de comunicações

Tenha em atenção que `TodoItemStorageController` é que *não* têm uma rota para carregar ou transferir um blob. Se ao facto de um cliente móvel interage com blob armazenamento *diretamente* para realizar estas operações após a primeira introdução um token de SA (partilhados assinatura de acesso) para aceder a uma determinada blob ou um contentor em segurança. Esta é uma estrutura de arquitectura importante, como contrário acesso ao armazenamento estaria limitado por escalabilidade e disponibilidade de back-end móvel. Em vez disso, ligando diretamente para o armazenamento do Windows Azure, o cliente móvel pode tirar partido das suas funcionalidades tais como criação automática de partições e distribuição de geo.

Uma assinatura de acesso partilhado oferece um acesso delegado para recursos na sua conta de armazenamento. Isto significa que o utilizador pode conceder que um cliente limitado permissões para objetos na sua conta de armazenamento para um determinado período de tempo e com um conjunto específico de permissões, sem ter de partilhar o seu teclas de acesso de conta. Para saber mais, consulte o artigo [Noções sobre assinaturas de acesso partilhado].

O diagrama abaixo mostra as interações cliente e servidor. Antes de carregar um ficheiro, o cliente pede um token de SA de serviço. O serviço utiliza a cadeia de ligação de armazenamento para gerar uma SA nova, em seguida, devolve para o cliente. As associações de segurança são limitado de tempo e restringe permissões para apenas um determinado ficheiro ou o contentor. O cliente móvel, em seguida, utiliza esta SA e o cliente de armazenamento do Windows Azure SDK para carregar o ficheiro ao armazenamento blob.

![Pedir um token de SA](./media/app-service-mobile-xamarin-forms-blob-storage/storage-token-diagram.png)

## <a name="update-your-client-app-to-add-image-support"></a>Atualizar a sua aplicação de cliente para adicionar o suporte de imagem

Abra o projeto de guia de introdução Xamarin.Forms no Visual Studio ou Xamarin Studio. Vai instalar pacotes Nuget e atualizar o projeto de biblioteca portáteis e o iOS, Android e Windows projetos de cliente:

- [Adicionar pacotes Nuget](#add-nuget)
- [Adicionar IPlatform interface](#add-iplatform)
- [Adicionar FileHelper classe](#add-filehelper)
- [Adicionar um processador de sincronização do ficheiro](#file-sync-handler)
- [Atualizar TodoItemManager](#update-todoitemmanager)
- [Adicionar uma vista de detalhes](#add-details-view)
- [Atualizar a vista principal](#update-main-view)
- [Actualizar o projecto Android](#update-android), [iOS projeto](#update-ios), [projecto do Windows](#update-windows)

>[AZURE.NOTE] Neste tutorial só contém as instruções para o Android, iOS e plataformas da loja Windows, não Windows Phone.

###<a name="add-nuget"></a>Adicionar pacotes Nuget

A solução com o botão direito e selecione **Gerir Nuget pacotes para solução**. Adicione os seguintes pacotes Nuget para **todos os** projetos da solução. Certifique-se de que verificar a **versão de pré-lançamento de incluir**.

  - [Microsoft.Azure.Mobile.Client.Files]

  - [Microsoft.Azure.Mobile.Client.SQLiteStore]

  - [PCLStorage]

Para sua comodidade, este exemplo utiliza a biblioteca de [PCLStorage] , mas não é necessária pelo cliente Azure Mobile aplicações SDK.

[PCLStorage]: https://www.nuget.org/packages/PCLStorage/

###<a name="add-iplatform"></a>Adicionar IPlatform interface

Criar uma nova interface `IPlatform` no projeto principal biblioteca portátil. Isto segue o padrão de [Xamarin.Forms DependencyService] para carregar a classe de para a direita específicos da plataforma o tempo de execução. Mais tarde irá adicionar específicos da plataforma implementações em cada uma dos projectos de cliente.

1. Adicione o seguinte utilizando instruções:

        using Microsoft.WindowsAzure.MobileServices.Files;
        using Microsoft.WindowsAzure.MobileServices.Files.Metadata;
        using Microsoft.WindowsAzure.MobileServices.Sync;

2. Substitua a implementação com o seguinte:

        public interface IPlatform
        {
            Task <string> GetTodoFilesPathAsync();

            Task<IMobileServiceFileDataSource> GetFileDataSource(MobileServiceFileMetadata metadata);

            Task<string> TakePhotoAsync(object context);

            Task DownloadFileAsync<T>(IMobileServiceSyncTable<T> table, MobileServiceFile file, string filename);
        }

###<a name="add-filehelper"></a>Adicionar FileHelper classe

1. Criar uma nova classe `FileHelper` no projeto principal biblioteca portátil. Adicione o seguinte utilizando instruções:

        using System.IO;
        using PCLStorage;
        using System.Threading.Tasks;
        using Xamarin.Forms;

2. Adicione a definição de classe:

        public class FileHelper
        {
            public static async Task<string> CopyTodoItemFileAsync(string itemId, string filePath)
            {
                IFolder localStorage = FileSystem.Current.LocalStorage;

                string fileName = Path.GetFileName(filePath);
                string targetPath = await GetLocalFilePathAsync(itemId, fileName);

                var sourceFile = await localStorage.GetFileAsync(filePath);
                var sourceStream = await sourceFile.OpenAsync(FileAccess.Read);

                var targetFile = await localStorage.CreateFileAsync(targetPath, CreationCollisionOption.ReplaceExisting);

                using (var targetStream = await targetFile.OpenAsync(FileAccess.ReadAndWrite)) {
                    await sourceStream.CopyToAsync(targetStream);
                }

                return targetPath;
            }

            public static async Task<string> GetLocalFilePathAsync(string itemId, string fileName)
            {
                IPlatform platform = DependencyService.Get<IPlatform>();

                string recordFilesPath = Path.Combine(await platform.GetTodoFilesPathAsync(), itemId);

                    var checkExists = await FileSystem.Current.LocalStorage.CheckExistsAsync(recordFilesPath);
                    if (checkExists == ExistenceCheckResult.NotFound) {
                        await FileSystem.Current.LocalStorage.CreateFolderAsync(recordFilesPath, CreationCollisionOption.ReplaceExisting);
                    }

                return Path.Combine(recordFilesPath, fileName);
            }

            public static async Task DeleteLocalFileAsync(Microsoft.WindowsAzure.MobileServices.Files.MobileServiceFile fileName)
            {
                string localPath = await GetLocalFilePathAsync(fileName.ParentId, fileName.Name);
                var checkExists = await FileSystem.Current.LocalStorage.CheckExistsAsync(localPath);

                if (checkExists == ExistenceCheckResult.FileExists) {
                    var file = await FileSystem.Current.LocalStorage.GetFileAsync(localPath);
                    await file.DeleteAsync();
                }
            }
        }

###<a name="file-sync-handler"></a>Adicionar um processador de sincronização do ficheiro

Criar uma nova classe `TodoItemFileSyncHandler` no projeto principal biblioteca portátil. Esta classe contém as chamadas a partir do SDK do Azure para notificar o seu código quando um ficheiro é adicionado ou removido de retorno.

O SDK do cliente móvel Azure realmente não armazenar quaisquer dados de ficheiros: o cliente SDK invoca a implementação do `IFileSyncHandler` que por sua vez determina se e como os ficheiros são armazenados no dispositivo local.

1. Adicione o seguinte utilizando instruções:

        using System.Threading.Tasks;
        using Microsoft.WindowsAzure.MobileServices.Files.Sync;
        using Microsoft.WindowsAzure.MobileServices.Files;
        using Microsoft.WindowsAzure.MobileServices.Files.Metadata;
        using Xamarin.Forms;

2. Substitua a definição de classe com o seguinte: 

        public class TodoItemFileSyncHandler : IFileSyncHandler
        {
            private readonly TodoItemManager todoItemManager;

            public TodoItemFileSyncHandler(TodoItemManager itemManager)
            {
                this.todoItemManager = itemManager;
            }

            public Task<IMobileServiceFileDataSource> GetDataSource(MobileServiceFileMetadata metadata)
            {
                IPlatform platform = DependencyService.Get<IPlatform>();
                return platform.GetFileDataSource(metadata);
            }

            public async Task ProcessFileSynchronizationAction(MobileServiceFile file, FileSynchronizationAction action)
            {
                if (action == FileSynchronizationAction.Delete) {
                    await FileHelper.DeleteLocalFileAsync(file);
                }
                else { // Create or update. We're aggressively downloading all files.
                    await this.todoItemManager.DownloadFileAsync(file);
                }
            }
        }

###<a name="update-todoitemmanager"></a>Atualizar TodoItemManager

1. No **TodoItemManager.cs**, remova os comentários da linha `#define OFFLINE_SYNC_ENABLED`.

2. No **TodoItemManager.cs**, adicione o seguinte utilizando instruções:

        using System.IO;
        using Xamarin.Forms;
        using Microsoft.WindowsAzure.MobileServices.Files;
        using Microsoft.WindowsAzure.MobileServices.Files.Sync;
        using Microsoft.WindowsAzure.MobileServices.Eventing;

3. No construtor do `TodoItemManager`, adicione o seguinte depois da chamada para `DefineTable()`:

        // Initialize file sync
        this.client.InitializeFileSyncContext(new TodoItemFileSyncHandler(this), store);

4. No construtor, substitua a chamada para `InitializeAsync` com o seguinte. Isto irá garantir que não existem chamadas de retorno quando os registos são modificados no arquivo local. A funcionalidade de sincronização de ficheiros utiliza estes chamadas de retorno para acionar o processador de sincronização do ficheiro.

        this.client.SyncContext.InitializeAsync(store, StoreTrackingOptions.NotifyLocalAndServerOperations);

5. No `SyncAsync()`, adicione o seguinte depois da chamada para `PushAsync()`:

        await this.todoTable.PushFileChangesAsync();

6. Adicionar os seguintes métodos para `TodoItemManager`:

        internal async Task DownloadFileAsync(MobileServiceFile file)
        {
            var todoItem = await todoTable.LookupAsync(file.ParentId);
            IPlatform platform = DependencyService.Get<IPlatform>();

            string filePath = await FileHelper.GetLocalFilePathAsync(file.ParentId, file.Name); 
            await platform.DownloadFileAsync(this.todoTable, file, filePath);
        }

        internal async Task<MobileServiceFile> AddImage(TodoItem todoItem, string imagePath)
        {
            string targetPath = await FileHelper.CopyTodoItemFileAsync(todoItem.Id, imagePath);
            return await this.todoTable.AddFileAsync(todoItem, Path.GetFileName(targetPath));
        }

        internal async Task DeleteImage(TodoItem todoItem, MobileServiceFile file)
        {
            await this.todoTable.DeleteFileAsync(file);
        }

        internal async Task<IEnumerable<MobileServiceFile>> GetImageFilesAsync(TodoItem todoItem)
        {
            return await this.todoTable.GetFilesAsync(todoItem);
        }

###<a name="add-details-view"></a>Adicionar uma vista de detalhes

Nesta secção, irá adicionar uma nova vista de detalhes de um item de todo. A vista é criada quando o utilizador seleciona um item de todo e permite que as novas imagens seja adicionada a um item.

1. Adicione uma nova classe **TodoItemImage** para o projeto de biblioteca portátil com a implementação do seguinte:

        public class TodoItemImage : INotifyPropertyChanged
        {
            private string name;
            private string uri;

            public MobileServiceFile File { get; private set; }

            public string Name
            {
                get { return name; }
                set
                {
                    name = value;
                    OnPropertyChanged(nameof(Name));
                }
            }

            public string Uri
            {
                get { return uri; }      
                set
                {
                    uri = value;
                    OnPropertyChanged(nameof(Uri));
                }
            }

            public TodoItemImage(MobileServiceFile file, TodoItem todoItem)
            {
                Name = file.Name;
                File = file;

                FileHelper.GetLocalFilePathAsync(todoItem.Id, file.Name).ContinueWith(x => this.Uri = x.Result);
            }

            public event PropertyChangedEventHandler PropertyChanged;

            private void OnPropertyChanged(string propertyName)
            {
                PropertyChanged?.Invoke(this, new PropertyChangedEventArgs(propertyName));
            }
        }

2. Edite **App.cs**. Substituir a inicialização da `MainPage` com o seguinte:
    
        MainPage = new NavigationPage(new TodoList());

3. No **App.cs**, adicione a propriedade seguinte:

        public static object UIContext { get; set; }

4. O projeto de biblioteca portátil com o botão direito e selecione **Adicionar** -> **Novo Item** -> **em diferentes plataformas** -> **Formulários Xaml página**. Atribuir um nome a vista `TodoItemDetailsView`.

5. Abra **TodoItemDetailsView.xaml** e substituir o corpo da ContentPage com o seguinte:

          <Grid>
            <Grid.RowDefinitions>
              <RowDefinition Height="Auto"/>
              <RowDefinition Height="Auto"/>
              <RowDefinition Height="*"/>
            </Grid.RowDefinitions>
            <Button Clicked="OnAdd" Text="Add image"></Button>
            <ListView x:Name="imagesList"
                      ItemsSource="{Binding Images}"
                      IsPullToRefreshEnabled="false"
                      Grid.Row="2">
              <ListView.ItemTemplate>
                <DataTemplate>
                  <ImageCell ImageSource="{Binding Uri}"
                             Text="{Binding Name}">
                  </ImageCell>
                </DataTemplate>
              </ListView.ItemTemplate>
            </ListView>
          </Grid>

6. Editar **TodoItemDetailsView.xaml.cs** e adicione o seguinte utilizando instruções:

        using System.Collections.ObjectModel;
        using Microsoft.WindowsAzure.MobileServices.Files;

7. Substituir a execução do `TodoItemDetailsView` com o seguinte:

        public partial class TodoItemDetailsView : ContentPage
        {
            private TodoItemManager manager;

            public TodoItem TodoItem { get; set; }        
            public ObservableCollection<TodoItemImage> Images { get; set; }

            public TodoItemDetailsView(TodoItem todoItem, TodoItemManager manager)
            {
                InitializeComponent();
                this.Title = todoItem.Name;

                this.TodoItem = todoItem;
                this.manager = manager;

                this.Images = new ObservableCollection<TodoItemImage>();
                this.BindingContext = this;
            }

            public async Task LoadImagesAsync()
            {
                IEnumerable<MobileServiceFile> files = await this.manager.GetImageFilesAsync(TodoItem);
                this.Images.Clear();

                foreach (var f in files) {
                    var todoImage = new TodoItemImage(f, this.TodoItem);
                    this.Images.Add(todoImage);
                }
            }

            public async void OnAdd(object sender, EventArgs e)
            {
                IPlatform mediaProvider = DependencyService.Get<IPlatform>();
                string sourceImagePath = await mediaProvider.TakePhotoAsync(App.UIContext);

                if (sourceImagePath != null) {
                    MobileServiceFile file = await this.manager.AddImage(this.TodoItem, sourceImagePath);

                    var image = new TodoItemImage(file, this.TodoItem);
                    this.Images.Add(image);
                }
            }
        }

###<a name="update-main-view"></a>Atualizar a vista principal 

Atualize a vista principal para abrir a vista de detalhes quando está selecionado um item de todo.

Na **TodoList.xaml.cs**, substitua a execução do `OnSelected` com o seguinte:

    public async void OnSelected(object sender, SelectedItemChangedEventArgs e)
    {
        var todo = e.SelectedItem as TodoItem;

        if (todo != null) {
            var detailsView = new TodoItemDetailsView(todo, manager);
            await detailsView.LoadImagesAsync();
            await Navigation.PushAsync(detailsView);
        }

        todoList.SelectedItem = null;
    }

###<a name="update-android"></a>Actualizar o projecto Android

Adicione código específico da plataforma do projecto Android, incluindo o código para transferir um ficheiro e utilizar a câmara para capturar uma nova imagem. 

Este código utiliza o Xamarin.Forms [DependencyService](https://developer.xamarin.com/guides/xamarin-forms/dependency-service/) para carregar a classe de para a direita específicos da plataforma o tempo de execução.

1. Adicione o componente **Xamarin.Mobile** ao projeto Android.

2. Adicionar uma nova classe `DroidPlatform` com a seguinte implementação. Substitua "YourNamespace" principal espaço de nomes do seu projeto.

        using System;
        using System.IO;
        using System.Threading.Tasks;
        using Android.Content;
        using Microsoft.WindowsAzure.MobileServices.Files;
        using Microsoft.WindowsAzure.MobileServices.Files.Metadata;
        using Microsoft.WindowsAzure.MobileServices.Files.Sync;
        using Microsoft.WindowsAzure.MobileServices.Sync;
        using Xamarin.Media;

        [assembly: Xamarin.Forms.Dependency(typeof(YourNamespace.Droid.DroidPlatform))]
        namespace YourNamespace.Droid
        {
            public class DroidPlatform : IPlatform
            {
                public async Task DownloadFileAsync<T>(IMobileServiceSyncTable<T> table, MobileServiceFile file, string filename)
                {
                    var path = await FileHelper.GetLocalFilePathAsync(file.ParentId, file.Name);
                    await table.DownloadFileAsync(file, path);
                }

                public async Task<IMobileServiceFileDataSource> GetFileDataSource(MobileServiceFileMetadata metadata)
                {
                    var filePath = await FileHelper.GetLocalFilePathAsync(metadata.ParentDataItemId, metadata.FileName);
                    return new PathMobileServiceFileDataSource(filePath);
                }

                public async Task<string> TakePhotoAsync(object context)
                {
                    try {
                        var uiContext = context as Context;
                        if (uiContext != null) {
                            var mediaPicker = new MediaPicker(uiContext);
                            var photo = await mediaPicker.TakePhotoAsync(new StoreCameraMediaOptions());

                            return photo.Path;
                        }
                    }
                    catch (TaskCanceledException) {
                    }

                    return null;
                }

                public Task<string> GetTodoFilesPathAsync()
                {
                    string appData = Environment.GetFolderPath(Environment.SpecialFolder.MyDocuments);
                    string filesPath = Path.Combine(appData, "TodoItemFiles");

                    if (!Directory.Exists(filesPath)) {
                        Directory.CreateDirectory(filesPath);
                    }

                    return Task.FromResult(filesPath);
                }
            }
        }

3. Edite **MainActivity.cs**. No `OnCreate`, adicione o seguinte antes das chamadas para `LoadApplication()`:

        App.UIContext = this;

###<a name="update-ios"></a>Actualizar o projecto iOS

Adicione código específico da plataforma ao projeto iOS.

1. Adicione o componente **Xamarin.Mobile** ao projeto iOS.

2. Adicionar uma nova classe `TouchPlatform` com a seguinte implementação. Substitua "YourNamespace" principal espaço de nomes do seu projeto.

        using System;
        using System.Collections.Generic;
        using System.IO;
        using System.Text;
        using System.Threading.Tasks;
        using Microsoft.WindowsAzure.MobileServices.Files;
        using Microsoft.WindowsAzure.MobileServices.Files.Metadata;
        using Microsoft.WindowsAzure.MobileServices.Files.Sync;
        using Microsoft.WindowsAzure.MobileServices.Sync;
        using Xamarin.Media;

        [assembly: Xamarin.Forms.Dependency(typeof(YourNamespace.iOS.TouchPlatform))]
        namespace YourNamespace.iOS
        {
            class TouchPlatform : IPlatform
            {
                public async Task DownloadFileAsync<T>(IMobileServiceSyncTable<T> table, MobileServiceFile file, string filename)
                {
                    var path = await FileHelper.GetLocalFilePathAsync(file.ParentId, file.Name);
                    await table.DownloadFileAsync(file, path);
                }

                public async Task<IMobileServiceFileDataSource> GetFileDataSource(MobileServiceFileMetadata metadata)
                {
                    var filePath = await FileHelper.GetLocalFilePathAsync(metadata.ParentDataItemId, metadata.FileName);
                    return new PathMobileServiceFileDataSource(filePath);
                }

                public async Task<string> TakePhotoAsync(object context)
                {
                    try {
                        var mediaPicker = new MediaPicker();
                        var mediaFile = await mediaPicker.PickPhotoAsync();
                        return mediaFile.Path;
                    }
                    catch (TaskCanceledException) {
                        return null;
                    }
                }

                public Task<string> GetTodoFilesPathAsync()
                {
                    string filesPath = Path.Combine(Environment.GetFolderPath(Environment.SpecialFolder.MyDocuments), "TodoItemFiles");

                    if (!Directory.Exists(filesPath)) {
                        Directory.CreateDirectory(filesPath);
                    }

                    return Task.FromResult(filesPath);
                }
            }
        }

3. Editar **AppDelegate.cs** e remova os comentários a chamada para `SQLitePCL.CurrentPlatform.Init()`.

###<a name="update-windows"></a>Atualizar o projeto do Windows

1. Instale a extensão do Visual Studio [SQLite para o Windows 8.1](http://go.microsoft.com/fwlink/?LinkID=716919). Para obter mais informações, consulte o tutorial [Ativar sincronização offline para a sua aplicação do Windows](app-service-mobile-windows-store-dotnet-get-started-offline-data.md). 

2. Editar **Package.appxmanifest** e verificar a função de **câmara Web** .

3. Adicionar uma nova classe `WindowsStorePlatform` com a seguinte implementação. Substitua "YourNamespace" principal espaço de nomes do seu projeto.

        using System;
        using System.Threading.Tasks;
        using Microsoft.WindowsAzure.MobileServices.Files;
        using Microsoft.WindowsAzure.MobileServices.Files.Metadata;
        using Microsoft.WindowsAzure.MobileServices.Files.Sync;
        using Microsoft.WindowsAzure.MobileServices.Sync;
        using Windows.Foundation;
        using Windows.Media.Capture;
        using Windows.Storage;
        using YourNamespace;

        [assembly: Xamarin.Forms.Dependency(typeof(WinApp.WindowsStorePlatform))]
        namespace WinApp
        {
            public class WindowsStorePlatform : IPlatform
            {
                public async Task DownloadFileAsync<T>(IMobileServiceSyncTable<T> table, MobileServiceFile file, string filename)
                {
                    var path = await FileHelper.GetLocalFilePathAsync(file.ParentId, file.Name);
                    await table.DownloadFileAsync(file, path);
                }

                public async Task<IMobileServiceFileDataSource> GetFileDataSource(MobileServiceFileMetadata metadata)
                {
                    var filePath = await FileHelper.GetLocalFilePathAsync(metadata.ParentDataItemId, metadata.FileName);
                    return new PathMobileServiceFileDataSource(filePath);
                }

                public async Task<string> GetTodoFilesPathAsync()
                {
                    var storageFolder = ApplicationData.Current.LocalFolder;
                    var filePath = "TodoItemFiles";

                    var result = await storageFolder.TryGetItemAsync(filePath);

                    if (result == null) {
                        result = await storageFolder.CreateFolderAsync(filePath);
                    }

                    return result.Name; // later operations will use relative paths
                }

                public async Task<string> TakePhotoAsync(object context)
                {
                    try {
                        CameraCaptureUI dialog = new CameraCaptureUI();
                        Size aspectRatio = new Size(16, 9);
                        dialog.PhotoSettings.CroppedAspectRatio = aspectRatio;

                        StorageFile file = await dialog.CaptureFileAsync(CameraCaptureUIMode.Photo);
                        return file.Path;
                    }
                    catch (TaskCanceledException) {
                        return null;
                    }
                }
            }
        }

##<a name="summary"></a>Resumo

Este artigo descrita como utilizar o novo suporte de ficheiro no cliente do Azure Mobile e o server SDK para trabalhar com o armazenamento do Windows Azure. 

- Criar uma conta de armazenamento e adicione a cadeia de ligação à sua aplicação móvel do back-end. Apenas o back-end tiver a tecla para armazenamento do Windows Azure: o cliente móvel pede um token de SA (partilhados assinatura do Access), sempre que necessita para aceder ao armazenamento do Windows Azure. Para saber mais sobre tokens de SA em armazenamento do Windows Azure, consulte [Noções sobre assinaturas de acesso partilhado].

- Criar um controlador que subclasses `StorageController` para processar os pedidos de tokens SA e obter os ficheiros que estão associados um registo. Por predefinição, ficheiros estão associados um registo ao utilizar o ID do registo como parte do nome do contentor; o comportamento pode ser personalizado ao especificar uma implementação do `IContainerNameResolver`. Também pode ser personalizada a política de token SA.

- O SDK do cliente móvel Azure não armazenar armazenam quaisquer dados dos ficheiros. Em vez disso, o cliente SDK invoca seu `IFileSyncHandler`, que, em seguida, decida como (e se) estão armazenados os ficheiros no dispositivo local. Processador a sincronização está registado da seguinte forma:

        client.InitializeFileSync(new MyFileSyncHandler(), store);

      + `IFileSyncHandler.GetDataSource`chama-se quando o SDK do cliente móvel Azure precisa dos dados de ficheiro (por exemplo, como parte do processo de carregamento). Isto dá-lhe gerir a capacidade como (e se) ficheiros são armazenados no dispositivo local e devolver essas informações quando for necessário.

      + `IFileSyncHandler.ProcessFileSynchronizationAction`é chamado como parte do fluxo de sincronização do ficheiro. Uma referência de ficheiro e um valor de enumeração FileSynchronizationAction são fornecidos para que possa decidir como a sua aplicação deverá lidar esse evento (por exemplo, transferir automaticamente um ficheiro quando-é criada ou atualizada, eliminação de um ficheiro a partir do dispositivo local quando esse ficheiro é eliminado no servidor).

- A `MobileServiceFile` podem ser utilizados, tanto no modo online ou offline, utilizando um `IMobileServiceTable` ou `IMobileServiceSyncTable`, respetivamente. O cenário offline, o carregamento irá ocorrer quando a aplicação chama `PushFileChangesAsync`. Isto faz com que a fila de operação offline para serem processados; para cada operação de ficheiro, o cliente do Azure Mobile SDK vai invocar a `GetDataSource` método na `IFileSyncHandler` instância para obter o conteúdo do ficheiro para o carregamento.

- Para obter ficheiros de um item, ligar para o ' GetFilesAsync` method on the  `IMobileServiceTable<T> ` or IMobileServiceSyncTable<T>` instância. Este método devolve uma lista de ficheiros associada ao item de dados fornecidos. (Nota: Este é uma operação de *local* e irá devolver os ficheiros com base no estado do objeto quando foi sincronizados pela última vez. Para obter uma lista actualizada de ficheiros do servidor, deve iniciar uma operação de sincronização pela primeira vez.)

        IEnumerable<MobileServiceFile> files = await myTable.GetFilesAsync(myItem);

- A funcionalidade de sincronização de ficheiros utiliza as notificações de alteração do registo no arquivo de local para obter os registos que o cliente recebeu como parte de uma operação de emissão ou por solicitação. Fazê-lo ativar notificações local e servidor para o contexto de sincronização utilizando o `StoreTrackingOptions` parâmetro. 

        this.client.SyncContext.InitializeAsync(store, StoreTrackingOptions.NotifyLocalAndServerOperations);

      + Outras opções de controlo do arquivo de estão disponíveis, como notificações só local ou apenas de servidor. Pode adicionar ou chamada de retorno personalizada utilizando o proprietário de `EventManager` propriedade de `IMobileServiceClient`:

            jobService.MobileService.EventManager.Subscribe<StoreOperationCompletedEvent>(StoreOperationEventHandler);

- É possível adicionar ou remover os ficheiros de um registo modificando armazenamento de BLOBs diretamente, uma vez que a associação é obtida através de uma convenção de nomenclatura. No entanto, neste caso deverá sempre **actualizar o carimbo de hora registo quando os blobs associados forem modificados**. O cliente móvel do Azure SDK atualiza sempre um registo ao adicionar ou remover um ficheiro. 

    O motivo para este requisito é que alguns clientes móveis já tiver o registo no local armazenamento. Quando estes clientes executam uma solicitação elementar, este registo não será devolvido e o cliente não será uma consulta para os novos ficheiros associados. Para evitar este problema, é recomendável que atualizar o registo carimbo de hora quando efetuar qualquer alteração de armazenamento de BLOBs que não utilize o cliente móvel do Azure SDK.

- Projecto do cliente utiliza o padrão de [Xamarin.Forms DependencyService] para carregar a classe de específicos da plataforma para a direita em tempo de execução. Neste exemplo, definido uma interface `IPlatform` com implementações em cada um dos projectos específicos da plataforma.

<!-- URLs. -->

[Visual Studio Community 2013]: https://go.microsoft.com/fwLink/p/?LinkID=534203
[Criar uma aplicação de Xamarin.Forms]: app-service-mobile-xamarin-forms-get-started.md
[Xamarin.Forms DependencyService]: https://developer.xamarin.com/guides/xamarin-forms/dependency-service/
[Microsoft.Azure.Mobile.Client.Files]: https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client.Files/
[Microsoft.Azure.Mobile.Client.SQLiteStore]: https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client.SQLiteStore/
[Microsoft.Azure.Mobile.Server.Files]: https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Files/
[Compreender as assinaturas de acesso partilhadas]: ../storage/storage-dotnet-shared-access-signature-part-1.md
[Criar uma conta de armazenamento Azure]:  ../storage/storage-create-storage-account.md#create-a-storage-account
