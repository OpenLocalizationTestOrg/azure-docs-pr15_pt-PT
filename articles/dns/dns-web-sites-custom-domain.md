<properties
   pageTitle="Criar registos DNS personalizados para uma aplicação web | Microsoft Azure  "
   description="Como criar registos DNS para o web app utilizando o Azure DNS de domínio personalizado."
   services="dns"
   documentationCenter="na"
   authors="sdwheeler"
   manager="carmonm"
   editor=""/>

<tags
   ms.service="dns"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/16/2016"
   ms.author="sewhee"/>

# <a name="create-dns-records-for-a-web-app-in-a-custom-domain"></a>Criar registos DNS para uma aplicação web num domínio personalizado

Pode utilizar o Azure DNS para um domínio personalizado para as suas aplicações web do anfitrião. Por exemplo, está a criar uma aplicação web do Azure e pretender que os utilizadores a aceder aos mesmos por um utilizar contoso.com, ou www.contoso.com como um FQDN.

Para executar esta tarefa, tem de criar os dois registos:

- Um registo de raiz "A" apontando para contoso.com
- Um registo "CNAME" para o nome do www que aponta para o registo

Tenha em atenção que se criar um registo a para uma aplicação web no Azure, o registo têm de ser manualmente atualizados se o subjacente endereço IP para que as alterações de aplicação web.

## <a name="before-you-begin"></a>Antes de começar

Antes de começar, tem primeiro de criar uma zona de DNS no Azure DNS e delegar a zona na sua entidade de registo ao Azure DNS.

1. Para criar uma zona de DNS, siga os passos no [artigo criar uma zona de DNS](dns-getstarted-create-dnszone.md).
2. Para delegar DNS para o Azure DNS, siga os passos na [delegação de domínio DNS](dns-domain-delegation.md).

Depois de criar uma zona e delegá-lo ao Azure DNS, em seguida, pode criar registos do seu domínio personalizado.


## <a name="1-create-an-a-record-for-your-custom-domain"></a>1. Crie um registo a para o seu domínio personalizado

Um registo é utilizado para mapear um nome para o seu endereço IP. No seguinte exemplo estamos atribuir @ como um registo para um endereço de IPv4:

### <a name="step-1"></a>Passo 1

Criar um registo e atribuir a uma variável $rs

    $rs= New-AzureRMDnsRecordSet -Name "@" -RecordType "A" -ZoneName "contoso.com" -ResourceGroupName "MyAzureResourceGroup" -Ttl 600

### <a name="step-2"></a>Passo 2

Adicionar o valor de IPv4 para o conjunto de registos criado anteriormente "@" utilizando a variável de $rs atribuída. O valor de IPv4 atribuído será o endereço IP para a sua aplicação web.

