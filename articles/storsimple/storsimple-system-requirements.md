<properties
   pageTitle="Requisitos de sistema StorSimple | Microsoft Azure"
   description="Descreve o software, redes e requisitos de elevada disponibilidade e melhores práticas para obter uma solução do Microsoft Azure StorSimple."
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
   ms.workload="TBD"
   ms.date="08/31/2016"
   ms.author="alkohli"/>

# <a name="storsimple-software-high-availability-and-networking-requirements"></a>StorSimple requisitos de software, elevada disponibilidade e redes

## <a name="overview"></a>Descrição geral

Bem-vindo ao Microsoft Azure StorSimple. Este artigo descreve os requisitos de sistema importantes e as melhores práticas para o seu dispositivo StorSimple e para os clientes de armazenamento aceder ao dispositivo. Recomendamos reveja as informações com atenção antes de implementar o seu sistema de StorSimple e, em seguida, volte a consultá-lo conforme necessário durante a implementação e operação subsequente.

Os requisitos de sistema incluem:

- **Requisitos de software para os clientes de armazenamento** - descreve os sistemas operativos suportados e quaisquer requisitos adicionais para esses sistemas operativos.
- **Requisitos para o dispositivo StorSimple de rede** - fornece informações sobre as portas que precisam de estar abertos na sua firewall para permitir para iSCSI, na nuvem ou tráfego de gestão.
- **Requisitos de elevada disponibilidade para StorSimple** - descreve os requisitos de elevada disponibilidade e as melhores práticas para o seu computador de dispositivo e anfitrião StorSimple. 


## <a name="software-requirements-for-storage-clients"></a>Requisitos de software para os clientes de armazenamento

São os seguintes requisitos de software para os clientes de armazenamento aceder ao seu dispositivo StorSimple.

