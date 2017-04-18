<properties
    pageTitle="Utilizar o resto para fazer cópia de segurança e restaurar aplicações de serviço de aplicação"
    description="Saiba como utilizar a RESTful API chamadas para fazer cópia de segurança e restaurar uma aplicação do serviço de aplicação do Azure"
    services="app-service"
    documentationCenter=""
    authors="NKing92"
    manager="wpickett"
    editor="" />

<tags
    ms.service="app-service"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/10/2016"
    ms.author="nicking"/>
# <a name="use-rest-to-back-up-and-restore-app-service-apps"></a>Utilizar o resto para fazer cópia de segurança e restaurar aplicações de serviço de aplicação

> [AZURE.SELECTOR]
- [PowerShell](../app-service/app-service-powershell-backup.md)
- [REST API](websites-csm-backup.md)

[Aplicações de serviço de aplicação](https://azure.microsoft.com/services/app-service/web/) pode ser cópias como blobs no Azure armazenamento. A cópia de segurança, também pode conter bases de dados a aplicação. Se a aplicação for nunca acidentalmente eliminada ou se a aplicação tem de ser revertidas para uma versão anterior, pode ser restaurado a partir de qualquer cópia de segurança anterior. Cópias de segurança podem ser efetuadas em qualquer altura a pedido ou cópias de segurança podem ser agendadas em intervalos adequados.

Este artigo explica como fazer cópia de segurança e restaurar uma aplicação com pedidos de RESTful API. Se pretender para criar e gerir as cópias de segurança de aplicação graficamente através do portal do Azure, consulte o artigo [Criar cópia de segurança de uma aplicação web na aplicação de serviço do Azure](web-sites-backup.md)

<a name="gettingstarted"></a>
## <a name="getting-started"></a>Introdução
Para enviar pedidos REST, tem de conhecer o **nome da**sua aplicação, **grupo de recursos**e **id da subscrição**. Podem encontrar estas informações ao clicar em sua aplicação na pá a **Aplicação de serviço** do [Azure portal](https://portal.azure.com). Para obter exemplos neste artigo, iremos está a configurar o Web site **backuprestoreapiexamples.azurewebsites.net**. É armazenado no grupo de recursos predefinido-Web-WestUS e está a ficar com uma subscrição com o ID 00001111-2222-3333-4444-555566667777.

![Informações do site de exemplo][SampleWebsiteInformation]

<a name="backup-restore-rest-api"></a>
## <a name="backup-and-restore-rest-api"></a>Cópia de segurança e restauro REST API
Agora Abordaremos vários exemplos de como utilizar a API REST para cópia de segurança e restaurar uma aplicação. Cada exemplo inclui um corpo do pedido URL e HTTP. O URL de exemplo contém os marcadores de posição desportivo chavetas, como {id da subscrição}. Substitua os marcadores de posição com a informação correspondente para a sua aplicação. Para sua referência, eis uma explicação de cada marcador de posição que aparece nos URLs de exemplo.

* id de subscrição – ID da subscrição do Azure que contém a aplicação
* recurso--nome do grupo – nome do grupo de recursos que contém a aplicação
* nome – nome da aplicação
* id de cópia de segurança – ID da cópia de segurança de aplicação

Para a documentação completa da API, incluindo vários parâmetros opcionais que podem ser incluídos no pedido de HTTP, consulte o [Azure recurso Explorer](https://resources.azure.com/).

<a name="backup-on-demand"></a>
## <a name="backup-an-app-on-demand"></a>Cópia de segurança de uma aplicação a pedido
Para fazer cópia de segurança uma aplicação imediatamente, enviar um pedido de **mensagem** para **https://management.azure.com/subscriptions/ {subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Web/sites/{name}/backup/**.

Eis o que o URL do aspeto utilizar site nosso exemplo. **https://Management.Azure.com/subscriptions/00001111-2222-3333-4444-555566667777/resourceGroups/Default-Web-WestUS/Providers/Microsoft.Web/sites/backuprestoreapiexamples/backup/**

Fornece um objeto JSON no corpo do seu pedido para especificar a conta de armazenamento a utilizar para armazenar a cópia de segurança. O objeto JSON tem de ter uma propriedade denominada **storageAccountUrl**, que possui um [URL de SA](../storage/storage-dotnet-shared-access-signature-part-1.md) conceder acesso de escrita para o contentor de armazenamento do Windows Azure que detém o blob da cópia de segurança. Se pretender agregar as bases de dados, também tem de fornecer uma lista que contém os nomes, tipos e cadeias de ligação de bases de dados para ser cópias de segurança.

```
{
    "properties":
    {
        "storageAccountUrl": “https://account.blob.core.windows.net/backups?sv=2015-02-21&sr=c&sig=DzlkBl7h32C8qCv%2BifdBRxE63r4iv0kZ9L7E0qP16sY%3D&se=2016-09-15T22%3A46%3A54Z&sp=rwdl”,
        “databases”: [
            {
                “databaseType”: “SqlAzure”,
                “name”: “MyDatabase1”,
                "connectionString": "<your database connection string>"
            }
        ]
    }
}
```

Uma cópia de segurança da aplicação começa imediatamente quando é recebido o pedido. O processo de cópia de segurança pode demorar muito tempo a concluir. A resposta de HTTP contém um ID que pode utilizar no pedido de outro para ver o estado da cópia de segurança. Eis um exemplo do corpo da resposta de HTTP a nossa pedido de cópia de segurança.

```
{
    "id": "/subscriptions/00001111-2222-3333-4444-555566667777/resourceGroups/Default-Web-WestUS/providers/Microsoft.Web/sites/backuprestoreapiexamples",
    "name": " backuprestoreapiexamples ",
    "type": "Microsoft.Web/sites",
    "location": "WestUS",
    "properties":    {
        "id": 1,
        "storageAccountUrl": “https://account.blob.core.windows.net/backups?sv=2015-02-21&sr=c&sig=DzlkBl7h32C8qCv%2BifdBRxE63r4iv0kZ9L7E0qP16sY%3D&se=2016-09-15T22%3A46%3A54Z&sp=rwdl”,
        "blobName": “backup_201509291825.zip”,
        "name": "backup_201509291825",
        "status": 4,
        "sizeInBytes": 0,
        "created": "2015-09-29T18:25:26.3992707Z",
        "log": null,
        "databases": [
            {
                "databaseType": "SqlAzure",
                "name": "MyDatabase1",
                "connectionString": "<your database connection string>"
            }
        ],
        "scheduled": false,
        "correlationId": "ea730f4d-dd06-4f7f-a090-9aa09k662h36",
    }
}
```

>[AZURE.NOTE] Mensagens de erro podem ser encontradas na propriedade do registo da resposta de HTTP.

<a name="schedule-automatic-backups"></a>
## <a name="schedule-automatic-backups"></a>Agendar cópias de segurança automáticas
Para além de cópias de uma aplicação a pedido, também pode agendar uma cópia de segurança para ocorrer automaticamente.

### <a name="set-up-a-new-automatic-backup-schedule"></a>Configurar uma nova agenda de cópia de segurança automática
Para configurar uma agenda de cópia de segurança, enviar um pedido de **colocar** para **https://management.azure.com/subscriptions/ {subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Web/sites/{name}/config/backup**.

Eis o aspeto do URL para o site nosso exemplo. **https://Management.Azure.com/subscriptions/00001111-2222-3333-4444-555566667777/resourceGroups/Default-Web-WestUS/Providers/Microsoft.Web/sites/backuprestoreapiexamples/config/backup**

Corpo do pedido tem de ter um objeto JSON que especifica a configuração de cópia de segurança. Eis um exemplo com todos os parâmetros.

```
{
    "location": "WestUS",
    "properties": // Represents an app restore request
    {
        "backupSchedule": { // Required for automatically scheduled backups
            "frequencyInterval": "7",
            "frequencyUnit": "Day",
            "keepAtLeastOneBackup": "True",
            "retentionPeriodInDays": "10",
        },
        "enabled": "True", // Must be set to true to enable automatic backups
        "name": “mysitebackup”,
        "storageAccountUrl": "https://account.blob.core.windows.net/backups?sv=2015-02-21&sr=c&sig=DzlkBl7h32C8qCv%2BifdBRxE63r4iv0kZ9L7E0qP16sY%3D&se=2016-09-15T22%3A46%3A54Z&sp=rwdl"
    }
}
```

Este exemplo configura a aplicação para ser automaticamente cópia de segurança sete dias. Os parâmetros **frequencyInterval** e **frequencyUnit** juntas determinam com que frequência ocorrem as cópias de segurança. Valores válidos para **frequencyUnit** são **hora** e o **dia**. Por exemplo, para fazer cópia de segurança uma aplicação a cada 12 horas, defina frequencyInterval para 12 e frequencyUnit para hora.

Cópias de segurança antigas são removidas automaticamente da conta de armazenamento. Pode controlar como antigo as cópias de segurança podem estar através da definição do parâmetro **retentionPeriodInDays** . Se pretende ter sempre pelo menos uma cópia de segurança guardada, independentemente de como antigo está, definido **keepAtLeastOneBackup** para true.

### <a name="get-the-automatic-backup-schedule"></a>Obter a agenda de cópia de segurança automática
Para obter a configuração de cópia de segurança de uma aplicação, enviar um pedido de **mensagem** para o URL **https://management.azure.com/subscriptions/ {subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Web/sites/{name}/config/backup/list**.

O URL do site nosso exemplo é **https://management.azure.com/subscriptions/00001111-2222-3333-4444-555566667777/resourceGroups/Default-Web-WestUS/providers/Microsoft.Web/sites/backuprestoreapiexamples/config/backup/list**.

<a name="get-backup-status"></a>
## <a name="get-the-status-of-a-backup"></a>Obter o estado de uma cópia de segurança
Dependendo de como grandes a aplicação está, uma cópia de segurança pode demorar algum tempo para concluir. Cópias de segurança podem também falhar, hora de saída, ou parcialmente concluída com êxito. Para ver o estado de cópias de segurança de todos os uma aplicação, enviar um pedido de **Introdução** para o URL **https://management.azure.com/subscriptions/ {subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Web/sites/{name}/backups**.

Para ver o estado de uma cópia de segurança específico, envie um pedido de obter para o URL **https://management.azure.com/subscriptions/ {subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Web/sites/{name}/backups/{backup-id}**.

Eis o aspeto do URL para o site nosso exemplo. **https://Management.Azure.com/subscriptions/00001111-2222-3333-4444-555566667777/resourceGroups/Default-Web-WestUS/Providers/Microsoft.Web/sites/backuprestoreapiexamples/backups/1**

O corpo da resposta contém um objeto JSON semelhante a este exemplo.

```
{
    "properties":  {
        "id": 1,
        "storageAccountUrl": "https://account.blob.core.windows.net/backups?sv=2015-02-21&sr=c&sig=DzlkBl7h32C8qCv%2BifdBRxE63r4iv0kZ9L7E0qP16sY%3D&se=2016-09-15T22%3A46%3A54Z&sp=rwdl",
        "blobName": "backup_201509291734.zip",
        "name": "backup_201509291734",
        "status": 2,
        "sizeInBytes": 151973,
        "created": "2015-09-29T17:34:57.2091605",
        "scheduled": false,
        "lastRestoreTimeStamp": null,
        "finishedTimeStamp": "2015-09-29T17:35:11.3293602",
        "correlationId": "2379fc13-418a-4b9c-920d-d06e73c5028d",
        "websiteSizeInBytes": 209920
    }
}
```

O estado de uma cópia de segurança é um tipo de enumerados. Segue-se todos os Estados possíveis.

* Em curso 0 –: A cópia de segurança foi iniciada, mas ainda não concluiu.
* 1 – Ocorreu uma falha: A cópia de segurança foi bem sucedida.
* 2 – foi concluída com êxito: A cópia de segurança foi concluída com êxito.
* 3 – tempo limite excedido: A cópia de segurança não concluiu a hora e foi cancelada.
* 4 – criadas: O pedido de cópia de segurança é colocado em fila mas ainda não foi iniciado.
* 5 – ignorados: A cópia de segurança não continuar devido a uma agenda acionar demasiadas cópias de segurança.
* 6 – a cópia de segurança de PartiallySucceeded: Com êxito, mas não existe uma cópia de segurança das alguns ficheiros porque não pode ser lido. Normalmente, isto acontece porque um bloqueio exclusivo foi colocado nos ficheiros.
* 7 – DeleteInProgress: A cópia de segurança foi solicitada a ser eliminada, mas ainda não foram eliminadas.
* 8 – DeleteFailed: Não foi possível eliminar a cópia de segurança. Isto pode acontecer porque o URL de SA que foi utilizado para criar a cópia de segurança expirou.
* 9 – eliminados: A cópia de segurança foi eliminada com êxito.

<a name="restore-app"></a>
## <a name="restore-an-app-from-a-backup"></a>Restaurar uma aplicação a partir de uma cópia de segurança
Se a sua aplicação foi eliminada ou se quiser reverter a sua aplicação para uma versão anterior, pode restaurar a aplicação a partir de uma cópia de segurança. Para invocar um restauro, enviar um pedido de **mensagem** para o URL **https://management.azure.com/subscriptions/ {subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Web/sites/{name}/backups/{backup-id}/restore**.

Eis o aspeto do URL para o site nosso exemplo. **https://Management.Azure.com/subscriptions/00001111-2222-3333-4444-555566667777/resourceGroups/Default-Web-WestUS/Providers/Microsoft.Web/sites/backuprestoreapiexamples/backups/1/Restore**

No corpo do pedido, envie um objeto JSON que contém as propriedades para a operação de restaurar. Eis um exemplo que contém todas as propriedades necessárias:

```
{
    "location": "WestUS",
    "properties":
    {
        "blobName": "backup_201509280431.zip",
        "databases": [ // Not required unless you’re restoring databases
            {
                “databaseType”: “SqlAzure”,
                “name”: “MyDatabase1”
        }],
        "overwrite": "true",
        "storageAccountUrl": "https://account.blob.core.windows.net/backups?sv=2015-02-21&sr=c&sig=DzlkBl7h32C8qCv%2BifdBRxE63r4iv0kZ9L7E0qP16sY%3D&se=2016-09-15T22%3A46%3A54Z&sp=rwdl" // SAS URL to storage container containing your website backup
    }
}
```

### <a name="restore-to-a-new-app"></a>Restaurar para uma nova aplicação
Por vezes, poderá pretender criar uma nova aplicação quando restaura uma cópia de segurança, em vez de substituir uma aplicação já existente. Para fazer isto, altere o URL do pedido para apontar para a nova aplicação que pretende criar e alterar a propriedade **Substituir** na JSON para **Falso**.

<a name="delete-app-backup"></a>
## <a name="delete-an-app-backup"></a>Eliminar uma cópia de segurança de aplicação
Se gostaria eliminar uma cópia de segurança, envie um pedido de **Eliminar** para o URL **https://management.azure.com/subscriptions/ {subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Web/sites/{name}/backups/{backup-id}**.

Eis o aspeto do URL para o site nosso exemplo. **https://Management.Azure.com/subscriptions/00001111-2222-3333-4444-555566667777/resourceGroups/Default-Web-WestUS/Providers/Microsoft.Web/sites/backuprestoreapiexamples/backups/1**

<a name="manage-sas-url"></a>
## <a name="manage-a-backups-sas-url"></a>Gerir SA URL uma cópia de segurança
Aplicação de serviço de Azure irá tentar eliminar a cópia de segurança de armazenamento do Windows Azure utilizando o URL de SA que foi fornecido quando a cópia de segurança foi criada. Se este URL SA já não é válido, a cópia de segurança não pode ser eliminada através da API REST. No entanto, pode atualizar o URL de SA associado uma cópia de segurança ao enviar um pedido de **mensagem** para o URL **https://management.azure.com/subscriptions/ {subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Web/sites/{name}/backups/{backup-id}/list**.

Eis o aspeto do URL para o site nosso exemplo. **https://Management.Azure.com/subscriptions/00001111-2222-3333-4444-555566667777/resourceGroups/Default-Web-WestUS/Providers/Microsoft.Web/sites/backuprestoreapiexamples/backups/1/List**

No corpo do pedido, envie um objeto JSON que contém o novo URL SA. Eis um exemplo.

```
{
    "properties":
    {
        "storageAccountUrl": "https://account.blob.core.windows.net/backups?sv=2015-02-21&sr=c&sig=DzlkBl7h32C8qCv%2BifdBRxE63r4iv0kZ9L7E0qP16sY%3D&se=2016-09-15T22%3A46%3A54Z&sp=rwdl"
    }
}
```

>[AZURE.NOTE] O URL de SA associado uma cópia de segurança não é devolvido por motivos de segurança, ao enviar um pedido de obter uma cópia de segurança específico. Se pretender ver o URL de SA associado uma cópia de segurança, envie um pedido de mensagem para o mesmo URL acima. Inclua um objeto JSON vazio no corpo do pedido. A resposta do servidor contém todas as informações da cópia de segurança, incluindo o URL de SA.

<!-- IMAGES -->
[SampleWebsiteInformation]: ./media/websites-csm-backup/01siteconfig.png
