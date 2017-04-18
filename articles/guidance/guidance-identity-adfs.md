<properties
   pageTitle="Implementar a ADFS no Azure | Microsoft Azure"
   description="Como implementar uma arquitetura de rede híbrido seguro com autorização de serviço de Federação do Active Directory no Azure."
   services="guidance,vpn-gateway,expressroute,load-balancer,virtual-network,active-directory"
   documentationCenter="na"
   authors="telmosampaio"
   manager="christb"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="guidance"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/13/2016"
   ms.author="telmos"/>

# <a name="implementing-active-directory-federation-services-adfs-in-azure"></a>Implementar a serviços de Federação do Active Directory (ADFS) no Azure

[AZURE.INCLUDE [pnp-RA-branding](../../includes/guidance-pnp-header-include.md)]

Este artigo descreve os procedimentos recomendados para implementar uma rede híbrido seguro que expande a sua rede no local para Azure e que utiliza [Serviços de Federação do Active Directory (ADFS)] [ active-directory-federation-services] para executar federada autenticação e autorização para componentes em execução na nuvem. Esta arquitetura expande a estrutura descrita pelo [Prolongamento Active Directory para Azure][implementing-active-directory].

> [AZURE.NOTE] Azure tem dois modelos de implementação diferente: [Gestor de recursos] [ resource-manager-overview] e clássico. Esta arquitetura de referência utiliza recurso Gestor de designs, a Microsoft recomenda para implementações novas.

ADFS pode executar no local, mas num cenário de híbrido onde aplicações estão localizadas no Azure poderá ser mais eficaz para implementar esta funcionalidade na nuvem. Casos de utilização típica para esta arquitectura incluem:

- Aplicações híbridas onde das cargas de trabalho executar parcialmente no local e parcialmente no Azure.

- Soluções que utilizam funcionalidades federada autorização para expor aplicações web para organizações do parceiro.

- Sistemas que suportam o acesso de browsers executar fora da firewall organizacional.

- Sistemas que permitem aos utilizadores para acederem aos aplicações web ligando a partir de dispositivos externos autorizados como computadores remotos, blocos de notas e outros dispositivos móveis. 

Para obter mais informações sobre como funciona o ADFS, consulte o artigo [Active Directory Federação descrição geral de serviços][active-directory-federation-services-overview]. Para além disso, o artigo [implementação ADFS no Azure] [ adfs-intro] contém uma introdução detalhada passo a passo para implementar o ADFS no Azure.

## <a name="architecture-diagram"></a>Diagrama de arquitectura

O diagrama seguinte realça os componentes importantes nesta arquitectura (*clique para ampliar*). Para mais informações sobre qualquer elemento não relacionadas com o ADFS, leia o artigo [Implementar uma arquitetura de rede seguro híbrido no Azure][implementing-a-secure-hybrid-network-architecture], [Implementar uma arquitetura de rede híbrido seguro com acesso à Internet no Azure][implementing-a-secure-hybrid-network-architecture-with-internet-access]e [Implementar uma arquitetura de rede seguro híbrida com o Active Directory identidades no Azure][implementing-active-directory]:

[! [0]][0]

>[AZURE.NOTE] Este diagrama apresenta os seguintes casos de utilização:
>
>- Código de aplicação em execução no interior de uma organização parceira acede a uma aplicação web alojada no interior do seu VNet Azure.
>
>- Um utilizador externo, registado (com as credenciais armazenadas no interior adiciona) aceder a uma aplicação web alojada no interior do seu VNet Azure.
>
>- Um utilizador estabelecer ligação ao seu VNet utilizando um dispositivo autorizado e executar uma aplicação web alojada no interior do seu VNet Azure.
>
>Para além disso, esta arquitetura foca-se em Federação passiva, onde os servidores de Federação decidirem como e quando para autenticar um utilizador. O utilizador é esperado para fornecer informações de início de sessão quando uma aplicação é iniciada em execução. Isto mecanismos mais frequentemente utilizado pelos browsers e envolve um protocolo que redireciona o browser para um site onde o utilizador pode fornecer as respetivas credenciais. ADFS também suporta a Federação do active através das quais uma aplicação assume a responsabilidade para fornecer as credenciais sem mais interação do utilizador, mas neste caso esteja fora do âmbito desta arquitectura.

- **Adiciona sub-rede.** Os servidores de adiciona estão contidos os seus próprios sub-rede. Regras NSG ajudam a proteger os servidores de adiciona e podem fornecer uma firewall contra tráfego a partir de origens inesperados.

- **Adiciona servidores.** Estes são os controladores de domínio em execução como VMs na nuvem. Nestes servidores podem fornecer a autenticação de identidades locais dentro do domínio.

- **Sub-rede ADFS.** Os servidores ADFS podem ser localizados dentro dos seus próprios sub-rede, com regras NSG serve como uma firewall.

- **Servidores de ADFS.** Os servidores ADFS fornecer federada autorização e autenticação. Nesta arquitectura, eles efetuar as seguintes tarefas:

    - Podem receber tokens de segurança que contém em afirmações efetuadas por um servidor de Federação do parceiro em nome de um utilizador do parceiro. ADFS pode verificar se estes tokens são válidos antes de passar os créditos a aplicação web em execução no Azure. A aplicação web empresarial (no Azure) pode utilizar estes em afirmações para autorizar pedidos. Neste cenário, a aplicação web da empresa está *Confiar festa*, e a responsabilidade do servidor de Federação do parceiro emitir afirma que sejam compreendidas pela aplicação web da empresa. Os servidores de Federação do parceiro são referidos como *parceiros de conta* porque submeter a pedidos de acesso em nome de contas autenticados na organização parceiro. Os servidores ADFS chamam *parceiros de recursos* porque fornecem acesso aos recursos (as aplicações web, neste caso).

    - Estes podem autenticar (através do adiciona e o [Serviço de registo de dispositivo do Active Directory][ADDRS]) e autorizar pedidos recebidos de utilizadores externos a execução de um web browser ou dispositivo que precisa de aceder às suas aplicações web da empresa. 

    Os servidores ADFS estão configurados como um farm, acedido através de um balanceador de carga Azure. Esta estrutura ajuda a melhorar a disponibilidade e escalabilidade. Além disso, tenha em atenção que os servidores ADFS não são expostos diretamente à Internet, em vez disso todo o tráfego Internet é filtrado através de servidores proxy do ADFS web aplicação e uma DMZ.

