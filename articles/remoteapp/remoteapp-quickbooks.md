<properties 
    pageTitle="Implementar do QuickBooks no Azure RemoteApp | Microsoft Azure" 
    description="Saiba como partilhar do QuickBooks com RemoteApp do Azure." 
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
    ms.author="elizapo" />



# <a name="how-do-you-deploy-quickbooks-in-azure-remoteapp"></a>Como pode implementar do QuickBooks no Azure RemoteApp?

> [AZURE.IMPORTANT]
> Está a ser descontinuada Azure RemoteApp. Leia o [anúncio](https://go.microsoft.com/fwlink/?linkid=821148) para obter detalhes.

Utilize as seguintes informações para partilhar do QuickBooks como uma aplicação no Azure RemoteApp.


Pode partilhar do QuickBooks 2015 Enterprise com RemoteApp do Azure na coleção de um híbrido ou na nuvem. O ficheiro da empresa tem se encontram uma VM que está a executar o servidor de bases de dados do QuickBooks separada dos servidores RemoteApp do Azure. Nunca armazenar o ficheiro da empresa na sua imagem do Azure RemoteApp - perda de dados é esperado se fazê-lo. Só do QuickBooks Enterprise suporta o ficheiro do QuickBooks numa partilha externa com o servidor de bases de dados do QuickBooks acessível através de funcionamento em rede do Windows padrão de alojamento.   

> [AZURE.IMPORTANT] O servidor de base de dados do QuickBooks que está a alojar o ficheiro da empresa tem se encontram uma VM separada dentro da mesma VNET como da coleção de RemoteApp do Azure.  

## <a name="steps-to-deploy-quickbooks"></a>Passos para implementar do QuickBooks

1. Crie uma VM Azure e instalar do QuickBooks, servidor de bases de dados do QuickBooks e coloque o ficheiro de empresa numa VM Azure.  Certifique-se para corretamente configurar regras de firewall.
2. Instale do QuickBooks numa [imagem personalizada](remoteapp-imageoptions.md) e crie uma [coleção de RemoteApp do Azure](remoteapp-collections.md), na nuvem ou híbrido, dentro de VNET mesmo exata onde reside VM que aloja o servidor de base de dados do QuickBooks com os ficheiros de empresa. 
3.  [Publicar](remoteapp-publish.md) Aplicação do QuickBooks aos utilizadores
4.  O cliente alojado no Azure RemoteApp do QuickBooks de iniciação, navegar utilizando o padrão funcionamento em rede Windows para a VM que aloja o servidor de base de dados do QuickBooks e abra o ficheiro da empresa. 

## <a name="documentation-references"></a>Referências de documentação

- Do QuickBooks [configurações suportadas](http://enterprisesuite.intuit.com/products/enterprise-solutions/technical/#top)
- Do QuickBooks [Opções de implementação](http://enterprisesuite.intuit.com/everythingenterprise/launchpad/new-user/)

Também pode verificar Ignite apresentação, [os conceitos básicos do Microsoft Azure RemoteApp gestão e administração](https://channel9.msdn.com/Events/Ignite/2015/BRK3868) - para a frente rápido para 1:02:45 para ir para a parte do QuickBooks.

## <a name="deployment-architecture"></a>Arquitetura de implementação

![Do QuickBooks + Azure RemoteApp implementação](./media/remoteapp-quickbooks/ra-quickbooks.png)