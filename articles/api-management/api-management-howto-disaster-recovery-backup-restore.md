<properties 
    pageTitle="Como implementar recuperação de falhas utilizando serviço cópia de segurança e restaurar na gestão de API do Azure | Microsoft Azure" 
    description="Saiba como utilizar a cópia de segurança e restaurar para executar a recuperação de falhas no Azure API gestão." 
    services="api-management" 
    documentationCenter="" 
    authors="steved0x" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="api-management" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/25/2016" 
    ms.author="sdanie"/>

# <a name="how-to-implement-disaster-recovery-using-service-backup-and-restore-in-azure-api-management"></a>Como implementar recuperação de falhas utilizando serviço cópia de segurança e restaurar na gestão de API do Azure

Ao selecionar publicar e gerir o seu APIs através de gestão de API do Azure que é tirar partido de vários tolerância a falhas e capacidades de infraestrutura caso contrário, tem de estruturar, implementar e gerir. A plataforma Azure atenua uma fração de potenciais falhas numa fração do custo de grandes dimensões.

Para recuperar da disponibilidade problemas que afetam a região onde a gestão de API serviço está alojado devem ser prontos para reconstituir o seu serviço numa região diferente em qualquer altura. Dependendo do seu objetivos disponibilidade e objectivo de tempo de recuperação poderá pretender reservar um serviço de cópia de segurança de uma ou mais regiões e tente manter o respetivo conteúdo e configuração sincronizado com o serviço activo. A cópia de segurança do serviço e funcionalidade Restauro fornece o bloco modular necessário para a sua estratégia de recuperação de falhas de execução.

Este guia mostra como autenticar pedidos de Gestor de recursos do Azure e como fazer cópia de segurança e restaurar as instâncias de serviço de gestão de API.

>[AZURE.NOTE] O processo de cópias de segurança e restaurar uma instância do serviço de gestão de API para recuperação de falhas pode também ser utilizado para a replicação de instâncias do serviço de gestão de API para cenários como teste.
>
>Note que cada cópia de segurança expira após 7 dias. Se tentar para restaurar uma cópia de segurança depois de ter expirado o período de expiração de 7 dias, o restauro irá falhar com uma `Cannot restore: backup expired` mensagem.

## <a name="authenticating-azure-resource-manager-requests"></a>Os pedidos de autenticação Gestor de recursos do Azure

