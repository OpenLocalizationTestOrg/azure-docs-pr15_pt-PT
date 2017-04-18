<properties
    pageTitle="Guia de introdução node.js ao | Microsoft Azure"
    description="Saiba como criar uma aplicação de web Node.js simple e implementá-la para um serviço em nuvem Azure."
    services="cloud-services"
    documentationCenter="nodejs"
    authors="rmcmurray"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="cloud-services"
    ms.workload="tbd"
    ms.tgt_pltfrm="na" 
    ms.devlang="nodejs"
    ms.topic="hero-article"
    ms.date="08/11/2016" 
    ms.author="robmcm"/>

# <a name="build-and-deploy-a-nodejs-application-to-an-azure-cloud-service"></a>Criar e implementar uma aplicação de Node.js num serviço de nuvem do Azure

> [AZURE.SELECTOR]
- [NODE.js](cloud-services-nodejs-develop-deploy-app.md)
- [.NET](cloud-services-dotnet-get-started.md)

Este tutorial mostra como criar uma aplicação Node.js simple a ser executada num serviço em nuvem Azure. Serviços em nuvem são blocos modulares de aplicações na nuvem dimensionáveis no Azure. Permitem a separação e independentes de gestão e escala de saída dos componentes de front-end e back-end da sua aplicação.  Serviços em nuvem fornecem uma máquina de virtual dedicada robusta para alojamento sujeito a cada função.

Para obter mais informações sobre os serviços em nuvem, e como se comparam para sites públicos do Azure e máquinas virtuais, consulte o artigo [sites públicos do Azure, serviços em nuvem e máquinas virtuais comparação].

>[AZURE.TIP] Está à procura para criar um Web site simple? Se o seu cenário envolve apenas um site simple front-end, considere [utilizar uma aplicação lightweight web]. Pode atualizar facilmente para um serviço na nuvem como a aplicação web do cresce e alterar os requisitos de.

Ao seguir este tutorial, irá criar uma aplicação web simples alojada no interior de uma função da web. Irá utilizar o emulador cluster para testar a sua aplicação localmente, em seguida, implementá-lo com ferramentas de linha de comandos do PowerShell.

A aplicação é uma aplicação "Olá mundo" simples:

![Um browser a apresentar a página de web Olá mundo][A web browser displaying the Hello World web page]

## <a name="prerequisites"></a>Pré-requisitos

> [AZURE.NOTE] Neste tutorial, utiliza PowerShell do Azure, que necessita do Windows.

- Instalar e configurar o [Azure Powershell].
- Transfira e instale o [SDK do Azure para .NET 2.7]. Na configuração da instalação, selecione:
    - MicrosoftAzureAuthoringTools
    - MicrosoftAzureComputeEmulator


## <a name="create-an-azure-cloud-service-project"></a>Criar um projeto de serviço em nuvem Azure

Efetue as seguintes tarefas para criar um novo projeto de serviço em nuvem Azure, juntamente com os andaimes Node.js básicos:

1. Executar **o Windows PowerShell** como administrador; a partir do **Menu Iniciar** ou **Ecrã Iniciar**, procure o **Windows PowerShell**.

2. [PowerShell ligar] à sua subscrição.

3. Introduza o seguinte cmdlet do PowerShell para criar para criar o projeto:

        New-AzureServiceProject helloworld

    ![O resultado do comando novo AzureService helloworld][The result of the New-AzureService helloworld command]

    O cmdlet **Novo AzureServiceProject** gera uma estrutura básica para publicar uma aplicação de Node.js num serviço na nuvem. Contém os ficheiros de configuração de publicação para Azure necessários. O cmdlet também altera o seu diretório de trabalho ao diretório no serviço.

    O cmdlet cria os seguintes ficheiros:

    -   **ServiceConfiguration.Cloud.cscfg**, **ServiceConfiguration.Local.cscfg** e **ServiceDefinition.csdef**: específicas do Azure ficheiros necessárias para publicar a sua aplicação. Para mais informações, consulte o artigo [Descrição geral da criação de um serviço alojado para Azure].

    -   **deploymentSettings.json**: armazena definições locais que são utilizadas pelos cmdlets de implementação do Azure PowerShell.

