<properties
    pageTitle="Publicar uma aplicação no Azure RemoteApp | Microsoft Azure"
    description="Saiba como publicar aplicações e recursos no Azure RemoteApp."
    services="remoteapp"
    documentationCenter=""
    authors="lizap"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/15/2016"
    ms.author="elizapo" />


# <a name="how-to-publish-an-app-in-remoteapp"></a>Como publicar uma aplicação no RemoteApp

> [AZURE.IMPORTANT]
> Está a ser descontinuada Azure RemoteApp. Leia o [anúncio](https://go.microsoft.com/fwlink/?linkid=821148) para obter detalhes.

Depois de criar a coleção de RemoteApp, tem de publicar o aplicações ou recursos que pretende tornar disponível para os seus utilizadores. Das imagens dos modelos fornecidas com a sua subscrição tem apenas algumas aplicações publicadas por predefinição - partilhar outras aplicações, tem de publicá-los.

> [AZURE.NOTE] Necessita de atualizar uma aplicação? Terá de [atualizar a imagem](remoteapp-update.md) pela primeira vez.

No separador **publicação** no portal do, clique em **Publicar**. Pode adicionar uma aplicação a partir do menu de **começar a** sua imagem de modelo ou forneça o caminho para onde a aplicação estiver instalada a imagem do modelo. Se optar por adicionar a partir do menu **Iniciar** , selecione a aplicação para publicar a partir da lista. Se optar por forneça o caminho para a aplicação, introduza um nome para a aplicação e o caminho para a aplicação. Utilizar variáveis no caminho - por exemplo, "% systemdrive %" em vez de "c:\".

> [AZURE.NOTE] Se pretender adicionar a aplicação a partir do menu **Iniciar** , tem de ter *adicionado nessa aplicação para o * *Iniciar* * menu na sua imagem do modelo.* Caso contrário, RemoteApp só verão que *está* no menu **Iniciar** e será confuso. 

>Para se certificar-se de que a sua aplicação está no menu **Iniciar** , coloca um ficheiro de atalho - **lnk** - dentro da pasta de Menu\Programs %systemdrive%\ProgramData\Microsoft\Windows\Start.

> Caso se tenha esquecido da de adicionar a aplicação ao menu **Iniciar** quando criou o modelo, optar por adicionar o caminho para a aplicação. (Ou recrie a sua imagem do modelo, mas que é bastante um pouco mais trabalho.)


 