<properties 
    pageTitle="Atualizar dos serviços de multimédia após gradual teclas de acesso de armazenamento | Microsoft Azure" 
    description="Este artigo dar-lhe orientações sobre como atualizar dos serviços de multimédia após gradual teclas de acesso de armazenamento." 
    services="media-services" 
    documentationCenter="" 
    authors="Juliako"
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/26/2016" 
    ms.author="milangada;cenkdin;juliako"/>

#<a name="update-media-services-after-rolling-storage-access-keys"></a>Atualizar dos serviços de multimédia após gradual teclas de acesso de armazenamento

Quando cria uma nova conta dos serviços de multimédia do Azure, é-lhe perguntado também para selecionar uma conta de armazenamento do Windows Azure que é utilizada para armazenar o conteúdo de multimédia. Tenha em atenção que pode [Adicionar mais contas de armazenamento](meda-services-managing-multiple-storage-accounts.md) à sua conta dos serviços de multimédia.

Quando é criada uma nova conta de armazenamento, Azure gera duas teclas de acesso de armazenamento de 512 bits, que são utilizadas para autenticar o acesso à sua conta de armazenamento. Para manter as suas ligações de armazenamento mais segura, é recomendável para periodicamente gerar e rodar a sua chave de acesso de armazenamento. Duas teclas de acesso (principais e secundárias) são fornecidas para poder permitem-lhe manter as ligações para a conta de armazenamento utilizando uma tecla de acesso, enquanto que gerar a tecla de acesso. Este procedimento é também chamado "teclas de acesso ficha técnica em".

Dos serviços de multimédia depende uma chave de armazenamento fornecida para a mesma. Especificamente, o que é utilizada para transmitir em fluxo ou transferir os seus ativos Locator variam consoante a tecla de acesso de armazenamento especificada. Quando é criada uma conta de AMS demora uma dependência na chave primária armazenamento acesso por predefinição, mas como um utilizador pode atualizar a chave de armazenamento tem AMS. Deve certificar-se permitir que os serviços de multimédia saber qual chave para utilizar ao seguir os passos descritos neste tópico. Além disso, quando gradual teclas de acesso de armazenamento, precisa de efetuar se esqueça de atualizar o seu Locator por isso, não serão sem interrupções no seu serviço de transmissão (este passo também é descrito no tópico).

>[AZURE.NOTE]Se tiver várias contas de armazenamento, teria de executar este procedimento com cada conta de armazenamento.
>
>Antes de executar os passos descritos neste tópico numa conta de produção, certifique-se testá-los numa conta de pré-produção.


## <a name="step-1-regenerate-secondary-storage-access-key"></a>Passo 1: Gerar tecla de acesso de armazenamento secundário

