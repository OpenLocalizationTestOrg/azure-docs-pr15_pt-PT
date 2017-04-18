<properties 
   pageTitle="Modificar a configuração do dispositivo StorSimple | Microsoft Azure" 
   description="Descreve como utilizar o serviço do Gestor de StorSimple para reconfigurar um dispositivo de StorSimple que já foi implementado." 
   services="storsimple" 
   documentationCenter="NA" 
   authors="SharS" 
   manager="carmonm" 
   editor=""/>

<tags
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD" 
   ms.date="09/29/2016"
   ms.author="v-sharos"/>

# <a name="use-the-storsimple-manager-service-to-modify-your-storsimple-device-configuration"></a>Utilize o serviço do Gestor de StorSimple para modificar a configuração do dispositivo StorSimple

## <a name="overview"></a>Descrição geral 

O portal clássico Azure página **Configurar** contém todos os parâmetros de dispositivo pode reconfigurar num dispositivo StorSimple gerido por um serviço de Gestor de StorSimple. Neste tutorial explica como pode utilizar a página **Configurar** para efetuar as seguintes tarefas de nível de dispositivo:

- Modificar as definições de dispositivo 
- Modificar as definições de tempo 
- Modificar as definições de DNS 
- Modificar interfaces de rede
- Trocar ou reatribuir IPs

## <a name="modify-device-settings"></a>Modificar as definições de dispositivo

As definições de dispositivo incluem o nome amigável do dispositivo e a descrição do dispositivo.

Um dispositivo de StorSimple está ligado ao serviço do Gestor de StorSimple é atribuído um nome predefinido. O nome predefinido reflete normalmente o número de série do dispositivo. Por exemplo, um nome de dispositivo predefinido que é 15 carateres, tal como 8600-SHX0991003G44HT indica o seguinte procedimento:

- **8600** – indica o modelo de dispositivo.
- **SHX** – indica o site de fabrico.
- **0991003** - indica um produto específico.
- **G44HT**- os últimos 5 dígitos são aumentados para criar os números de série exclusivos. Isto pode não estar um conjunto sequencial.

Pode utilizar o portal do Azure clássico para alterar o nome do dispositivo e atribua-lhe um nome amigável único da sua escolha. O nome amigável pode conter carateres e pode ser um máximo de 64 carateres de comprimento.

Também pode especificar uma descrição do dispositivo. Uma descrição do dispositivo normalmente ajuda a identificar o proprietário e a localização física do dispositivo. Descrição o campo tem de conter menos de 256 carateres.
 
## <a name="modify-time-settings"></a>Modificar as definições de tempo

O dispositivo tem de sincronizar tempo para autenticar com o seu fornecedor de serviço de armazenamento na nuvem. Selecione o fuso horário a partir da lista pendente e especificar até dois servidores de rede tempo Protocol (PTR). O servidor NTP primário é necessário e é especificado quando utilizar o Windows PowerShell para StorSimple para configurar o seu dispositivo. Pode especificar o Windows Server de predefinido **time.windows.com** como o seu servidor NTP. Pode ver a configuração do servidor NTP primária através do portal do Azure clássico, mas tem de utilizar a interface do Windows PowerShell para alterá-la.

A configuração do servidor NTP secundária é opcional. Pode utilizar o portal clássico para configurar um servidor NTP secundário. 

Quando configurar o servidor NTP, certifique-se de que a rede permite que o tráfego NTP passar a partir do seu centro de dados à Internet. Quando especificar um servidor NTP público, deve certificar-se de que o seu firewalls de rede e outros dispositivos de segurança estão configurados para permitir o tráfego NTP viajar de e para a rede exterior. Se não é permitido tráfego NTP bidirecional, tem de utilizar um servidor NTP interno (esta função fornece o controlador de domínio do Windows). Se o seu dispositivo não é possível sincronizar a hora, poderá não conseguir comunicar com o seu fornecedor de armazenamento na nuvem.

