
<properties
    pageTitle="Informações para um VNET no Azure RemoteApp de dimensionamento | Microsoft Azure"
    description="Saiba mais sobre os requisitos de endereço IP do Azure RemoteApp a trabalhar com um VNET"
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



# <a name="sizing-information-for-a-vnet-in-azure-remoteapp"></a>Informações de redimensionamento para um VNET no RemoteApp do Azure

> [AZURE.IMPORTANT]
> Está a ser descontinuada Azure RemoteApp. Leia o [anúncio](https://go.microsoft.com/fwlink/?linkid=821148) para obter detalhes.

Quando utiliza o Azure RemoteApp com uma rede virtual (VNET), RemoteApp utiliza endereços IP dentro da sub-rede. Com base na escala do seu serviço de RemoteApp, é necessário para se certificar de que a sua sub-rede aceda tem suficiente endereços IP disponíveis para máquinas virtuais de RemoteApp. Enquanto estas orientações de dimensionamento não não perfeita dada como o RemoteApp dinamicamente gira e gira para baixo máquinas virtuais numa coleção, ajudarão a estimativa o intervalo de sub-rede. Isto é particularmente importante como, assim que um serviço de RemoteApp é colocado num VNET, não é possível aumentar o tamanho de sub-rede sem Remover RemoteApp.

Para cada coleção de RemoteApp que pretende executar na capacidade máxima, deve ter 100 endereços IP disponíveis. Por exemplo, se tiver uma colecção de RemoteApp no plano de padrão e pretender que o máximo de 500 utilizadores, deve ter 100 endereços IP para essa coleção. Da mesma forma, precisa de 100 endereços IP para uma coleção de RemoteApp no plano de base tem 800 utilizadores. Se planeia ter menos utilizadores (menor que o valor máximo), pode reduzir os endereços IP necessários por coleção. O requisito de tamanho de sub-rede mínima é de 30 endereços IP (/ 27).

Dar saída as seguintes informações para se certificar de que seu VNET é propertly configurada e a funcionar:

- [Migrar a partir de um VNET pessoal para um VNET Azure](remoteapp-migratevnet.md)
- [Validar o Azure VNET para utilizar com RemoteApp do Azure](remoteapp-vnet.md)
