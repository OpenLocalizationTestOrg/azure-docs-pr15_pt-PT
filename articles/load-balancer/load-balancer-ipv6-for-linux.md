<properties
    pageTitle="Configurar o DHCPv6 para Linux VMs | Microsoft Azure"
    description="Como configurar DHCPv6 para Linux VMs."
    services="load-balancer"
    documentationCenter="na"
    authors="sdwheeler"
    manager="carmonm"
    editor=""
    keywords="IPv6, Balanceador de carga azure, pilha dupla, endereço ip público, ipv6 nativo, mobile, iot"
/>
<tags
    ms.service="load-balancer"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="infrastructure-services"
    ms.date="09/14/2016"
    ms.author="sewhee"
/>

# <a name="configuring-dhcpv6-for-linux-vms"></a>Configurar o DHCPv6 para Linux VMs

Algumas das imagens de máquina virtual Linux no Azure Marketplace não possui DHCPv6 configurado, por predefinição. Para suportar IPv6, DHCPv6 tem de ser configurado no dentro da distribuição de Linux SO que está a utilizar. Diferentes Linux distribuições tem diferentes maneiras de configurar DHCPv6 uma vez que utilizam diferentes pacotes.

>[AZURE.NOTE] Foram pré-configurada com DHCPv6 recentes SUSE Linux e CoreOS imagens do Azure Marketplace. Não adicionais são necessárias alterações ao utilizar essas imagens.

Este documento descreve como ativar DHCPv6 para que o seu máquina de virtual Linux obtém um endereço IPv6.

>[AZURE.WARNING] Indevidamente edição de ficheiros de configuração de rede pode causar a perder o acesso de rede à sua VM. Recomendamos que teste as alterações à configuração nos sistemas de não sejam de produção. As instruções neste artigo foram testadas nas versões mais recentes das imagens Linux no Azure Marketplace. Consulte a documentação para a sua versão específica da Linux para obter informações mais detalhadas.

## <a name="ubuntu"></a>Ubuntu

1. Editar o ficheiro `/etc/dhcp/dhclient6.conf` e adicione a linha seguinte:

        timeout 10;

2. Edite a configuração de rede para a interface de eth0 com a configuração seguinte:

    * No **Ubuntu 12.04 e 14.04**, edite o ficheiro`/etc/network/interfaces.d/eth0.cfg`
    * No **Ubuntu 16.04**, edite o ficheiro`/etc/network/interfaces.d/50-cloud-init.cfg`

    ```bash
    iface eth0 inet6 auto
        up sleep 5
        up dhclient -1 -6 -cf /etc/dhcp/dhclient6.conf -lf /var/lib/dhcp/dhclient6.eth0.leases -v eth0 || true
    ```

3. Renove endereço IPv6:

    ```bash
    # sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="debian"></a>Debian

1. Editar o ficheiro `/etc/dhcp/dhclient6.conf` e adicione a linha seguinte:

        timeout 10;

2. Editar o ficheiro `/etc/network/interfaces` e adicione a configuração do seguinte:

        iface eth0 inet6 auto
            up sleep 5
            up dhclient -1 -6 -cf /etc/dhcp/dhclient6.conf -lf /var/lib/dhcp/dhclient6.eth0.leases -v eth0 || true

3. Renove endereço IPv6:

    ```bash
    # sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="rhel--centos--oracle-linux"></a>RHEL / CentOS / Oracle Linux

1. Editar o ficheiro `/etc/sysconfig/network` e adicione o parâmetro seguinte:

        NETWORKING_IPV6=yes

2. Editar o ficheiro `/etc/sysconfig/network-scripts/ifcfg-eth0` e adicionar os parâmetros de duas seguintes:

        IPV6INIT=yes
        DHCPV6C=yes

3. Renove endereço IPv6:

    ```bash
    # sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="sles-11--opensuse-13"></a>SLES 11 & openSUSE 13

Imagens SLES e openSUSE recentes no Azure tem sido pré-configurada com DHCPv6. Não adicionais são necessárias alterações ao utilizar essas imagens. Se tiver uma VM com base numa imagem SUSE personalizada ou mais antiga, utilize os seguintes passos:

1. Instalar o `dhcp-client` compactar, se for necessário:

    ```bash
    # sudo zypper install dhcp-client
    ```

2. Editar o ficheiro `/etc/sysconfig/network/ifcfg-eth0` e adicione o parâmetro seguinte:

        DHCLIENT6_MODE='managed'

3. Renove o endereço IPv6:

    ```bash
    # sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="sles-12-and-opensuse-leap"></a>SLES 12 e openSUSE bissexto

Imagens SLES e openSUSE recentes no Azure tem sido pré-configurada com DHCPv6. Não adicionais são necessárias alterações ao utilizar essas imagens. Se tiver uma VM com base numa imagem SUSE personalizada ou mais antiga, utilize os seguintes passos:

1. Editar o ficheiro `/etc/sysconfig/network/ifcfg-eth0` e substituir este parâmetro

        #BOOTPROTO='dhcp4'

    com o seguinte valor:

        BOOTPROTO='dhcp'

2. Adicionar o parâmetro seguinte para `/etc/sysconfig/network/ifcfg-eth0`:

        DHCLIENT6_MODE='managed'

3. Renove o endereço IPv6:

    ```bash
    # sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="coreos"></a>CoreOS

Imagens de CoreOS recentes no Azure tem sido pré-configurada com DHCPv6. Não adicionais são necessárias alterações ao utilizar essas imagens. Se tiver uma VM com base numa imagem CoreOS personalizada ou mais antiga, utilize os seguintes passos:

1. Editar o ficheiro.`/etc/systemd/network/10_dhcp.network`

        [Match]
        eth0

        [Network]
        DHCP=ipv6

2. Renove o endereço IPv6:

    ```bash
    # sudo systemctl restart systemd-networkd
    ```