Para ver uma lista de servidores NTP públicos, ir para a [Web de servidores NTP](http://support.ntp.org/bin/view/Servers/WebHome). 

### <a name="what-happens-if-the-device-is-deployed-in-a-different-time-zone"></a>O que acontece se o dispositivo está implementado no fuso horário diferente?

Se o dispositivo está implementado no fuso horário diferente, o fuso horário do dispositivo será alterada. Dado que todas as políticas de cópia de segurança utilizam o fuso horário do dispositivo, as políticas de cópia de segurança irão ajustar automaticamente em conformidade com o novo fuso horário. É necessária o sem intervenção do utilizador.

## <a name="modify-dns-settings"></a>Modificar as definições de DNS

Um servidor DNS é utilizado quando o seu dispositivo tenta comunicar com o seu fornecedor de serviços de armazenamento na nuvem. Para elevada disponibilidade, são necessários para configurar a página principal e os servidores DNS secundários durante a implementação de dispositivo inicial. Reconfigurar o servidor DNS principal, terá de utilizar a interface do Windows PowerShell no seu dispositivo de StorSimple.

Para modificar o servidor DNS secundário, pode utilizar o portal clássico Azure.



## <a name="modify-network-interfaces"></a>Modificar interfaces de rede

O dispositivo tem seis interfaces de rede de dispositivo, quatro das quais são 1 GbE e dois dos quais 10 GbE. Estas interfaces são identificadas como dados 0 – 5 de dados. DADOS 0, 1 de dados, dados 4 e 5 de dados são 1 GbE, Considerando que dados 2 e 3 de dados estão 10 interfaces de rede GbE.

Configure **Definições de Interface de rede** para cada uma das interfaces para ser utilizado. Para garantir a disponibilidade de alta, recomendamos que tem, pelo menos, duas interfaces de iSCSI e duas interfaces com capacidade de nuvem no seu dispositivo. Recomendamos, mas não requer que não utilizadas interfaces ser desativado.

Quando configurar qualquer uma das interfaces de rede, tem de configurar um IP virtual (VIP).

DADOS 0 são activado nuvem por predefinição. Quando configurar dados 0, são também necessários para configurar dois fixos endereços IP, uma para cada controlador. Estes endereços IP fixos podem ser utilizados para aceder diretamente os controladores do dispositivo e são úteis quando instalar atualizações no dispositivo ou quando acede aos controladores para resolução de problemas.

Na atualização 1 para StorSimple 8000 série, a métrica do encaminhamento de dados 0 está definida para o menor; por isso, se o seu dispositivo está a ser executado StorSimple 8000 série atualização 1, todo o tráfego de nuvem será encaminhado através dos dados 0. Se tiver mais do que uma interface de rede com capacidade de nuvem no seu dispositivo StorSimple, tome nota do presente.

>[AZURE.NOTE] Os endereços IP fixos para o controlador de são utilizados para servir as atualizações para o dispositivo. Por conseguinte, a IPs fixo tem de ser conseguir ligar à Internet e encaminháveis.

Para cada interface de rede, são apresentados os seguintes parâmetros:

- **Velocidade** – não é um parâmetro configuráveis pelo utilizador. DADOS 0, 1 de dados, dados 4 e 5 de dados são sempre 1 GbE, Considerando que dados 2 e 3 de dados estão 10 interfaces de GbE.

     >[AZURE.NOTE] Velocidade e duplex são sempre automática-negociação. Jumbo frames não é suportado.
 
- **Estado da interface** – uma interface pode ser ativada ou desactivada. Se ativada, o dispositivo tentará utilizar a interface. Recomendamos que apenas essas interfaces que estão ligadas à rede e utilizadas estar ativada. Desative as interfaces que não está a utilizar.

- **Tipo de interface** – este parâmetro permite-lhe isolar iSCSI tráfego de tráfego de armazenamento na nuvem. Este parâmetro pode ser um dos seguintes procedimentos:

    - **Nuvem ativada** – quando ativada, o dispositivo utilizar esta interface para comunicar com a nuvem.
    - **iSCSI ativado** – quando ativada, o dispositivo irá utilizar esta interface para comunicar com o anfitrião o iSCSI.

    Recomendamos que isolá iSCSI tráfego de tráfego de armazenamento na nuvem. Tenha também em atenção que se o seu sistema anfitrião estiver dentro da mesma sub-rede como o seu dispositivo, é necessário atribuir um gateway; No entanto, se o seu sistema anfitrião estiver no seu dispositivo sub-rede diferente, terá de atribuir um gateway.

- **Endereço IP** – Isto pode ser IPv4 ou IPv6 ou ambos. O IPv4 e o IPv6 famílias de tipos de endereço são suportadas para as interfaces de rede do dispositivo. Ao utilizar IPv4, especifique um endereço IP de 32 bits (*xxx.xxx*) na notação ponto decimal. Quando utilizar o IPv6, simplesmente fornecer um prefixo de 4 dígitos e um endereço de 128-bit gerado automaticamente para a interface de rede de dispositivo com base nesse prefixo.

- **Sub-rede** – Isto refere-se para a máscara de sub-rede e está configurado através da interface do Windows PowerShell.

- **Gateway** – este é o gateway predefinido que deve ser utilizado por esta interface quando tenta comunicar connosco que não estão dentro do mesmo espaço de endereços IP (sub-rede). O gateway predefinido deve estar no mesmo espaço de endereços (sub-rede) como a interface do endereço IP, tal como determinados pela máscara de sub-rede.

- **Endereço IP fixo** – este campo só está disponível enquanto configura os dados 0 interface. Para operações como atualizações ou o dispositivo de resolução de problemas, poderá ter de ligar diretamente para o controlador de dispositivo. O endereço IP fixo pode ser utilizado para aceder ao activo e o controlador de passivo no seu dispositivo.

Pode reconfigurar o controlador de 0 e 1 de controlador através do portal do Azure clássico.

>[AZURE.NOTE] 
>
>- Para assegurar o funcionamento, verifique a velocidade da interface e frente e verso no comutador que cada interface dispositivo estiver ligado a. Mudar interfaces quer deve negoceie com ou estar configuradas para Gigabit Ethernet (1000 Mbps) e estar em full-duplex. Interfaces operativo a velocidades ou no meio-duplex irão resultar em problemas de desempenho.
>
>- Para minimizar interrupções e tempo de inatividade, recomendamos que permitem portfast em cada uma das portas mudar que irá ligar a interface de rede iSCSI do seu dispositivo. Isto irá garantir que a conectividade da rede pode ser estabelecida rapidamente trabalho uma activação pós-falha.
 
## <a name="swap-or-reassign-ips"></a>Trocar ou reatribuir IPs

Atualmente, se qualquer interface de rede no controlador é atribuída uma VIP que está a ser utilizado (ao mesmo dispositivo ou outro dispositivo na rede), em seguida, o controlador de falhará sobre. Por isso, tem a seguir o procedimento inicial se são trocar VIP para a interface de rede de dispositivo, uma vez que irá criar uma situação IP duplicada.

Execute os seguintes passos para trocar ou reatribuir VIP para qualquer uma das interfaces de rede:

#### <a name="to-reassign-ips"></a>Para reatribuir IPs

1. Desmarque o endereço IP do ambas as interfaces.

2. Depois dos endereços IP estão desmarcados, atribua os novos endereços IP para as respetivas interfaces.

## <a name="next-steps"></a>Próximos passos

- Saiba como [Configurar MPIO para o seu dispositivo StorSimple](storsimple-configure-mpio-windows-server.md).

- Saiba como [utilizar o serviço do Gestor de StorSimple para administrar o seu dispositivo StorSimple](storsimple-manager-service-administration.md).
     