- **Sub-rede de proxy ADFS.** Os servidores de proxy ADFS podem contidos dos seus próprios sub-rede, com regras NSG fornecer proteção. Os servidores de nesta sub-rede são expostos à Internet através de um conjunto de eletrodomésticos virtual de rede que fornecem uma firewall entre o Azure virtual a rede e Internet.

- **ADFS web servidores proxy (WAP) de aplicações.** Estes computadores actuam como servidores ADFS para pedidos recebidos a partir de dispositivos externos e organizações de parceiro. Os servidores WAP agir como um filtro, protegendo os servidores ADFS a partir do access direta da Internet pública. Tal como acontece com servidores ADFS, implementar o WAP servidores num farm com balanceamento de carga dá-lhe maior disponibilidade e escalabilidade que implementar um conjunto de servidores autónomos.

    >[AZURE.NOTE] Para obter informações detalhadas sobre como instalar servidores WAP, consulte o artigo [instalar e configurar o servidor de Proxy de aplicação Web][install_and_configure_the_web_application_proxy_server]

- **Organização do parceiro.** Esta é uma organização de parceiro do exemplo, o que é executada uma aplicação web que os pedidos de acesso para a aplicação web a ser executada em Azure. O servidor de Federação da organização parceira autentica pedidos localmente e submete tokens de segurança que contém afirmações para ADFS em execução no Azure. ADFS no Azure validar os tokens de segurança e, se forem válidos-pode passar as afirmações para a aplicação web a ser executada em Azure autorizá-los.

    >[AZURE.NOTE] Também pode configurar um túnel VPN utilizando Azure Gateway para fornecer acesso direto aos ADFS para parceiros fidedignos. Pedidos recebidos de parceiros destes não passar através dos servidores WAP.

## <a name="recommendations"></a>Recomendações

Esta secção resume recomendações para a implementação ADFS no Azure, que abrange que:

- Recomendações VM.

- Recomendações de funcionamento em rede.

- Recomendações de disponibilidade.

- Recomendações de segurança.

- Recomendações de instalação ADFS.

- Confiar ADFS recomendações.

### <a name="vm-recommendations"></a>Recomendações VM

Crie VMs com recursos suficientes para processar o volume de tráfego esperado. Utilize o tamanho das máquinas existentes alojamento ADFS no local, como ponto de partida. Monitorize a utilização do recurso. Pode redimensionar os VMs e Dimensionar para baixo se forem demasiado grandes.

Siga as recomendações listadas na [executar uma VM do Windows no Azure][vm-recommendations].

### <a name="networking-recommendations"></a>Recomendações de funcionamento em rede

Configure a interface de rede para cada um dos VMs alojamento servidores ADFS e WAP com endereços IP estáticos privados.

Não dar os endereços IP públicos ADFS VMs. Para obter mais informações, consulte o artigo [Considerações de segurança][security-considerations].

Defina o endereço IP dos servidores de DNS preferidos e secundários para interfaces de rede para cada ADFS e WAP VM fazer referência a VMs adiciona (que deve estar em execução DNS). Este passo é necessário ativar cada VM aderir ao domínio.

### <a name="availability-recommendations"></a>Recomendações de disponibilidade

Crie um farm ADFS com, pelo menos, dois servidores para aumentar a disponibilidade do serviço ADFS.

Utilize contas de armazenamento diferente para cada VM ADFS no farm. Esta abordagem ajuda-o para se certificar de que uma falha numa conta armazenamento única não efetuar todo o farm inacessível.

Crie conjuntos de disponibilidade Azure em separado para o ADFS e WAP VMs. Certifique-se de que não existem, pelo menos, duas VMs em cada conjunto. Cada conjunto de disponibilidade tem de ter, pelo menos, dois domínios de atualização e dois domínios falhas.

Configure os balanceadores de carga para o ADFS VMs e WAP VMs da seguinte forma:

- Utilize um balanceador de carga Azure para fornecer acesso externo a VMs WAP e um balanceador de carga interna para distribuir a carga pelos servidores de ADFS no ADFS farm.

- Só passe tráfego não apareça no porta 443 (HTTPS) para os servidores ADFS/WAP.

- Dar o Balanceador de carga um endereço IP estático.

- Crie uma sonda de estado de funcionamento utilizando o protocolo TCP em vez de HTTPS. Pode executar o ping porta 443 para verificar se um servidor ADFS está a funcionar.

    >[AZURE.NOTE] Os servidores de ADFS utilizam o protocolo de indicação de nome de servidor (SNI), por isso, tentar sonda utilizando um ponto final HTTPS a partir do falha do Balanceador de carga.

- Adicione um registo DNS *A* para o domínio para o Balanceador de carga ADFS. 

    Especifique o endereço IP do Balanceador de carga e atribua um nome de domínio (como adfs.contoso.com). Este é o nome pelo qual clientes e os servidores WAP aceder o farm de servidores ADFS.

### <a name="security-recommendations"></a>Recomendações de segurança

Impedir que exposição direta dos servidores ADFS na Internet. Os servidores ADFS são computadores façam parte de um domínio que têm o completa autorização para conceder tokens de segurança. Se um servidor ADFS está comprometido, um utilizador malicioso pode emitir tokens de acesso total a todas as aplicações web e para os servidores de federação que estejam protegidos por ADFS. Se o seu sistema de tem processar os pedidos de utilizadores externos não necessariamente ligar a partir de sites fidedignos parceiros, utilize os servidores de WAP para processar estes pedidos. Para obter mais informações, consulte o artigo [onde pretende colocar um Proxy de servidor de Federação][where-to-place-an-fs-proxy].

Os servidores de local ADFS e servidores WAP no sub-redes separadas com os seus próprios firewalls. Pode utilizar as regras NSG para definir regras de firewall. Se necessitar de mais abrangente proteção que possa implementar um perímetro segurança adicional à volta de servidores utilizando um par de sub-redes e NVAs, tal como descrito pelo documento [Implementar uma arquitetura de rede híbrido seguro com acesso à Internet no Azure][implementing-a-secure-hybrid-network-architecture-with-internet-access]. Note que todos os firewalls deverão permitir o tráfego na porta 443 (HTTPS).

Restringir o acesso de início de sessão direta para os servidores ADFS e WAP. Apenas os docentes DevOps devem ser conseguir ligar.

