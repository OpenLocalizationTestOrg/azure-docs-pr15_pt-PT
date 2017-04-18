<properties
   pageTitle="Criar um contentor de serviço de nuvem com PowerShell | Microsoft Azure"
   description="Este artigo explica como criar um contentor de serviço de nuvem com PowerShell. O contentor aloja funções web e trabalhador."
   services="cloud-services"
   documentationCenter=".net"
   authors="cawaMS"
   manager="timlt"
   editor=""/>

<tags
   ms.service="cloud-services"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="powershell"
   ms.workload="na"
   ms.date="07/29/2016"
   ms.author="cawa"/>

# <a name="use-an-azure-powershell-command-to-create-an-empty-cloud-service-container"></a>Utilizar um comando Azure PowerShell para criar um contentor de serviço de nuvem vazia
Este artigo explica como criar rapidamente um contentor de serviços em nuvem utilizar cmdlets do Azure PowerShell. Siga os passos abaixo:

1. Instale o cmdlet do PowerShell do Microsoft Azure a partir da página de [transferências do Azure PowerShell](http://aka.ms/webpi-azps) .
2. Abra a linha de comandos do PowerShell.
3. Utilize a [Adicionar AzureAccount](https://msdn.microsoft.com/library/dn495128.aspx) para iniciar sessão.

    > [AZURE.NOTE] Para obter mais instruções sobre como instalar o cmdlet do PowerShell do Azure e ligar à sua subscrição do Azure, consulte [como instalar e configurar o Azure PowerShell](../powershell-install-configure.md).

4. Utilize o cmdlet **AzureService novo** para criar um contentor de serviço de nuvem Azure vazia.

    ```
    New-AzureService [-ServiceName] <String> [-AffinityGroup] <String> [[-Label] <String>] [[-Description] <String>] [[-ReverseDnsFqdn] <String>] [<CommonParameters>]
    New-AzureService [-ServiceName] <String> [-Location] <String> [[-Label] <String>] [[-Description] <String>] [[-ReverseDnsFqdn] <String>] [<CommonParameters>]
```

5. Siga este exemplo para invocar o cmdlet:
```
New-AzureService -ServiceName "mytestcloudservice" -Location "Central US" -Label "mytestcloudservice"
```

Para mais informações sobre a criação de serviço em nuvem Azure, execute:
```
Get-help New-AzureService
```

### <a name="next-steps"></a>Próximos passos

 * Para gerir a implementação de serviço de nuvem, consulte os comandos [Get-AzureService](https://msdn.microsoft.com/library/azure/dn495131.aspx), [Remover AzureService](https://msdn.microsoft.com/library/azure/dn495120.aspx)e [AzureService conjunto](https://msdn.microsoft.com/library/azure/dn495242.aspx) . Também pode consultar a [como configurar os serviços em nuvem](cloud-services-how-to-configure.md) para obter mais informações.

 * Para publicar o projeto de serviço de nuvem Azure, consulte o código de **PublishCloudService.ps1** exemplo a partir de [entrega contínua para serviço na nuvem no Azure](cloud-services-dotnet-continuous-delivery.md).
