<properties
    pageTitle="Carregar ficheiros a partir de dispositivos utilizando IoT concentrador | Microsoft Azure"
    description="Siga este tutorial para saber como carregar ficheiros a partir de dispositivos com Azure IoT concentrador c#."
    services="iot-hub"
    documentationCenter=".net"
    authors="fsautomata"
    manager="timlt"
    editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="dotnet"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="06/21/2016"
     ms.author="elioda"/>

# <a name="tutorial-how-to-upload-files-from-devices-to-the-cloud-with-iot-hub"></a>Tutorial: Como carregar ficheiros a partir de dispositivos na nuvem com IoT concentrador

## <a name="introduction"></a>Introdução

Azure IoT concentrador é um serviço totalmente gerido que permite fiável e seguras bidirecional as comunicações entre milhões de IoT dispositivos e uma aplicação back-end. O dispositivo para cloud básico ilustram a tutoriais anterior ([Introdução ao centro de IoT] e [enviar mensagens de dispositivo de nuvem com IoT concentrador]) e na nuvem-a-dispositivo funcionalidade mensagens de IoT concentrador e o tutorial de [mensagens de processo dispositivo para nuvem] descreve uma forma de forma fiável guardar mensagens de nuvem de dispositivo de armazenamento de Blobs do Azure. No entanto, em alguns cenários não é possível facilmente mapear os dados que os dispositivos de enviar para as mensagens de dispositivo para nuvem relativamente pequenas que aceita IoT concentrador. Alguns exemplos incluem ficheiros grandes que contenham imagens, vídeos, dados de vibração amostrados com elevada frequência, ou que contenham algumas formulário de dados pré-processado. Estes ficheiros normalmente são processadas na nuvem utilizando ferramentas como [Azure dados fábrica] ou na pilha de [Hadoop] em lote. Quando carregamentos de ficheiros a partir de um dispositivo preferidos para enviar eventos, ainda pode utilizar a funcionalidade de segurança e fiabilidade IoT concentrador.

Neste tutorial constrói no código no tutorial [enviar mensagens de dispositivo de nuvem com IoT concentrador] para mostrar-lhe como utilizar as funcionalidades de carregamento de ficheiro do IoT concentrador. Mostra como segura fornecer um dispositivo com um Azure blob URI para carregar um ficheiro e como utilizar as notificações de carregamento de ficheiro IoT concentrador para acionar processamento do ficheiro no seu back-end da aplicação.

No final deste tutorial executa duas aplicações de consola do Windows:

* **SimulatedDevice**, uma versão modificada da aplicação que criou no tutorial [enviar mensagens de dispositivo de nuvem com IoT concentrador] , que carrega um ficheiro de armazenamento utilizando um URI SA fornecido pelo seu centro IoT.
* **ReadFileUploadNotification**, que recebe as notificações de carregamento de ficheiros a partir do seu centro IoT.

> [AZURE.NOTE] Concentrador IoT suporta vários plataformas de dispositivos e idiomas (incluindo C, Java e Javascript) através do dispositivo de Azure IoT SDK. Referem-se para o [Centro de programadores do Azure IoT] para obter instruções passo a passo sobre como ligar o seu dispositivo para o código mostrado neste tutorial e geralmente Azure IoT concentrador.

Para poder concluir este tutorial, precisa do seguinte:

+ Microsoft Visual Studio 2015,

+ Uma conta do Azure active. (Se não tiver uma conta, pode criar uma [conta gratuita] [ lnk-free-trial] apenas de duas minutos.)

## <a name="associate-an-azure-storage-account-to-iot-hub"></a>Associar uma conta de armazenamento do Windows Azure a IoT concentrador

Uma vez que o dispositivo simulado carrega um ficheiro para um blob, tem de ter uma conta de [Armazenamento do Windows Azure] associada a IoT concentrador. Ao associar uma conta de armazenamento do Windows Azure um concentrador de IoT, o concentrador pode gerar um URI de SA que pode utilizar um dispositivo para segura carregar um ficheiro a um contentor blob. O serviço de IoT concentrador e dos SDK dispositivo coordenam o processo que gera o URI SA e torna disponível um dispositivo para utilizar para carregar um ficheiro.