Não participar os servidores WAP para o domínio.

### <a name="adfs-installation-recommendations"></a>Recomendações de instalação ADFS

O artigo [Implementar um Farm de servidores de Federação] [ Deploying_a_federation_server_farm] fornece instruções detalhadas para instalar e configurar o ADFS. Efetue as seguintes tarefas antes de configurar o primeiro servidor ADFS no farm:

1. Obter um certificado publicamente fidedigno para efetuar a autenticação de servidor. O *nome de assunto* tem de conter o nome pelo qual clientes aceder ao serviço de Federação. Isto pode ser o nome DNS registado para Balanceador de carga, por exemplo, *adfs.contoso.com* (evitar utilizar tais como nomes de caracteres universais **. contoso.com*, por motivos de segurança). Utilize o mesmo certificado em todos os VMs de servidor ADFS. Pode adquirir um certificado a partir de uma autoridade de certificação fidedigna, mas se a sua organização utiliza os serviços de certificado do Active Directory pode criar a sua própria. 

    É utilizado o *nome de assunto alternativo* à DRS de ativar o acesso a partir de dispositivos externos. Isto deve ser o formulário *enterpriseregistration.contoso.com*.

    Para obter mais informações, consulte o artigo [obter e configurar um certificado SSL para ADFS][adfs_certificates].

2. No controlador de domínio, gere uma nova chave de raiz para o serviço de distribuição de chave. Defina a hora eficaz para ser a hora atual menos 10 horas (esta configuração reduz o atraso que pode ocorrer em distribuir e sincronizar teclas ao longo do domínio). Este passo é necessário suporta a criação de conta de serviço de grupo que são utilizadas para executar o serviço ADFS. O comando Powershell seguinte mostra um exemplo de como fazer isto:

    ```powershell
    Add-KdsRootKey -EffectiveTime (Get-Date).AddHours(-10)
    ```

3. Adicione cada servidor ADFS VM para o domínio.

>[AZURE.NOTE] Para instalar o ADFS, o controlador de domínio a executar o emulador PDC função FSMO para o domínio tem de estar em execução e acessíveis a partir do VMs ADFS.

### <a name="adfs-trust-recommendations"></a>Confiar ADFS recomendações

Estabelecer fidedignidade de Federação entre a instalação do ADFS e os servidores de Federação de qualquer organizações do parceiro. Configure qualquer em afirmações filtragem e mapeamento necessário. Este processo requer:

- DevOps pessoal **na organização cada parceiro** para adicionar uma fidedignidade festa dependente para as aplicações web acessíveis através do seu servidores ADFS.

- DevOps pessoal **na sua organização** para configurar confiança de afirmações fornecedor para ativar os seus servidores de ADFS confiar em afirmações que fornecem organizações do parceiro.

- DevOps pessoal **na sua organização** para configurar ADFS para transmitir em afirmações para aplicações de web da sua organização.

    Para obter mais informações, consulte o artigo [Que estabelece Federação confiar][establishing-federation-trust].

Publicar aplicações de web da sua organização e torná-las disponível para parceiros externos utilizando pré-autenticação através dos servidores WAP. Para obter mais informações, consulte [publicar aplicações utilizar pré-autenticação ADFS][publish_applications_using_AD_FS_preauthentication]

Repare que o ADFS suporta transformação token e de expansão. Azure Active Directory não fornece esta funcionalidade. Com o ADFS, ao configurar as relações de fidedignidade, pode:

- Configure transformações afirmação para regras de autorização. Por exemplo, pode mapear de segurança de grupo a partir de uma representação utilizada por uma organização que não sejam Microsoft de parceiro para algo que pode autorizar que adiciona na sua organização.

- Transforme em afirmações de um formato para outro. Por exemplo, pode mapear a partir do SAML 2.0 para SAML 1.1 se a sua aplicação suporta apenas afirmações SAML 1.1. 

## <a name="availability-considerations"></a>Considerações sobre a disponibilidade

Pode utilizar o SQL Server ou Windows interno da base de dados (WID) para colocar em espera informações sobre a configuração ADFS. WID redundância básica. As alterações são escritas diretamente apenas uma das bases de dados ADFS no ADFS cluster, apesar dos outros servidores de utilizam a replicação por solicitação para manter as suas bases de dados atualizados. Utilizar o SQL Server pode fornecer redundância de base de dados completa e elevada disponibilidade utilizando activação pós-falha clusters ou espelhando as.

## <a name="security-considerations"></a>Considerações de segurança

ADFS utiliza o protocolo HTTPS, por isso, certifique-se de que as regras NSG para a sub-rede que contém a web camada VMs licença HTTPS pedidos. Estes pedidos podem originar da rede no local, sub-redes que contém a camada web, camada de negócio, camada de dados, DMZ privado, DMZ público e sub-rede que contém os servidores ADFS.

Considere utilizar um conjunto de eletrodomésticos virtual de rede que regista informações detalhadas sobre o tráfego de percorrer o limite da sua rede virtual para efeitos de auditoria.

## <a name="scalability-considerations"></a>Considerações de escalabilidade

As seguintes considerações resumidas do documento [Planear a implementação de ADFS][plan-your-adfs-deployment], fornecer um ponto de partida para redimensionamento ADFS farms:

- Se tiver menos de 1000 utilizadores, não crie os servidores de ADFS dedicados, mas em vez disso, instalar ADFS em cada um dos servidores adiciona na nuvem (Certifique-se de que tem, pelo menos, duas adiciona servidores, para manter a disponibilidade). Crie um servidor WAP único.

- Se tiver entre 1000 e 15000 utilizadores, crie duas dedicados ADFS servidores e duas dedicada WAP.

- Se tiver entre 15000 e 60000 utilizadores, crie entre três a cinco servidores ADFS dedicados e pelo menos dois servidores WAP dedicadas.

Estes valores partem do pressuposto de que está a utilizar duplos Quadrilátero core VMs (D4_v2 padrão ou melhor) para os servidores no Azure do anfitrião.

Tenha em atenção que se estiver a utilizar a base de dados internas do Windows para armazenar dados de configuração de ADFS, está limitados aos servidores ADFS oito do farm. Se antecipar necessário especificá-las mais, em seguida, utilize o SQL Server. Para mais informações, consulte [A função da base de dados de configuração de ADFS][adfs-configuration-database].

