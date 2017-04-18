<properties 
   pageTitle="Sobre os dispositivos de VPN para ligações de Site para o Site VPN Gateway para redes virtuais Azure | Microsoft Azure"
   description="Este artigo aborda dispositivos VPN e parâmetros IPsec para ligações de S2S VPN Gateway e contém ligações para instruções de configuração e exemplos."
   services="vpn-gateway"
   documentationCenter="na"
   authors="yushwang"
   manager="rossort"
   editor=""
  tags="azure-resource-manager, azure-service-management"/>
<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/13/2016"
   ms.author="yushwang;cherylmc" />

# <a name="about-vpn-devices-for-site-to-site-vpn-gateway-connections"></a>Sobre os dispositivos VPN para ligações de Site para o Site VPN Gateway

É necessário um dispositivo VPN para configurar uma ligação VPN do Site para Site (S2S). Ligações de site para o Site podem ser utilizadas para criar uma solução híbrido ou sempre que pretender uma ligação segura entre a sua rede no local e a sua rede virtual. Este artigo aborda compatível com dispositivos VPN e parâmetros de configuração. 

>[AZURE.NOTE] Quando configurar uma ligação de Site para o Site, um endereço de IPv4 IP público é necessário para o seu dispositivo VPN.                                                                                                                                                                               

