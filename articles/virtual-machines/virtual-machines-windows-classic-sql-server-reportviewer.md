<properties 
    pageTitle="Utilizar ReportViewer num Web site | Microsoft Azure"
    description="Este tópico descreve como criar um Web site Microsoft Azure com o controlo do Visual Studio ReportViewer que apresenta um relatório armazenado numa máquina de Virtual do Microsoft Azure."
    services="virtual-machines-windows"
    documentationCenter="na"
    authors="guyinacube"
    manager="erikre"
    editor="monicar" 
    tags="azure-service-management" />
<tags 
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="10/04/2016"
    ms.author="asaxton" />

# <a name="use-reportviewer-in-a-web-site-hosted-in-azure"></a>Utilizar ReportViewer num Web site alojado no Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]


Pode criar um Web site Microsoft Azure com o controlo do Visual Studio ReportViewer que apresenta um relatório armazenado numa máquina de Virtual do Microsoft Azure. O controlo de ReportViewer está numa aplicação Web que cria utilizando o modelo de aplicação Web do ASP.NET.

>[AZURE.IMPORTANT] Os modelos de aplicação Web do ASP.NET MVC não suportam o controlo de ReportViewer.

Para incorporar ReportViewer no seu Web site Microsoft Azure, tem de concluir as seguintes tarefas.

- **Adicionar** Assemblagem para o pacote de implementação

- **Configurar** Autorização e autenticação

- **Publicar** a aplicação Web do ASP.NET Azure

## <a name="prerequisites"></a>Pré-requisitos

Reveja a secção "recomendação geral e as melhores práticas" no [SQL Server Business Intelligence em máquinas virtuais do Azure](virtual-machines-windows-classic-ps-sql-bi.md).