## <a name="management-considerations"></a>Considerações sobre a gestão

DevOps docentes deverão estar preparado para efetuar as seguintes tarefas:

- Gerir os servidores de Federação (Gerir o farm ADFS, gestão de política de confiança nos servidores de Federação e gerir os certificados utilizados pelos serviços de Federação).

- Gerir os servidores WAP (Gerir do farm WAP, gerir os certificados WAP).

- Gerir aplicações web (configurar mapeamentos de afirmações, métodos de autenticação e dependente partes).

- Cópias de segurança de componentes ADFS.

## <a name="monitoring-considerations"></a>Considerações de monitorização

O [Microsoft pacote de gestão do Centro de sistema do Active Directory Federação serviços 2012 R2] [ oms-adfs-pack] fornece pro-activos e reactivar monitorização da sua implementação ADFS para o servidor de Federação. Este pacote de gestão de monitoriza:

- Eventos que o ADFS serviço registos nos registos de eventos ADFS.

- Os dados de desempenho recolhem os contadores de desempenho ADFS. 

- O estado de funcionamento geral as aplicações de web e do sistema ADFS (dependente partes) e fornece alertas para problemas críticos e avisos.

## <a name="solution-components"></a>Componentes da solução

Um script de solução de exemplo, [Implementar ReferenceArchitecture.ps1][solution-script], está disponível que pode utilizar para implementar a arquitetura que se segue as recomendações descritas neste artigo. Este script utiliza modelos de Gestor de recursos do Azure. Os modelos estão disponíveis como um conjunto de blocos modulares fundamentais, cada um dos quais executa uma ação específica como criar um VNet ou configurar uma NSG. O objetivo do script é orquestrar implementação do modelo.

Os modelos são parametrizados, com os parâmetros contidos em ficheiros JSON separados. Pode modificar os parâmetros nestes ficheiros para configurar a implementação para cumprir os seus próprios requisitos. Não necessita de alterar os modelos de si. Tenha em atenção que não tem de alterar os esquemas dos objetos nos ficheiros do parâmetro.

Quando edita os modelos, criar objetos que se seguem as convenções de nomenclatura descritas na [Recomendado convenções de nomenclatura de para recursos de Azure][naming-conventions].

A solução de exemplo cria e configura o ambiente na nuvem que inclua DMZ de sub-rede e servidores, sub-rede ADFS e servidores, sub-rede de proxy ADFS e servidores, adiciona, camada web, camada de negócio e componentes de níveis de acesso de dados, VPN gateway e camadas de gestão. Solução da amostra também inclui uma configuração opcional para a criação de um simulada ambiente no local.

As secções seguintes descrevem os elementos no local e na nuvem configurações.

### <a name="on-premises-components"></a>Componentes no local

>[AZURE.NOTE] Estes componentes não são o foco principal de arquitectura descrita neste documento e são fornecidos simplesmente para lhe dar a oportunidade de ambiente de teste o nuvem em segurança, em vez de utilizar um ambiente de produção real. Por este motivo, esta secção resume apenas os ficheiros de parâmetro de chave. Pode modificar as definições de como os endereços IP ou os tamanhos das VMs, mas é aconselhável para deixar muitos dos outros parâmetros inalterados.

Este ambiente inclui uma floresta AD para um domínio com o nome contoso.com. O domínio contém dois adiciona servidores com os endereços IP 192.168.0.4 e 192.168.0.5. Estes dois servidores também executar o serviço de DNS. Chama-se na conta de administrador local no ambas as VMs `testuser` com palavra-passe `AweS0me@PW`. Para além disso, a configuração configura um gateway VPN para ligar à VNet na nuvem. Pode modificar a configuração ao editar os seguintes ficheiros JSON localizados nos [**parâmetros/onpremise**] [ on-premises-folder] pasta:

- ** [virtualNetwork.parameters.json][on-premises-vnet-parameters]**. Este ficheiro define o espaço de endereços de rede para o ambiente no local.

- ** [virtualMachines adds.parameters.json][on-premises-virtualmachines-adds-parameters]**. Este ficheiro contém a configuração para os VMs no local adiciona serviços de alojamento. Por predefinição, são criados dois *padrão-DS3-v2* VMs.

- ** [virtualNetworkGateway.parameters.json] [ on-premises-virtualnetworkgateway-parameters] ** e ** [connection.parameters.json][on-premises-connection-parameters]**. Estes ficheiros mantenha as definições para a ligação de VPN para o gateway Azure VPN na nuvem, incluindo a chave partilhada para ser utilizado para proteger o tráfego percorrer o gateway.

Os ficheiros na pasta restantes contêm as informações de configuração utilizadas para criar o domínio no local com este infraestrutura. Utilizá-los para instalar adiciona, programa de configuração de DNS, crie uma floresta e configurar os sites de replicação para a floresta.

### <a name="cloud-components"></a>Componentes de nuvem

O essencial esta arquitetura de formulário estes componentes. Os [**parâmetros/azure**] [ azure-folder] pasta contém os seguintes ficheiros de parâmetro para configurar estes componentes:

- ** [virtualNetwork.parameters.json][vnet-parameters]**. Este ficheiro define a estrutura do VNet para os VMs e outros componentes na nuvem. Inclui definições, tais como o nome, espaço de endereços, sub-redes e os endereços de todos os servidores DNS necessários. Tenha em atenção que os endereços de DNS é mostrados neste exemplo referenciam os endereços IP dos servidores DNS no local e o servidor Azure DNS predefinido. Modificar estes endereços para fazer referência a sua própria configuração DNS se não estiver a utilizar o exemplo-ambiente no local:

    ```json
    {
        "virtualNetworkSettings": {
            "value": {
                "name": "ra-adfs-vnet",
                "resourceGroup": "ra-adfs-network-rg",
                "addressPrefixes": [
                    "10.0.0.0/16"
                ],
                "subnets": [
                    {
                        "name": "dmz-private-in",
                        "addressPrefix": "10.0.0.0/27"
                    },
                    {
                        "name": "dmz-private-out",
                        "addressPrefix": "10.0.0.32/27"
                    },
                    {
                        "name": "dmz-public-in",
                        "addressPrefix": "10.0.0.64/27"
                    },
                    {
                        "name": "dmz-public-out",
                        "addressPrefix": "10.0.0.96/27"
                    },
                    {
                        "name": "mgmt",
                        "addressPrefix": "10.0.0.128/25"
                    },
                    {
                        "name": "GatewaySubnet",
                        "addressPrefix": "10.0.255.224/27"
                    },
                    {
                        "name": "web",
                        "addressPrefix": "10.0.1.0/24"
                    },
                    {
                        "name": "biz",
                        "addressPrefix": "10.0.2.0/24"
                    },
                    {
                        "name": "data",
                        "addressPrefix": "10.0.3.0/24"
                    },
                    {
                        "name": "adds",
                        "addressPrefix": "10.0.4.0/27"
                    },
                    {
                        "name": "adfs",
                        "addressPrefix": "10.0.5.0/27"
                    },
                    {
                        "name": "proxy",
                        "addressPrefix": "10.0.6.0/27"
                    }
                ],
                "dnsServers": [
                    "192.168.0.4",
                    "192.168.0.5",
                    "168.63.129.16"
                ]
            }
        }
    }
    ```

