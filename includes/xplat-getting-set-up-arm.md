<properties services="virtual-machines" title="Using Azure CLI with Azure Resource Manager" authors="squillace" solutions="" manager="timlt" editor="tysonn" />

<tags
   ms.service="virtual-machine"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="linux"
   ms.workload="infrastructure"
   ms.date="04/13/2015"
   ms.author="rasquill" />

## <a name="using-azure-cli-with-azure-resource-manager-arm"></a>Utilizar o clip Azure com Azure Gestor de recursos (processador)

Antes de poder utilizar o clip Azure com comandos do Gestor de recursos e modelos para implementar o Azure recursos e utilizar grupos de recursos das cargas de trabalho, será necessário uma conta com Azure (obviamente). Se não tiver uma conta, pode obter uma [Azure versão de avaliação gratuita aqui](https://azure.microsoft.com/pricing/free-trial/).

> [AZURE.NOTE] Se ainda não tiver uma conta Azure mas tiver uma subscrição de subscrição do MSDN, pode obter gratuito créditos Azure ao ativar o seu [aqui benefícios do MSDN subscritor](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) – ou podem utilizar a conta gratuita. Quer irá funcionar para acesso Azure.

### <a name="step-1-verify-the-azure-cli-version"></a>Passo 1: Verificar a versão do clip do Azure

Para utilizar o clip do Azure para comandos obrigatório e modelos de processador, tem de ter, pelo menos, versão 0.8.17. Para verificar a sua versão, introduza `azure --version`. Deverá visualizar algo parecido com:

    $ azure --version
    0.8.17 (node: 0.10.25)

Se precisar de atualizar a sua versão do Azure clip, consulte o artigo [Clip Azure](https://github.com/Azure/azure-xplat-cli).

### <a name="step-2-verify-you-are-using-a-work-or-school-identity-with-azure"></a>Passo 2: Certifique-se de que está a utilizar uma trabalho ou escola identidade com o Azure

Só pode utilizar o modo de comando processador se estiver a utilizar um [inquilino do Azure Active Directory](https://msdn.microsoft.com/library/azure/jj573650.aspx#BKMK_WhatIsAnAzureADTenant) ou um [Nome Principal de serviço](https://msdn.microsoft.com/library/azure/dn132633.aspx). (Estes são também chamados *ids organizacionais*.)

Para ver se tiver um, inicie sessão no escrevendo `azure login` e com o seu trabalho ou escola nome de utilizador e palavra-passe quando lhe for pedido. Se tiver uma, deverá ver algo semelhante ao seguinte:

    $ azure login
    info:    Executing command login
    warn:    Please note that currently you can login only via Microsoft organizational account or service principal. For instructions on how to set them up, please read http://aka.ms/Dhf67j.
    Username: ahmet@contoso.onmicrosoft.com
    Password: *********
  	|info:    Added subscription Visual Studio Ultimate with MSDN
    info:    Setting subscription Visual Studio Ultimate with MSDN as default
    info:    Added subscription Azure Free Trial
    +
    info:    login command OK

Se não vir este, terá de criar um novo inquilino (ou serviço principal) com a sua identidade de conta Microsoft. (Isto é frequentemente as maiúsculas/minúsculas com subscrições MSDN pessoais ou subscrições de avaliação gratuitas.) Para criar uma trabalho ou escola o id da sua conta Azure criada com um id do Microsoft, consulte o artigo [associar um Azure AD Directory com uma nova subscrição Azure](https://msdn.microsoft.com/library/azure/jj573650.aspx#BKMK_WhatIsAnAzureADTenant). Se achar que deve ter um id organizacional já, poderá ter de modo a falar com a pessoa que criou a conta para si.

### <a name="step-3-choose-your-azure-subscription"></a>Passo 3: Escolher a sua subscrição do Azure

Se tiver apenas uma subscrição na sua conta Azure, Azure clip associa próprio esse subscrição por predefinição. Se tiver mais do que uma subscrição, terá de selecione a subscrição que pretende utilizar, escrevendo `azure account set <subscription id or name> true` onde _id da subscrição ou nome_ é o id da subscrição ou no nome da subscrição que pretender para trabalhar com a sessão actual.

Deverá visualizar algo semelhante ao seguinte:

    $ azure account set "Azure Free Trial" true
    info:    Executing command account set
    info:    Setting subscription to "Azure Free Trial" with id "2lskd82-434-4730-9df9-akd83lsk92sa".
    info:    Changes saved
    info:    account set command OK

### <a name="step-4-place-your-azure-cli-in-the-arm-mode"></a>Passo 4: Coloque o seu clip Azure no modo de processador

Para utilizar o modo de gestão de recursos do Azure (processador) com o clip do Azure, escreva `azure config mode arm`. Deverá visualizar algo semelhante ao seguinte:

    $ azure config mode arm
    info:    New mode is arm

> [AZURE.NOTE] Pode mudar para o utilizar comandos de gestão de serviço Azure escrevendo `azure config mode asm`.
