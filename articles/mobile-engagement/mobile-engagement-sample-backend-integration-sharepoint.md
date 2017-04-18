<properties 
    pageTitle="Azure Cativação móvel - integração de back-end" 
    description="Ligar o Azure Mobile Cativação com um back-end do SharePoint para criar campanhas a partir do SharePoint" 
    services="mobile-engagement" 
    documentationCenter="mobile" 
    authors="piyushjo" 
    manager="dwrede" 
    editor=""/>

<tags 
    ms.service="mobile-engagement" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="mobile-multiple" 
    ms.devlang="dotnet" 
    ms.topic="article" 
    ms.date="08/19/2016" 
    ms.author="piyushjo" />

# <a name="azure-mobile-engagement---api-integration"></a>Azure Cativação móvel - integração API

Num sistema de marketing automatizado, criar e ativar as campanhas de marketing também ocorrem automaticamente. Para este efeito - Azure Mobile Cativação permite criar essas campanhas de marketing automatizadas utilizando APIs também. 

Normalmente, os clientes utilizam a interface de front-end Mobile Cativação para criar anúncios/inquéritos etc como parte das suas campanhas de marketing. No entanto à medida que as campanhas de marketing ficam maduros, existe uma necessidade de tirar partido dos dados bloqueados nos sistemas de back-end (como qualquer sistema CRM ou sistema CMS como o SharePoint) para que pode ser criada uma tubagem totalmente automatizada que cria campanhas no Mobile Cativação dinamicamente com base nos dados a fluir na partir dos sistemas de back-end. 

![][5]

Neste tutorial vai através de um cenário em que um utilizador empresarial do SharePoint preenche uma lista do SharePoint com dados de marketing e um processo automatizado levantar o auscultador de itens a partir da lista e liga-se com o sistema de Cativação Mobile utilizando APIs do resto disponíveis para criar uma campanha de marketing a partir de dados do SharePoint. 
 
> [AZURE.IMPORTANT] Em geral, pode utilizar este exemplo como ponto de partida para compreender como ligar a qualquer Mobile Cativação REST API como detalhes de dois aspectos chaves do chamar APIs - parâmetros de autenticação e que passe. 

## <a name="sharepoint-integration"></a>Integração do SharePoint
1. Eis o aspeto da lista do SharePoint de exemplo. **Título**, **categoria**, **NotificationTitle**, **mensagem** e **URL** são utilizadas para criar o anúncio. Existe uma coluna denominada **IsProcessed** que é utilizado pelo processo de automatização do exemplo, a forma de um programa de consola. É possível executar esta consola do programa como um WebJob Azure para que é possível agendar ou diretamente pode utilizar o fluxo de trabalho do SharePoint para o programa criar e ativar o anúncio quando é inserido um item na lista do SharePoint. Neste exemplo estamos a utilizar o programa de consola que analise os itens na lista do SharePoint e criar anúncio no Azure Mobile Cativação para cada um deles e, em seguida, por fim, assinala o sinalizador de **IsProcessed** para ser VERDADEIRO na criação de anúncio efetuada com êxito.

    ![][1]