- ** [virtualMachines adds.parameters.json ] [ virtualmachines-adds-parameters] **. Este ficheiro configura VMs executar adiciona na nuvem. A configuração consiste em duas VMs. Deverá alterar o nome de utilizador de administração e a palavra-passe na `virtualMachineSettings` secção e, opcionalmente, pode modificar o tamanho da memória virtual para que correspondam aos requisitos do domínio:

    Para obter mais informações, consulte o artigo [Prolongamento do Active Directory para Azure][extending-ad-to-azure].

    ```json
            "virtualMachinesSettings": {
                "value": {
                    "namePrefix": "ra-adfs-ad",
                    "computerNamePrefix": "aad",
                    "size": "Standard_DS3_v2",
                    "osType": "Windows",
                    "adminUsername": "testuser",
                    "adminPassword": "AweS0me@PW",
                    "osAuthenticationType": "password",
                    "nics": [
                        {
                            "isPublic": "false",
                            "subnetName": "adds",
                            "privateIPAllocationMethod": "Static",
                            "startingIPAddress": "10.0.4.4",
                            "enableIPForwarding": false,
                            "dnsServers": [
                            ],
                            "isPrimary": "true"
                        }
                    ],
                    "imageReference": {
                        "publisher": "MicrosoftWindowsServer",
                        "offer": "WindowsServer",
                        "sku": "2012-R2-Datacenter",
                        "version": "latest"
                    },
                    "dataDisks": {
                        "count": 1,
                        "properties": {
                            "diskSizeGB": 127,
                            "caching": "None",
                            "createOption": "Empty"
                        }
                    },
                    "osDisk": {
                        "caching": "ReadWrite"
                    },
                    "extensions": [
                    ],
                    "availabilitySet": {
                        "useExistingAvailabilitySet": "No",
                        "name": "ra-adfs-as"
                    }
                }
            },
            "virtualNetworkSettings": {
                "value": {
                    "name": "ra-adfs-vnet",
                    "resourceGroup": "ra-adfs-network-rg"
                }
            },
            "buildingBlockSettings": {
                "value": {
                    "storageAccountsCount": 2,
                    "vmCount": 2,
                    "vmStartIndex": 1
                }
            }
        }
    ```

- ** [Adicionar-adiciona-domínio-controller.parameters.json][add-adds-domain-controller-parameters]**. Este ficheiro contém as definições para criar o domínio CONTOSO que abrangem os servidores de adiciona. Utiliza as extensões personalizadas que estabelecer o domínio e adicionar os servidores de adiciona à mesma. A não ser que crie servidores adiciona adicionais (caso em deverá adicioná-los para o `vms` matriz), altere os seus nomes de predefinido ou pretende criar um domínio com um nome diferente, não precisa de modificar este ficheiro.

- ** [loadBalancer adfs.parameters.json] [loadbalancer-adfs-parameters] ** O ficheiro contém dois conjuntos de parâmetros de configuração. O `virtualMachineSettings` secção define os VMs que alojam o serviço ADFS na nuvem. Por predefinição, o script, cria duas destes VMs no mesmo conjunto de disponibilidade:

    ```json
        "virtualMachinesSettings": {
            "value": {
                "namePrefix": "ra-adfs-adfs",
                "computerNamePrefix": "adfs",
                "size": "Standard_DS1_v2",
                "osType": "windows",
                "adminUsername": "testuser",
                "adminPassword": "AweS0me@PW",
                "osAuthenticationType": "password",
                "nics": [
                    {
                        "isPublic": "false",
                        "subnetName": "adfs",
                        "privateIPAllocationMethod": "Static",
                        "startingIPAddress": "10.0.5.4",
                        "isPrimary": "true",
                        "enableIPForwarding": false,
                        "dnsServers": [ ]
                    }
                ],
                "imageReference": {
                    "publisher": "MicrosoftWindowsServer",
                    "offer": "WindowsServer",
                    "sku": "2012-R2-Datacenter",
                    "version": "latest"
                },
                "dataDisks": {
                    "count": 1,
                    "properties": {
                        "diskSizeGB": 128,
                        "caching": "None",
                        "createOption": "Empty"
                    }
                },
                "osDisk": {
                    "caching": "ReadWrite"
                },
                "extensions": [ ],
                "availabilitySet": {
                    "useExistingAvailabilitySet": "No",
                    "name": "ra-adfs-adfs-vm-as"
                }
            }
        }
        ...
        "buildingBlockSettings": {
            "value": {
                "storageAccountsCount": 2,
                "vmCount": 2,
                "vmStartIndex": 1
            }
        }
    ```

    O `loadBalancerSettings` secção fornece a descrição do Balanceador de carga para estas VMs. O Balanceador de carga transmite tráfego que aparece na porta 443 (HTTPS) para uma ou outra das VMs:

    ```json
        "loadBalancerSettings": {
            "value": {
                "name": "ra-adfs-adfs-lb",
                "frontendIPConfigurations": [
                    {
                        "name": "ra-adfs-adfs-lb-fe",
                        "loadBalancerType": "internal",
                        "internalLoadBalancerSettings": {
                            "privateIPAddress": "10.0.5.30",
                            "subnetName": "adfs"
                        }
                    }
                ],
                "backendPools": [
                    {
                        "name": "ra-adfs-adfs-lb-bep",
                        "nicIndex": 0
                    }
                ],
                "loadBalancingRules": [
                    {
                        "name": "https-rule",
                        "frontendPort": 443,
                        "backendPort": 443,
                        "protocol": "Tcp",
                        "backendPoolName": "ra-adfs-adfs-lb-bep",
                        "frontendIPConfigurationName": "ra-adfs-adfs-lb-fe",
                        "probeName": "https-probe",
                        "enableFloatingIP": false
                    }
                ],
                "probes": [
                    {
                        "name": "https-probe",
                        "port": 443,
                        "protocol": "Tcp",
                        "requestPath": null
                    }
                ],
                "inboundNatRules": [ ]
            }
        },
        "virtualNetworkSettings": {
            "value": {
                "name": "ra-adfs-vnet",
                "resourceGroup": "johns-adfs-network-rg"
            }
        }
    ```

