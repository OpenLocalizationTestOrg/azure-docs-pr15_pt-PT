<properties
   pageTitle="Gerir os registos DNS inverso para os seus serviços de Azure (clássico) através do PowerShell | Microsoft Azure"
   description="Como gerir os registos DNS inverso ou registos PTR serviços Azure através do PowerShell no modelo clássico de implementação do. "
   services="DNS"
   documentationCenter="na"
   authors="s-malone"
   manager="carmonm"
   editor=""
   tags="azure-service-management"
/>
<tags
   ms.service="DNS"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/28/2016"
   ms.author="smalone" />

# <a name="how-to-manage-reverse-dns-records-for-your-azure-services-classic-using-azure-powershell"></a>Como gerir inverso registos DNS para os seus serviços Azure (clássico) através do PowerShell do Azure

[AZURE.INCLUDE [dns-reverse-dns-record-operations-arm-selectors-include.md](../../includes/dns-reverse-dns-record-operations-arm-selectors-include.md)]
<BR>
[AZURE.INCLUDE [DNS-reverse-dns-record-operations-intro-include.md](../../includes/dns-reverse-dns-record-operations-intro-include.md)]
<BR>
[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-classic-include.md)]Obter informações sobre como [executar estes passos utilizando o modelo de Gestor de recursos](dns-reverse-dns-record-operations-ps.md).

## <a name="validation-of-reverse-dns-records"></a>Validação de registos DNS inversa
Para se certificar que de terceiros não é possível criar registos DNS inversos mapeamento aos seus domínios DNS, o Azure só permite a criação de um registo DNS inversa onde uma das seguintes afirmações é verdadeira:

- O inverso FQDN de DNS é o nome do serviço em nuvem para a qual foi especificado, ou qualquer nome de serviço na nuvem na mesma subscrição, por exemplo, é o DNS inversa "contosoapp1.cloudapp.net.".
- Reencaminhar o inversa FQDN DNS é resolvido para o nome ou IP do serviço na nuvem para que tenha sido especificado ou a qualquer nome de serviço na nuvem ou IP dentro da mesma subscrição por exemplo, "app1.contoso.com." é o DNS inversa qual é um alias de CName para contosoapp1.cloudapp.net.

Verificações de validação só são executadas quando a propriedade inversa de DNS para um serviço na nuvem é definida ou modificada. Não é executada validação re periódica.

## <a name="add-reverse-dns-to-existing-cloud-services"></a>Adicionar DNS inversa aos serviços em nuvem existente
Pode adicionar um registo DNS inverso para um serviço de nuvem existente utilizando o cmdlet "Conjunto AzureService":

    PS C:\> Set-AzureService –ServiceName “contosoapp1” –Description “App1 with Reverse DNS” –ReverseDnsFqdn “contosoapp1.cloudapp.net.”

## <a name="create-a-cloud-service-with-reverse-dns"></a>Criar um serviço na nuvem com DNS inversa
Pode adicionar um novo serviço de nuvem com a propriedade DNS inversa especificada utilizando o cmdlet "Conjunto AzureService":

    PS C:\> New-AzureService –ServiceName “contosoapp1” –Location “West US” –Description “App1 with Reverse DNS” –ReverseDnsFqdn “contosoapp1.cloudapp.net.”

## <a name="view-reverse-dns-for-existing-cloud-services"></a>Vista DNS inverso dos serviços em nuvem existente
É possível visualizar o valor configurado para um serviço de nuvem existente utilizando o cmdlet "Get-AzureService":

    PS C:\> Get-AzureService "contosoapp1"

## <a name="remove-reverse-dns-from-existing-cloud-services"></a>Remover inversa DNS dos serviços em nuvem existente
Pode remover uma propriedade DNS inversa de um serviço de nuvem existente utilizando o cmdlet "Conjunto AzureService". Isto é feito ao definir o valor da propriedade DNS inverso em branco:

    PS C:\> Set-AzureService –ServiceName “contosoapp1” –Description “App1 with Reverse DNS” –ReverseDnsFqdn “”

[AZURE.INCLUDE [FAQ1](../../includes/dns-reverse-dns-record-operations-faq-host-own-arpa-zone-include.md)]

[AZURE.INCLUDE [FAQ2](../../includes/dns-reverse-dns-record-operations-faq-asm-include.md)]
