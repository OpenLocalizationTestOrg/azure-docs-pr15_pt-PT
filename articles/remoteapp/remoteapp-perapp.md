<properties
   pageTitle="Publicar aplicações para que os utilizadores individuais numa coleção de RemoteApp do Azure (pré-visualização) | Microsoft Azure"
   description="Saiba como pode publicar aplicações para utilizadores individuais, em vez de dependendo grupos, no Azure RemoteApp."
   services="remoteapp-preview"
   documentationCenter=""
   authors="piotrci"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="remoteapp"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="compute"
   ms.date="08/15/2016"
   ms.author="piotrci"/>

# <a name="publish-applications-to-individual-users-in-an-azure-remoteapp-collection-preview"></a>Publicar aplicações para que os utilizadores individuais numa coleção de RemoteApp do Azure (pré-visualização)

> [AZURE.IMPORTANT]
> Está a ser descontinuada Azure RemoteApp. Leia o [anúncio](https://go.microsoft.com/fwlink/?linkid=821148) para obter detalhes.

Este artigo explica como publicar aplicações para que os utilizadores individuais numa coleção de RemoteApp do Azure. Nova funcionalidade no Azure RemoteApp, este é atualmente em "privada pré-visualização do" e está disponível apenas para selecionar vanguarda para efeitos de avaliação.

Azure RemoteApp ativadas originalmente apenas uma forma de "publicação" de aplicações: o administrador seria publicar aplicações a partir da imagem e são visíveis para todos os utilizadores na coleção de.

Um cenário comum é incluir muitas aplicações numa única imagem e implementar uma colecção de para reduzir os custos de gestão. Não é frequente todas as aplicações são relevantes para todos os utilizadores – os administradores seriam preferem publicar aplicações para que os utilizadores individuais, para que esse utilizador não verá aplicações desnecessárias na sua aplicação de feed.

Isto é agora possível no Azure RemoteApp – atualmente como uma funcionalidade de pré-visualização limitado. Eis um breve resumo das novas funcionalidades:

1. Uma coleção de pode ser definida para um dos dois modos:
 
  - o original "coleção modo de", onde podem ver todos os utilizadores numa coleção de todas as publicadas aplicações. Este é o modo predefinido.
  - o novo "aplicação modo", onde os utilizadores veem apenas a aplicações que foram explicitamente atribuída aos mesmos

2. Neste momento o modo de aplicação só pode ser ativado utilizar cmdlets do PowerShell do Azure RemoteApp.

  - Quando definido para o modo de aplicação, a atribuição de utilizador na coleção de não pode ser gerida através do portal do Azure. Atribuição de utilizador tem de ser gerido durante os cmdlets do PowerShell.

3. Os utilizadores irão ver apenas as aplicações publicadas diretamente às mesmas. No entanto, poderá ainda ser possível para um utilizador iniciar as aplicações disponíveis na imagem acedendo-los diretamente no sistema operativo.
  - Esta funcionalidade não fornece um seguro bloqueio de aplicações; apenas limita visibilidade na aplicação do feed.
  - Se precisar de identificar os utilizadores a partir de aplicações, terá de utilizar colecções separadas para que.

## <a name="how-to-get-azure-remoteapp-powershell-cmdlets"></a>Como obter os cmdlets do PowerShell de RemoteApp do Azure

Para experimentar a nova funcionalidade de pré-visualização, terá de utilizar cmdlets do Azure PowerShell. Atualmente não é possível utilizar o portal de gestão do Azure para ativar o modo de publicação nova aplicação.

Em primeiro lugar, certifique-se de que tem o [módulo Azure PowerShell](../powershell-install-configure.md) instalado.

Em seguida, inicie consola do PowerShell no modo de administrador e execute o cmdlet seguinte:

        Add-AzureAccount

Vai apresentar-lhe o Azure nome de utilizador e palavra-passe. Assim que iniciar sessão, vai conseguir executar RemoteApp do Azure cmdlets contra as suas subscrições Azure.

## <a name="how-to-check-which-mode-a-collection-is-in"></a>Como verificar se o modo em que uma coleção de está no

Execute o cmdlet seguinte:

        Get-AzureRemoteAppCollection <collectionName>

![Verificar o modo de coleções de sites](./media/remoteapp-perapp/araacllelvel.png)

A propriedade AclLevel pode ter os seguintes valores:

- Colecção de: o original publicação modo de. Todos os utilizadores ver que todas as aplicações de publicadas.
- Aplicação: o novo publicação modo. Os utilizadores veem apenas as aplicações publicadas diretamente às mesmas.

## <a name="how-to-switch-to-application-publishing-mode"></a>Como mudar para o modo de publicação de aplicações

Execute o cmdlet seguinte:

        Set-AzureRemoteAppCollection -CollectionName -AclLevel Application

Estado de publicação a aplicação será preservado: inicialmente todos os utilizadores irão ver todas as aplicações publicadas originais.

## <a name="how-to-list-users-who-can-see-a-specific-application"></a>Como os utilizadores de lista quem pode ver uma aplicação específica

Execute o cmdlet seguinte:

        Get-AzureRemoteAppUser -CollectionName <collectionName> -Alias <appAlias>

Lista todos os utilizadores a quem podem ver a aplicação.

Nota: Pode ver os aliases de aplicação (denominados "alias de aplicação" na sintaxe acima) ao executar Get AzureRemoteAppProgram - NomeColecção <collectionName>.

## <a name="how-to-assign-an-application-to-a-user"></a>Como atribuir uma aplicação a um utilizador

Execute o cmdlet seguinte:

        Add-AzureRemoteAppUser -CollectionName <collectionName> -UserUpn <user@domain.com> -Type <OrgId|MicrosoftAccount> -Alias <appAlias>

O utilizador verá agora a aplicação no cliente do RemoteApp do Azure e será possível ligar ao mesmo.

## <a name="how-to-remove-an-application-from-a-user"></a>Como remover uma aplicação a partir de um utilizador

Execute o cmdlet seguinte:

        Remove-AzureRemoteAppUser -CollectionName <collectionName> -UserUpn <user@domain.com> -Type <OrgId|MicrosoftAccount> -Alias <appAlias>

## <a name="providing-feedback"></a>Fornecer comentários
Agradecemos-lhe os comentários e sugestões sobre esta funcionalidade de pré-visualização. Preencha o [inquérito](http://www.instant.ly/s/FDdrb) para diga-na sua opinião.

## <a name="havent-had-a-chance-to-try-the-preview-feature"></a>Não foi a oportunidade de experimentar a funcionalidade de pré-visualização?
Se de que não participaram na pré-visualização do ainda, utilize este [inquérito](http://www.instant.ly/s/AY83p) para pedir acesso.