| Sistemas operativos suportados | Versão necessária | Requisitos de adicional |
| --------------------------- | ---------------- | ------------- |
| Windows Server              | 2008R2 SP1, 2012, 2012R2 |StorSimple iSCSI volumes são suportados para utilização no apenas os tipos de disco de Windows seguintes:<ul><li>Volume simples no disco básico</li><li>Volume Simple e espelhado no disco dinâmico</li></ul>Funcionalidades ODX e Windows Server 2012 fina aprovisionamento são suportadas se estiver a utilizar um volume de iSCSI StorSimple.<br><br>StorSimple pode criar pequenos aprovisionada e totalmente aprovisionada volumes. Não consegue criar volumes parcialmente aprovisionados.<br><br>Reformatar um pouco aprovisionado volume poderá demorar muito tempo. Recomendamos que o volume de eliminar e, em seguida, criar uma nova em vez de reformatar. No entanto, se, ainda assim prefere reformatar um volume:<ul><li>Execute o seguinte comando antes da reformatar para evitar atrasos de valorização de espaço: <br>`fsutil behavior set disabledeletenotify 1`</br></li><li>Depois da formatação estiver concluída, utilize o seguinte comando para voltar a ativar valorização de espaço:<br>`fsutil behavior set disabledeletenotify 0`</br></li><li>Aplique a correcção do Windows Server 2012 conforme descrito no [KB 2878635](https://support.microsoft.com/kb/2870270) para o seu computador Windows Server.</li></ul></li></ul></ul> Se estiver a configurar o Gestor de instantâneo StorSimple ou StorSimple adaptador para o SharePoint, aceda a [requisitos de Software para componentes opcionais](#software-requirements-for-optional-components).|
| VMWare ESX | 5.5 e 6.0 | Suportado com VMWare vSphere como iSCSI cliente. Funcionalidade de bloco VAAI é suportada com VMware vSphere em StorSimple dispositivos.
| Linux RHEL/CentOS | 5, 6 e 7. | Suporte para Linux iSCSI clientes com versões do iniciador de abrir iSCSI 5, 6 e 7. |
| Linux | SUSE Linux 11 | |
 > [AZURE.NOTE] IBM AIX atualmente não é suportado com StorSimple.

## <a name="software-requirements-for-optional-components"></a>Requisitos de software para componentes opcionais

São os seguintes requisitos de software para os componentes de StorSimple opcionais (StorSimple instantâneo gestor e StorSimple adaptador para o SharePoint).

| Componente | Plataforma do anfitrião | Requisitos de adicional |
| --------------------------- | ---------------- | ------------- |
| Gestor de instantâneo StorSimple | Windows Server 2008R2 SP1, 2012, 2012R2 | Utilização do StorSimple instantâneo gestor no Windows Server é necessária para cópia de segurança/restauro de discos dinâmicos espelhados e para qualquer aplicação consistentes cópias de segurança.<br> Gestor de instantâneo StorSimple só é suportado em Windows Server 2008 R2 SP1 (64 bits), Windows 2012 R2 e Windows Server 2012.<ul><li>Se estiver a utilizar janela Server 2012, tem de instalar .NET 3.5 – 4,5 antes de instalar o Gestor de instantâneo StorSimple.</li><li>Se estiver a utilizar o Windows Server 2008 R2 SP1, tem de instalar o Windows Management Framework 3.0 antes de instalar o Gestor de instantâneo StorSimple.</li></ul> |
| Placa StorSimple para o SharePoint | Windows Server 2008R2 SP1, 2012, 2012R2 |<ul><li>Placa StorSimple do SharePoint só é suportada no SharePoint 2010 e do SharePoint 2013.</li><li>RBS requer o SQL Server Enterprise Edition, versão 2008 R2 ou 2012.</li></ul>|

## <a name="networking-requirements-for-your-storsimple-device"></a>Requisitos de funcionamento em rede para o seu dispositivo StorSimple

Dispositivo StorSimple é um dispositivo bloqueado para baixo. No entanto, portas tem de ser aberto no firewall para permitir o tráfego de gestão, na nuvem e iSCSI. A tabela seguinte lista as portas que precisam de ser aberto na firewall. Nesta tabela, *no* ou *entrada* refere-se a direção a partir do qual pedidos recebidos de cliente aceder ao seu dispositivo. *Saída* ou *saída* que se refere a direção na qual o seu dispositivo StorSimple envia dados externamente, para além de implementação: por exemplo, saído à Internet.

| Porta n. º<sup>1,2</sup> | Ou reduzir | Âmbito de porta | Obrigatório | Notas |
|------------------------|-----------|------------|----------|-------|
|TCP 80 (HTTP)<sup>3</sup>|  Saída |  WAN | N |<ul><li>Porta de saída é utilizada para aceder à Internet para obter atualizações.</li><li>O proxy de saída web é configuráveis pelo utilizador.</li><li>Para permitir que as atualizações do sistema, esta porta também têm de ser aberta para que o controlador de fixa IPs.</li></ul> |
|TCP 443 (HTTPS)<sup>3</sup>| Saída | WAN | Sim |<ul><li>Porta de saída é utilizada para aceder aos dados na nuvem.</li><li>O proxy de saída web é configuráveis pelo utilizador.</li><li>Para permitir que as atualizações do sistema, esta porta também têm de ser aberta para que o controlador de fixa IPs.</li><li>Esta porta também é utilizada em ambos os controladores de para recolha de lixo.</li></ul>|
|UDP 53 (DNS) | Saída | WAN | Em alguns casos; ver as notas. |Esta porta é necessária apenas se estiver a utilizar um servidor de DNS baseados na Internet. |
| UDP 123 (PTR) | Saída | WAN | Em alguns casos; ver as notas. |Esta porta é necessária apenas se estiver a utilizar um servidor NTP baseados na Internet. |
| TCP 9354 | Saída | WAN | Sim |Porta de saída é utilizada pelo dispositivo StorSimple para comunicar com o serviço do Gestor de StorSimple. |
| 3260 (iSCSI) | No | LAN | N | Esta porta é utilizada para aceder aos dados ao longo do iSCSI.|
| 5985 | No | LAN | N | Porta de entrada é utilizada pelo StorSimple instantâneo gestor para comunicar com o dispositivo StorSimple.<br>Esta porta também é utilizada quando remotamente ligar ao Windows PowerShell para StorSimple através de HTTP. |
| 5986 | No | LAN | N | Esta porta é utilizada quando remotamente ligar ao Windows PowerShell para StorSimple por HTTPS. |

<sup>1</sup> não portas de entrada tem de ser aberto na Internet pública.

<sup>2</sup> se múltiplas portas executar uma configuração de gateway, a ordem de tráfego de saída encaminhada será determinada com base na ordem de encaminhamento de porta descrito no [Encaminhamento de porta](#routing-metric), abaixo.

<sup>3</sup> o controlador de fixa IPs no seu dispositivo StorSimple tem de ser conseguir ligar à Internet e encaminháveis. Os endereços IP fixos são utilizados para servir as atualizações para o dispositivo. Se os controladores do dispositivo não é possível ligar à Internet através de IPs fixo, não conseguir atualizar o seu dispositivo StorSimple.

> [AZURE.IMPORTANT] Certifique-se de que a firewall não modificar ou desencriptar qualquer tráfego SSL entre o dispositivo de StorSimple e Azure.

### <a name="url-patterns-for-firewall-rules"></a>Padrões de URL para regras de firewall

Os administradores de rede com frequência podem configurar regras de firewall avançadas com base nos padrões de URL para filtrar a entrada e o tráfego de saída. Dispositivo StorSimple e o serviço do Gestor de StorSimple dependem das outras aplicações do Microsoft como Azure Service Bus, controlo de acesso do Azure Active Directory, contas de armazenamento e servidores do Microsoft Update. Os padrões de URL associados estas aplicações podem ser utilizados para configurar regras de firewall. É importante compreender o que podem alterar os padrões de URL associados estas aplicações. Isto sucessivamente necessitarão o administrador de rede monitorizar e atualizar regras de firewall para o seu StorSimple como e quando for necessário.

Recomendamos que o utilizador defina as regras de firewall para o tráfego de saída, com base em StorSimple fixada liberally endereços IP, na maioria dos casos. No entanto, pode utilizar as informações abaixo para definir regras de firewall avançadas que são necessários para criar ambientes seguros.

> [AZURE.NOTE] O dispositivo (origem) IPs deve ser sempre definido para todas as interfaces de rede activadas. O destino que IPs deve ser definido como [intervalos IP do Centro de dados Azure](https://www.microsoft.com/en-us/download/confirmation.aspx?id=41653).

#### <a name="url-patterns-for-azure-portal"></a>Padrões de URL para o Azure portal
| Padrão de URL                                                      | Funcionalidade do componente                                           | IPs do dispositivo                           |
|------------------------------------------------------------------|---------------------------------------------------------------|-----------------------------------------|
| `https://*.storsimple.windowsazure.com/*`<br>`https://*.accesscontrol.windows.net/*`<br>`https://*.servicebus.windows.net/*`   | Serviço do Gestor de StorSimple<br>Serviço de controlo de acesso<br>Serviço Azure Bus| Interfaces de rede com capacidade de nuvem        |
|`https://*.backup.windowsazure.com`|Registo de dispositivo| Apenas dados 0|
|`http://crl.microsoft.com/pki/*`<br>`http://www.microsoft.com/pki/*`|De revogação de certificados |Interfaces de rede com capacidade de nuvem |
| `https://*.core.windows.net/*` <br>`https://*.data.microsoft.com`<br>`http://*.msftncsi.com` | Contas de armazenamento Azure e monitorização | Interfaces de rede com capacidade de nuvem        |
| `http://*.windowsupdate.microsoft.com`<br>`https://*.windowsupdate.microsoft.com`<br>`http://*.update.microsoft.com`<br> `https://*.update.microsoft.com`<br>`http://*.windowsupdate.com`<br>`http://download.microsoft.com`<br>`http://wustat.windows.com`<br>`http://ntservicepack.microsoft.com`| Servidores do Microsoft Update<br>                             | Controlador de fixa IPs apenas               |
| `http://*.deploy.akamaitechnologies.com`                         |Akamai CDN |Controlador de fixa IPs apenas   |
| `https://*.partners.extranet.microsoft.com/*`                    | Pacote de suporte                                                  | Interfaces de rede com capacidade de nuvem        |

#### <a name="url-patterns-for-azure-government-portal"></a>Padrões de URL para o portal de administração pública do Azure
| Padrão de URL                                                      | Funcionalidade do componente                                           | IPs do dispositivo                           |
|------------------------------------------------------------------|---------------------------------------------------------------|-----------------------------------------|
| `https://*.storsimple.windowsazure.us/*`<br>`https://*.accesscontrol.usgovcloudapi.net/*`<br>`https://*.servicebus.usgovcloudapi.net/*`   | Serviço do Gestor de StorSimple<br>Serviço de controlo de acesso<br>Serviço Azure Bus| Interfaces de rede com capacidade de nuvem        |
|`https://*.backup.windowsazure.us`|Registo de dispositivo| Apenas dados 0|
|`http://crl.microsoft.com/pki/*`<br>`http://www.microsoft.com/pki/*`|De revogação de certificados |Interfaces de rede com capacidade de nuvem |
| `https://*.core.usgovcloudapi.net/*` <br>`https://*.data.microsoft.com`<br>`http://*.msftncsi.com` | Contas do Azure armazenamento e de monitorização | Interfaces de rede com capacidade de nuvem        |
| `http://*.windowsupdate.microsoft.com`<br>`https://*.windowsupdate.microsoft.com`<br>`http://*.update.microsoft.com`<br> `https://*.update.microsoft.com`<br>`http://*.windowsupdate.com`<br>`http://download.microsoft.com`<br>`http://wustat.windows.com`<br>`http://ntservicepack.microsoft.com`| Servidores do Microsoft Update<br>                             | Controlador de fixa IPs apenas               |
| `http://*.deploy.akamaitechnologies.com`                         |Akamai CDN |Controlador de fixa IPs apenas   |
| `https://*.partners.extranet.microsoft.com/*`                    | Pacote de suporte                                                  | Interfaces de rede com capacidade de nuvem        |

### <a name="routing-metric"></a>Métrica de encaminhamento

Métrica de encaminhamento está associada com as interfaces e o gateway que encaminhar os dados para as redes especificadas. Métrica de encaminhamento é utilizada pelo protocolo de encaminhamento para calcular o melhor caminho para um determinado destino, se-aprende que existirem vários caminhos para o mesmo destino. No canto inferior a métrica do encaminhamento, quanto maior a preferência.

No contexto de StorSimple, se várias interfaces de rede e gateways estiverem configurados para o tráfego de canais, métricas de encaminhamento irão entrar em Reproduzir para determinar a sequência relativa nos quais as interfaces irão obter utilizadas. Métricas de encaminhamento não podem ser alteradas pelo utilizador. No entanto pode utilizar o `Get-HcsRoutingTable` cmdlet para imprimir a tabela encaminhamento (e métricas) no seu dispositivo StorSimple. Mais informações sobre o cmdlet Get-HcsRoutingTable na [implementação StorSimple resolução de problemas](storsimple-troubleshoot-deployment.md).

Encaminhamento dos algoritmos métricos são diferentes consoante a versão do software em execução no seu dispositivo StorSimple.

**Lançamentos anteriores a 1 de actualização**

Isto inclui versões de software antes de atualização 1 como das versões DG, 0,1, 0,2 ou 0,3 lançamento. A ordem com base em métricas de encaminhamento é da seguinte forma:

   *Última configurado 10 interface da rede GbE > interface da rede de outros 10 GbE > última configurado 1 interface da rede GbE > interface da rede outras GbE de 1*


**Versões de partida entre 1 de atualização e antes de Update 2**

Isto inclui versões de software tal como 1, 1.1 ou 1.2. A ordem com base em métricas de encaminhamento for decidida da seguinte forma:

   *DADOS 0 > última configurado 10 interface da rede GbE > interface da rede de outros 10 GbE > última configurado 1 interface da rede GbE > interface da rede outras GbE de 1*

   Na atualização 1, é estabelecida a métrica do encaminhamento de dados 0 mais baixos; Por conseguinte, é encaminhado todo o nuvem-tráfego através dos dados 0. Se existirem mais do que uma interface de rede com capacidade de nuvem no seu dispositivo StorSimple, tome nota do presente.


**Lançamentos começando Update 2**

Actualização 2 tem várias melhorias ao nível relacionados com o funcionamento em rede e métricas de encaminhamento foi alterado. O comportamento pode ser explicado da seguinte forma.

- Um conjunto de valores predeterminados foram atribuídas para interfaces de rede.   

- Considere a hipótese de uma tabela de exemplo apresentada abaixo, com valores atribuídos a várias interfaces de rede quando se encontram na nuvem-ativadas ou desativado na nuvem, mas com um gateway configurado. Tenha em atenção os valores atribuídos aqui estão apenas valores de exemplo.


  	| Interface da rede | Preparados na nuvem | Nuvem-disabled com gateway |
  	|-----|---------------|---------------------------|
  	| Dados 0  | 1            | -                        |
  	| Dados 1  | 2            | 20                       |
  	| Dados 2  | 3            | 30                       |
  	| Dados 3  | 4            | 40                       |
  	| Dados 4  | 5            | 50                       |
  	| Dados 5  | 6            | 60                       |


- A ordem pela qual o tráfego de nuvem será encaminhado através das interfaces de rede é:

    *Dados 0 > dados 1 > data 2 > dados 3 > dados 4 > dados 5*

    Isto pode ser explicado pelo exemplo seguinte.

    Considere a hipótese de um dispositivo StorSimple com duas interfaces de rede com capacidade de nuvem, dados 0 e 5 de dados. Dados de 1 a 4 de dados são nuvem-disabled mas tem um gateway configurado. A ordem pela qual o tráfego vai ser encaminhado para este dispositivo será:

    *Dados de 0 (1) > dados 5 (6) > dados 1 (20) > dados 2 (30) > dados 3 (40) > dados 4 (50)*

    *onde os números entre parênteses indicam as respetivas métricas de encaminhamento.*

    Se falhar dados 0, o tráfego de nuvem irá reencaminhado a 5 de dados. Dado que é configurado um gateway outra rede se dados 0 e 5 de dados foram falha, o tráfego de nuvem subscreve através de dados 1.


- Se uma interface de rede com capacidade de nuvem falhar, em seguida, são 3 tentativas com um atraso de segundo 30 para ligar para a interface. Se todas as repetições falharem, o tráfego é encaminhado para a seguinte disponível com capacidade de nuvem interface conforme determinado pela tabela de encaminhamento. Se todas as com capacidade de nuvem da rede interfaces falhas, em seguida, o dispositivo irá falhar ao longo para o controlador (sem reinicialização neste caso).

- Se existir uma falha de VIP para uma interface de rede com capacidade de iSCSI, vai ser 3 tentativas com um atraso de 2 segundos. Este comportamento tiver estado a mesma a partir de versões anteriores. Se todas as interfaces de rede do iSCSI falharem, irá ocorrer uma activação de pós-falha de controlador (acompanhado de reiniciar o computador).


- Um alerta também é elevado no seu dispositivo StorSimple quando existe uma falha de VIP. Para obter mais informações, aceda ao [alerta de referência rápida](storsimple-manage-alerts.md).

- Em termos tentativas, iSCSI terá precedência sobre na nuvem.

    Considere o seguinte exemplo: StorSimple de um dispositivo tem duas interfaces de rede ativadas, os dados 0 e 1 de dados. Dados 0 são a nuvem com capacidade de Considerando que dados 1 é ambos os nuvem e com capacidade de iSCSI. Outras interfaces de rede neste dispositivo estão ativados para a nuvem ou iSCSI.

    Se falha de dados 1, tendo em conta-lo a interface de rede iSCSI última, isto irá resultar num controlador activação pós-falha a 1 de dados no outro controlador de.


### <a name="networking-best-practices"></a>Melhores práticas de rede

Para além dos requisitos de rede acima, para um desempenho ideal da sua solução StorSimple, consulte adira as melhores práticas seguintes:

- Certifique-se de que o seu dispositivo StorSimple tem uma largura de banda Mbps dedicada 40 (ou mais) disponível sempre. Não deve ser partilhada esta largura de banda (ou atribuição deve ser garante através da utilização de políticas QoS) com quaisquer outras aplicações.

- Certifique-se à Internet a conectividade da rede está disponível sempre. Esporádica, não fiáveis ou ligações à Internet para os dispositivos, incluindo sem ligação à Internet qualquer, irão resultar numa configuração não suportada.

- Isole o tráfego iSCSI e nuvem por ter dedicado interfaces de rede no seu dispositivo de acesso iSCSI e na nuvem. Para obter mais informações, consulte o artigo como [Modificar interfaces de rede](storsimple-modify-device-config.md#modify-network-interfaces) no seu dispositivo StorSimple.

- Não utilize uma configuração de protocolo de controlo de agregação de ligação (LACP) para interfaces de rede. Esta é uma configuração não suportada.


## <a name="high-availability-requirements-for-storsimple"></a>Requisitos de elevada disponibilidade para StorSimple

A plataforma de hardware que está incluída na solução StorSimple tem funcionalidades de disponibilidade e fiabilidade que fornecem um foundation para uma infraestrutura de armazenamento disponível altamente, tolerância a falhas no seu centro de dados. No entanto, existem requisitos e as melhores práticas que devem cumprir para ajudar a garantir a disponibilidade da sua solução StorSimple. Antes de implementar StorSimple, reveja cuidadosamente as seguintes requisitos e práticas recomendadas para o dispositivo de StorSimple e computadores de anfitrião ligado.

Para mais informações sobre a monitorização e manutenção os componentes de hardware do seu dispositivo StorSimple, aceda a [utilizar o serviço do Gestor de StorSimple para monitorizar a componentes de hardware e o estado](storsimple-monitor-hardware-status.md) e [substituição de componente de hardware StorSimple](storsimple-hardware-component-replacement.md).

### <a name="high-availability-requirements-and-procedures-for-your-storsimple-device"></a>Requisitos de elevada disponibilidade e procedimentos para o seu dispositivo StorSimple

Reveja as informações seguintes cuidadosamente para garantir a disponibilidade do seu dispositivo StorSimple alta.

#### <a name="pcms"></a>PCMs

Dispositivos de StorSimple incluem power quente mudança, redundante e refrigeração módulos (PCMs). Cada PCM tem capacidade suficiente para fornecer suporte para o quadro inteiro. Para garantir a disponibilidade de alta, ambos os PCMs tem de estar instalados.

- Ligar o seu PCMs a origens de energia diferente para fornecer disponibilidade se uma fonte de alimentação falhar.
- Se um PCM falhar, pedir substituição imediatamente.
- Remova um falhado PCM apenas quando tiver a substituição e estiver pronto para o instalar.
- Não remove ambas as PCMs em simultâneo. O módulo PCM inclui o módulo carregador de cópia de segurança. Remover ambos os PCMs irá resultar num encerramento sem proteção de baterias e o estado do dispositivo não será guardado. Para mais informações sobre a baterias, vá para [manter o módulo carregador de cópia de segurança](storsimple-battery-replacement.md#maintain-the-backup-battery-module).

#### <a name="controller-modules"></a>Módulos controlador

Os dispositivos de StorSimple incluem módulos controlador quente mudança, redundantes. Os módulos controlador funcionam no modo activo/passivo. Em qualquer altura determinada, um módulo de controlador está ativo e fornece o serviço de, enquanto outras módulo do controlador é passivo. O módulo do controlador passivas está ligado e torna-se operacional se o módulo do controlador de active falha ou é removido. Cada módulo controlador tem capacidade suficiente para fornecer suporte para o quadro inteiro. Ambos os módulos controlador tem de estar instalados para garantir a disponibilidade de alta.

- Certifique-se de que ambos os módulos controlador estão instalados sempre.

- Se um módulo de controlador falhar, pedir substituição imediatamente.

- Remova um módulo de falha de controlador apenas quando tiver a substituição e estiver pronto para o instalar. Remover um módulo para períodos adicional irão afetar o fluxo de ar e, consequentemente, refrigeração do sistema.

- Certifique-se de que as ligações de rede para ambos os módulos controlador são idênticas e as interfaces de rede ligada tem uma configuração de rede idênticas.

- Se um módulo de controlador falha ou precisa de substituição, certifique-se de que o módulo de controlador está num estado activo antes de substituir o módulo do controlador falhar. Para verificar se um controlador está ativo, vá para [identificar o controlador de ativo no seu dispositivo](storsimple-controller-replacement.md#identify-the-active-controller-on-your-device).

- Não remova ambos os módulos controlador ao mesmo tempo. Se for um controlador activação pós-falha em curso, não encerrar o módulo do controlador em espera ou removê-lo do quadro.

- Após uma falha na ligação controlador, aguarde pelo menos 5 minutos antes de remover o módulo do controlador.

#### <a name="network-interfaces"></a>Interfaces de rede

StorSimple dispositivo controlador módulos cada têm quatro 1 Gigabit e duas 10 Gigabit Ethernet interfaces de rede.

- Certifique-se de que as ligações de rede para ambos os módulos controlador são idênticas e interfaces de rede que estão ligadas as interfaces de módulo do controlador de ter uma configuração de rede idênticas.

- Sempre que possível, implemente ligações de rede entre diferentes parâmetros para garantir a disponibilidade de serviço em caso de falha de dispositivo de rede.

- Quando desliga o único ou último interface com capacidade de iSCSI restante (com IPs atribuídas), desactivar a interface pela primeira vez e, em seguida, desligue os cabos de. Se a interface está desligada pela primeira vez, em seguida, irão provocar o controlador de active a falha ao longo para o controlador de passivo. Se o controlador de passivo também possui as suas correspondentes interfaces desligados, o ambos os controladores reiniciar várias vezes antes de liquidar num controlador de.

- Ligue, pelo menos, duas interfaces de dados para a rede do cada módulo controlador.

- Se ativou as duas 10 GbE interfaces, implementar aqueles através de parâmetros de diferentes.

- Sempre que possível, utilize MPIO nos servidores para se certificar de que os servidores de podem tolerar uma ligação, rede ou falha da interface.

Para obter mais informações sobre o funcionamento em rede seu dispositivo, para elevada disponibilidade e desempenho, aceda ao [instalar o seu dispositivo StorSimple 8100](storsimple-8100-hardware-installation.md#cable-your-storsimple-8100-device) ou [instalar o seu dispositivo StorSimple 8600](storsimple-8600-hardware-installation.md#cable-your-storsimple-8600-device).

#### <a name="ssds-and-hdds"></a>SSDs e HDDs

Dispositivos StorSimple incluem discos estado sólido (SSDs) e unidades de disco rígido (HDDs) que são protegidas utilizando espelhado espaços. Utilizar os espaços espelhados assegura que o dispositivo está conseguir tolerar a falha de uma ou mais SSDs ou HDDs.

- Certifique-se de que todos os módulos SSD e disco estão instalados.

- Se um SSD ou disco falhar, pedir substituição imediatamente.

- Se um SSD ou disco falha ou necessita de substituição, certifique-se de que o remova apenas a SSD ou o disco que necessita de substituição.

- Não remova mais do que um SSD ou disco do sistema em qualquer ponto no tempo.
Uma falha de discos 2 ou mais de determinado tipo (disco, SSD) ou falha consecutiva num período de hora abreviada poderá resultar na perda de dados de falha e potencial do sistema. Se tal acontecer, [contacte o suporte da Microsoft](storsimple-contact-microsoft-support.md) para obter assistência.

- Durante a substituição, monitorize o **Estado do Hardware** na página de **Manutenção** de unidades na SSDs e HDDs. Um Estado de verificação verde indica que os discos são saudável ou OK, Considerando que aponte um ponto de exclamação vermelho indica uma falha SSD ou disco.

- Recomendamos que configure instantâneos na nuvem para todos os volumes que precisa para proteger em caso de uma falha de sistema.

#### <a name="ebod-enclosure"></a>Inclusão EBOD

Modelo de dispositivo de StorSimple 8600 inclui uma inclusão expandido conjunto de discos (EBOD) para além de inclusão principal. Um EBOD contém EBOD controladores e unidades de disco rígido (HDDs) que são protegidas utilizando espelhado espaços. Utilizar os espaços espelhados assegura que o dispositivo está conseguir tolerar a falha de uma ou mais HDDs. A inclusão EBOD está ligado à inclusão primária através de redundantes cabos de SA.

- Certifique-se de que ambos os módulos controlador de inclusão EBOD, cabos SA tanto todas as unidades de disco rígido instaladas sempre.

- Se um módulo de controlador de inclusão EBOD falhar, pedir substituição imediatamente.

- Se um módulo de controlador de inclusão EBOD falhar, certifique-se de que o módulo de controlador está ativo antes de substituir o módulo falhado. Para verificar se um controlador está ativo, vá para [identificar o controlador de ativo no seu dispositivo](storsimple-controller-replacement.md#identify-the-active-controller-on-your-device).

- Durante uma substituição de módulo do controlador EBOD, continuamente monitorizar o estado do componente no serviço do Gestor de StorSimple acedendo a **Manutenção** > **Estado do Hardware**.

- Se um cabo SA falha ou requer substituição (Microsoft Support devem estar envolvida para fazer como uma imagem), certifique-se de que o remova apenas o cabo SA que necessita de substituição.

- Não em simultâneo remova ambos os cabos de SA do sistema em qualquer ponto no tempo.

### <a name="high-availability-recommendations-for-your-host-computers"></a>Recomendações de elevada disponibilidade para os computadores anfitrião

Reveja cuidadosamente estes procedimentos recomendados para garantir a disponibilidade de anfitriões ligado ao seu dispositivo StorSimple alta.

- Configurar StorSimple com [configurações de cluster de servidor do ficheiro de duas nó][1]. Removendo pontos únicos de falha e de construção no redundância no lado anfitrião, a solução inteira torna-se vivamente disponível.

- Utilizar continuamente disponível (AC) partilha disponível com o Windows Server 2012 (SMB 3.0) para elevada disponibilidade durante activação pós-falha dos controladores de armazenamento. Para obter informações adicionais para configurar clusters de servidor do ficheiro e partilhas continuamente disponíveis com o Windows Server 2012, consulte este [vídeo de demonstração](http://channel9.msdn.com/Events/IT-Camps/IT-Camps-On-Demand-Windows-Server-2012/DEMO-Continuously-Available-File-Shares).

## <a name="next-steps"></a>Próximos passos

- [Saiba mais sobre os limites do sistema StorSimple](storsimple-limits.md).
- [Saiba como implementar a solução StorSimple](storsimple-deployment-walkthrough-u2.md).

<!--Reference links-->
[1]: https://technet.microsoft.com/library/cc731844(v=WS.10).aspx
