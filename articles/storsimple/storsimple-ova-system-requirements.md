<properties
   pageTitle="Requisitos de sistema StorSimple Virtual matriz"
   description="Saiba mais sobre o software e os requisitos de funcionamento em rede para sua matriz de Virtual StorSimple"
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="carmonm"
   editor=""/>

<tags
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="10/17/2016"
   ms.author="alkohli"/>

# <a name="storsimple-virtual-array-system-requirements"></a>Requisitos de sistema StorSimple Virtual matriz

## <a name="overview"></a>Descrição geral

Este artigo descreve os requisitos de sistema importantes para o Microsoft Azure StorSimple Virtual matriz (também conhecido como StorSimple no local virtual dispositivo ou StorSimple virtual) e para os clientes de armazenamento aceder a matriz. Recomendamos reveja as informações com atenção antes de implementar o seu sistema de StorSimple e, em seguida, volte a consultá-lo conforme necessário durante a implementação e operação subsequente.

Os requisitos de sistema incluem:

-   **Requisitos de software para os clientes de armazenamento** - descreve as plataformas suportadas virtualização, os browsers, iniciadores iSCSI, SMB clientes, requisitos mínimos dispositivo virtual e quaisquer requisitos adicionais para esses sistemas operativos.

-   **Requisitos para o dispositivo StorSimple de rede** - fornece informações sobre as portas que precisam de estar abertos na sua firewall para permitir para iSCSI, na nuvem ou tráfego de gestão.

As informações de requisitos de sistema StorSimple publicadas neste artigo aplica-se apenas ao StorSimple Virtual matrizes.

- Para dispositivos 8000 série, consulte [requisitos de sistema para o seu dispositivo de série StorSimple 8000](storsimple-system-requirements.md).
 