Para localizar o endereço IP para uma aplicação web, siga os passos em [configurar um nome de domínio personalizado no Azure aplicação de serviço](../web-sites-custom-domain-name.md#Find-the-virtual-IP-address).

    Add-AzureRMDnsRecordConfig -RecordSet $rs -Ipv4Address <your web app IP address>

### <a name="step-3"></a>Passo 3

Consolide as alterações para o conjunto de registos. Utilizar `Set-AzureRMDnsRecordSet` para carregar as alterações para o registo definido para Azure DNS:

    Set-AzureRMDnsRecordSet -RecordSet $rs

## <a name="2-create-a-cname-record-for-your-custom-domain"></a>2. Crie um registo CNAME para o seu domínio personalizado

Se o seu domínio já está a ser gerido por Azure DNS (consulte [delegação de domínio DNS](dns-domain-delegation.md), pode utilizar o seguinte exemplo para criar um registo CNAME para contoso.azurewebsites.net.

### <a name="step-1"></a>Passo 1

Abra o PowerShell e criar um novo conjunto de registo CNAME e atribuir a uma variável $rs. Este exemplo irá criar um tipo de conjunto de registos CNAME com uma "hora para a live" de 600 segundos na zona DNS com o nome "contoso.com".

    $rs = New-AzureRMDnsRecordSet -ZoneName contoso.com -ResourceGroupName myresourcegroup -Name "www" -RecordType "CNAME" -Ttl 600

    Name              : www
    ZoneName          : contoso.com
    ResourceGroupName : myresourcegroup
    Ttl               : 600
    Etag              : 8baceeb9-4c2c-4608-a22c-229923ee1856
    RecordType        : CNAME
    Records           : {}
    Tags              : {}


### <a name="step-2"></a>Passo 2

Quando o conjunto de registos CNAME estiver criado, tem de criar um valor de alias que irá apontar para a aplicação web.

Utilizando a variável de anteriormente atribuída "$rs" pode utilizar o comando do PowerShell abaixo para criar o alias para o contoso.azurewebsites.net de aplicação web.

    Add-AzureRMDnsRecordConfig -RecordSet $rs -Cname "contoso.azurewebsites.net"

    Name              : www
    ZoneName          : contoso.com
    ResourceGroupName : myresourcegroup
    Ttl               : 600
    Etag              : 8baceeb9-4c2c-4608-a22c-229923ee185
    RecordType        : CNAME
    Records           : {contoso.azurewebsites.net}
    Tags              : {}

### <a name="step-3"></a>Passo 3

Consolide as alterações utilizando o `Set-AzureRMDnsRecordSet` cmdlet:

    Set-AzureRMDnsRecordSet -RecordSet $rs

Pode validar o registo foi criado corretamente consultando "www.contoso.com" utilizando o nslookup, conforme apresentado abaixo:

    PS C:\> nslookup
    Default Server:  Default
    Address:  192.168.0.1

    > www.contoso.com
    Server:  default server
    Address:  192.168.0.1

    Non-authoritative answer:
    Name:    <instance of web app service>.cloudapp.net
    Address:  <ip of web app service>
    Aliases:  www.contoso.com
    contoso.azurewebsites.net
    <instance of web app service>.vip.azurewebsites.windows.net

## <a name="create-an-awverify-record-for-web-apps"></a>Criar um registo de "awverify" para web apps


Se decidir utilizar um registo a para a sua aplicação web, terá de percorrer um processo de verificação para garantir que é proprietário do domínio personalizado. Este passo de verificação é feito através da criação de um registo CNAME especial denominado "awverify". Esta secção aplica-se para uma só registos.


### <a name="step-1"></a>Passo 1

Crie o registo de "awverify". Exemplo abaixo, irá criar o registo de "aweverify" para contoso.com confirmar que é o proprietário do domínio personalizado.

    $rs = New-AzureRMDnsRecordSet -ZoneName contoso.com -ResourceGroupName myresourcegroup -Name "awverify" -RecordType "CNAME" -Ttl 600

    Name              : awverify
    ZoneName          : contoso.com
    ResourceGroupName : myresourcegroup
    Ttl               : 600
    Etag              : 8baceeb9-4c2c-4608-a22c-229923ee1856
    RecordType        : CNAME
    Records           : {}
    Tags              : {}


### <a name="step-2"></a>Passo 2

Depois do conjunto de registos "awverify" é criada, atribua o registo CNAME definir alias. No exemplo abaixo, podemos vai atribuir o registo CNAMe alias definido para awverify.contoso.azurewebsites.net.

    Add-AzureRMDnsRecordConfig -RecordSet $rs -Cname "awverify.contoso.azurewebsites.net"

    Name              : awverify
    ZoneName          : contoso.com
    ResourceGroupName : myresourcegroup
    Ttl               : 600
    Etag              : 8baceeb9-4c2c-4608-a22c-229923ee185
    RecordType        : CNAME
    Records           : {awverify.contoso.azurewebsites.net}
    Tags              : {}

### <a name="step-3"></a>Passo 3

Consolide as alterações utilizando o `Set-AzureRMDnsRecordSet cmdlet`, conforme mostrado no comando abaixo.

    Set-AzureRMDnsRecordSet -RecordSet $rs



## <a name="next-steps"></a>Próximos passos

Siga os passos na [configuração de um nome de domínio personalizado para a aplicação de serviço](../app-service-web/web-sites-custom-domain-name.md) para configurar a sua aplicação web para utilizar um domínio personalizado.