Se o seu dispositivo não aparecer na tabela [validado VPN dispositivos](#devicetable) , consulte a secção de [dispositivos validado não VPN](#additionaldevices) deste artigo. É possível que o seu dispositivo ainda pode funcionar com o Azure. Para obter suporte de dispositivo VPN, contacte o fabricante do dispositivo.

**Itens a tenha em atenção quando vê as tabelas:**

- Foi uma alteração de terminologia para o encaminhamento de estático e dinâmico. Provavelmente encontrará que ambos os termos. Não existe nenhuma alteração à funcionalidade, apenas os nomes estão a alterar.
    - Encaminhamento estático = PolicyBased
    - Encaminhamento dinâmico = RouteBased
- Especificações para o gateway de alto desempenho VPN e gateway RouteBased VPN são os mesmos salvo indicação em contrário. Por exemplo, os dispositivos VPN validados que são compatíveis com gateways RouteBased VPN também são compatíveis com o gateway Azure alto desempenho VPN. 


## <a name="devicetable"></a>Dispositivos VPN validados 

Vamos validadas um conjunto de dispositivos VPN padrão em parceria com fornecedores de dispositivo. Todos os dispositivos as famílias de tipos de dispositivo contidas na seguinte lista devem trabalhar com gateways Azure VPN. Consulte o artigo [Sobre o Gateway de VPN](vpn-gateway-about-vpngateways.md) para verificar o tipo de gateway tem de criar para a solução que pretende configurar. 

Para ajudar a configurar o seu dispositivo VPN, consulte as ligações que correspondem aos família dispositivo apropriado. Para obter suporte de dispositivo VPN, contacte o fabricante do dispositivo.



| **Fornecedor**                      | **Família do dispositivo**                                        | **Versão do SO mínimo**                             | **PolicyBased**                                                                                                                                                                                                             | **RouteBased**                                                                                                                                                                    |
|---------------------------------|----------------------------------------------------------|----------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Allied Telesis                  | AR série VPN Routers                                    | 2.9.2                                              | Brevemente                                                                                                                                                                                                                                          | Não compatível                                                                                                                                                                                               |
| Barracuda redes, Inc.        | Barracuda NextGen Firewall F-série             | PolicyBased: 5.4.3, RouteBased: 6.2.0  | [Instruções de configuração](https://techlib.barracuda.com/NGF/AzurePolicyBasedVPNGW) | [Instruções de configuração](https://techlib.barracuda.com/NGF/AzureRouteBasedVPNGW)                                                                                                                                                                                              |
| Barracuda redes, Inc.        |  Série de NextGen Firewall X barracuda                 | Barracuda da Firewall 6.5 | [Barracuda Firewall](https://techlib.barracuda.com/BFW/ConfigAzureVPNGateway) | Não compatível                                                                                                                                                                                               |
| Bordado florido                         | Vyatta 5400 vRouter                                      | Das versões de 6.6R3 virtual Router DG                            | [Instruções de configuração](http://www1.brocade.com/downloads/documents/html_product_manuals/vyatta/vyatta_5400_manual/wwhelp/wwhimpl/js/html/wwhelp.htm#href=VPN_Site-to-Site%20IPsec%20VPN/Preface.1.1.html)                                       | Não compatível                                                                                                                                                                                               |
| Ponto de verificação                     | Gateway de segurança                                         | R75.40, R75.40VS                                     | [Instruções de configuração](https://supportcenter.checkpoint.com/supportcenter/portal?eventSubmit_doGoviewsolutiondetails=&solutionid=sk101275)                                         | [Instruções de configuração](https://supportcenter.checkpoint.com/supportcenter/portal?eventSubmit_doGoviewsolutiondetails=&solutionid=sk101275) |
| Cisco                           | ASA                                                      | 8.3                                                | [Exemplos de Cisco](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Cisco/Current/ASA)                                                                                                                                                                        | Não compatível                                                                                                                                                                                               |
| Cisco                           | RECUPERAÇÃO AUTOMÁTICA                                                      | IOS 15.1 (PolicyBased), IOS 15.2 (RouteBased)                | [Exemplos de Cisco](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Cisco/Current/ASR)                                                                                                                                                                        | [Exemplos de Cisco](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Cisco/Current/ASR)                                                                                                                                 |
| Cisco                           | ISR                                                      | IOS 15.0 (PolicyBased), IOS 15.1 (RouteBased *)               | [Exemplos de Cisco](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Cisco/Current/ISR)                                                                                                                                                                        | [Cisco amostras *](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Cisco/Current/ISR)                                                                                                                                |
| Citrix                          | NetScaler MPX, SDX, VPX      |10.1 e superior                                           | [Instruções de integração](https://docs.citrix.com/en-us/netscaler/11-1/system/cloudbridge-connector-introduction/cloudbridge-connector-azure.html)                                                                                                                                                                            | Não compatível                                                                                                                                                                                               |
| Dell SonicWALL                  | Série TZ, NSA série, série SuperMassive, "e" classe NSA série | SonicOS 5.8.x, [SonicOS 5.9.x](http://documents.software.dell.com/sonicos/5.9/microsoft-azure-configuration-guide/supported-platforms?ParentProduct=850), [SonicOS 6. x](http://documents.software.dell.com/sonicos/6.2/microsoft-azure-configuration-guide/supported-platforms?ParentProduct=646 )          | [Instruções - SonicOS 6.2](http://documents.software.dell.com/sonicos/6.2/microsoft-azure-configuration-guide?ParentProduct=646) [Instruções - SonicOS 5,9](http://documents.software.dell.com/sonicos/5.9/microsoft-azure-configuration-guide?ParentProduct=850)                                                                                                                                   | [Instruções - SonicOS 6.2](http://documents.software.dell.com/sonicos/6.2/microsoft-azure-configuration-guide?ParentProduct=646) [Instruções - SonicOS 5,9](http://documents.software.dell.com/sonicos/5.9/microsoft-azure-configuration-guide?ParentProduct=850)                                                                                                                                                                                      |
| F5                              | Série de BIG IP                                 |           12.0                                            | [Instruções de configuração](https://devcentral.f5.com/articles/connecting-to-windows-azure-with-the-big-ip)                                                                                                                                                                          | [Instruções de configuração](https://devcentral.f5.com/articles/big-ip-to-azure-dynamic-ipsec-tunneling)                                                                                                                                                                                         |
| Fortinet                        | FortiGate                                                | FortiOS 5.2.7                                      | [Instruções de configuração](http://docs.fortinet.com/d/fortigate-configuring-ipsec-vpn-between-a-fortigate-and-microsoft-azure)                                                                                                                                                                          | [Instruções de configuração](http://docs.fortinet.com/d/fortigate-configuring-ipsec-vpn-between-a-fortigate-and-microsoft-azure)                                                                                                                                  |
| Japão iniciativa de Internet (IIJ) | Série SEIL                                              | SEIL / X 4.60 4.60 SEIL/B1, SEIL/x86 3.20            | [Instruções de configuração](http://www.iij.ad.jp/biz/seil/ConfigAzureSEILVPN.pdf)                                                                                                                                                                   | Não compatível                                                                                                                                                                                               |
| Zimbro                         | SRX                                                      | JunOS 10.2 (PolicyBased), JunOS 11.4 (RouteBased)            | [Exemplos de zimbro](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/SRX)                                                                                                                                                                      | [Exemplos de zimbro](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/SRX)                                                                                                                              |
| Zimbro                         | Série de J                                                 | JunOS 10.4r9 (PolicyBased), JunOS 11.4 (RouteBased)          | [Exemplos de zimbro](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/JSeries)                                                                                                                                                                 | [Exemplos de zimbro](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/JSeries)                                                                                                                         |
| Zimbro                         | ISG                                                      | ScreenOS 6.3 (PolicyBased e RouteBased)                  | [Exemplos de zimbro](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/ISG)                                                                                                                                                                      | [Exemplos de zimbro](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/ISG)                                                                                                                              |
| Zimbro                         | SSG                                                      | ScreenOS 6.2 (PolicyBased e RouteBased)                  | [Exemplos de zimbro](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/SSG)                                                                                                                                                                      | [Exemplos de zimbro](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/SSG)                                                                                                                              |
| Microsoft                       | Serviço de acesso remoto e encaminhamento                        | Windows Server 2012                                | Não compatível                                                                                                                                                                                                                                       | [Exemplos da Microsoft](http://go.microsoft.com/fwlink/p/?LinkId=717761)                                                                                           |
| Abrir sistemas AG | Controlo de missão Gateway de segurança | N/D | [Guia de instalação](https://www.open.ch/_pdf/Azure/AzureVPNSetup_Installation_Guide.pdf) | [Guia de instalação](https://www.open.ch/_pdf/Azure/AzureVPNSetup_Installation_Guide.pdf) |
| Openswan                        | Openswan                                                 | 2.6.32                                             | (Brevemente)                                                                                                                                                                                                                                        | Não compatível                                                                                                                                                                                               |
| Palo Alto redes              | Todos os dispositivos de executar o sistema operativo-panorâmica     | Panorâmica-SO 6.1.5 ou posterior (PolicyBased), panorâmica-SO 7.0.5 ou posterior (RouteBased)       | [Instruções de configuração]( https://live.paloaltonetworks.com/t5/Configuration-Articles/How-to-Configure-VPN-Tunnel-Between-a-Palo-Alto-Networks/ta-p/59065)                                                                                                                                                                                          | [Instruções de configuração](https://live.paloaltonetworks.com/t5/Integration-Articles/Configuring-IKEv2-VPN-for-Microsoft-Azure-Environment/ta-p/60340)                                                                                                                                                                                    |
| WatchGuard                      | Todos os                                                      | Fireware XTM v11.x                                 | [Instruções de configuração](http://customers.watchguard.com/articles/Article/Configure-a-VPN-connection-to-a-Windows-Azure-virtual-network/)                                                                                                                                                                          | Não compatível                                                                                                                                                                                               |

(*) Routers ISR 7200 série suportam apenas PolicyBased VPNs.

## <a name="additionaldevices"></a>Dispositivos VPN validado não

Se não vir o seu dispositivo indicado na tabela de dispositivos validado VPN, ainda pode funcionar com uma ligação de Site para o Site. Verifique se o seu dispositivo VPN cumpre os requisitos mínimos descritos na secção de Gateway requisitos do artigo [Sobre VPN Gateways](vpn-gateway-about-vpngateways.md#gateway-requirements) . Dispositivos que satisfaçam os requisitos mínimos devem também funciona bem com VPN gateways. Contacte o fabricante do dispositivo para instruções de configuração e suporte adicionais.


## <a name="editing-device-configuration-samples"></a>Amostras de configuração de dispositivo de edição

Depois de transferir o exemplo de configuração de dispositivo VPN fornecido, terá de substituir alguns dos valores para refletir as definições para o seu ambiente. 

**Para editar um exemplo:**

1. Abra a amostra de utilizar o bloco de notas. 
1. Procurar e substituir todas as cadeias de <*texto*> com os valores que pertencem aos seu ambiente. Certifique-se de que incluir < e >. Quando não é especificado um nome, o nome que selecione deve ser único. Se um comando não funciona, consulte a documentação do fabricante de dispositivo.

| **Texto de exemplo**                | **Alterar para**                                                                                                        |
|----------------------------------|----------------------------------------------------------------------------------------------------------------------|
| &lt;RP_OnPremisesNetwork&gt;           | O nome que selecionou para este objeto. Exemplo: myOnPremisesNetwork                                                       |
| &lt;RP_AzureNetwork&gt;                | O nome que selecionou para este objeto. Exemplo: myAzureNetwork                                                            |
| &lt;RP_AccessList&gt;                  | O nome que selecionou para este objeto. Exemplo: myAzureAccessList                                                         |
| &lt;RP_IPSecTransformSet&gt;           | O nome que selecionou para este objeto. Exemplo: myIPSecTransformSet                                                       |
| &lt;RP_IPSecCryptoMap&gt;              | O nome que selecionou para este objeto. Exemplo: myIPSecCryptoMap                                                          |
| &lt;SP_AzureNetworkIpRange&gt;         | Especifique o intervalo. Exemplo: 192.168.0.0                                                                                  |
| &lt;SP_AzureNetworkSubnetMask&gt;      | Especificar a máscara de sub-rede. Exemplo: 255.255.0.0                                                                            |
| &lt;SP_OnPremisesNetworkIpRange&gt;    | Especifique o intervalo no local. Exemplo: 10.2.1.0                                                                         |
| &lt;SP_OnPremisesNetworkSubnetMask&gt; | Especifique a máscara de sub-rede no local. Exemplo: 255.255.255.0                                                              |
| &lt;SP_AzureGatewayIpAddress&gt;       | Estas informações específicas da rede virtual e está localizado no Portal de gestão como **endereço IP do Gateway**. |
| &lt;SP_PresharedKey&gt;                | Esta informação específica à sua rede virtual e está localizada no Portal de gestão como gerir chave.          |



## <a name="ipsec-parameters"></a>Parâmetros IPsec

>[AZURE.NOTE] Apesar dos valores listados na seguinte tabela são suportados pelos Azure VPN Gateway, neste momento não existe nenhuma forma para especificar ou selecione uma combinação específica a partir do Azure VPN Gateway. Tem de especificar quaisquer restrições a partir do dispositivo VPN no local. Além disso, tem braçadeira MSS na 1350.

### <a name="ike-phase-1-setup"></a>Programa de configuração IKE fase 1

| **Propriedade**                                       | **PolicyBased** | **RouteBased e padrão ou alto desempenho VPN gateway** |
|----------------------------------------------------|--------------------------------|------------------------------------------------------------------|
| Versão IKE                                        | IKEv1                          | IKEv2                                                            |
| Grupo Diffie-Hellman                               | Grupo de 2 (1024 bits)             | Grupo de 2 (1024 bits)                                               |
| Método de autenticação                              | Chave pré-partilhada                 | Chave pré-partilhada                                                   |
| Algoritmos de encriptação                              | AES256 AES128 3DES             | AES256 3DES                                                      |
| Algoritmo hashing                                  | SHA1(SHA128)                   | SHA1(SHA128), SHA2(SHA256)                                                     |
| Fase 1 segurança associação (SA) duração (hora) | 28.800 segundos                 | segundos 10,800                                                   |


### <a name="ike-phase-2-setup"></a>Configuração de IKE fase 2

| **Propriedade**                                                             | **PolicyBased**                 | **RouteBased e padrão ou alto desempenho VPN gateway**   |
|--------------------------------------------------------------------------|------------------------------------------------|--------------------------------------------------------------------|
| Versão IKE                                                              | IKEv1                                          | IKEv2                                                              |
| Algoritmo hashing                                                        | SHA1(SHA128)                                   | SHA1(SHA128)                                                       |
| Fase 2 segurança associação (SA) duração (hora)                        | 3600 segundos                                  | 3600 segundos                                                                  |
| Fase 2 segurança associação (SA) duração (débito)                  | 102,400,000 KB                                 | -                                                                  |
| Encriptação de SA IPsec & autenticação oferece (por ordem de preferência) | 1. ESP-AES256 2. ESP AES128 3. ESP 3DES 4. N/D | Consulte o artigo a *associação de segurança do RouteBased Gateway IPsec (SA) oferece* (abaixo) |
| (Com Forward Secrecy)                                            | N                                             | Sem (*)|
| Mortos deteção de ponto a ponto                                                      | Não é suportado                                  | Suportada                                                          |

(*) Azure Gateway como dispositivo de resposta IKE pode aceitar PFS DH grupo 1, 2, 5, 14, 24.

### <a name="routebased-gateway-ipsec-security-association-sa-offers"></a>Oferece de associação de segurança do RouteBased Gateway IPsec (SA)

A tabela seguinte lista IPsec SA encriptação e oferece uma autenticação. Ofertas estão listadas a ordem de preferência de que a oferta é apresentada ou aceites.

| **Encriptação de IPsec SA e ofertas de autenticação** | **Azure Gateway como iniciador**                               | **Azure Gateway como dispositivo de resposta**                                   |
|---------------------------------------------------|--------------------------------------------------------------|--------------------------------------------------------------|
| 1                                                 | ESP AES_256 SHA                                              | ESP AES_128 SHA                                              |
| 2                                                 | ESP AES_128 SHA                                              | ESP 3_DES MD5                                                |
| 3                                                 | ESP 3_DES MD5                                                | ESP 3_DES SHA                                                |
| 4                                                 | ESP 3_DES SHA                                                | AH SHA1 com ESP AES_128 com HMAC nulo                      |
| 5                                                 | AH SHA1 com ESP AES_256 com HMAC nulo                      | SHA1 AH com ESP 3_DES com HMAC nulo                        |
| 6                                                 | AH SHA1 com ESP AES_128 com HMAC nulo                      | AH MD5 com ESP 3_DES com HMAC nulo, sem as durações de proposta |
| 7                                                 | SHA1 AH com ESP 3_DES com HMAC nulo                        | AH SHA1 com ESP 3_DES SHA1, sem durações                    |
| 8                                                 | AH MD5 com ESP 3_DES com HMAC nulo, sem as durações de proposta | AH MD5 com ESP 3_DES MD5, sem durações                     |
| 9                                                 | AH SHA1 com ESP 3_DES SHA1, sem durações                    | ESP DES MD5                                                  |
| 10                                                | AH MD5 com ESP 3_DES MD5, sem durações                     | ESP DES SHA1, sem durações                                   |
| 11                                                | ESP DES MD5                                                  | Aqui SHA1 com HMAC nulo ESP dos, sem as durações de proposta        |
| 12                                                | ESP DES SHA1, sem durações                                   | Aqui MD5 com HMAC nulo ESP dos, sem as durações de proposta        |
| 13                                                | Aqui SHA1 com HMAC nulo ESP dos, sem as durações de proposta        | AH SHA1 com ESP DES SHA1, sem durações                      |
| 14                                                | Aqui MD5 com HMAC nulo ESP dos, sem as durações de proposta        | AH MD5 com ESP DES MD5, sem durações                       |
| 15                                                | AH SHA1 com ESP DES SHA1, sem durações                      | ESP SHA, sem durações                                        |
| 16                                                | AH MD5 com ESP DES MD5, sem durações                       | ESP MD5, sem durações                                        |
| 17                                                | -                                                            | Aqui SHA, sem durações                                         |
| 18                                                | -                                                            | MD5 AH, sem durações                                        |


- Pode especificar encriptação IPsec ESP NULL com gateways RouteBased e alto desempenho VPN. Encriptação baseada em nula não fornece proteção de dados no trânsito e só deverá ser utilizada quando máximo e mínimo, débito latência é necessária.  Os clientes podem optar por utilizar este em cenários de comunicação VNet para VNet ou quando encriptação está a ser aplicada noutro local da solução.

- Para conetividade de publicação em local através da Internet, utilize as predefinições do gateway Azure VPN com encriptação e algoritmos hashing listados nas tabelas acima para garantir a segurança da sua comunicação crítica.