- Para 7000 dispositivos de série, consulte [requisitos de sistema para o seu dispositivo de série de 5000 7000 StorSimple](http://onlinehelp.storsimple.com/1_StorSimple_System_Requirements).


## <a name="software-requirements"></a>Requisitos de software

Os requisitos de software incluem informações sobre os browsers suportados, versões da SMB, plataformas Virtualização e os requisitos mínimos dispositivo virtual.

### <a name="supported-virtualization-platforms"></a>Plataformas suportadas Virtualização


| **Hipervisor** | **Versão**                          |
|----------------|--------------------------------------|
| Hyper-V        | Windows Server 2008 R2 SP1 ou posterior |
| VMware ESXi    | 5,5 e posterior                        |

### <a name="virtual-device-requirements"></a>Requisitos de dispositivo virtual

| **Componente**                                | **Requisito de registo**            |
|----------------------------------------------|----------------------------|
| Número mínimo de processadores virtuais (núcleos) | 4                          |
| Memória mínima (RAM)                         | 8 GB                       |
| Espaço de disco<sup>1</sup>                       | Disco SO - 80 GB <br></br>Disco de dados - 500 GB para 8 TB|
| Número mínimo de interfaces de rede       | 1                          |
| Mínimo de largura de banda de Internet<sup>2</sup>       | 5 Mbps                     |

<sup>1</sup> - fino aprovisionado

<sup>2</sup> - requisitos da rede podem variar dependendo da velocidade de alterar dados diárias. Por exemplo, se tem um dispositivo de cópia de segurança de 10 GB ou mais alterações durante um dia, em seguida, a cópia de segurança diária através de uma ligação de Mbps 5 poderá demorar até 4,25 horas (se os dados não podem ser comprimidos ou deduplicated).

### <a name="supported-web-browsers"></a>Browsers suportados

| **Componente**     | **Versão** | **Requisitos de adicional** |
|-------------------|-----------------|-----------------------------------|
| Microsoft Edge    | Versão mais recente  |                                   |
| Internet Explorer | Versão mais recente  | Testado com o Internet Explorer 11  |
| Google Chrome     | Versão mais recente  | Testado com o Chrome 46             |

### <a name="supported-storage-clients"></a>Clientes de armazenamento suportado 

São os seguintes requisitos de software para os iniciadores de iSCSI aceder à sua matriz de Virtual StorSimple (configurada como um servidor iSCSI).

| **Sistemas operativos suportados** | **Versão necessária** | **Requisitos de adicional** |
| --------------------------- | ---------------- | ------------- |
| Windows Server              | 2008R2 SP1, 2012, 2012R2 |StorSimple pode criar pequenos aprovisionada e totalmente aprovisionada volumes. Não consegue criar volumes parcialmente aprovisionados. StorSimple iSCSI volumes só são suportados para: <ul><li>Volumes simples no discos básicos do Windows.</li><li>NTFS do Windows para a formatação de um volume.</li>|


São os seguintes requisitos de software para os clientes SMB aceder à sua matriz de Virtual StorSimple (configurada como um servidor de ficheiros).

| **Versão SMB** |
|-------------|
| SMB 2. x     |
| SMB 3.0     |
| SMB 3.02    |

> [AZURE.IMPORTANT] Copiar ou não armazenar ficheiros protegidos por Windows ficheiro (EFS sistema) ao servidor de ficheiros StorSimple Virtual matriz; Isto resultará numa configuração não suportada. 

## <a name="networking-requirements"></a>Requisitos de rede 

A tabela seguinte lista as portas que precisam de ser aberto no seu firewall para permitir iSCSI, SMB, na nuvem ou tráfego de gestão. Nesta tabela, *no* ou *entrada* refere-se a direção a partir do qual pedidos recebidos de cliente aceder ao seu dispositivo. *Saída* ou *saída* que se refere a direção na qual o seu dispositivo StorSimple envia dados externamente, para além de implementação: por exemplo, saído à Internet.

| **Porta n. º<sup>1</sup>** | **Ou reduzir** | **Âmbito de porta** | **Obrigatório**              | **Notas**                                                                                                            |
|--------------------------|---------------|----------------|---------------------------|----------------------------------------------------------------------------------------------------------------------|
| TCP 80 (HTTP)            | Saída           | WAN            | N                        | Porta de saída é utilizada para aceder à Internet para obter atualizações. <br></br>O proxy de saída web é configuráveis pelo utilizador. |
| TCP 443 (HTTPS)          | Saída           | WAN            | Sim                       | Porta de saída é utilizada para aceder aos dados na nuvem. <br></br>O proxy de saída web é configuráveis pelo utilizador. |
| UDP 53 (DNS)             | Saída           | WAN            | Em alguns casos; ver as notas. | Esta porta é necessária apenas se estiver a utilizar um servidor de DNS baseados na Internet. <br></br> **Nota**: Se implementar o servidor de ficheiros, recomendamos que utilize o servidor de DNS local.|
| UDP 123 (PTR)            | Saída           | WAN            | Em alguns casos; ver as notas. | Esta porta é necessária apenas se estiver a utilizar um servidor NTP baseados na Internet.<br></br> **Nota:** Se implementar o servidor de ficheiros, recomendamos que a sincronização de tempo com os controladores de domínio do Active Directory.  |
| TCP 80 (HTTP)           | No            | LAN            | Sim                       | Esta é a porta de entrada para IU local no dispositivo StorSimple para a gestão local. <br></br> **Nota**: aceder a IU local através de HTTP irá redirecionar automaticamente para HTTPS.|
| TCP 443 (HTTPS)          | No            | LAN            | Sim                       | Esta é a porta de entrada para IU local no dispositivo StorSimple para a gestão local.|
| TCP 3260 (iSCSI)         | No            | LAN            | N                        | Esta porta é utilizada para aceder aos dados ao longo do iSCSI.|

<sup>1</sup> não portas de entrada tem de ser aberto na Internet pública.

> [AZURE.IMPORTANT] Certifique-se de que a firewall não modificar ou desencriptar qualquer tráfego SSL entre o dispositivo de StorSimple e Azure.


### <a name="url-patterns-for-firewall-rules"></a>Padrões de URL para regras de firewall 

Os administradores de rede com frequência podem configurar regras de firewall avançadas com base nos padrões de URL para filtrar a entrada e o tráfego de saída. A matriz virtual e o serviço do Gestor de StorSimple dependem das outras aplicações do Microsoft como Azure Service Bus, controlo de acesso do Azure Active Directory, contas de armazenamento e servidores do Microsoft Update. Os padrões de URL associados estas aplicações podem ser utilizados para configurar regras de firewall. É importante compreender o que podem alterar os padrões de URL associados estas aplicações. Isto sucessivamente necessitam o administrador de rede monitorizar e atualizar regras de firewall para o seu StorSimple como e quando for necessário. 

Recomendamos que o utilizador defina as regras de firewall para o tráfego de saída, com base em StorSimple fixada liberally endereços IP, na maioria dos casos. No entanto, pode utilizar as informações abaixo para definir regras de firewall avançadas que são necessários para criar ambientes seguros.

> [AZURE.NOTE] 
> 
> - O dispositivo (origem) IPs deve ser sempre definido para todas as interfaces de rede com capacidade de nuvem. 
> - O destino que IPs deve ser definido como [intervalos IP do Centro de dados Azure](https://www.microsoft.com/en-us/download/confirmation.aspx?id=41653).


| Padrão de URL                                                      | Funcionalidade do componente                                           |
|------------------------------------------------------------------|---------------------------------------------------------------|
| `https://*.storsimple.windowsazure.com/*`<br>`https://*.accesscontrol.windows.net/*`<br>`https://*.servicebus.windows.net/*`   | Serviço do Gestor de StorSimple<br>Serviço de controlo de acesso<br>Serviço Azure Bus|
|`http://*.backup.windowsazure.com`|Registo de dispositivo|
|`http://crl.microsoft.com/pki/*`<br>`http://www.microsoft.com/pki/*`|De revogação de certificados |
| `https://*.core.windows.net/*`<br>`https://*.data.microsoft.com`<br>`http://*.msftncsi.com` | Contas do Azure armazenamento e de monitorização |
| `http://*.windowsupdate.microsoft.com`<br>`https://*.windowsupdate.microsoft.com`<br>`http://*.update.microsoft.com`<br> `https://*.update.microsoft.com`<br>`http://*.windowsupdate.com`<br>`http://download.microsoft.com`<br>`http://wustat.windows.com`<br>`http://ntservicepack.microsoft.com`| Servidores do Microsoft Update<br>                        |
| `http://*.deploy.akamaitechnologies.com`                         |Akamai CDN |
| `https://*.partners.extranet.microsoft.com/*`                    | Pacote de suporte                                                  |
| `http://*.data.microsoft.com `                   | Serviço de telemetria no Windows, consulte a [atualização para a experiência do cliente e diagnóstico telemetria](https://support.microsoft.com/en-us/kb/3068708)                                                  |

## <a name="next-step"></a>Passo seguinte

-   [Preparar o portal para implementar a sua matriz de Virtual StorSimple](storsimple-ova-deploy1-portal-prep.md)
