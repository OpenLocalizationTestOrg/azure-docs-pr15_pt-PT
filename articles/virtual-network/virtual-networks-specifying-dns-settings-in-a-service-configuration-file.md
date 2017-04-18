<properties 
   pageTitle="Especificar definições de DNS num ficheiro de configuração do serviço | Microsoft Azure"
   description="especificar definições de DNS personalizadas utilizando o ficheiro de configuração do serviço de rede virtual"
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor="tysonn" />
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/24/2016"
   ms.author="jdial" />

# <a name="specifying-dns-settings-in-a-service-configuration-file"></a>Especificar definições de DNS num ficheiro de configuração do serviço

## <a name="dns-elements"></a>Elementos DNS

Um ficheiro de configuração do serviço poderá conter um elemento de DnsServers com uma lista de endereços IPv4 para os servidores de sistema de nomes de domínio (DNS) que irá utilizar o serviço. Definições no ficheiro de configuração de serviço precedência sobre as definições no ficheiro de configuração de rede. Para mais informações, consulte o [Esquema de configuração do Azure Service (.cscfg ficheiro)](https://msdn.microsoft.com/library/azure/ee758710.aspx).

**Elemento NetworkConfiguration**

      <DnsServers>
        <DnsServer name="ID1" IPAddress="IPAddress1" />
        <DnsServer name="ID2" IPAddress="IPAddress2" />
        <DnsServer name="ID3" IPAddress="IPAddress3" />
      </DnsServers>

>[AZURE.WARNING] O atributo **nome** no elemento de **servidor de DNS** é utilizado apenas como um nome de referência. Este não representar o nome do anfitrião do servidor de DNS. Cada valor do atributo **servidor de DNS** tem de ser exclusivo em toda a subscrição do Microsoft Azure.

## <a name="see-also"></a>Consulte também

[Esquema de configuração do serviço Azure (.cscfg)](https://msdn.microsoft.com/library/windowsazure/ee758710)

[Esquema de configuração de rede Virtual Azure](http://go.microsoft.com/fwlink/?LinkId=248093)

[Configurar uma rede Virtual utilizar ficheiros de configuração de rede](http://go.microsoft.com/fwlink/?LinkId=248094)

[Acerca das definições de rede Virtual no Portal de gestão](http://go.microsoft.com/fwlink/?LinkId=248092)

