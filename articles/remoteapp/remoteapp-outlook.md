<properties
    pageTitle="Utilizar o Outlook no Azure RemoteApp | Microsoft Azure" 
    description="Saiba como configurar e utilizar o Outlook no Azure RemoteApp | Microsoft Azure"
    services="remoteapp"
    documentationCenter=""
    authors="pavithir"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="08/15/2016"
    ms.author="elizapo" />

# <a name="using-microsoft-outlook-in-azure-remoteapp"></a>Utilizar o Microsoft Outlook Azure RemoteApp

> [AZURE.IMPORTANT]
> Está a ser descontinuada Azure RemoteApp. Leia o [anúncio](https://go.microsoft.com/fwlink/?linkid=821148) para obter detalhes.

Azure RemoteApp suporta O365 do Microsoft Outlook. Leia mais sobre como [funciona o Office no Azure RemoteApp](remoteapp-officesubscription.md). Existem algumas definições recomendadas para o Outlook quando utilizada num RemoteApp do Azure.

## <a name="cached-mode"></a>Modo em cache
O modo em cache é uma configuração recomendada quando utilizar o Outlook no Azure RemoteApp. Quando configurar uma conta do Outlook 2013 para utilizar o modo Exchange em cache, o Outlook 2013 funciona a partir de uma cópia local da caixa de correio do Microsoft Exchange do utilizador que está armazenada num ficheiro de dados offline (ficheiro OST) no computador do utilizador, juntamente com o livro de endereços Offline (OAB). A caixa de correio em cache e OAB são atualizadas periodicamente a partir do serviço do Office 365. Leia mais sobre [as diferenças entre o modo online e em cache](https://technet.microsoft.com/library/jj683103.aspx).

O utilizador pode selecionar **Modo Exchange em cache** ou o **Modo Online** durante a configuração de conta ou ao alterar as definições da conta. Também pode implementar um modo ou o outro utilizando a ferramenta de personalização do Office (OCT) ou a política de grupo.  

Leia as [instruções passo a passo sobre como ativar o modo em cache](https://technet.microsoft.com/library/c6f4cad9-c918-420e-bab3-8b49e1885034#proc).

## <a name="search"></a>Pesquisa
No Azure RemoteApp, utilizando a pesquisa do Outlook tem limitações. Azure RemoteApp utiliza VMs agrupados para acomodar sessões de utilizador. A indexação da pesquisa depende o ID de máquina, é diferente para VMs diferentes. É possível que, sempre que um utilizador inicia sessão no Azure RemoteApp, estes são direcionados para uma nova VM. Seria significa que, se podemos ativar a pesquisa local, o indexador será executada sempre que o ID de máquina altera (quando o utilizador está numa VM diferente). Dependendo do tamanho da. Ficheiro OST, o indexador pode demorar muito tempo a concluir e utilizar o recursos necessários para outras aplicações. Pesquisa não apenas seria lenta, mas não poderá produzir resultados. Utilizar um perfil de conta modo Online seria resolver este problema, mas desempenho global seria sofrem devido à falta de uma cache local (consulte a ligação acima para obter mais informações sobre a diferença entre o modo em cache e online). Infelizmente, não pode ser desativada indexados/local pesquisa e pesquisa online não pode ser ativada por predefinição no Outlook 2013.