2. Estamos a utilizar o código do exemplo *autenticação remota no SharePoint Online utilizando o modelo de objeto do cliente* [aqui](https://code.msdn.microsoft.com/Remote-Authentication-in-b7b6f43c) para autenticar com a lista do SharePoint.
 
3. Uma vez autenticado, podemos loop os itens de lista para descobrir quaisquer itens recentemente criados (que terá **IsProcessed** = false). 

        static async void CreateCampaignFromSharepoint()
        {
            using (ClientContext clientContext = ClaimClientContext.GetAuthenticatedContext(targetSharepointSite))
            {
                // Using https://code.msdn.microsoft.com/Remote-Authentication-in-b7b6f43c for authentication     
                Web site = clientContext.Web;
                List list = site.Lists.GetByTitle("VideoContent");
                CamlQuery query = new CamlQuery();
                query.ViewXml = "<View/>";
                ListItemCollection items = list.GetItems(query);

                // Load the SharePoint list
                clientContext.Load(list);
                clientContext.Load(items);
                clientContext.ExecuteQuery();

                // Loop through the list to go through all the items which are newly added
                foreach (ListItem item in items)
                    if (item["IsProcessed"].ToString() != "Yes")
                    {
                        string name = item["Title"].ToString();
                        string notificationTitle = item["NotificationTitle"].ToString();
                        string notificationMessage = item["Message"].ToString();
                        string category = item["Category"].ToString();
                        string actionURL = ((FieldUrlValue)item["URL"]).Url;

                        // Send an HTTP request to create AzME campaign
                        int campaignId = CreateAzMECampaign
                            (name, notificationTitle, notificationMessage, category, actionURL).Result;

                        if (campaignId != -1)
                        {
                            // If creating campaign is successful then set this to true
                            item["IsProcessed"] = "Yes";

                            // Now try to activate the campaign also
                            await ActivateAzMECampaign(campaignId);
                        }
                        else
                        {
                            item["IsProcessed"] = "Error";
                        }
                        item.Update();
                    }
                clientContext.ExecuteQuery();
            }
        }

## <a name="mobile-engagement-integration"></a>Integração de Cativação Mobile
1.  Assim que foi encontrar um item que necessita de processamento - podemos extrair as informações necessárias para criar um anúncio do item de lista e de chamada `CreateAzMECampaign` criá-lo e, posteriormente `ActivateAzMECampaign` ativá-lo. Estes são essencialmente REST API as chamadas que entra em contacto para o back-end Cativação Mobile. 

2.  O Mobile Cativação REST APIs necessitam de um **cabeçalho de HTTP de autorização de esquema auth básicas** que é composta a `ApplicationId` e o `ApiKey` que obtém o portal do Azure. Certifique-se de que está a utilizar a tecla a partir da secção de **teclas de api** e *não* a partir da secção de **teclas sdk** . 

    ![][2]

        static string CreateAuthZHeader()
        {
            string BasicAuthzHeaderString = "Basic " + EncodeTo64(ApplicationId + ":" + ApiKey);
            return BasicAuthzHeaderString;
        }

        static public string EncodeTo64(string toEncode)
        {
            byte[] toEncodeAsBytes = System.Text.ASCIIEncoding.ASCII.GetBytes(toEncode);
            string returnValue = System.Convert.ToBase64String(toEncodeAsBytes);
            return returnValue;
        }  

3. Para criar o anúncio type campanha - consulte a [documentação](https://msdn.microsoft.com/library/azure/mt683750.aspx). É necessário para se certificar de que está a especificar da campanha `kind` como *anúncio* e a [carga útil](https://msdn.microsoft.com/library/azure/mt683751.aspx) e transmitir como FormUrlEncodedContent. 

        static async Task<int> CreateAzMECampaign(string campaignName, string notificationTitle, 
            string notificationMessage, string notificationCategory, string actionURL)
        {
            string createURIFragment = "/reach/1/create";

            using (var client = new HttpClient())
            {
                // Add Authorization Header
                client.DefaultRequestHeaders.TryAddWithoutValidation("Authorization", CreateAuthZHeader());
                
                // Create the payload to send the content
                // Reference -> https://msdn.microsoft.com/library/dn913749.aspx
                string data =
                    @"{""name"":""" + campaignName + @"""," + 
                    @"""type"":""only_notif""," + 
                    @"""deliveryTime"":""any"","" + 
                    @""notificationTitle"":""" + notificationTitle + 
                    @""",""notificationMessage"":""" + notificationMessage + 
                    @""",""actionUrl"":""" + actionURL + @"""}";
                
                var content = new FormUrlEncodedContent(new[] 
                {
                    new KeyValuePair<string, string>("kind", "announcement"),
                    new KeyValuePair<string, string>("data", data),
                });

                // Send the POST request
                var response = await client.PostAsync(url + createURIFragment, content);
                var responseString = response.Content.ReadAsStringAsync().Result;
                int campaignId = -1;
                if (response.StatusCode.ToString() == "OK")
                {
                    // This is the campaign id
                    campaignId = Convert.ToInt32(responseString);
                    Console.WriteLine("Campaign successfully created with id {0}", campaignId);
                }
                else
                {
                    Console.WriteLine("Campaign creation failed with error - '{0}'", responseString);
                }
                return campaignId;
            }
        }

4. Assim que tiver anúncio criado, verá algo semelhante ao seguinte no portal de Cativação Mobile (tenha em atenção que o estado = Rascunho e activado = n/d)

    ![][3]

5. `CreateAzMECampaign`cria uma campanha de anúncio e devolve o respetivo Id para o autor da chamada. `ActivateAzMECampaign`requer este Id como o parâmetro para ativar a campanha. 

        static async Task<bool> ActivateAzMECampaign(int campaignId)
        {
            string activateUriFragment = "/reach/1/activate";
            using (var client = new HttpClient())
            {
                // Add Authorization Header
                client.DefaultRequestHeaders.TryAddWithoutValidation("Authorization", CreateAuthZHeader());

                var content = new FormUrlEncodedContent(new[] 
                {
                    new KeyValuePair<string, string>("kind", "announcement"),
                    new KeyValuePair<string, string>("id", campaignId.ToString()),
                });

                // Send the POST request
                var response = await client.PostAsync(url + activateUriFragment, content);
                var responseString = response.Content.ReadAsStringAsync().Result;
                if (response.StatusCode.ToString() == "OK")
                {
                    Console.WriteLine("Campaign successfully activated");
                    return true;
                }
                else
                {
                    Console.WriteLine("Campaign activation failed with error - '{0}'", responseString);
                    return false;
                }
            }
        }

6. Assim que tiver anúncio ativado, verá algo semelhante ao seguinte no portal de Cativação Mobile:

    ![][4]

7. Assim que a campanha é activada, todos os dispositivos que satisfaçam o critério de campanha iniciará Ver notificações. 

8. Também irá reparar que o item de lista marcado com IsProcessed = false foi definida como verdadeiro depois de campanha anúncio é criada.  

Este exemplo criado uma campanha de anúncio simples especificando principalmente as propriedades necessárias. Pode personalizá-la, tanto quanto pode a partir do portal com as informações [aqui](https://msdn.microsoft.com/library/azure/mt683751.aspx). 

<!-- Images. -->
[1]: ./media/mobile-engagement-sample-backend-integration-sharepoint/sharepointlist.png
[2]: ./media/mobile-engagement-sample-backend-integration-sharepoint/properties.png
[3]: ./media/mobile-engagement-sample-backend-integration-sharepoint/new-announcement.png
[4]: ./media/mobile-engagement-sample-backend-integration-sharepoint/activate-announcement.png
[5]: ./media/mobile-engagement-sample-backend-integration-sharepoint/diagram.png


 