>[AZURE.NOTE] Controlos de ReportViewer são enviados com Visual Studio, Standard Edition ou acima. Se estiver a utilizar o Web Developer Express Edition, tem de instalar o [Tempo de execução do relatório Visualizador 2012](https://www.microsoft.com/download/details.aspx?id=35747) para utilizar as funcionalidades de runtime ReportViewer.
>
>ReportViewer configurado no modo de processamento local não é suportada no Microsoft Azure.

Reveja o papel de branco [controlo do Visualizador de relatórios do Reporting Services e máquina de virtual do Microsoft Azure com base servidores de relatórios](http://download.microsoft.com/download/2/2/0/220DE2F1-8AB3-474D-8F8B-C998F7C56B5D/Reporting%20Services%20report%20viewer%20control%20and%20Azure%20VM%20based%20report%20servers.docx).

## <a name="adding-assemblies-to-the-deployment-package"></a>Adicionar conjuntos para o pacote de implementação

Quando a alojar o seu ASP.NET aplicação no local, conjuntos de ReportViewer são normalmente instalados diretamente na cache de assemblagem global (GAC) do servidor do IIS durante a instalação do Visual Studio e podem ser acedidos diretamente através da aplicação. No entanto, quando alojar o seu pedido de ASP.NET na nuvem, Microsoft Azure não permite alguma coisa para ser instalado em GAC, para que deve certificar-se de que conjuntos de ReportViewer estão disponíveis localmente para a sua aplicação. Pode fazê-lo adicionando as referências às-los no seu projeto e configurá-los sejam copiados localmente.

No modo de processamento remoto, o controlo de ReportViewer utiliza os seguintes conjuntos:

- **Microsoft.ReportViewer.WebForms.dll**: contém o código de ReportViewer, tem de utilizar ReportViewer na sua página. Uma referência para esta assemblagem é adicionada ao projeto quando larga um controlo de ReportViewer para uma página ASP.NET no projeto.

- **Microsoft.ReportViewer.Common.dll**: contém classes utilizadas pelo controlo ReportViewer em tempo de execução. -Não é adicionado automaticamente ao seu projeto.

### <a name="to-add-a-reference-to-microsoftreportviewercommon"></a>Para adicionar uma referência a Microsoft.ReportViewer.Common

- Nó de **referências** do seu projeto com o botão direito e selecione **Adicionar referência**, selecione a assemblagem no separador .NET e clique em **OK**.

### <a name="to-make-the-assemblies-locally-accessible-by-your-aspnet-application"></a>Para tornar os conjuntos localmente acessível pela sua aplicação do ASP.NET

1. Na pasta **referências** , clique em assemblagem Microsoft.ReportViewer.Common para que sejam apresentadas as respetivas propriedades no painel de propriedades.

1. No painel Propriedades, defina **Cópia Local** para True.

1. Repita os passos 1 e 2 para Microsoft.ReportViewer.WebForms.

### <a name="to-get-reportviewer-language-pack"></a>Para obter o pacote de idiomas ReportViewer

1. Instale o pacote de redistributable de tempo de execução do relatório Visualizador 2012 adequado a partir do [Centro de transferências da Microsoft](http://go.microsoft.com/fwlink/?LinkId=317386).

1. Selecione o idioma na lista pendente e a página redirecionada à página do Centro de transferências correspondente.

1. Clique em **Transferir** para iniciar a transferência de ReportViewerLP.exe.

1. Depois de transferir ReportViewerLP.exe, clique em **Executar** para instalar imediatamente, ou clique em **Guardar** para guardá-lo para o seu computador. Se clicar em **Guardar**, lembre-se o nome da pasta onde guardar o ficheiro.

1. Localize a pasta onde o guardou o ficheiro. Com o botão direito ReportViewerLP.exe, clique em **Executar como administrador**e, em seguida, clique em **Sim**.

1. Depois de executar ReportViewerLP.exe, irá ver a c:\windows\assembly tem o recurso ficheiros **Microsoft.ReportViewer.Webforms.Resources** e **Microsoft.ReportViewer.Common.Resources**.

### <a name="to-configure-for-localized-reportviewer-control"></a>Para configurar para o controlo de ReportViewer localizado

1. Transfira e instale o pacote de redistributable Microsoft relatório Visualizador 2012 Runtime ao seguir as instruções especificadas acima.

1. Criar <language> pasta no project e copiar o conjunto de recursos associados ficheiros aí. Os ficheiros de assemblagem de recurso a ser copiado são: **Microsoft.ReportViewer.Webforms.Resources.dll** e **Microsoft.ReportViewer.Common.Resources.dll**. Selecione os ficheiros de agrupamento de recursos e, no painel Propriedades, defina o **Copiar para directório de saída** para "**Copiar sempre**".

1. Defina a cultura & UICulture para o projecto da web. Para mais informações sobre como configurar a cultura e cultura da IU para uma página Web do ASP.NET, consulte o artigo [como: definir a cultura e cultura da IU para global de página Web do ASP.NET](http://go.microsoft.com/fwlink/?LinkId=237461).

## <a name="configuring-authentication-and-authorization"></a>Configurar a autenticação e autorização

O ReportViewer tem de utilizar credenciais adequadas para autenticar com o servidor de relatórios e as credenciais têm de ser autorizadas pelo servidor de relatório para aceder a relatórios que pretende. Para obter informações sobre a autenticação, consulte o artigo o papel de branco [controlo do Visualizador de relatórios do Reporting Services e máquina de virtual do Microsoft Azure com base servidores de relatórios](https://msdn.microsoft.com/library/azure/dn753698.aspx).

## <a name="publish-the-aspnet-web-application-to-azure"></a>Publicar a aplicação Web do ASP.NET Azure

Para obter instruções sobre a publicação de uma aplicação Web do ASP.NET para Azure, consulte o artigo [como: migrar e publicar uma aplicação Web Azure a partir do Visual Studio](../vs-azure-tools-migrate-publish-web-app-to-cloud-service.md) e [comece a trabalhar com aplicações Web e ASP.NET](../app-service-web/web-sites-dotnet-get-started.md).

>[AZURE.IMPORTANT] Se o comando Adicionar projeto de implementação do Azure ou adicionar projeto de serviço de nuvem Azure não aparecer no menu de atalho no Explorador de solução, poderá ter de alterar a arquitetura de destino para o projeto para .NET Framework 4.
>
>Os dois comandos fornecem, essencialmente, a mesma funcionalidade. Um ou o comando outras aparecerá no menu de atalho dependendo da versão do Microsoft Azure SDK tiver instalado.

## <a name="resources"></a>Recursos

[Relatórios da Microsoft](http://go.microsoft.com/fwlink/?LinkId=205399)

[SQL Server Business Intelligence em máquinas virtuais do Azure](virtual-machines-windows-classic-ps-sql-bi.md)

[Utilizar o PowerShell para criar um VM Azure com um servidor de relatórios do modo nativo](virtual-machines-windows-classic-ps-sql-report.md)

[Relatório de controlo do Visualizador de relatórios de serviços e Microsoft Azure máquina virtual servidores baseados no relatório](http://download.microsoft.com/download/2/2/0/220DE2F1-8AB3-474D-8F8B-C998F7C56B5D/Reporting%20Services%20report%20viewer%20control%20and%20Azure%20VM%20based%20report%20servers.docx)