- ** [adfs-farm domínio join.parameters.json ] [ adfs-farm-domain-join-parameters] **. Este ficheiro contém as definições utilizadas para adicionar os servidores ADFS ao domínio CONTOSO. Só precisa de modificar este ficheiro se tiver criado servidores ADFS adicionais (atualizar o `vms` matriz neste caso), ou se tiver alterado o nome de domínio.

- ** [gmsa.parameters.json][gmsa-parameters]**, ** [adfs-farm-first.parameters.json][adfs-farm-first-parameters]**, e ** [adfs-farm-rest.parameters.json][adfs-farm-rest-parameters]**. O script utiliza as definições nestes ficheiros para criar o farm de servidores ADFS. 

    O ficheiro *gmsa.parameters.json* contém as definições para a conta de serviço de grupo gerido utilizado pelo serviço ADFS. Pode modificar este ficheiro se pretender alterar o nome da conta ou o domínio.

    O ficheiro *adfs-farm-first.parameters.json* mantém as informações necessárias para criar o farm de servidores ADFS e adicionar o primeiro servidor. Se tiver alterado o domínio ou o nome da conta de serviço de grupo gerido, deverá actualizar este ficheiro.

    O ficheiro *adfs-farm-rest.parameters.json* é utilizado para adicionar os servidores ADFS restantes ao farm. Novamente, se tiver alterado o domínio ou o nome da conta de serviço de grupo gerido, deverá actualizar este ficheiro. Atualizar a `vms` matriz se tiver criado servidores ADFS adicionais.

- ** [loadBalancer adfsproxy.parameters.json][loadBalancer-adfsproxy-parameters]**. Este ficheiro é semelhante em estrutura e conteúdo para o ficheiro *loadBalancer adfs.parameters.json* . Que contém os dados para criar os servidores de proxy ADFS e Balanceador de carga.

- ** [adfsproxy-farm-first.parameters.json][adfsproxy-farm-first-parameters]**, e ** [adfsproxy-farm-rest.parameters.json][adfsproxy-farm-rest-parameters]**. O script utiliza as definições nestes ficheiros para criar o farm de servidores proxy ADFS. 

- ** [virtualNetworkGateway.parameters.json][virtualnetworkgateway-parameters]**. Este ficheiro contém as definições utilizadas para criar o gateway Azure VPN na nuvem utilizada para ligar à rede no local. Deverá modificar o `sharedKey` valor na `connectionsSettings` secção para que correspondam às que o dispositivo VPN no local. Para obter mais informações, consulte o artigo [Implementar uma arquitetura de rede híbrida com o Azure e no local VPN][hybrid-azure-on-prem-vpn].

- ** [dmz private.parameters.json] [ dmz-private-parameters] ** e ** [dmz public.parameters.json ] [ dmz-public-parameters] **. Configurar a estes ficheiros (público) entrado e saídos lados (privados) dos VMs que compõem a DMZ, proteger os servidores na nuvem. Para mais informações sobre estes elementos e a respectiva configuração, consulte o artigo [Implementar uma DMZ entre Azure e a Internet][implementing-a-secure-hybrid-network-architecture-with-internet-access].

- ** [loadBalancer-web.parameters-json][loadBalancer-web-parameters]**, ** [loadBalancer-biz.parameters-json][loadBalancer-biz-parameters]**, e ** [loadBalancer-data.parameters-json][loadBalancer-data-parameters]**. Estes ficheiros de parâmetros contêm as especificações VM para as camadas de acesso web, empresas e dados e configure balanceadores de carga para cada camada. Estes são os VMs que alojam aplicações web e bases de dados e executar as cargas de trabalho de negócio para a organização. Pode modificar os tamanhos e o número de VMs em cada camada de acordo com os requisitos de.

- ** [virtualMachines mgmt.parameters.json][virtualMachines-mgmt-parameters]**. Este ficheiro contém a configuração de salto caixa/gestão VMs. Só é possível conseguir início de sessão e acesso administrativo ao VMs na web, empresas e camadas de dados a partir da caixa de hiperligação. Por predefinição, o script cria uma única *Standard_DS1_v2* VM, mas pode modificar este ficheiro para criar VMs maiores ou adicionais se a carga de trabalho gestão é provável que seja significativo.

## <a name="solution-deployment"></a>Implementação da solução

A solução pressupõe os pré-requisitos seguintes:

- Tem uma subscrição existente do Azure na qual pode criar grupos de recursos.

- Ter transferiu e instalou a compilação mais recente do Azure Powershell. Consulte o artigo [aqui] [ azure-powershell-download] para obter instruções.

Para executar o script que implementar a solução:

1. Mover para uma pasta conveniente no seu computador local e crie as seguintes subpastas:

    - Scripts

    - Scripts/parâmetros

    - Parâmetros/scripts/Onpremise

    - Parâmetros/scripts/Azure

2. Transferir o [Implementar ReferenceArchitecture.ps1] [ solution-script] ficheiro para a pasta de Scripts

3. Transferir o conteúdo da [parâmetros/onpremise] [ on-premises-folder] pasta para a pasta de Scripts/parâmetros/Onpremise:

4. Transferir o conteúdo da [parâmetros/azure] [ azure-folder] pasta para a pasta de Scripts/parâmetros/Azure.

