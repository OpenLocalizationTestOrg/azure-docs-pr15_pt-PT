<properties 
    pageTitle="Operações de execução longa de consulta | Microsoft Azure" 
    description="Este tópico mostra como as operações de execução longa de inquérito." 
    services="media-services" 
    documentationCenter="" 
    authors="juliako" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/26/2016" 
    ms.author="juliako"/>


#<a name="delivering-live-streaming-with-azure-media-services"></a>Entregar a transmissão em direto com os serviços de multimédia do Microsoft Azure

##<a name="overview"></a>Descrição geral

Serviços de multimédia do Microsoft Azure oferece APIs que enviar pedidos para serviços de multimédia para iniciar o operações (por exemplo: criar, iniciar, parar ou eliminar um canal). Estas operações são execução longa.

Serviços de multimédia .NET SDK fornece APIs que envie o pedido e aguarde que a operação esteja concluída (internamente, as APIs são de consulta para o progresso da operação em algumas intervalos). Por exemplo, ao ligar para o canal. Start (), o método devolve depois do canal for iniciado. Também pode utilizar a versão assíncrona: aguardar canal. StartAsync() (para obter informações sobre o padrão de assíncrona baseado em tarefas, consulte o artigo [TOQUE](https://msdn.microsoft.com/library/hh873175(v=vs.110).aspx)). APIs que enviar um pedido de operação e, em seguida, as inquérito para o estado até está concluída a operação de são designadas por "inquéritos métodos". Estes métodos (especialmente a versão de assíncrona) são recomendados para aplicações de cliente avançada e/ou serviços com estado.

Existem cenários onde uma aplicação não é possível aguardar para um pedido de http longa e pretende consultar manualmente para o progresso de operação. Um exemplo típico seria num browser interagir com um serviço web sem estado: quando o browser pede para criar um canal, o serviço web inicia uma operação de execução longa e devolve a identificação de operação para o browser. Browser, em seguida, pode pedir o serviço web para obter o estado de funcionamento com base no ID da. Serviços de multimédia .NET SDK fornece APIs que são úteis para este cenário. Estas APIs são designadas por "não inquéritos métodos".
"Métodos de que não sejam inquéritos" tem o seguinte padrão de nomenclatura: enviar*OperationName*operação (por exemplo, SendCreateOperation). Enviar métodos de operação de*OperationName*devolvem o objeto **IOperation** ; o objeto devolvido contém informações que podem ser utilizadas para controlar a operação. Os métodos de enviar*OperationName*OperationAsync devolvem **tarefa<IOperation>**.

Atualmente, as seguintes classes suportam não inquéritos métodos: **canal**, **StreamingEndpoint**e **programa**.

Verifique a existência do Estado da operação, utilize o método de **GetOperation** na classe **OperationBaseCollection** . Utilizar os seguintes intervalos para verificar o estado de funcionamento: para operações de **canal** e **StreamingEndpoint** , utilizar 30 segundos; para operações de **programa** , utilize 10 segundos.


##<a name="example"></a>Exemplo

O exemplo seguinte define uma classe denominada **ChannelOperations**. Esta definição de classe pode ser um ponto de partida para a sua definição de classe de serviço web. Para simplificar, os exemplos seguintes utilizam as versões de que não sejam assíncrona dos métodos.

O exemplo também mostra como o cliente poderá utilizar esta classe.

###<a name="channeloperations-class-definition"></a>Definição de classe ChannelOperations

    /// <summary> 
    /// The ChannelOperations class only implements 
    /// the Channel’s creation operation. 
    /// </summary> 
    public class ChannelOperations
    {
        // Read values from the App.config file.
        private static readonly string _mediaServicesAccountName =
            ConfigurationManager.AppSettings["MediaServicesAccountName"];
        private static readonly string _mediaServicesAccountKey =
            ConfigurationManager.AppSettings["MediaServicesAccountKey"];
    
        // Field for service context.
        private static CloudMediaContext _context = null;
        private static MediaServicesCredentials _cachedCredentials = null;
    
        public ChannelOperations()
        {
                _cachedCredentials = new MediaServicesCredentials(_mediaServicesAccountName,
                    _mediaServicesAccountKey);
    
                _context = new CloudMediaContext(_cachedCredentials);    }
    
        /// <summary>  
        /// Initiates the creation of a new channel.  
        /// </summary>  
        /// <param name="channelName">Name to be given to the new channel</param>  
        /// <returns>  
        /// Operation Id for the long running operation being executed by Media Services. 
        /// Use this operation Id to poll for the channel creation status. 
        /// </returns> 
        public string StartChannelCreation(string channelName)
        {
            var operation = _context.Channels.SendCreateOperation(
                new ChannelCreationOptions
                {
                    Name = channelName,
                    Input = CreateChannelInput(),
                    Preview = CreateChannelPreview(),
                    Output = CreateChannelOutput()
                });
    
            return operation.Id;
        }
    
        /// <summary> 
        /// Checks if the operation has been completed. 
        /// If the operation succeeded, the created channel Id is returned in the out parameter.
        /// </summary> 
        /// <param name="operationId">The operation Id.</param> 
        /// <param name="channel">
        /// If the operation succeeded, 
        /// the created channel Id is returned in the out parameter.</param>
        /// <returns>Returns false if the operation is still in progress; otherwise, true.</returns> 
        public bool IsCompleted(string operationId, out string channelId)
        {
            IOperation operation = _context.Operations.GetOperation(operationId);
            bool completed = false;
    
            channelId = null;
    
            switch (operation.State)
            {
                case OperationState.Failed:
                    // Handle the failure. 
                    // For example, throw an exception. 
                    // Use the following information in the exception: operationId, operation.ErrorMessage.
                    break;
                case OperationState.Succeeded:
                    completed = true;
                    channelId = operation.TargetEntityId;
                    break;
                case OperationState.InProgress:
                    completed = false;
                    break;
            }
            return completed;
        }
    
    
        private static ChannelInput CreateChannelInput()
        {
            return new ChannelInput
            {
                StreamingProtocol = StreamingProtocol.RTMP,
                AccessControl = new ChannelAccessControl
                {
                    IPAllowList = new List<IPRange>
                    {
                        new IPRange
                        {
                            Name = "TestChannelInput001",
                            Address = IPAddress.Parse("0.0.0.0"),
                            SubnetPrefixLength = 0
                        }
                    }
                }
            };
        }
    
        private static ChannelPreview CreateChannelPreview()
        {
            return new ChannelPreview
            {
                AccessControl = new ChannelAccessControl
                {
                    IPAllowList = new List<IPRange>
                    {
                        new IPRange
                        {
                            Name = "TestChannelPreview001",
                            Address = IPAddress.Parse("0.0.0.0"),
                            SubnetPrefixLength = 0
                        }
                    }
                }
            };
        }
    
        private static ChannelOutput CreateChannelOutput()
        {
            return new ChannelOutput
            {
                Hls = new ChannelOutputHls { FragmentsPerSegment = 1 }
            };
        }
    }

###<a name="the-client-code"></a>O código do cliente

    ChannelOperations channelOperations = new ChannelOperations();
    string opId = channelOperations.StartChannelCreation("MyChannel001");
    
    string channelId = null;
    bool isCompleted = false;
    
    while (isCompleted == false)
    {
        System.Threading.Thread.Sleep(TimeSpan.FromSeconds(30));
        isCompleted = channelOperations.IsCompleted(opId, out channelId);
    }
    
    // If we got here, we should have the newly created channel id.
    Console.WriteLine(channelId);
 


##<a name="media-services-learning-paths"></a>Caminhos de aprendizagem dos serviços de multimédia

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fornecer comentários

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
