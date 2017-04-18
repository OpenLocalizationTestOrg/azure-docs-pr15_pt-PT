
<properties
    pageTitle="Validar o Azure VNET para utilizar com o Azure RemoteApp | Microsoft Azure"
    description="Saiba como para se certificar de que o seu VNET Azure está pronto para utilizar com RemoteApp do Azure"
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



# <a name="validate-the-azure-vnet-to-use-with-azure-remoteapp"></a>Validar o Azure VNET para utilizar com RemoteApp do Azure

> [AZURE.IMPORTANT]
> Está a ser descontinuada Azure RemoteApp. Leia o [anúncio](https://go.microsoft.com/fwlink/?linkid=821148) para obter detalhes.

Antes de utilizar uma VNET Azure com RemoteApp do Azure, poderá querer validar o VNET. Isto ajuda a evitar problemas com a conectividade.

Para validar o VNET Azure, faça o seguinte:

1. Crie uma máquina de virtual Azure dentro da sub-rede de VNET o Azure que pretende utilizar com RemoteApp do Azure.

2. Ligar para essa VM utilizando a opção **Ligar** no portal de gestão.
3. Participar a máquina virtual para o mesmo domínio que pretende utilizar com o Azure RemoteApp. Se estiver a criar uma coleção de híbrido que se liga à sua rede no local, aderir a máquina virtual para o seu domínio local.

Se esta é efetuada com êxito, o Azure VNET está pronto para utilizar com RemoteApp.

Para mais informações sobre o fluxo de trabalho do fim para fim híbrido coleções de sites, consulte os artigos seguintes:

- [Como planear a sua rede virtual para RemoteApp do Azure](remoteapp-planvnet.md)
- [Criar uma coleção de híbrido](remoteapp-create-hybrid-deployment.md)
- [Implementar RemoteApp do Azure coleção à sua rede Virtual Azure (com o suporte para ExpressRoute)](http://blogs.msdn.com/b/rds/archive/2015/04/23/deploy-azure-remoteapp-collection-to-your-azure-virtual-network-with-support-for-expressroute.aspx)
