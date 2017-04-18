<properties
   pageTitle="Descrição geral de portais de vários necessárias para criar uma oferta para mercado | Microsoft Azure"
   description="Descrição geral de portais de vários necessárias para criar uma oferta para mercado"
   services="marketplace-publishing"
   documentationCenter=""
   authors="HannibalSII"
   manager="hascipio"
   editor=""/>

<tags
   ms.service="marketplace"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/27/2016"
   ms.author="hascipio" />


# <a name="portals-you-will-need"></a>Terá de portais
Antes de iniciar o processo de uma oferta de publicação, vamos começar introduzida para os vários portais que terá de. Segue-se uma breve resumo sobre os portais – Developer Center, Portal de publicação do Azure e Azure Portal – pela ordem que irão interagir com os mesmos.                                                                            
## <a name="developer-center"></a>Centro de programadores
[http://Dev.Windows.com/registration?accountprogram=Azure](http://dev.windows.com/registration?accountprogram=azure)
### <a name="description"></a>Descrição
Criar a sua conta Microsoft Developer Center é uma tarefa única. Certifique-se de que a empresa ainda não tiver uma conta do Centro de programadores antes de tentar criar uma. Durante o processo, vamos recolher informações de conta bancária, informações de imposto e informações de endereço da empresa.

> [AZURE.NOTE] Se está a publicar ofertas apenas livres (ou oferece trazer-your-proprietário-licença), podemos não necessitam de informações do imposto e banco.

### <a name="identityaccount-used"></a>Conta/identidade utilizada
Idealmente, esta é uma lista de distribuição ou grupo de segurança (por exemplo, azurepublishing@ *partnercompany*.com). A distribuição lista ou segurança grupo **tem** de estar registado como uma conta Microsoft.

> [AZURE.TIP] Recomendamos que utilize uma lista de distribuição ou grupo de segurança porque remove a dependência de qualquer outra pessoa, apesar de uma conta individual pode ser utilizada, assim.

## <a name="publishing-portal"></a>Portal de publicação
[https://publish.windowsazure.com](https://publish.windowsazure.com)

### <a name="description"></a>Descrição
Este é o portal do que utilizar para trabalhar na oferta e publicá-lo (de marketing, preços, publicar, certificação se aplicável, etc.).

### <a name="identityaccount-used"></a>Conta/identidade utilizada
O grupo de segurança ou lista de distribuição acima tem de ser utilizado pela primeira vez para iniciar sessão no portal de publicação. Mais tarde, outros utilizadores podem ser adicionados como co-administradores. Este é o modo como obtém mapeado para os dados de registo do Centro de programadores.

## <a name="azure-portal"></a>Portal do Azure
[https://portal.Azure.com](https://portal.azure.com)
### <a name="description"></a>Descrição
Este é o portal de onde pode ver as ofertas de faseada e publicadas do Azure Marketplace (aplicável para VMs, modelos de soluções e serviços de programador com base no Gestor de recursos do Azure).
### <a name="identityaccount-used"></a>Conta/identidade utilizada
Enquanto está a transição uma oferta a partir do portal de publicação, um ID da subscrição tem de estar whitelisted. Mesma subscrição (há um nome de utilizador e palavra-passe associada) tem de ser utilizada para iniciar sessão para este portal para testar a oferta faseada.

## <a name="see-also"></a>Consulte também
- [Introdução: como publicar uma oferta para Azure Marketplace](marketplace-publishing-getting-started.md)