4.  Introduza o seguinte comando para adicionar um novo perfil de web:

        Add-AzureNodeWebRole

    ![O resultado do comando Adicionar AzureNodeWebRole][The output of the Add-AzureNodeWebRole command]

    O cmdlet **Adicionar AzureNodeWebRole** cria uma aplicação Node.js básica. Também modifica os ficheiros **.csfg** e **.csdef** para adicionar entradas de configuração para a nova função.

    > [AZURE.NOTE] Se não especificar um nome de função, um nome predefinido é utilizado. Pode fornecer um nome como primeiro parâmetro cmdlet:`Add-AzureNodeWebRole MyRole`

A aplicação de Node.js é definida no ficheiro **server.js**, localizada no diretório para a função da web (**WebRole1** por predefinição). Eis o código:

    var http = require('http');
    var port = process.env.port || 1337;
    http.createServer(function (req, res) {
        res.writeHead(200, { 'Content-Type': 'text/plain' });
        res.end('Hello World\n');
    }).listen(port);

Este código é, essencialmente, igual a amostra de "Olá mundo" no Web site da [nodejs.org] , exceto que utilize o número de porta atribuído pelo ambiente da nuvem.

## <a name="deploy-the-application-to-azure"></a>Implementar a aplicação para Azure

    [AZURE.INCLUDE [create-account-note](../../includes/create-account-note.md)]

### <a name="download-the-azure-publishing-settings"></a>Transferir as definições de publicação Azure

Para implementar a aplicação para Azure, tem de transferir as definições de publicação para a sua subscrição Azure.

1.  Execute o seguinte cmdlet do Azure PowerShell:

        Get-AzurePublishSettingsFile

    Isto irá utilizar o seu browser para navegar para a página de transferência de definições de publicar. Poderá ser-lhe pedido para iniciar sessão com uma Account Microsoft. Se Sim, utilize a conta associada à sua subscrição Azure.

    Guarde o perfil transferido para uma localização de ficheiro que pode aceder facilmente.

2.  Execute o cmdlet para importar o perfil de publicação que transferiu das seguintes:

        Import-AzurePublishSettingsFile [path to file]


    > [AZURE.NOTE] Depois de importar as definições de publicar, considere eliminar o ficheiro transferido .publishSettings, porque contém informações que poderiam permitir que outra pessoa aceder à sua conta.

### <a name="publish-the-application"></a>Publicar a aplicação

Para publicar, execute os seguintes comandos:

    $ServiceName = "NodeHelloWorld" + $(Get-Date -Format ('ddhhmm'))   
    Publish-AzureServiceProject -ServiceName $ServiceName  -Location "East US" -Launch

- **-ServiceName** Especifica o nome para a implementação. Tem de ser um nome exclusivo, caso contrário, o processo de publicar irá falhar. O comando **Get-Date** tachas numa cadeia de data/hora que deverá facilitar o nome exclusivo.

- **-Localização** Especifica o Centro de dados que a aplicação será alojada no. Para ver uma lista de centros de dados disponíveis, utilize o cmdlet **Get-AzureLocation** .

- **-Iniciação** abre uma janela do browser e navega para o serviço alojado depois de concluída a implementação.

Depois de publicação é concluída com êxito, irá ver uma resposta semelhante ao seguinte:

![O resultado do comando AzureService publicar][The output of the Publish-AzureService command]

> [AZURE.NOTE]
> Pode demorar alguns minutos para a aplicação implementar e ficam disponíveis quando publicado pela primeira vez.

Assim que tiver concluído a implementação, uma janela do browser será abra e navegue para o serviço de nuvem.

![Numa janela do browser a apresentar a página do mundo Olá; o URL indica que a página é alojada no Azure.][A browser window displaying the hello world page; the URL indicates the page is hosted on Azure.]

A aplicação agora está em execução no Azure.

