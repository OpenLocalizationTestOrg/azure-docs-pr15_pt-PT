<properties
    pageTitle="Descrição geral do IPv6 para Balanceador de carga Azure | Microsoft Azure"
    description="Noções sobre o suporte do IPv6 do Balanceador de carga Azure e VMs balanceamento de carga."
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

# <a name="overview-of-ipv6-for-azure-load-balancer"></a>Descrição geral do IPv6 para Balanceador de carga Azure

Podem ser implementadas balanceadores de carga de acesso à Internet com um endereço IPv6. Para além de conectividade de IPv4, deste modo, as seguintes capacidades:

* Conectividade nativa de ponto a ponto IPv6 entre os clientes da Internet públicos e máquinas virtuais do Azure (VMs) através do Balanceador de carga.
* Conectividade nativa de ponto a ponto IPv6 saída entre VMs e clientes com capacidade de Internet IPv6 públicos.

A imagem seguinte ilustra a funcionalidade do IPv6 para Balanceador de carga Azure.

![Balanceador de carga Azure com o IPv6](./media/load-balancer-ipv6-overview/load-balancer-ipv6.png)

Depois de implementado, um cliente IPv4 ou Internet com capacidade de IPv6 pode comunicar com endereços IPv4 ou IPv6 pública (ou nomes de anfitriões) do Balanceador de carga de acesso à Internet do Azure. O Balanceador de carga encaminha os pacotes de IPv6 para os endereços do IPv6 privados de VMs com endereços de rede tradução (NAT). O cliente da Internet do IPv6 não é possível comunicar diretamente com o endereço IPv6 das VMs.

## <a name="features"></a>Funcionalidades

Suporte do IPv6 nativo para VMs implementado através do Gestor de recursos do Azure fornece:

1. Serviços de balanceamento de carga de IPv6 para clientes do IPv6 na Internet
2. Pontos finais de IPv6 nativas e IPv4 VMs ("duas empilhadas")
3. Entrada e saída iniciados por ligações IPv6 nativas
4. Protocolos suportados como TCP, UDP e HTTP (S) ativar uma gama completa de arquiteturas de serviços

## <a name="benefits"></a>Benefícios

Esta funcionalidade permite as seguintes vantagens de chaves:

* Reunir regulamentos de administração pública que exijam que novas aplicações estejam acessíveis para os clientes apenas de IPv6
* Ativar mobile e Internet de programadores coisas (IOT) a utilizar máquinas virtuais do Azure (IPv4 + IPv6) empilhadas duplo para resolver o mobile crescente & IOT mercados

## <a name="details-and-limitations"></a>Detalhes e limitações

Detalhes

* O serviço de Azure DNS contém IPv4 A e IPv6 AAAA registos de nome e responde com ambos os registos do Balanceador de carga. O cliente escolhe qual o endereço (IPv4 ou IPv6) a comunicar com.
* Quando uma VM inicia uma ligação para um dispositivo ligado à Internet IPv6 público, origem a VM endereço IPv6 é endereço de rede traduzido (NAT) para o endereço IPv6 público do Balanceador de carga.
* VMs a executar o sistema operativo Linux tem de ser configurados para receber um endereço IP do IPv6 através de DHCP. Muitas das imagens Linux na Galeria de Azure já estão configuradas para suportar IPv6 sem modificação. Para obter mais informações, consulte o artigo [Configurar DHCPv6 para Linux VMs](load-balancer-ipv6-for-linux.md)
* Se optar por utilizar uma sonda de estado de funcionamento com o seu Balanceador de carga, crie uma sonda IPv4 e utilizá-la com os pontos finais de IPv4 e o IPv6. Se o serviço no seu VM vai para baixo, a IPv4 e o IPv6 os pontos finais são tidos terminar rotação.

Limitações

* Não pode adicionar regras de balanceamento de carga de IPv6 no portal do Azure. As regras só podem ser criadas através do modelo, clip, PowerShell.
* Não pode atualizar VMs existentes para utilizar endereços IPv6. Tem de implementar VMs novos.
* Um único endereço IPv6 pode ser atribuído a uma interface única rede em cada VM.
* Os endereços do IPv6 públicos não podem ser atribuídos a uma VM. Só pode ser atribuídos a um balanceador de carga.
* Não é possível configurar a pesquisa inversa de DNS para os seus endereços IPv6 públicos.
* VMs com os endereços do IPv6 não podem ser membros de um serviço de nuvem Azure. Que podem ser ligados a uma rede Virtual do Azure (VNet) e comunicar com os outros sobre os respetivos endereços IPv4.
* Endereços IPv6 privado podem ser implementados no VMs individuais num grupo de recursos, mas não podem ser implementados num grupo de recursos através de conjuntos de escala.
* Não consegue ligar Azure VMs através do IPv6 para outros VMs outros serviços do Azure dispositivos ou no local. Só pode comunicar com o Balanceador de carga Azure através do IPv6. No entanto, pode comunicar com estes outros recursos utilizando IPv4.
* Proteção de grupo de segurança de rede (NSG) para IPv4 é suportada no implementações pilha dupla (IPv4 + IPv6). NSGs não são aplicadas aos pontos finais IPv6.
* O ponto final do IPv6 na VM não é exposto diretamente à internet. É atrás de um balanceador de carga. Apenas as portas especificadas nas regras de Balanceador de carga são acessíveis através do IPv6.
* Alterar o parâmetro IdleTimeout para o IPv6 é **actualmente não suportadas**. A predefinição é quatro minutos.

## <a name="next-steps"></a>Próximos passos

Saiba como implementar um balanceador de carga com o IPv6.

* [Disponibilidade do IPv6 por região](https://go.microsoft.com/fwlink/?linkid=828357)
* [Implementar um balanceador de carga com o IPv6 através de um modelo](load-balancer-ipv6-internet-template.md)
* [Implementar um balanceador de carga com o IPv6 através do PowerShell do Azure](load-balancer-ipv6-internet-ps.md)
* [Implementar um balanceador de carga com o IPv6 utilizando o clip do Azure](load-balancer-ipv6-internet-cli.md)
