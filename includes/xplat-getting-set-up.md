<properties services="virtual-machines" title="Setting up Azure CLI for service management" authors="squillace" solutions="" manager="timlt" editor="tysonn" />

<tags
   ms.service="virtual-machine"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="linux"
   ms.workload="infrastructure"
   ms.date="04/13/2015"
   ms.author="rasquill" />

## <a name="using-azure-cli"></a>Utilizar o clip Azure

Os passos seguintes ajudá-lo utilizar Azure clip facilmente com a versão mais recente e a subscrição inicial maiúscula. Se precisar de instalar o clip do Azure e ligá-lo à sua conta pela primeira vez, consulte o artigo a [Interface de comandos do Azure (Azure CLI)](xplat-cli-install.md).

### <a name="step-1-update-azure-cli-version"></a>Passo 1: Versão de atualização Azure clip

Para utilizar o clip do Azure para comandos obrigatório com o modo de gestão de serviço, deve ter uma versão recente se possível. Para verificar a sua versão, introduza `azure --version`. Deverá visualizar algo parecido com:

    $ azure --version
    0.8.17 (node: 0.10.25)

Se pretende atualizar a sua versão do Azure clip, consulte o artigo [Clip Azure](https://github.com/Azure/azure-xplat-cli).

### <a name="step-2-set-the-azure-account-and-subscription"></a>Passo 2: Configurar a conta Azure e subscrição

Assim que tiver ligado seu clip Azure com a conta que pretende utilizar, poderá ter mais de uma subscrição. Se fizer, deverá rever as subscrições disponíveis para a sua conta ao escrever `azure account list`e, em seguida, selecione a subscrição que pretende utilizar, escrevendo `azure account set <subscription id or name> true` onde _id da subscrição ou nome_ é o id da subscrição ou no nome da subscrição que pretender para trabalhar com a sessão actual. Deverá visualizar algo semelhante ao seguinte:

    $ azure account set "Visual Studio Ultimate with MSDN" true
    info:    Executing command account set
    info:    Setting subscription to "Visual Studio Ultimate with MSDN" with id "0e220bf6-5caa-4e9f-8383-51f16b6c109f".
    info:    Changes saved
    info:    account set command OK

> [AZURE.NOTE] Se ainda não tiver uma conta Azure mas tiver uma subscrição de subscrição do MSDN, pode obter gratuito créditos Azure ao ativar o seu [aqui benefícios do MSDN subscritor](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) – ou podem utilizar a conta gratuita. Quer irá funcionar para acesso Azure.
