<properties
   pageTitle="Família SO convidado 1 extinção Repare | Microsoft Azure"
   description="Fornece informações sobre quando a extinção Azure convidado SO família 1 aconteceu e como determinar se são afetados"
   services="cloud-services"
   documentationCenter="na"
   authors="raiye"
   manager="timlt"
   editor=""/>

<tags
   ms.service="cloud-services"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="tbd"
   ms.date="10/24/2016"
   ms.author="raiye"/>



# <a name="guest-os-family-1-retirement-notice"></a>Aviso de reforma convidado SO família 1

A reforma do SO família 1 pela primeira vez foi anunciada em 1 de Junho de 2013.

**2 de Setembro de 2014** O sistema operativo de convidado Azure (convidado SO) família 1. x, qual é baseado no sistema operativo Windows Server 2008, oficialmente foi foi removida. Todas as tentativas para implementar novos serviços ou atualizar os serviços existentes utilizando 1 de família irão falhar com uma mensagem de erro que indica que o convidado SO família 1 foi retirado.

**3 de Novembro de 2014** Suporte adicional para o convidado SO família 1 terminou e é foi totalmente removida. Vai ser afectados todos os serviços ainda na família 1. Vamos podem deixar desses serviços em qualquer altura. Não há garantias que seus serviços irão continuar a executar, a menos que manualmente atualizá-los manualmente.

Se tiver questões adicionais, visite os [Fóruns de serviços na nuvem](http://social.msdn.microsoft.com/Forums/home?forum=windowsazuredevelopment&filter=alltypes&sort=lastpostdesc) ou [contactar o suporte do Azure](https://azure.microsoft.com/support/options/).




## <a name="are-you-affected"></a>São afetados?

Os serviços em nuvem são afetados se aplica-se de qualquer um dos seguintes procedimentos:

1. Tem um valor de "osFamily ="1"explicitamente especificada no ficheiro ServiceConfiguration.cscfg do seu serviço de nuvem.
2. Não tem um valor para osFamily explicitamente especificada no ficheiro ServiceConfiguration.cscfg do seu serviço de nuvem. Atualmente, o sistema utiliza neste caso o valor predefinido de "1".
3. Portal clássico do Azure lista o valor da família do sistema operativo convidado como "Windows Server 2008".

Para localizar qual dos seus serviços em nuvem estiver a executar o qual da família SO, pode executar o script abaixo no Azure PowerShell, apesar de tem de [configurar o Azure PowerShell](../powershell-install-configure.md) pela primeira vez. Para detalhes adicionais sobre o script, consulte o artigo [Azure convidado SO família 1 fim do ciclo de vida: Junho de 2014](http://blogs.msdn.com/b/ryberry/archive/2014/04/02/azure-guest-os-family-1-end-of-life-june-2014.aspx). 

```Powershell
foreach($subscription in Get-AzureSubscription) {
    Select-AzureSubscription -SubscriptionName $subscription.SubscriptionName

    $deployments=get-azureService | get-azureDeployment -ErrorAction Ignore | where {$_.SdkVersion -NE ""}

    $deployments | ft @{Name="SubscriptionName";Expression={$subscription.SubscriptionName}}, ServiceName, SdkVersion, Slot, @{Name="osFamily";Expression={(select-xml -content $_.configuration -xpath "/ns:ServiceConfiguration/@osFamily" -namespace $namespace).node.value }}, osVersion, Status, URL
}
```

Os serviços em nuvem vai ser afetados pelo extinção SO família 1 se a coluna osFamily no resultado do script estiver vazia ou contiver "1".

## <a name="recommendations-if-you-are-affected"></a>Se estiver a afectado de recomendações

Recomendamos que migrar o seu funções de serviço na nuvem para uma das famílias de tipos de SO convidado suportados:

**SO convidado 4. x da família** -Windows Server 2012 R2 *(recomendado)*

1. Certifique-se de que a aplicação está a utilizar o SDK 2.1 ou posterior com o .NET framework 4.0, 4,5 ou 4.5.1.
2. Defina o atributo osFamily para "4" no ficheiro ServiceConfiguration.cscfg e implementar o seu serviço de nuvem.


**SO convidado 3. x da família** -Windows Server 2012

1. Certifique-se de que a aplicação está a utilizar o SDK 1,8 ou posterior com o .NET framework 4.0 ou 4,5.
2. Defina o atributo osFamily para "3" no ficheiro ServiceConfiguration.cscfg e implementar o seu serviço de nuvem.


**SO convidado 2. x da família** -Windows Server 2008 R2

1. Certifique-se de que a sua aplicação utiliza SDK 1.3 e superior com o .NET framework 3.5 ou 4.0.
2. Defina o atributo osFamily para "2" no ficheiro ServiceConfiguration.cscfg e implementar o seu serviço de nuvem.


## <a name="extended-support-for-guest-os-family-1-ended-nov-3-2014"></a>Suporte adicional para o convidado SO família 1 terminou 3 de Novembro de 2014
Serviços em nuvem na família SO convidado 1 já não são suportados. Migre fórum desativar família 1 mais cedo possível para evitar interrupção de serviço.  

## <a name="next-steps"></a>Próximos passos
Reveja o mais recente [disponibilização de SO convidado](cloud-services-guestos-update-matrix.md).