Siga as instruções no [ficheiro de configurar os carregamentos pendentes através do portal Azure] [ lnk-configure-upload] para associar uma conta de armazenamento do Windows Azure ao seu centro IoT.

## <a name="upload-a-file-from-a-simulated-device"></a>Carregar um ficheiro a partir de um dispositivo simulado

Nesta secção, pode modifica a aplicação de dispositivo simulada que criou no [enviar mensagens de dispositivo de nuvem com IoT concentrador] para receber mensagens de dispositivo de nuvem a partir do hub a IoT.

1. No Visual Studio, com o botão direito do projecto **SimulatedDevice** , clique em **Adicionar**e, em seguida, clique em **Item existente**. Navegue para um ficheiro de imagem e inclui-los no seu projeto. Neste tutorial assume a imagem se chamar `image.jpg`.

2. Com o botão direito na imagem e, em seguida, clique em **Propriedades**. Certifique-se de que a **Copiar para directório de saída** está definido para **Copiar sempre**.

    ![][1]

3. No ficheiro de **Program.cs** , adicione as seguintes instruções na parte superior do ficheiro:

        using System.IO;

4. Adicione o seguinte método para a classe de **programa** :
         
        private static async void SendToBlobAsync()
        {
            string fileName = "image.jpg";
            Console.WriteLine("Uploading file: {0}", fileName);
            var watch = System.Diagnostics.Stopwatch.StartNew();

            using (var sourceData = new FileStream(@"image.jpg", FileMode.Open))
            {
                await deviceClient.UploadToBlobAsync(fileName, sourceData);
            }

            watch.Stop();
            Console.WriteLine("Time to upload file: {0}ms\n", watch.ElapsedMilliseconds);
        }

    O `UploadToBlobAsync` método leva na origem de nome e uma sequência de ficheiro do ficheiro a ser carregados e trata o carregamento ao armazenamento. A aplicação da consola apresenta o tempo necessário para carregar o ficheiro.

5. Adicione o seguinte método no método **principal** , para a direita antes do `Console.ReadLine()` linha:

        SendToBlobAsync();

> [AZURE.NOTE] Para saké do simplificar, este tutorial não implementa qualquer política de repetir. No código de produção, deverá implementar políticas de repetir (como duplicar exponencial), tal como sugerido no artigo MSDN [Processamento de falhas breves].

## <a name="receive-a-file-upload-notification"></a>Receber uma notificação de carregamento de ficheiro

Nesta secção, escreva uma aplicação de consola do Windows que recebe mensagens de notificação de carregamento de ficheiro a partir do IoT Hub.

1. Na solução do Visual Studio atual, crie um novo projeto do Visual c# Windows utilizando o modelo de projeto de **Aplicação de consola** . Nome do projeto **ReadFileUploadNotification**.

    ![Novo projeto no Visual Studio][2]

2. No Explorador de solução, com o botão direito do projecto **ReadFileUploadNotification** e, em seguida, clique em **Gerir pacotes de NuGet**.

    Esta ação apresentará a janela gerir pacotes de NuGet.

2. Procurar `Microsoft.Azure.Devices`, clique em **instalar**e aceite os termos de utilização. 

    Isto transfere, instalações e adiciona uma referência para o [Azure IoT - pacote de serviço SDK NuGet] no projeto **ReadFileUploadNotification** .

3. No ficheiro de **Program.cs** , adicione as seguintes instruções na parte superior do ficheiro:

        using Microsoft.Azure.Devices;

4. Adicione os campos seguintes para a classe de **programa** . Substitua o valor de marcador de posição com a cadeia de ligação IoT concentrador de [Introdução ao centro de IoT]:

        static ServiceClient serviceClient;
        static string connectionString = "{iot hub connection string}";
        
