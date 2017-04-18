
<properties
    pageTitle="Resolver problemas de coleções de nuvem RemoteApp - criação | Microsoft Azure"
    description="Saiba como resolver problemas de falhas de criação de coleções de sites do RemoteApp na nuvem"
    services="remoteapp"
    documentationCenter=""
    authors="vkbucha"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/15/2016"
    ms.author="elizapo" />



# <a name="troubleshoot-creating-remoteapp-cloud-collections"></a>Criar RemoteApp nuvem coleções de resolução de problemas

> [AZURE.IMPORTANT]
> Está a ser descontinuada Azure RemoteApp. Leia o [anúncio](https://go.microsoft.com/fwlink/?linkid=821148) para obter detalhes.

Se estiver a ter problemas ao criar uma coleção de nuvem, consulte o artigo as seguintes informações.

## <a name="your-image-is-invalid"></a>A imagem é inválida ##
Se vir uma mensagem como "GoldImageInvalid" quando estão a aguardar Azure aprovisionar a sua coleção de, significa que a imagem do modelo não cumpre [definidos requisitos de imagem](remoteapp-imagereqs.md). Sim, aceda ler essas [exigências](remoteapp-imagereqs.md), corrigir a sua imagem e tente criar novamente a coleção.

## <a name="common-errors-seen-in-the-azure-management-portal"></a>Erros comuns vistos no portal de gestão do Azure

    DNS server could not be reached
    ProvisioningTimeout

Nuvem coleções de sites com frequência falhas durante a criação de devido à está a utilizar imagens personalizadas.  Se vir um dos erros acima e estiver a utilizar uma imagem personalizada para criar a coleção, verifique as seguintes coisas:

- Certifique-se de que a imagem personalizada carregou cumpre os requisitos de imagem.
- Com mais frequência o problema comum é que a imagem não foi devidamente processada por Sysprep.  
- Verifique se a imagem pode arranque dentro Hyper-V ou experimente criar um VM IAAS diretamente na sua subscrição do Azure utilizando a imagem. Se a VM falha de arranque e não iniciado, em seguida, normalmente indica que a imagem personalizada não estava preparada corretamente.  Certifique-se de que a imagem personalizada foi criada seguir como para criar uma imagem de modelo personalizado para RemoteApp

Se estiver a utilizar uma das imagens do Microsoft incluídas com a sua subscrição, tente criar novamente a coleção. Se o problema persistir, em seguida, contacte o suporte da Microsoft.

    PlatformImageTrialModeOnly

Se vir este erro normalmente, isto significa que foram atualizados para uma paga conta mas está a tentar utilizar uma imagem de fornecido pela Microsoft é válida apenas durante o modo de avaliação do serviço. Neste caso, experimente criar novamente a coleção de nuvem, mas certifique-se de que especifique a imagem correta.
