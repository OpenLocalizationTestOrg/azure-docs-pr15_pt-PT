<properties
   pageTitle="Gerir os registos DNS inverso para os seus serviços Azure utilizando o clip do Azure | Microsoft Azure"
   description="Como gerir os registos DNS inverso ou registos de PTR para serviços Azure utilizando o clip do Azure no Gestor de recursos"
   services="DNS"
   documentationCenter="na"
   authors="s-malone"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"
/>
<tags
   ms.service="DNS"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/28/2016"
   ms.author="smalone" />

# <a name="how-to-manage-reverse-dns-records-for-your-azure-services-using-the-azure-cli"></a>Como gerir inverso registos DNS para os seus serviços Azure utilizando o clip do Azure

[AZURE.INCLUDE [DNS-reverse-dns-record-operations-arm-selectors-include.md](../../includes/dns-reverse-dns-record-operations-arm-selectors-include.md)]
<BR>
[AZURE.INCLUDE [DNS-reverse-dns-record-operations-intro-include.md](../../includes/dns-reverse-dns-record-operations-intro-include.md)]
<BR>
[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)][modelo de implementação clássica](dns-reverse-dns-record-operations-classic-ps.md).

## <a name="validation-of-reverse-dns-records"></a>Validação de registos DNS inversa
Para se certificar que de terceiros não é possível criar registos DNS inversos mapeamento aos seus domínios DNS, o Azure só permite a criação de um registo DNS inversa onde uma das seguintes afirmações é verdadeira:

- "ReverseFqdn" é igual a "Fqdn" para o recurso de endereço IP público para a qual foi especificada ou o "Fqdn" para qualquer endereço IP público dentro da mesma subscrição por exemplo, "ReverseFqdn" é "contosoapp1.northus.cloudapp.azure.com.".

- "ReverseFqdn" reencaminhar é resolvido para o nome ou IP do endereço IP público para a qual tenha sido especificado ou a qualquer endereço IP público "Fqdn" ou IP dentro da mesma subscrição por exemplo, "ReverseFqdn" é "app1.contoso.com." qual é um alias CName para "contosoapp1.northus.cloudapp.azure.com."

Verificações de validação só são executadas quando a propriedade inversa de DNS para um endereço IP público é definida ou modificada. Não é executada validação re periódica.

## <a name="add-reverse-dns-to-existing-public-ip-addresses"></a>Adicionar DNS inversa para os endereços de IP público existentes
Pode adicionar inversa de DNS para um endereço IP público existente utilizando o conjunto de ip público azure rede:

    azure network public-ip set -n PublicIp -g NRP-DemoRG-PS -f contosoapp1.westus.cloudapp.azure.com.

Se pretender adicionar inversa de DNS para um endereço de IP público existente que já não tem um nome de DNS, também tem de especificar um nome de DNS. Pode adicionar alcançar isto utilizando o conjunto de ip público azure rede:

    azure network public-ip set -n PublicIp -g NRP-DemoRG-PS -d contosoapp1 -f contosoapp1.westus.cloudapp.azure.com.

## <a name="create-a-public-ip-address-with-reverse-dns"></a>Criar um endereço IP público com DNS inversa
Pode adicionar um novo endereço IP público com a propriedade DNS inversa especificada utilizando o público-ip da rede azure criar:

    azure network public-ip create -n PublicIp3 -g NRP-DemoRG-PS -l westus -d contosoapp3 -f contosoapp3.westus.cloudapp.azure.com.

## <a name="view-reverse-dns-for-existing-public-ip-addresses"></a>Vista DNS inversa para os endereços de IP público existente
É possível visualizar o valor configurado para um endereço IP público existente utilizando a azure rede ip público voltar a mostrar:

    azure network public-ip show -n PublicIp3 -g NRP-DemoRG-PS

## <a name="remove-reverse-dns-from-existing-public-ip-addresses"></a>Remover DNS inversa de endereços IP público existentes
Pode remover uma propriedade DNS inversa de um endereço IP público utilizando rede azure ip público conjunto. Isto é feito ao definir o valor da propriedade ReverseFqdn em branco:

    azure network public-ip set -n PublicIp3 -g NRP-DemoRG-PS –f “”

[AZURE.INCLUDE [FAQ1](../../includes/dns-reverse-dns-record-operations-faq-host-own-arpa-zone-include.md)]

[AZURE.INCLUDE [FAQ2](../../includes/dns-reverse-dns-record-operations-faq-arm-include.md)]