5. Adicione o seguinte método para a classe de **programa** :
   
        private async static Task ReceiveFileUploadNotificationAsync()
        {
            var notificationReceiver = serviceClient.GetFileNotificationReceiver();

            Console.WriteLine("\nReceiving file upload notification from service");
            while (true)
            {
                var fileUploadNotification = await notificationReceiver.ReceiveAsync();
                if (fileUploadNotification == null) continue;

                Console.ForegroundColor = ConsoleColor.Yellow;
                Console.WriteLine("Received file upload noticiation: {0}", string.Join(", ", fileUploadNotification.BlobName));
                Console.ResetColor();

                await notificationReceiver.CompleteAsync(fileUploadNotification);
            }
        }

    Note que o padrão de receção é o mesmo que utilizou para receber mensagens de dispositivo de nuvem a partir da aplicação de dispositivo.

6. Por fim, adicione as seguintes linhas para o método **principais** :

        Console.WriteLine("Receive file upload notifications\n");
        serviceClient = ServiceClient.CreateFromConnectionString(connectionString);
        ReceiveFileUploadNotificationAsync().Wait();
        Console.ReadLine();

## <a name="run-the-applications"></a>Execute as aplicações

Agora está pronto para executar as aplicações.

1. No Visual Studio, a solução com o botão direito e selecione **projetos de definir o arranque**. Selecione **vários projetos de arranque**e, em seguida, selecione a ação **Iniciar** para **ReadFileUploadNotification** e **SimulatedDevice**.

2. Prima **F5**. Ambas as aplicações deverão começar. Deverá visualizar o carregamento efetuado numa aplicação de consola e a mensagem de notificação de carregamento a ser recebida pela aplicação de consola. Pode utilizar o [Azure portal] ou o Explorador de servidor do Visual Studio para verificar a presença do ficheiro carregado na sua conta de armazenamento do Windows Azure.

  ![][50]


## <a name="next-steps"></a>Próximos passos

Neste tutorial, aprendeu como tirar partido as capacidades de carregamento de ficheiro do concentrador de IoT para simplificar a carregamentos de ficheiros a partir de dispositivos. Pode continuar a explorar funcionalidades de concentrador de IoT e cenários com os seguintes artigos:

- [Criar um concentrador de IoT através de programação][lnk-create-hub]
- [Introdução ao C SDK][lnk-c-sdk]
- [Concentrador IoT SDK][lnk-sdks]

Para explorar ainda mais as funcionalidades do concentrador de IoT, consulte:

- [Simulação de um dispositivo com o SDK do Gateway IoT][lnk-gateway]

<!-- Images. -->

[50]: ./media/iot-hub-csharp-csharp-file-upload/run-apps1.png
[1]: ./media/iot-hub-csharp-csharp-file-upload/image-properties.png
[2]: ./media/iot-hub-csharp-csharp-file-upload/create-identity-csharp1.png

<!-- Links -->

[Portal do Azure]: https://portal.azure.com/

[Dados Azure fábrica]: https://azure.microsoft.com/documentation/services/data-factory/
[Hadoop]: https://azure.microsoft.com/documentation/services/hdinsight/

[Enviar mensagens de nuvem-a-dispositivo com IoT concentrador]: iot-hub-csharp-csharp-c2d.md
[Processar mensagens de dispositivo à nuvem]: iot-hub-csharp-csharp-process-d2c.md
[Introdução ao centro de IoT]: iot-hub-csharp-csharp-getstarted.md
[Centro de programadores do Azure IoT]: http://www.azure.com/develop/iot

[Processamento de breves de falhas]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[Armazenamento Azure]: ../storage/storage-create-storage-account.md#create-a-storage-account
[lnk-configure-upload]: iot-hub-configure-file-upload.md
[Azure IoT - serviço SDK NuGet pacote]: https://www.nuget.org/packages/Microsoft.Azure.Devices/
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[lnk-create-hub]: iot-hub-rm-template-powershell.md
[lnk-c-sdk]: iot-hub-device-sdk-c-intro.md
[lnk-sdks]: iot-hub-devguide-sdks.md

[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md