O cmdlet **Publicar AzureServiceProject** executa os seguintes passos:

1.  Cria um pacote para implementar. O pacote contém todos os ficheiros na sua pasta de aplicação.

2.  Se não existir, cria uma nova **conta de armazenamento** . A conta de armazenamento Azure é utilizada para armazenar o pacote de aplicação durante a implementação. Em segurança pode eliminar a conta de armazenamento depois de concluir o processo implementação.

3.  Se um ainda não existir, cria um novo **serviço na nuvem** . Um **serviço na nuvem** é o contentor na qual a aplicação está alojada quando for implementado Azure. Para mais informações, consulte o artigo [Descrição geral da criação de um serviço alojado para Azure].

4.  Publica o pacote de implementação do Azure.


## <a name="stopping-and-deleting-your-application"></a>Parar a e eliminar a sua aplicação

Depois de o implementar a aplicação, poderá pretender desativá-lo para que pode evitar custos extra. Faturas Azure web instâncias de função por hora de tempo de servidor consumida. Hora do servidor é consumida assim que a sua aplicação é implementada, mesmo se as instâncias não estiver a executar e são no estado de parado.

1.  Na janela do Windows PowerShell, pare a implementação do service criada na secção anterior com o cmdlet seguinte:

        Stop-AzureService

    Parar o serviço poderá demorar vários minutos. Quando o serviço está parado, recebe uma mensagem que indica que deixou de.

    ![O estado do comando parar AzureService][The status of the Stop-AzureService command]

2.  Para eliminar o serviço, contacte o cmdlet seguinte:

        Remove-AzureService

    Quando lhe for pedido, introduza **Y** para eliminar o serviço.

    Eliminar o serviço poderá demorar vários minutos. Depois do serviço foi eliminado recebe uma mensagem a indicar que o serviço foi eliminado.

    ![O estado do comando Remover AzureService][The status of the Remove-AzureService command]

    > [AZURE.NOTE] Eliminar o serviço não eliminar a conta de armazenamento que foi criada quando o serviço foi publicado inicialmente e continuará a ser faturada para armazenamento utilizado. Se nada mais estiver a utilizar o armazenamento, poderá eliminá-la.

## <a name="next-steps"></a>Próximos passos

Para mais informações, consulte o [Centro de programadores do Node.js].

<!-- URL List -->

[Comparação de Web sites, serviços em nuvem e máquinas virtuais Azure]: ../app-service-web/choose-web-site-cloud-service-vm.md
[utilizar uma aplicação lightweight web]: ../app-service-web/web-sites-nodejs-develop-deploy-mac.md
[Azure Powershell]: ../powershell-install-configure.md
[Azure SDK para .NET 2.7]: http://www.microsoft.com/en-us/download/details.aspx?id=48178
[Ligar o PowerShell]: ../powershell-install-configure.md#how-to-connect-to-your-subscription
[nodejs.org]: http://nodejs.org/
[Descrição geral da criação de um serviço de alojado Azure]: https://azure.microsoft.com/documentation/services/cloud-services/
[Centro de programadores do node.js]: https://azure.microsoft.com/develop/nodejs/

<!-- IMG List -->

[The result of the New-AzureService helloworld command]: ./media/cloud-services-nodejs-develop-deploy-app/node9.png
[The output of the Add-AzureNodeWebRole command]: ./media/cloud-services-nodejs-develop-deploy-app/node11.png
[A web browser displaying the Hello World web page]: ./media/cloud-services-nodejs-develop-deploy-app/node14.png
[The output of the Publish-AzureService command]: ./media/cloud-services-nodejs-develop-deploy-app/node19.png
[A browser window displaying the hello world page; the URL indicates the page is hosted on Azure.]: ./media/cloud-services-nodejs-develop-deploy-app/node21.png
[The status of the Stop-AzureService command]: ./media/cloud-services-nodejs-develop-deploy-app/node48.png
[The status of the Remove-AzureService command]: ./media/cloud-services-nodejs-develop-deploy-app/node49.png