>[AZURE.IMPORTANT] API REST para cópia de segurança e restauro utiliza o Gestor de recursos do Azure e tem um mecanismo de autenticação diferente que os REST APIs para gerir as entidades de gestão de API. Os passos nesta secção descrevem como autenticar pedidos de Gestor de recursos do Azure. Para mais informações, consulte [Gestor de recursos do Azure autenticar pedidos](http://msdn.microsoft.com/library/azure/dn790557.aspx).

Todas as tarefas que fazer recursos utilizando o Gestor de recursos do Azure têm de ser autenticadas com o Azure Active Directory utilizando os seguintes passos.

-   Adicione uma aplicação para o inquilino do Azure Active Directory.
-   Definir permissões para a aplicação que adicionou.
-   Obter o token para autenticar pedidos para o Gestor de recursos do Azure.

O primeiro passo é criar uma aplicação do Azure Active Directory. Inicie sessão no [Portal clássica Azure](http://manage.windowsazure.com/) utilizando a subscrição que contém a instância do serviço de gestão de API e navegue para o separador de **aplicações** para o seu predefinido do Azure Active Directory.

>[AZURE.NOTE] Se o directório do Azure Active Directory predefinido não estiver visível à sua conta, contacte o administrador da subscrição do Azure para conceder as permissões necessárias para a sua conta. Para obter informações sobre a localização no diretório da sua predefinido, consulte o artigo [localize no diretório da sua predefinido](../virtual-machines/virtual-machines-windows-create-aad-work-id.md#locate-your-default-directory-in-the-azure-portal).

![Criar a aplicação do Azure Active Directory][api-management-add-aad-application]

Clique em **Adicionar**, **Adicionar uma aplicação a minha organização está a desenvolver**e selecione a **aplicação de cliente nativa**. Introduza um nome descritivo e clique na seta seguinte. Introduza um URL de marcador de posição tal como `http://resources` para **Redirecionar URI**, tal como é um campo obrigatório, mas não é utilizado o valor mais tarde. Clique na caixa de verificação para guardar a aplicação.

Assim que a aplicação é guardada, clique em **Configurar**, desloque para baixo para a secção **permissões para outras aplicações** e clique em **Adicionar aplicação**.

![Adicionar permissões][api-management-aad-permissions-add]

Selecione do **Windows** **Azure API de gestão do serviço** e clique na caixa de verificação para adicionar a aplicação.

![Adicionar permissões][api-management-aad-permissions]

Clique em **Permissões do delegado** ao lado da aplicação do **Windows** **Azure Service gestão API** recentemente adicionada, selecione a caixa para **Gestão de serviços de Azure do Access (pré-visualização)**e clique em **Guardar**.

![Adicionar permissões][api-management-aad-delegated-permissions]

Antes das APIs que gerar a cópia de segurança e restaurar a invocar, é necessário obter um token. O exemplo seguinte utiliza o pacote de nuget [Microsoft.IdentityModel.Clients.ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory) para obter o token.

    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using System;

    namespace GetTokenResourceManagerRequests
    {
        class Program
        {
            static void Main(string[] args)
            {
                var authenticationContext = new AuthenticationContext("https://login.windows.net/{tenant id}");
                var result = authenticationContext.AcquireToken("https://management.azure.com/", {application id}, new Uri({redirect uri});

                if (result == null) {
                    throw new InvalidOperationException("Failed to obtain the JWT token");
                }

                Console.WriteLine(result.AccessToken);

                Console.ReadLine();
            }
        }
    }

Substituir `{tentand id}`, `{application id}`, e `{redirect uri}` utilizando as seguintes instruções.

Substituir `{tenant id}` com o id de inquilino da aplicação Azure Active Directory que acabou de criar. Pode aceder ao id ao clicar em **pontos finais de vista**.

![Pontos finais][api-management-aad-default-directory]

![Pontos finais][api-management-endpoint]

Substituir `{application id}` e `{redirect uri}` utilizando o **Id de cliente** e o URL da secção **Uris redirecionar** a partir do separador de **Configurar** a aplicação Azure Active Directory. 

![Recursos][api-management-aad-resources]

Depois dos valores são especificados, o exemplo de código deve devolver um token semelhante ao exemplo seguinte.

![Token][api-management-arm-token]

Antes de chamar as operações de cópia de segurança e restauro descritas nas secções seguintes, defina o cabeçalho de pedido de autorização para sua chamada restantes.

    request.Headers.Add(HttpRequestHeader.Authorization, "Bearer " + token);

## <a name="step1"> </a>Um serviço de gestão de API de cópia de segurança
A cópia de segurança de uma gestão API serviço emitir o pedido de HTTP seguinte:

`POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ApiManagement/service/{serviceName}/backup?api-version={api-version}`

em que:

* `subscriptionId`-id da subscrição que contém o serviço de gestão de API que está a tentar fazer cópia de segurança
* `resourceGroupName`-uma cadeia em forma de 'Api - Default-{serviço região}' onde `service-region` identifica o Azure região onde a gestão de API do serviço que está a tentar está alojada cópia de segurança, por exemplo,`North-Central-US`
* `serviceName`-o nome do serviço de gestão de API de estiver a fazer uma cópia de segurança especificado no momento da sua criação
* `api-version`-Substituir por`2014-02-14`

No corpo do pedido, especifique o nome de conta de armazenamento Azure de destino, tecla de acesso, nome do contentor blob e nome da cópia de segurança:

    '{  
        storageAccount : {storage account name for the backup},  
        accessKey : {access key for the account},  
        containerName : {backup container name},  
        backupName : {backup blob name}  
    }'

Definir o valor da `Content-Type` cabeçalho pedido para `application/json`.

Cópia de segurança é uma operação de execução longa que poderá demorar vários minutos a concluir.  Se o pedido foi efetuada com êxito e o processo de cópia de segurança foi iniciado irá receber um `202 Accepted` código de estado de resposta com um `Location` cabeçalho.  Tornar 'GET' pedidos para o URL na `Location` cabeçalho para saber o estado da operação de. Enquanto a cópia de segurança está em curso irão continuar a receber um código de estado '202 aceites'. Um código de resposta de `200 OK` irá indicar a conclusão da operação de cópia de segurança.

**Nota**:

- **Contentor** especificado no corpo pedido **tem de existir**.
* Enquanto a cópia de segurança está em curso **não deve tentar a qualquer operações de gestão de serviço** como o SKU atualizar ou alterar, alteração de nome de domínio, etc. 
* Restauro de uma **cópia de segurança é garantir apenas durante 7 dias** desde o momento em que da sua criação. 
* **Dados de utilização** utilizados para criar a análise de relatórios **não foi incluído** na cópia de segurança. Utilize o [Azure API gestão REST API][] para obter periodicamente relatórios analytics para salvaguarda.
* A frequência com que efetuar cópias de segurança do serviço irão afetar a sua objectivo de ponto de recuperação. Para minimizar-Recomendamos que contacte nas execução cópias de segurança regulares, bem como efetuar cópias de segurança a pedido depois de efetuar alterações importantes no seu serviço de gestão de API.
* **Alterações** efectuadas para a configuração do serviço (por exemplo, APIs, políticas, aspeto portal programador) durante a operação de cópia de segurança é no processo **poderá não serão incluídas na cópia de segurança e, consequentemente, serão perdidas**.

## <a name="step2"> </a>Restaurar um serviço de gestão de API
Para restaurar uma gestão API do serviço a partir de uma cópia de segurança criado anteriormente fazer o pedido HTTP seguinte:

`POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ApiManagement/service/{serviceName}/restore?api-version={api-version}`

em que:

* `subscriptionId`-id da subscrição que contém o serviço de gestão de API está a restaurar uma cópia de segurança para
* `resourceGroupName`-uma cadeia em forma de 'Api - Default-{serviço região}' onde `service-region` identifica o Azure região onde está alojado o serviço de gestão de API está a restaurar uma cópia de segurança para, por exemplo,`North-Central-US`
* `serviceName`-o nome da gestão de API do serviço a ser restaurado para especificado no momento da sua criação
* `api-version`-Substituir por`2014-02-14`

No corpo do pedido, especifique a localização do ficheiro de cópia de segurança, ou seja, nome da conta de armazenamento Azure, tecla de acesso, nome do contentor blob e nome da cópia de segurança:

    '{  
        storageAccount : {storage account name for the backup},  
        accessKey : {access key for the account},  
        containerName : {backup container name},  
        backupName : {backup blob name}  
    }'

Definir o valor da `Content-Type` cabeçalho pedido para `application/json`.

O restauro é uma operação de execução longa que poderá demorar até 30 ou mais minutos para concluir.  Se o pedido foi efetuada com êxito e o processo de restauro foi iniciado irá receber um `202 Accepted` código de estado de resposta com um `Location` cabeçalho.  Tornar 'GET' pedidos para o URL na `Location` cabeçalho para saber o estado da operação de. Enquanto o restauro está em curso irão continuar a receber 'aceites 202' código de estado. Um código de resposta de `200 OK` irá indicar a conclusão da operação de restaurar.

>[AZURE.IMPORTANT] **O SKU** do serviço que está a ser restaurado em **tem de corresponder** o SKU do serviço com cópia de segurança a ser restaurado.
>
>**Alterações** efectuadas para a configuração do serviço (por exemplo, APIs, políticas, aspeto portal programador) durante a operação de restauro está em curso **podem ser substituídos**.

## <a name="next-steps"></a>Próximos passos
Consulte os seguintes blogues do Microsoft para duas tutoriais diferentes do processo de cópia de segurança/restauro.

-   [Criar uma réplica da Azure API gestão contas](https://www.returngis.net/en/2015/06/replicate-azure-api-management-accounts/) 
    -   Obrigado para Gisela à sua contribuição para deste artigo.
-   [Gestão de Azure API: Cópias de segurança e restaurar a configuração](http://blogs.msdn.com/b/stuartleeks/archive/2015/04/29/azure-api-management-backing-up-and-restoring-configuration.aspx)
    -   A abordagem detalhada por Stuart não corresponder a orientação oficial mas é muito interessante.

[Backup an API Management service]: #step1
[Restore an API Management service]: #step2


[Gestão de Azure API REST API]: http://msdn.microsoft.com/library/azure/dn781421.aspx

[api-management-add-aad-application]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-add-aad-application.png

[api-management-aad-permissions]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-permissions.png
[api-management-aad-permissions-add]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-permissions-add.png
[api-management-aad-delegated-permissions]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-delegated-permissions.png
[api-management-aad-default-directory]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-default-directory.png
[api-management-aad-resources]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-resources.png
[api-management-arm-token]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-arm-token.png
[api-management-endpoint]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-endpoint.png
 