Comece com voltar a gerar chave armazenamento secundário. Por predefinição, a chave secundária não é utilizada pelos serviços de multimédia.  Para obter informações sobre como recuperar as teclas de armazenamento, consulte o artigo [como: vista, copiar e voltar a gerar armazenamento as teclas de acesso](../storage-create-storage-account.md#view-copy-and-regenerate-storage-access-keys).
  
##<a id="step2"></a>Passo 2: Atualização dos serviços de multimédia para utilizar a nova chave de armazenamento secundário

Atualize dos serviços de multimédia para utilizar a tecla de acesso de armazenamento secundário. Pode utilizar um dos seguintes dois métodos para sincronizar a chave de armazenamento recuperada com os serviços de multimédia.

- Utilizar o portal do Azure: para localizar o nome e a chave de valores, aceda ao portal do Azure e selecione a sua conta. Na janela definições que é apresentado à direita. Na janela definições, selecione teclas. Dependendo de qual chave de armazenamento que pretende para os serviços de multimédia para sincronizar com, selecione a chave primária de sincronizar ou botão de chave secundário sincronizar. Neste caso, utilize a tecla secundária.

- Utilize a gestão dos serviços de multimédia REST API.

Exemplo de código que se segue mostra como construir o https://endpoint/***subscriptionId/serviços/mediaservices/contas/accountName*/StorageAccounts/*storageAccountName*pedido /Key para sincronizar a chave de armazenamento especificados com os serviços de multimédia. Neste caso, é utilizado o valor de chave do armazenamento secundário. Para obter mais informações, consulte o artigo [como: utilizar multimédia serviços de gestão de REST API](http://msdn.microsoft.com/library/azure/dn167656.aspx).
    
    public void UpdateMediaServicesWithStorageAccountKey(string mediaServicesAccount, string storageAccountName, string storageAccountKey)
    {
        var clientCert = GetCertificate(CertThumbprint);
        
        HttpWebRequest request = (HttpWebRequest)WebRequest.Create(string.Format("{0}/{1}/services/mediaservices/Accounts/{2}/StorageAccounts/{3}/Key",
        Endpoint, SubscriptionId, mediaServicesAccount, storageAccountName));
        request.Method = "PUT";
        request.ContentType = "application/json; charset=utf-8";
        request.Headers.Add("x-ms-version", "2011-10-01");
        request.Headers.Add("Accept-Encoding: gzip, deflate");
        request.ClientCertificates.Add(clientCert);
        
        
        using (var streamWriter = new StreamWriter(request.GetRequestStream()))
        {
            streamWriter.Write("\"");
            streamWriter.Write(storageAccountKey);
            streamWriter.Write("\"");
            streamWriter.Flush();
        }
        
        using (var response = (HttpWebResponse)request.GetResponse())
        {
            string jsonResponse;
            Stream receiveStream = response.GetResponseStream();
            Encoding encode = Encoding.GetEncoding("utf-8");
            if (receiveStream != null)
            {
                var readStream = new StreamReader(receiveStream, encode);
                jsonResponse = readStream.ReadToEnd();
            }
        }
    }

Após neste passo, atualize Locator existente (que têm dependência na chave de armazenamento antiga) conforme apresentado no passo seguinte.

>[AZURE.NOTE]Espere 30 minutos antes de executar qualquer operações com os serviços de multimédia (por exemplo, criar Locator novo) para impedir que o qualquer impacto no tarefas pendentes.

##<a name="step-3-update-locators"></a>Passo 3: Locator de atualização

>[AZURE.NOTE]Quando gradual teclas de acesso de armazenamento, precisa de efetuar se esqueça de atualizar o Locator existente, pelo que não existe nenhuma interrupções no seu serviço de transmissão.

Aguarde pelo menos 30 minutos depois de sincronizar a nova chave de armazenamento com AMS. Em seguida, pode recriar Locator seu pedido para que manter dependência a chave de armazenamento especificados e mantêm o URL existente.

Tenha em atenção que, quando atualizar (ou recriar) um localizador de SA, o URL sempre será alterado.

>[AZURE.NOTE] Para se certificar de que preservar os URLs da sua Locator sob existentes, tem de eliminar o localizador de existente e criar uma nova com o mesmo ID.

O exemplo de .NET abaixo mostra como recriar um locator com o mesmo ID.

privado ILocator RecreateLocator(CloudMediaContext context, ILocator locator) estático {/ / guardar as propriedades de locator existente.
var elementos = locator. Activo; var accessPolicy = locator. AccessPolicy; var locatorId = locator. ID de; var datainício = locator. Hora de início; var locatorType = locator. Escreva; var locatorName = locator. Nome;

Elimine locator antigo.
localizador. DELETE ();

Se (localizador. ExpirationDateTime < = DateTime.UtcNow) {gerar nova exceção (String.Format ("não é possível recriar locator Id = {0} uma vez que é o tempo de expiração locator no passado", locator. ID de)); }
    
        // Create new locator using saved properties.
        var newLocator = context.Locators.CreateLocator(
            locatorId,
            locatorType,
            asset,
            accessPolicy,
            startDate,
            locatorName);
    
    
    
        return newLocator;
    }


##<a name="step-5-regenerate--primary-storage-access-key"></a>Passo 5: Gerar tecla de acesso de armazenamento principal

Voltar a gerar a tecla de acesso de armazenamento principal. Para obter informações sobre como recuperar as teclas de armazenamento, consulte o artigo [como: vista, copiar e voltar a gerar armazenamento as teclas de acesso](../storage-create-storage-account.md#view-copy-and-regenerate-storage-access-keys).

##<a name="step-6-update-media-services-to-use-the-new-primary-storage-key"></a>Passo 6: Atualização dos serviços de multimédia para utilizar a nova chave primária de armazenamento
    
Utilizar o mesmo procedimento conforme descrito no [passo 2](media-services-roll-storage-access-keys.md#step2) , mas desta vez sincronizar a nova tecla de acesso de armazenamento primário com a conta dos serviços de multimédia.

>[AZURE.NOTE]Espere 30 minutos antes de executar qualquer operações com os serviços de multimédia (por exemplo, criar Locator novo) para impedir que o qualquer impacto no tarefas pendentes.

##<a name="step-7-update-locators"></a>Passo 7: Locator de atualização  

Após 30 minutos possa recriar Locator seu pedido para que tirar dependência na nova chave primária de armazenamento e mantêm o URL existente.

Utilize o mesmo procedimento conforme descrito no [passo 3](media-services-roll-storage-access-keys.md#step-3-update-locators).


##<a name="media-services-learning-paths"></a>Caminhos de aprendizagem dos serviços de multimédia

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fornecer comentários

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]



###<a name="acknowledgments"></a>Confirmações 

Gostaríamos de reconhecermos as seguintes pessoas a quem contribuíram concreto este documento: Cenk Dingiloglu, Milão Gada, Seva Titov.
