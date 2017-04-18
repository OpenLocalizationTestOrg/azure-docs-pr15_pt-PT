
<properties
    pageTitle="Carregar uma imagem personalizada para RemoteApp do Azure | Microsoft Azure"
    description="Saiba como carregar uma imagem personalizada para RemoteApp do Azure"
    services="remoteapp"
    documentationCenter=""
    authors="ericorman"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/15/2016"
    ms.author="ericor" />



# <a name="upload-a-custom-image-for-azure-remoteapp"></a>Carregar uma imagem personalizada para RemoteApp do Azure

> [AZURE.IMPORTANT]
> Está a ser descontinuada Azure RemoteApp. Leia o [anúncio](https://go.microsoft.com/fwlink/?linkid=821148) para obter detalhes.

Agora que criou a sua imagem de modelo personalizado ou se tiver o atualizados com as alterações, tem de carregar imagem para a biblioteca de imagens do Azure RemoteApp. Utilize estes passos.


## <a name="before-you-start"></a>Antes de começar

1.      Verifique se que a imagem personalizada cumpre os [requisitos de imagem](remoteapp-imagereqs.md) e os [requisitos da aplicação](remoteapp-appreqs.md).
2.      Instale o [módulo Azure PowerShell](../powershell-install-configure.md).

## <a name="step-by-step-on-how-to-upload-custom-image"></a>Passo a passo sobre como carregar imagem personalizada

1.      Abra o Portal de gestão do Azure e navegue para a página de RemoteApp.
2.      No separador **imagens de modelos** , clique em **carregar** na parte inferior da página.
4.      Introduza um nome amigável para a imagem e especifique a localização da conta de armazenamento. Certifique-se de que está a localização na mesma localização que a sua coleção de RemoteApp ou para uma localização onde pretende criar uma.
5.      Quando lhe for pedido, transfira o script para o seu PC local.
6.      Copie os parâmetros do comando na caixa de texto para a sua área de transferência.
7.      Abra uma janela do Windows PowerShell elevada.
8.      A partir da janela do Windows PowerShell elevada, navegue para o mesmo directório onde tenha transferido o script.
9.      Cole o comando copiado e prima **Enter**.

    O processo de carregamento será iniciado e duração pode depender de vários fatores incluindo sua velocidade da rede e o tamanho da imagem

11.    Se o carregamento não teve êxito devido a interrupção de rede ou coisas desta forma, pode sempre retomar o processo de carregamento que começou a. Para retomar a um carregamento, execute o script novamente utilizando a mesma linha de comandos.

> [AZURE.WARNING] Nunca modificar o script de carregamento. Foram implementadas verificações específicas para garantir que a imagem cumpre os requisitos de imagem e os requisitos da aplicação.

## <a name="common-problems"></a>Problemas comuns

- Certifique-se de que utiliza o Windows PowerShell, não Azure PowerShell. Tem de instalar o módulo Azure PowerShell porque determinados módulos são necessárias durante o processo de carregamento.
- Nunca alter o script, validações existem para sua conveniência.
- Se o ficheiro vhd for bloqueado durante o carregamento, copie o ficheiro ou movê-lo para um novo carregamento de localização e tente novamente. Poderá haver algumas processo do Windows que está a impedir o carregamento.  