5. Editar o ficheiro de implementar ReferenceArchitecture.ps1 na pasta Scripts e alterar as linhas seguintes para especificar os grupos de recursos que devem ser criados ou utilizados para manter os recursos criados pelo script:

    ```powershell
    # Azure Onpremise Deployments
        $onpremiseNetworkResourceGroupName = "ra-adfs-onpremise-rg"
        
        # Azure ADDS Deployments
        $azureNetworkResourceGroupName = "ra-adfs-network-rg"
        $workloadResourceGroupName = "ra-adfs-workload-rg"
        $securityResourceGroupName = "ra-adfs-security-rg"
        $addsResourceGroupName = "ra-adfs-adds-rg"
        $adfsResourceGroupName = "ra-adfs-adfs-rg"
        $adfsproxyResourceGroupName = "ra-adfs-proxy-rg"
    ```

6. Edite os ficheiros de parâmetro nos parâmetros/Scripts/Onpremise e as pastas de parâmetros/Scripts/Azure. Atualize as referências de grupo de recursos nestes ficheiros para corresponder aos nomes dos grupos de recursos atribuídos a variáveis no ficheiro ReferenceArchitecture.ps1 implementar. A tabela seguinte mostra os ficheiros que parâmetro referenciam o grupo de recursos. Note que os grupos *RT-adfs carga de trabalho rg*, *RT adfs-segurança rg*, *RT-adfs-adiciona-rg*, *RT-adfs adfs rg*e *RT-adfs proxy rg* só são utilizados no script do PowerShell e não ocorrem nos ficheiros do parâmetro.

  	|Grupo de recursos|Ficheiros específicos de parâmetro|
  	|--------------|--------------|
  	|RT-adfs onpremise rg|parameters\onpremise\connection.Parameters.JSON<br /> parameters\onpremise\virtualMachines-ADDS.Parameters.JSON<br />parameters\onpremise\virtualNetwork-ADDS-DNS.Parameters.JSON<br />parameters\onpremise\virtualNetwork.Parameters.JSON<br />parameters\onpremise\virtualNetworkGateway.Parameters.JSON<br />parameters\azure\virtualNetworkGateway.Parameters.JSON
  	|RT adfs-rede rg|parameters\onpremise\connection.Parameters.JSON<br />parameters\azure\dmz-Private.Parameters.JSON<br />parameters\azure\dmz-public.Parameters.JSON<br />parameters\azure\loadBalancer-ADFS.Parameters.JSON<br />parameters\azure\loadBalancer-adfsproxy.Parameters.JSON<br />parameters\azure\loadBalancer-Biz.Parameters.JSON<br />parameters\azure\loadBalancer-data.Parameters.JSON<br />parameters\azure\loadBalancer-Web.Parameters.JSON<br />parameters\azure\virtualMachines-ADDS.Parameters.JSON<br />parameters\azure\virtualMachines-Mgmt.Parameters.JSON<br />parameters\azure\virtualNetwork-with-OnPremise-and-Azure-DNS.Parameters.JSON<br />parameters\azure\virtualNetwork.Parameters.JSON<br />parameters\azure\virtualNetworkGateway.Parameters.JSON (*duas ocorrências*)

    Para além disso, defina a configuração para o local e na nuvem componentes, conforme descrito na secção [componentes da solução] [componentes da solução].

7. Abrir uma janela do Azure PowerShell, mover para a pasta de Scripts e execute o seguinte comando:

    ```powershell
    .\Deploy-ReferenceArchitecture.ps1 <subscription id> <location> <mode>
    ```

    Substituir `<subscription id>` com o seu ID de subscrição Azure.

    Para `<location>`, especifique uma região Azure, tal como `eastus` ou `westus`.

    O `<mode>` parâmetro pode ter um dos seguintes valores:

    - `Onpremise`, para criar o simulada ambiente no local.

    - `Infrastructure`, para criar a infraestrutura de VNet e saltar caixa na nuvem.

    - `CreateVpn`, para criar o gateway de rede virtual Azure e ligá-lo à rede no local.

    - `AzureADDS`, para construir VMs serve como adiciona servidores, implementar o Active Directory para estes VMs e criar o domínio na nuvem.

    - `AdfsVm`Para criar o VMs ADFS e associá-los ao domínio na nuvem.

    - `ProxyVm`Para criar o proxy ADFS VMs e associá-los ao domínio na nuvem.

    - `Prepare`, executa a todas as tarefas acima. **Este é a opção recomendada se estiver a criar uma implementação completamente nova e não tiver uma infraestrutura no local existente.**

    >[AZURE.NOTE] Também pode executar o script com um `<mode>` parâmetro da `Workload` para criar a web, empresas e camadas de dados VMs e de rede. Esta configuração não está incluída como parte da `Prepare` modo.

    Se utilizar o `Prepare` opção, o script demora várias horas para concluir e acabamentos com a mensagem *preparação estar concluído. Instale o certificado de todos os ADFS e proxy VMs.*

8.  Reinicie a caixa de hiperligação (*RT adfs-gestão vm1* no grupo *RT adfs-segurança rg* ) para permitir que as definições de DNS entre em vigor.

9.  [Obter um certificado SSL para ADFS] [ adfs_certificates] e instalar o este certificado VMs o ADFS. Tenha em atenção que se possa ligar à VMs ADFS através da caixa de hiperligação. Os endereços IP são *10.0.5.4* e *10.0.5.5*. O nome de utilizador predefinido é *contoso\testuser* com palavra-passe *AweSome@PW*.

    >[AZURE.NOTE] Os comentários no script implementar ReferenceArchitecture.ps1 neste momento fornecem instruções detalhadas sobre como criar um certificado autoassinado teste e autoridade utilizando o `makecert` comando. No entanto, não utilize os certificados gerados pelo makecert num ambiente de produção.

10. Execute o seguinte comando do Powershell para configurar o farm de servidores ADFS:

    ```powershell
    .\Deploy-ReferenceArchitecture.ps1 <subscription id> <location> Adfs
    ```

11. Na caixa hiperligação, navegue até à *https://adfs.contoso.com/adfs/ls/idpinitiatedsignon.htm* para testar a instalação de ADFS (poderá receber um aviso de certificado, pode ignorar para este teste). Confirme que a página de início de sessão no Contoso Corporation aparece. Iniciar sessão como *contoso\testuser* com palavra-passe *AweS0me@PW*.

12. Instale o certificado SSL no ADFS proxy VMs. Os endereços IP são *10.0.6.4* e *10.0.6.5*.

13. Execute o seguinte comando do Powershell para configurar o primeiro servidor de proxy ADFS:

    ```powershell
    .\Deploy-ReferenceArchitecture.ps1 <subscription id> <location> Proxy1
    ```

