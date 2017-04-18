<properties 
   pageTitle="Especificar definições de DNS num ficheiro de configuração de rede virtual | Microsoft Azure"
   description="Como alterar as definições de servidor DNS numa rede virtual com um ficheiro de configuração de rede virtual no modelo de implementação clássico"
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor="tysonn" 
   tags="azure-service-management" />
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/23/2016"
   ms.author="jdial" /> 


# <a name="specifying-dns-settings-in-a-virtual-network-configuration-file"></a>Especificar definições de DNS num ficheiro de configuração de rede virtual

Um ficheiro de configuração de rede tem dois elementos que pode utilizar para especificar as definições do sistema de nomes de domínio (DNS): **DnsServers** e **DnsServerRef**. Pode adicionar uma lista dos servidores DNS ao especificar os respetivos endereços IP e nomes de referência para o elemento **DnsServers** . Em seguida, pode utilizar um elemento **DnsServerRef** para especificar as entradas do servidor DNS a partir do elemento DnsServers são utilizadas para sites de rede diferente na rede da sua virtual.

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]Este artigo abrange o modelo de implementação clássica.

O ficheiro de configuração de rede pode conter os seguintes elementos. O título de cada elemento está ligado a uma página que fornece informações adicionais sobre as definições de valor de elemento.

>[AZURE.IMPORTANT] Para obter informações sobre como configurar o ficheiro de configuração de rede, consulte o artigo [configurar um rede Virtual utilizando um ficheiro de configuração de rede](virtual-networks-using-network-configuration-file.md). Para obter informações sobre cada elemento incluído no ficheiro de configuração de rede, consulte o artigo [Azure Virtual esquema de configuração de rede](https://msdn.microsoft.com/library/azure/jj157100.aspx).

[Elemento de DNS](http://go.microsoft.com/fwlink/?LinkId=248093)

    <Dns>
      <DnsServers>
        <DnsServer name="ID1" IPAddress="IPAddress1" />
        <DnsServer name="ID2" IPAddress="IPAddress2" />
        <DnsServer name="ID3" IPAddress="IPAddress3" />
      </DnsServers>
    </Dns>

>[AZURE.WARNING] O atributo **nome** no elemento de **servidor de DNS** é utilizado apenas como uma referência para o elemento de **DnsServerRef** . Este não representar o nome do anfitrião do servidor de DNS. Cada valor do atributo **servidor de DNS** tem de ser exclusivo em toda a subscrição do Microsoft Azure

[Elemento de Sites de rede virtual](http://go.microsoft.com/fwlink/?LinkId=248093)

    <DnsServersRef>
      <DnsServerRef name="ID1" />
      <DnsServerRef name="ID2" />
      <DnsServerRef name="ID3" />
    </DnsServersRef>

>[AZURE.NOTE] Para poder especificar esta definição para o elemento Virtual Sites da rede, tem de ser anteriormente definido no elemento de DNS. O *nome* de DnsServerRef no elemento de Sites de rede virtuais têm de referenciar um valor de nome especificado no elemento de DNS para o *nome*do servidor de DNS.

## <a name="next-steps"></a>Próximos passos

- Compreenda o [esquema de configuração de rede Virtual Azure](http://go.microsoft.com/fwlink/?LinkId=248093).
- Compreenda o [esquema de configuração do serviço Azure](https://msdn.microsoft.com/library/windowsazure/ee758710).
- [Configurar uma rede virtual utilizar ficheiros de configuração de rede](virtual-networks-using-network-configuration-file.md).
