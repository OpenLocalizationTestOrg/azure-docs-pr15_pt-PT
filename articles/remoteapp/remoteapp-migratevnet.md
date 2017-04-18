<properties
    pageTitle="Como migrar a partir de um VNET RemoteApp para um VNET Azure | Microsoft Azure"
    description="Saiba como migrar a partir de um VNET RemoteApp para um VNET Azure"
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



# <a name="how-to-migrate-a-hybrid-collection-from-a-remoteapp-vnet-to-an-azure-vnet"></a>Como migrar uma coleção de híbrido a partir de um VNET RemoteApp para um VNET Azure

> [AZURE.IMPORTANT]
> Está a ser descontinuada Azure RemoteApp. Leia o [anúncio](https://go.microsoft.com/fwlink/?linkid=821148) para obter detalhes.

Boas notícias! Vamos ativou implementar coleções de RemoteApp híbrido diretamente na sua existentes Azure redes virtuais (VNETs) em vez de criar VNETs RemoteApp específicas. Permite-lhe tirar partido das funcionalidades mais recentes VNET (como ExpressRoute) e conceder acesso de rede direta de colecções de híbrido para outros serviços Azure e máquinas virtuais implementadas esse VNET.  (Isto obtém é um melhor desempenho e configuração mais fácil de configurações VNET para VNET).


Digamos que já criou um híbrido coleção RemoteApp denominado *OriginalCollection* com um RemoteApp VNET denominado *RemoteAppVNET*. Eis os passos para migrá-lo para um novo VNET do Azure denominado *AzureVNET*.

1.  No separador **redes** no [portal de gestão](http://manage.windowsazure.com/), crie uma VNET denominado *AzureVNET*, utilizando a mesma localização, a configuração de DNS e o espaço de endereços (para pelo menos um dos sub-redes *AzureVNET* ) como que foi utilizado para *RemoteAppVNET*.
2.  Configurar *AzureVNET* para um dos anfitrião ou tenha conectividade de rede para a implementação do Active Directory que *OriginalCollection* é o domínio aderido ao.
3.  No separador **RemoteApps** , crie uma nova coleção de RemoteApp denominada *Nova coleção*. (Utilize a opção **criar com VNET** , não a **Criação rápida**).
3.  Configure *NewCollection* para ser implementada para uma sub-rede *AzureVNET*.
4.  Configure *NewCollection* para utilizar a mesma imagem e informações de associação de domínio como que foi utilizado para *OriginalCollection*.
5.  Após algumas horas, *NewCollection* irá aparecer na sua lista de coleções de sites com um estado activo.

Agora, se não precisar de migrar quaisquer informações de utilizador da colecção original para a nova coleção de, efetue os seguintes passos seguinte:

6.  Elimine *OriginalCollection*.
7.  Elimine *RemoteAppVNET*.

E, já está!

Em alternativa, se precisar de migrar informações do utilizador da colecção original para a nova coleção de, siga estes passos seguinte:

6.  Enviar um e-mail para [remoteappforum@microsoft.com](mailto:remoteappforum@microsoft.com?subject=Azure%20RemoteApp%20user%20information%20migration) com o seu ID da subscrição Azure, o nome da sua coleção de original e o nome da sua coleção de novo e peça-lhes para migrar as suas informações de utilizador.
7.  Dentro de 2 dias úteis a equipa de RemoteApp irá mover a lista de acesso de utilizador e todos os documentos de utilizador e as definições de utilizador da colecção original para a nova coleção.
8.  Elimine *OriginalCollection*.
9.  Elimine *RemoteAppVNET*.

E agora, já está!

Se tiver dúvidas ou necessitar de assistência especial, e-mail [remoteappforum@microsoft.com](mailto:remoteappforum@microsoft.com?subject=Azure%20RemoteApp%20VNET%20migration%20help).