14. Siga as instruções apresentadas pelo script para testar a instalação do primeiro servidor de proxy ADFS.

15. Execute o seguinte comando do Powershell para configurar o segundo servidor de proxy ADFS primeiro:

    ```powershell
    .\Deploy-ReferenceArchitecture.ps1 <subscription id> <location> Proxy2
    ```

16. Siga as instruções apresentadas pelo script para testar a configuração do proxy ADFS concluída.

## <a name="next-steps"></a>Próximos passos

- [Azure Active Directory][aad].

- [Azure Active Directory B2C][aadb2c].

<!-- links -->

[vm-recommendations]: ./guidance-compute-single-vm.md#Recommendations
[naming-conventions]: ./guidance-naming-conventions.md
[implementing-active-directory]: ./guidance-identity-adds-extend-domain.md
[resource-manager-overview]: ../azure-resource-manager/resource-group-overview.md
[implementing-a-secure-hybrid-network-architecture]: ./guidance-iaas-ra-secure-vnet-hybrid.md
[implementing-a-secure-hybrid-network-architecture-with-internet-access]: ./guidance-iaas-ra-secure-vnet-dmz.md
[DRS]: https://technet.microsoft.com/library/dn280945.aspx
[where-to-place-an-fs-proxy]: https://technet.microsoft.com/library/dd807048.aspx
[ADDRS]: https://technet.microsoft.com/library/dn486831.aspx
[plan-your-adfs-deployment]: https://msdn.microsoft.com/library/azure/dn151324.aspx
[ad_network_recommendations]: #network_configuration_recommendations_for_AD_DS_VMs
[domain_and_forests]: https://technet.microsoft.com/library/cc759073(v=ws.10).aspx
[adfs_certificates]: https://technet.microsoft.com/library/dn781428(v=ws.11).aspx
[create_service_account_for_adfs_farm]: https://technet.microsoft.com/library/dd807078.aspx
[import_server_authentication_certificate]: https://technet.microsoft.com/library/dd807088.aspx
[adfs-configuration-database]: https://technet.microsoft.com/en-us/library/ee913581(v=ws.11).aspx
[active-directory-federation-services]: https://technet.microsoft.com/windowsserver/dd448613.aspx
[security-considerations]: #security-considerations
[recommendations]: #recommendations
[claims-aware applications]: https://msdn.microsoft.com/en-us/library/windows/desktop/bb736227(v=vs.85).aspx
[active-directory-federation-services-overview]: https://technet.microsoft.com/en-us/library/hh831502(v=ws.11).aspx
[establishing-federation-trust]: https://blogs.msdn.microsoft.com/alextch/2011/06/27/establishing-federation-trust/
[Deploying_a_federation_server_farm]: https://technet.microsoft.com/library/dn486775.aspx
[install_and_configure_the_web_application_proxy_server]: https://technet.microsoft.com/library/dn383662.aspx
[publish_applications_using_AD_FS_preauthentication]: https://technet.microsoft.com/library/dn383640.aspx
[managing-adfs-components]: https://technet.microsoft.com/library/cc759026.aspx
[oms-adfs-pack]: https://www.microsoft.com/download/details.aspx?id=41184
[azure-powershell-download]: https://azure.microsoft.com/documentation/articles/powershell-install-configure/
[aad]: https://azure.microsoft.com/documentation/services/active-directory/
[aadb2c]: https://azure.microsoft.com/documentation/services/active-directory-b2c/
[adfs-intro]: ../active-directory/active-directory-aadconnect-azure-adfs.md
[solution-script]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adfs/Deploy-ReferenceArchitecture.ps1
[on-premises-folder]: https://github.com/mspnp/reference-architectures/tree/master/guidance-identity-adfs/parameters/onpremise
[on-premises-vnet-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adfs/parameters/onpremise/virtualNetwork.parameters.json
[on-premises-virtualmachines-adds-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adfs/parameters/onpremise/virtualMachines-adds.parameters.json
[on-premises-virtualnetworkgateway-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adfs/parameters/onpremise/virtualNetworkGateway.parameters.json
[on-premises-connection-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adfs/parameters/onpremise/connection.parameters.json
[azure-folder]: https://github.com/mspnp/reference-architectures/tree/master/guidance-identity-adfs/parameters/azure
[vnet-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adfs/parameters/azure/virtualNetwork.parameters.json
[dmz-private-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adfs/parameters/azure/dmz-private.parameters.json
[dmz-public-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adfs/parameters/azure/dmz-public.parameters.json
[virtualnetworkgateway-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adfs/parameters/azure/virtualNetworkGateway.parameters.json
[hybrid-azure-on-prem-vpn]: ./guidance-hybrid-network-vpn.md
[virtualmachines-adds-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adfs/parameters/azure/virtualMachines-adds.parameters.json
[extending-ad-to-azure]: ./guidance-identity-adds-extend-domain.md
[loadbalancer-adfs-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adfs/parameters/azure/loadBalancer-adfs.parameters.json
[add-adds-domain-controller-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adfs/parameters/azure/add-adds-domain-controller.parameters.json
[gmsa-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adfs/parameters/azure/gmsa.parameters.json
[adfs-farm-first-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adfs/parameters/azure/adfs-farm-first.parameters.json
[adfs-farm-rest-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adfs/parameters/azure/adfs-farm-rest.parameters.json
[adfs-farm-domain-join-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adfs/parameters/azure/adfs-farm-domain-join.parameters.json
[loadBalancer-web-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adfs/parameters/azure/loadBalancer-web.parameters.json
[loadBalancer-biz-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adfs/parameters/azure/loadBalancer-biz.parameters.json
[loadBalancer-data-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adfs/parameters/azure/loadBalancer-data.parameters.json
[virtualMachines-mgmt-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adfs/parameters/azure/virtualMachines-mgmt.parameters.json
[loadBalancer-adfsproxy-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adfs/parameters/azure/loadBalancer-adfsproxy.parameters.json
[adfsproxy-farm-first-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adfs/parameters/azure/adfsproxy-farm-first.parameters.json
[adfsproxy-farm-rest-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adfs/parameters/azure/adfsproxy-farm-rest.parameters.json
[0]: ./media/guidance-iaas-ra-secure-vnet-adfs/figure1.png "Seguro arquitetura de rede híbrida com o Active Directory"
