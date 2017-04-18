<properties
   pageTitle="Utilizando o DNS dinâmico para registar nomes de anfitriões"
   description="Esta página dá detalhes sobre como configurar o DNS dinâmico para registar a nomes de anfitriões no seus próprio servidores DNS."
   services="dns"
   documentationCenter="na"
   authors="GarethBradshawMSFT"
   manager="carmonm"
   editor="" />
<tags
   ms.service="dns"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/31/2016"
   ms.author="sewhee" />

# <a name="using-dynamic-dns-to-register-hostnames-in-your-own-dns-server"></a>Utilizando o DNS dinâmico para registar a nomes de anfitriões no seu próprio servidor DNS

[Azure fornece resolução de nomes](virtual-networks-name-resolution-for-vms-and-role-instances.md) para máquinas virtuais (VMs) e instâncias de função. No entanto, quando precisa de aceder a resolução de nomes para além das fornecidas pelo Azure, pode fornecer os seus servidores DNS. Isto resultará power para personalizar a sua solução DNS para se adequar às necessidades específicas. Por exemplo, poderá ter acesso aos recursos no local através do seu controlador de domínio do Active Directory.

Quando os servidores DNS personalizados são alojados como Azure VMs pode reencaminhar hostname consultas para o mesmo vnet para Azure para resolver nomes de anfitriões. Se não pretender utilizar esta rota, pode registar os nomes de anfitriões VM no seu servidor DNS utilizando o DNS dinâmico.  Azure não tem a capacidade (por exemplo, credenciais) para criar diretamente registos no seu servidores DNS, para que disposições alternativas frequentemente são necessários. Eis alguns cenários comuns com alternativas.

## <a name="windows-clients"></a>Clientes do Windows

Os clientes Windows não domínio associadas tentam de atualizações de DNS dinâmico (DDNS) não segura quando estes arranque ou quando altera o endereço IP do respetivo. O nome DNS é o nome do anfitrião plus o sufixo DNS. Azure deixa o sufixo DNS em branco, mas pode defini-lo na VM, através da [IU](https://technet.microsoft.com/library/cc794784.aspx) ou [utilizando a automatização](https://social.technet.microsoft.com/forums/windowsserver/3720415a-6a9a-4bca-aa2a-6df58a1a47d7/change-primary-dns-suffix).

Os clientes Windows façam parte de um domínio registam os respetivos endereços de IP com o controlador de domínio ao utilizar DNS dinâmico seguro. O processo de associação de domínio define o sufixo DNS no cliente e cria e mantém a relação de fidedignidade.

## <a name="linux-clients"></a>Linux clientes

Os clientes Linux geralmente não registam-se automaticamente com o servidor DNS no arranque, eles partem do princípio de que o servidor DHCP. Os servidores do Azure DHCP não possui a capacidade ou credenciais para registar a registos no seu servidor de DNS.  Pode utilizar uma ferramenta denominada *nsupdate*, que está incluído no pacote vincular, para enviar atualizações de DNS dinâmicos. Porque é padronizado o protocolo de DNS dinâmico, pode utilizar *nsupdate* mesmo quando não estiver a utilizar vincular no servidor de DNS.

Pode utilizar os hooks que são fornecidos pelo cliente DHCP para criar e manter a entrada hostname no servidor de DNS. Durante o ciclo de DHCP, o cliente executa os scripts num */etc/dhcp/dhclient-exit-hooks.d/*. Isto pode ser utilizado para registar os novos endereços IP utilizando *nsupdate*. Por exemplo:

        #!/bin/sh
        requireddomain=mydomain.local

        # only execute on the primary nic
        if [ "$interface" != "eth0" ]
        then
            return
        fi

        # when we have a new IP, perform nsupdate
        if [ "$reason" = BOUND ] || [ "$reason" = RENEW ] ||
           [ "$reason" = REBIND ] || [ "$reason" = REBOOT ]
        then
            host=`hostname`
            nsupdatecmds=/var/tmp/nsupdatecmds
              echo "update delete $host.$requireddomain a" > $nsupdatecmds
              echo "update add $host.$requireddomain 3600 a $new_ip_address" >> $nsupdatecmds
              echo "send" >> $nsupdatecmds

              nsupdate $nsupdatecmds
        fi

        #done
        exit 0;

Também pode utilizar o comando *nsupdate* para executar atualizações de DNS dinâmico seguras. Por exemplo, quando estiver a utilizar um servidor DNS vincular, um par de chaves públicas privadas é [gerado](http://linux.yyz.us/nsupdate/).  O servidor DNS está [configurado](http://linux.yyz.us/dns/ddns-server.html) com a peça da chave pública, de modo que possa verificar a assinatura no pedido de. Tem de utilizar a opção *-k* para fornecer que o par de chave para *nsupdate* para que o DNS dinâmico actualizar o pedido de ser assinados.

Quando estiver a utilizar um servidor de DNS do Windows, pode utilizar a autenticação Kerberos com o parâmetro *-g* no *nsupdate* (não disponível na versão do Windows do *nsupdate*). Para executar esta tarefa, utilize *kinit* para carregar as credenciais (por exemplo, a partir de um [ficheiro de keytab](http://www.itadmintools.com/2011/07/creating-kerberos-keytab-files.html)). Em seguida, *nsupdate g* vai selecionar as credenciais da cache de.

Se for necessário, pode adicionar um sufixo de pesquisa DNS para o seu VMs. O sufixo de DNS é especificado no ficheiro */etc/resolv.conf* . A maior parte dos Linux distros gerir automaticamente o conteúdo deste ficheiro, por isso, normalmente não é possível editá-lo. No entanto, pode substituir o sufixo utilizando *substitui o* comando o cliente DHCP. Para fazer isto, no */etc/dhcp/dhclient.conf*, adicione:

        supersede domain-name <required-dns-suffix>;

