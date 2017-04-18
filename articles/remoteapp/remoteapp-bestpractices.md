<properties
    pageTitle="Azure RemoteApp melhores práticas | Microsoft Azure"
    description="Práticas recomendadas para configurar e utilizar RemoteApp do Azure."
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

# <a name="best-practices-for-configuring-and-using-azure-remoteapp"></a>Práticas recomendadas para configurar e utilizar RemoteApp do Azure

> [AZURE.IMPORTANT]
> Está a ser descontinuada Azure RemoteApp. Leia o [anúncio](https://go.microsoft.com/fwlink/?linkid=821148) para obter detalhes.

As seguintes informações podem ajudá-lo a configurar e utilizar RemoteApp do Azure produtiva.

## <a name="connectivity"></a>Conectividade


- Utilize sempre a versão mais recente do cliente. Utilizar clientes mais antigos poderá resultar em problemas de conectividade e de outros experiências degradadas. Ativar atualizações automáticas de aplicação para o seu dispositivo irá garantir que o cliente mais recente sempre está instalado.
- Utilize sempre a mais estável e fiável ligação à internet disponível para si.  
- Utilize apenas suportadas ligações de proxy para obter um desempenho ideal conectividade.  O proxy SOCKS não é suportado.

## <a name="applications"></a>Aplicações


- Guarde e feche RemoteApp aplicações quando tiver terminado com a aplicação. A aplicação de fecho não poderá resultar na perda de dados.
- Valide aplicações personalizadas antes de os utilizar no Azure RemoteApp. Isto inclui assegurar que trabalha numa plataforma múltiplas sessões e não consumam recursos desnecessários, tais como memória e CPU poderá suprimir outro utilizador na mesma colecção de. Para obter informações, transfira e reveja a [Aplicação compatibilidade melhores práticas para serviços de ambiente de trabalho remoto](http://www.dabcc.com/resources/Application%20Compatibility%20Best%20Practices%20for%20Remote%20Desktop%20Services.pdf).

## <a name="configuration-and-management"></a>Configuração e gestão


- Manter as imagens de modelo atualizados, instalar atualizações de software e outras correções críticas conforme necessário. Este procedimento garante que como RemoteApp do Azure automática-as escalas de realizar a sua capacidade a, cada instância é corrigida.  
- Certifique-se a que sua implementação de serviços de Federação do Active Directory (AD FS) é segura e fiável. Caso contrário, poderá falhar autenticação de cliente, impedindo que utilizadores acedam RemoteApp do Azure.
- Configure imagens de modelos com aplicações instaladas, funções ou funcionalidades assim que forem sem estado. Não deve confiar em qualquer instâncias das máquinas virtuais num serviço RemoteApp a ser num estado persistente.
    - Armazene todos os dados de utilizador em perfis de utilizador ou outras localizações de armazenamento externas para o serviço, tal como no local ficheiro partilhas ou OneDrive.
    - Armazene dados partilhados em localizações de armazenamento externas para o serviço, tal como no local ficheiro partilhas ou OneDrive.
    - Configure as definições de todo o sistema na imagem do modelo, em vez de em máquinas virtuais individuais num serviço.
    - Desativar as atualizações automáticas de software para as aplicações publicadas - em vez disso, aplicá-los manualmente para a imagem do modelo e testá-las antes de implementar a partir do modelo.
