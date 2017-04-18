<properties
    pageTitle="Qual é das imagens dos modelos RemoteApp do Azure? | Microsoft Azure"
    description="Saiba mais acerca das imagens dos modelos incluídas RemoteApp do Azure."
    services="remoteapp"
    documentationCenter=""
    authors="lizap"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="08/15/2016"
    ms.author="elizapo" />

# <a name="what-is-in-the-azure-remoteapp-template-images"></a>Qual é das imagens dos modelos RemoteApp do Azure?

> [AZURE.IMPORTANT]
> Está a ser descontinuada Azure RemoteApp. Leia o [anúncio](https://go.microsoft.com/fwlink/?linkid=821148) para obter detalhes.

A subscrição RemoteApp do Azure inclui três imagens de modelos:


- Windows Server 2012
- Microsoft Office 365 ProPlus (obrigatório subscrição do Office 365)
- Microsoft Office 2013 Professional Plus (versão de avaliação do apenas)

> [AZURE.IMPORTANT]A sua subscrição do Azure RemoteApp concede o que acesso ao software nas imagens, com a exceção Office 365 ProPlus, solicitar uma subscrição separada e Office 2013, que não podem ser utilizados em produção. Isto significa que pode partilhar programas ou aplicações das imagens dos modelos com os seus utilizadores. Por exemplo, se criar uma coleção de que utiliza a imagem do Windows Server 2012 R2, pode publicar sistema Centro Endpoint Protection para os utilizadores acedam através de RemoteApp.
>
> Consulte o artigo os [Detalhes de licenciamento RemoteApp](remoteapp-licensing.md) para obter mais informações. E [Utilizar o Office com o Azure RemoteApp](remoteapp-o365.md) para o Office licenciamento em informações.

Continue a ler para obter detalhes sobre o que cada imagem contém.

## <a name="windows-server-2012-r2--the-vanilla-image"></a>Windows Server 2012 R2 ("a imagem baunilha")
Esta imagem é baseada num sistema operativo de centro de dados do Microsoft Windows Server 2012 R2 e tem as seguintes funções e funcionalidades instaladas para cumprir os requisitos para RemoteApp do Azure imagens de modelos:


- 4,5, do .NET framework 3.5.1, 3.5
- Experiência de ambiente de trabalho
- Serviços de escrita manual e tinta digital
- Multimédia Foundation
- Anfitrião da sessão de ambiente de trabalho remoto
- O Windows PowerShell 4.0
- O Windows PowerShell ISE
- Suporte de WoW64

Esta imagem também tem os seguintes aplicações instaladas:

- Adobe Flash Player
- O Microsoft Silverlight
- Microsoft sistema Centro 2012 Endpoint Protection
- Microsoft Windows Media Player


## <a name="microsoft-office-365-proplus-subscription-required"></a>Microsoft Office 365 ProPlus (subscrição necessário)
Office 365 é a aplicação mais pedida, para que criámos uma imagem para que possa trabalhar com "personalizada".

Esta imagem é uma extensão da imagem baunilha e tem os seguintes componentes de instalado para além dos componentes descritos na imagem do Windows Server 2012 R2 do Microsoft Office 365 ProPlus:


- Access
- Excel
- Lync
- OneNote
- OneDrive para empresas (Repare que o agente de sincronização não é suportado para utilização com o Azure RemoteApp)
- Outlook
- PowerPoint
- Word
- Ferramentas de verificação linguística do Microsoft Office

A imagem também inclui o Visio Pro e o Project Pro.

E as seguintes aplicações, assim:

- SQL Native client
- Controlador ODBC
- SQL Server Data Mining cliente
- MasterDataServices cliente
- O Microsoft Publisher
- PowerQuery
- Power Map


A funcionalidade completa de aplicações do Office 365 ProPlus só está disponível para utilizadores que têm um plano do Office 365 ProPlus. Para obter mais detalhes sobre o Office 365 planos de subscrição consulte o artigo [planos de serviço do Office 365](http://technet.microsoft.com/library/office-365-plan-options.aspx). Ainda tem perguntas? Consulte as informações do [Office 365 + RemoteApp](remoteapp-o365.md) . Consulte também o artigo novo, [como utilizar a sua subscrição do Office 365 RemoteApp do Azure](remoteapp-officesubscription.md).

Tenha em atenção que terá de licença em separado - Office 365 ProPlus, Visio Pro e Project Pro cada possuem os seus próprios licença.

## <a name="microsoft-office-2013-professional-plus-trial-only"></a>Microsoft Office 2013 Professional Plus (versão de avaliação do apenas)
Durante o período de avaliação gratuito, pode testar o serviço com a imagem do Office 2013.

Esta imagem é uma extensão da imagem baunilha e tem os seguintes componentes do Microsoft Office 2013 Professional Plus instalado para além dos componentes descritos na imagem do Windows Server 2012 R2:


- Access
- Excel
- Lync
- OneNote
- OneDrive para empresas (Repare que o agente de sincronização não é suportado para utilização com o Azure RemoteApp)
- Outlook
- PowerPoint
- Projecto
- Visio
- Word
- Ferramentas de verificação linguística do Microsoft Office

> [AZURE.IMPORTANT]**Informações legais:** Esta imagem não inclui uma licença do Microsoft Office e, em seguida, *não podem ser utilizados para produção*. Office 2013 Professional Plus imagem destina-se apenas a utilização de avaliação. Se pretender utilizar as aplicações do Office no Azure RemoteApp para produção, terá de utilizar a imagem do Office 365 ProPlus. Para obter mais detalhes sobre o licenciamento do Office, consulte o artigo [utilizar o Office 365 com RemoteApp do Azure](remoteapp-o365.md)
