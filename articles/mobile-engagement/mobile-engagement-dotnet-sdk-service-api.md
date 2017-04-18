<properties 
    pageTitle="Utilizar .NET SDK para aceder à API de serviço do Azure Mobile Cativação" 
    description="Descreve como utilizar o Mobile Cativação .NET SDK para aceder ao Azure Mobile Cativação serviço APIs"        
    services="mobile-engagement" 
    documentationCenter="mobile" 
    authors="piyushjo" 
    manager="erikre" 
    editor="" />

<tags 
    ms.service="mobile-engagement" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="mobile-multiple" 
    ms.devlang="dotnet" 
    ms.topic="article" 
    ms.date="08/19/2016" 
    ms.author="piyushjo" />

#<a name="using-net-sdk-to-access-azure-mobile-engagement-service-apis"></a>Utilizar .NET SDK para aceder à API de serviço do Azure Mobile Cativação

Azure Mobile Cativação expõe um conjunto de API para gerir dispositivos, campanhas de atingir/Push etc. Para interagir com estas APIs, também fornecemos-lhe um [ficheiro de Swagger](https://github.com/Azure/azure-rest-api-specs/blob/master/arm-mobileengagement/2014-12-01/swagger/mobile-engagement.json) que pode utilizar com ferramentas para gerar SDK para o idioma preferido. Recomendamos que utilize a ferramenta de [AutoRest](https://github.com/Azure/AutoRest) para gerar o SDK a partir de ficheiro nosso Swagger. 

Criámos um .NET SDK de maneira semelhante permite-lhe interagir com estas APIs utilizando um dispositivo de moldagem c# e não tem de fazer a negociação token de autenticação e atualize o seu próprio.  

Este exemplo atravessa o conjunto de passos a seguir para utilizar o .NET SDK:

1. Em primeiro lugar, tem de configurar a autenticação para sua APIs utilizando o Azure Active Directory como descrito [aqui](mobile-engagement-api-authentication.md#authentication). No final destes passos, deverá ter um válido **SubscriptionId**, **TenantId**, **ApplicationId** e **palavra-passe**. 

2. Irá utilizamos uma aplicação de consola do Windows simple para demonstrar como trabalhar com o .NET SDK com o cenário de criação de uma campanha de anúncio. Por isso, abrir o Visual Studio e criar uma **Aplicação de consola**.   

3. A seguir tem de transferir o SDK .NET que está disponível como a **Biblioteca de gestão do Microsoft Azure Cativação** na Galeria de Nuget [aqui](https://www.nuget.org/packages/Microsoft.Azure.Management.Engagement/).
Se estiver a instalar o Nuget do Visual Studio, é necessário para se certificar de que tem verificação marcada a opção **incluir a versão de pré-lançamento** ao procurar o pacote de:

    ![][1]

4. No `Program.cs` de ficheiros, adicione os espaços de nomes seguintes:

        using Microsoft.Rest.Azure.Authentication;
        using Microsoft.Azure.Management.Engagement;
        using Microsoft.Azure.Management.Engagement.Models;

5. Em seguida, é necessário definir as seguintes constantes que utilizamos para a autenticação e interagir com a aplicação de Cativação Mobile na qual está a criar a campanha anúncio:

        // For authentication
        const string TENANT_ID = "<Your TenantId>";
        const string CLIENT_ID = "<Your Application Id>";
        const string CLIENT_SECRET = "<Your Secret>";
        const string SUBSCRIPTION_ID = "<Your Subscription Id>";

        // This is the Azure Resource group concept for grouping together resources 
        //  see here: https://azure.microsoft.com/en-us/documentation/articles/resource-group-portal/
        const string RESOURCE_GROUP = "";

        // For Mobile Engagement operations
        // App Collection Name 
        const string APP_COLLECTION_NAME = "";
        // Application Resource Name - make sure you are using the one as specified in the Azure portal (NOT the App Name)
        const string APP_RESOURCE_NAME = "";

6. Definir o `EngagementManagementClient` variável que utilizamos para ligar os métodos de Mobile Cativação SDK para:

        static EngagementManagementClient engagementClient; 

7. Adicionar o seguinte procedimento para sua `Main` método:

        try
            {
                // Initialize the Engagement SDK to call out APIs. 
                InitEngagementClient().Wait();

                // Create a Reach campaign
                CreateCampaign().Wait();
            }
            catch (Exception ex)
            {
                Console.WriteLine(ex.InnerException.Message);
                throw ex;
            }

8. Definir o método seguinte que leva-o até o cuidado de inicialização a `EngagementManagementClient` ao primeiro de autenticação e, em seguida associando próprio com a aplicação de Cativação Mobile que planeia criar a campanha anúncio:

        private static async Task InitEngagementClient()
        {
            var credentials = await ApplicationTokenProvider.LoginSilentAsync(TENANT_ID, CLIENT_ID, CLIENT_SECRET);
            engagementClient = new EngagementManagementClient(credentials) { SubscriptionId = SUBSCRIPTION_ID };
            
            // This is the Azure concept of ResourceGroup
            engagementClient.ResourceGroupName = RESOURCE_GROUP;

            // This is your Mobile Engagement App Collection & App Resource Name in which you create the campaign
            engagementClient.AppCollection = APP_COLLECTION_NAME;
            engagementClient.AppName = APP_RESOURCE_NAME;
        }

    > [AZURE.IMPORTANT] Tenha em atenção que precisa de utilizar o **Nome do recurso de aplicação** , tal como foi definido no portal de gestão Azure para o parâmetro nome. 

9. Por fim, defina o método de CreateCampaign que encarregam de utilização de EngagementClient anteriormente inicializado para criar uma simples **qualquer altura** & **só de notificação de** campanha com um título e uma mensagem: 

        private async static Task CreateCampaign()
        {
            //  Refer to the Announcement Campaign format from here - 
            //      https://msdn.microsoft.com/en-us/library/azure/mt683751.aspx
            // Make sure you are passing all the non-optional parameters
            Campaign parameters = new Campaign(
                name:"WelcomeCampaign",
                notificationTitle: "Welcome", 
                notificationMessage: "Thank you for installing the app!",
                type:"only_notif",
                deliveryTime:"any"
                );

            // Refer to the Campaign Kinds from here - https://msdn.microsoft.com/en-us/library/azure/mt683742.aspx
            CampaignStateResult result = 
                await engagementClient.Campaigns.CreateAsync(CampaignKinds.Announcements, parameters);
            Console.WriteLine("Campaign Id '{0}' was created successfully and it is in '{1}' state", result.Id, result.State);
        }

10. Executar a aplicação de consola e verá o seguinte na criação com êxito da campanha:

    **Id de campanha '159' foi criado com êxito e se encontra no estado 'rascunho'**

<!-- Images. -->

[1]: ./media/mobile-engagement-dotnet-sdk-service-api/include-prerelease.png
