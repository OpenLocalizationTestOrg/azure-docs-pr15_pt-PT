<properties
    pageTitle="Criar uma imagem de RemoteApp do Azure | Microsoft Azure"
    description="Saiba mais sobre as opções disponíveis para a criação de imagens para RemoteApp do Azure"
    services="remoteapp"
    documentationCenter=""
    authors="lizap"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/15/2016"
    ms.author="elizapo" />



# <a name="create-an-azure-remoteapp-image"></a>Criar uma imagem de RemoteApp do Azure

> [AZURE.IMPORTANT]
> Está a ser descontinuada Azure RemoteApp. Leia o [anúncio](https://go.microsoft.com/fwlink/?linkid=821148) para obter detalhes.

Azure RemoteApp utiliza imagens para colocar em espera as aplicações que partilha com os seus utilizadores. (Recomendamos tomar a sua imagem e utilizá-la para criar VMs - que do que o acesso de utilizadores quando que inicie sessão no Azure RemoteApp.) Para criar uma coleção de RemoteApp do Azure com a sua escolha de aplicações, quer seja na nuvem ou híbrido, comece por criar uma imagem com essas aplicações instaladas. Em seguida, crie uma coleção de que utiliza a imagem, atribuir aos utilizadores para a coleção de e publicar aplicações para esses utilizadores.

Tem várias opções para criar ou utilizar imagens. O [requisito](remoteapp-imagereqs.md) de básicas de uma imagem é que executar o Windows Server 2012 R2 e ter instalada a função de anfitrião de sessão de ambiente de trabalho remoto (RDSH). Como obter que é onde obter interessantes coisas.

Tem as seguintes opções quando chegar a imagens:

- Pode importar e utilizar uma [imagem com base numa máquina virtual Azure](remoteapp-image-on-azurevm.md). Isto é bom para as aplicações de linha de negócio que necessitam de configurações personalizadas. Pode personalizar a imagem para trabalhar para a aplicação.
- Pode [criar e carregar uma imagem personalizada](remoteapp-create-custom-image.md). Isto é bom se já tiver uma imagem que utilizar para a sua implementação de serviços de ambiente de trabalho remoto no local.
- Pode utilizar uma das [imagens de modelos de](remoteapp-images.md) incluído na sua subscrição RemoteApp. Estas imagens são criadas e mantidas pela equipa RemoteApp e contenham algumas aplicações padrão (como o conjunto de aplicações do Office) que pode disponibilizar aos seus utilizadores. Tenha em atenção que apenas a imagem do Office 365 Pro Plus pode ser utilizada numa definição de produção.

Independentemente de onde obtém a sua imagem ou como criá-lo, irá querer para se certificar de que compreende os [requisitos da aplicação](remoteapp-appreqs.md) para garantir que a sua aplicação funciona bem na RemoteApp. Em seguida, o próximo passo é criar uma coleção de [nuvem](remoteapp-create-cloud-deployment.md) ou [híbrido](remoteapp-create-hybrid-deployment.md) .
