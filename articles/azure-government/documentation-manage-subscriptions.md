<properties
    pageTitle="Serviços de administração pública Azure | Microsoft Azure"
    description="Informações sobre como gerir a sua subscrição na administração pública do Azure"
    services="Azure-Government"
    cloud="gov" 
    documentationCenter=""
    authors="zakramer"
    manager="liki"
    editor="" />

<tags
    ms.service="multiple"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="azure-government"
    ms.date="10/21/2016"
    ms.author="zakramer" />


#  <a name="managing-and-connecting-to-your-subscription-in-azure-government"></a>Gerir e ligar à sua subscrição no Azure Governo

Administração pública Azure tem único URLs e os pontos finais para gerir o seu ambiente. É importante utilizar as ligações à direita para gerir o seu ambiente através do portal ou PowerShell. Assim que estiver ligado ao ambiente do Azure Governo, as operações normais para gerir um serviço funciona se o componente ter sido implementado.

## <a name="connecting-via-the-portal"></a>Ligar através do portal
O portal é a forma principal que a maioria das pessoas se ligam ao Azure Governo.  Para ligar, navegue para o portal em [https://portal.azure.us](https://portal.azure.us).  A versão legada do portal do Azure pode ser acedida através de [https://manage.windowsazure.us](https://manage.windowsazure.us).

Subscrições podem ser criadas para a sua conta ao ligar-se ao [https://account.windowsazure.us](https://account.windowsazure.us).

## <a name="connecting-via-powershell"></a>Ligar através do PowerShell

Se estiver a utilizar Azure PowerShell para gerir uma grande subscrição através do acesso ou script funcionalidades que não estão atualmente disponíveis no portal do Azure que precisa de aceder ao Azure Governo em vez de público Azure.  Se tiver utilizado o PowerShell Azure público, é principalmente da mesma.  Diferenças no Azure governo são:

+ Ligar a sua conta
+ Nomes de região

>[AZURE.NOTE] Se ainda não utilizou PowerShell, consulte o artigo [Introdução ao Azure PowerShell](../powershell-install-configure.md).

Quando iniciar o PowerShell, tem de indicar ao Azure o PowerShell para ligar ao Azure Governo ao especificar um parâmetro de ambiente.  O parâmetro assegura que PowerShell está a ligar os pontos finais corretos.  O conjunto de pontos finais é determinado quando ligar o início de sessão à sua conta.  APIs diferentes requerem diferentes versões do parâmetro ambiente:

Tipo de ligação | Comando
---|----
Comandos de [Gestão de serviços](https://msdn.microsoft.com/library/dn708504.aspx) | `Add-AzureAccount -Environment AzureUSGovernment`
Comandos de [Gestão de recursos](https://msdn.microsoft.com/library/mt125356.aspx) | `Add-AzureRmAccount -EnvironmentName AzureUSGovernment`
Comandos do [Azure Active Directory](https://msdn.microsoft.com/library/azure/jj151815.aspx) | `Connect-MsolService -AzureEnvironment UsGovernment`
[V2 de comando do Azure Active Directory](https://msdn.microsoft.com/library/azure/mt757189.aspx) | `Connect-AzureAD -AzureEnvironmentName AzureUSGovernment`

Também poderá utilizar o parâmetro de ambiente ao ligar a uma conta de armazenamento utilizando AzureStorageContext novo e especificar AzureUSGovernment.

### <a name="determining-region"></a>Para determinar a região

Assim que estiver ligado, existe uma diferença adicional – as regiões utilizadas para um serviço de destino.  Cada nuvem Azure tem regiões diferentes.  Pode vê-las listadas na página de disponibilidade do serviço.  Utilizar o parâmetro de localização da região de um comando normalmente.

Existe uma captura.  As regiões do Azure Governo tem de ser formatado de forma diferente que os seus nomes comuns:

Nome comum | Comando
---|----
Virginia de administração pública dos e.u.a. | USGov Virginia
Iowa de administração pública dos e.u.a. | USGov Iowa

>[AZURE.NOTE] Não existe nenhum espaço entre EUA e administração pública ao utilizar o parâmetro de localização.

Se nunca pretender validar as regiões disponíveis no Azure Governo, pode executar os seguintes comandos e imprimir a lista atual:

    Get-AzureLocation

Se estiver curioso os ambientes disponíveis ao longo do Azure, pode executar:

    Get-AzureEnvironment

## <a name="next-steps"></a>Próximos passos

Se estiver à procura para obter mais informações, pode consultar o artigo:

+ [Documentos do PowerShell no GitHub](https://github.com/Azure/azure-powershell)
+ [Instruções passo a passo sobre como ligar para a gestão de recursos](https://blogs.msdn.microsoft.com/azuregov/2015/10/08/configuring-arm-on-azure-gc/)
+ [Azure documentos do PowerShell no MSDN](https://msdn.microsoft.com/library/mt619274.aspx)

Para informações suplementares e atualizações subscrever o [Microsoft Azure administração pública blogue] (https://blogs.msdn.microsoft.com/azuregov/)
