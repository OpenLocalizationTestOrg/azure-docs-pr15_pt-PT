<properties
    pageTitle="Preparar um VHD do Windows para carregar para o Azure | Microsoft Azure"
    description="Práticas recomendadas para preparar um VHD Windows antes de a carregar para do Azure"
    services="virtual-machines-windows"
    documentationCenter=""
    authors="genlin"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/18/2016"
    ms.author="glimoli;genli"/>

# <a name="prepare-a-windows-vhd-to-upload-to-azure"></a>Preparar um VHD do Windows para carregar para o Azure
Para carregar uma VM Windows nos locais para Azure, tem de preparar corretamente o disco rígido virtual (VHD). Existem vários passos recomendados que pode concluir antes de carregar um VHD para Azure. Este artigo mostra-lhe como preparar um VHD do Windows para carregar para o Microsoft Azure e também explica [quando e como utilizar o Sysprep](#step23).

## <a name="prepare-the-virtual-disk"></a>Preparar o disco virtual

>[AZURE.NOTE] 
> Azure suporta apenas [máquinas virtuais de geração 1](http://blogs.technet.com/b/ausoemteam/archive/2015/04/21/deciding-when-to-use-generation-1-or-generation-2-virtual-machines-with-hyper-v.aspx) que estão no formato de ficheiro VHD. O formato mais recente do VHDX não é suportado no Azure. 
>
> O VHD tem de ser um tamanho fixo, não dinâmicos. Se for necessário, as instruções abaixo de detalhe converter a partir de discos VHDX ou dinâmica. O tamanho máximo permitido para o VHD é 1,023 GB.


Certifique-se de que o VHD Windows está a funcionar corretamente no servidor local. Resolva erros dentro da VM propriamente dito antes de tentar para converter ou carregar para o Azure.

Se precisar de converter o disco virtual para o formato necessário para Azure, utilize um dos métodos indicados nas secções seguintes. Criar cópias de segurança a VM antes de executar qualquer processo de conversão de disco virtual ou Sysprep.

### <a name="convert-using-hyper-v-manager"></a>Converter utilizando o Gestor de Hyper-V
- Abra o Gestor de Hyper-V e selecione o seu computador local à esquerda. No menu acima do mesmo, clique em **ação** > **Editar disco**.
    - No ecrã **localize Virtual no disco rígido** , navegue para e selecione disco virtual.
    - Selecione a opção **Converter** no ecrã seguinte
        - Se precisar de converter o VHDX, selecione **VHD** e clique em **seguinte**
        - Se precisar de converter a partir do disco dinâmico, selecione **tamanho fixo** e clique em **seguinte**

    - Procure e selecione o **caminho para o novo ficheiro VHD**.
    - Clique em **Concluir** para fechar.

### <a name="convert-using-powershell"></a>Converter através do PowerShell
Pode converter um disco virtual utilizando o [cmdlet do PowerShell VHD converter](http://technet.microsoft.com/library/hh848454.aspx). No exemplo seguinte, que são converter a partir de um VHDX VHD e converter a partir de um dinâmico fixo tipo:

```powershell
Convert-VHD –Path c:\test\MY-VM.vhdx –DestinationPath c:\test\MY-NEW-VM.vhd -VHDType Fixed
```

### <a name="convert-from-vmware-vmdk-disk-format"></a>Converter o formato de disco VMware VMDK
Se tiver uma imagem do Windows VM no [formato de ficheiro VMDK](https://en.wikipedia.org/wiki/VMDK), convertê-la para um VHD utilizando o [Conversor de Máquina Virtual da Microsoft](https://www.microsoft.com/download/details.aspx?id=42497). Ler o blogue [como converter uma VMDK VMware VHD Hyper-V](http://blogs.msdn.com/b/timomta/archive/2015/06/11/how-to-convert-a-vmware-vmdk-to-hyper-v-vhd.aspx) para obter mais informações.

## <a name="prepare-windows-configuration-for-upload"></a>Preparar a configuração do Windows para carregar

> [AZURE.NOTE] Execute os seguintes comandos com [privilégios administrativos](https://technet.microsoft.com/library/cc947813.aspx).

1. Remova qualquer rota persistente estática na tabela de encaminhamento:

    - Para visualizar a tabela de rotas, execute `route print`.
    - Verifique as secções **Persistente rotas** . Se existir uma rota persistente, utilize a [rota delete](https://technet.microsoft.com/library/cc739598.apx) para removê-la.

2. Remova o proxy WinHTTP:

    ```
    netsh winhttp reset proxy
    ```

3. Configure o disco política de SAN para [Onlineall](https://technet.microsoft.com/library/gg252636.aspx):

    ```
    diskpart san policy=onlineall
    ```

4. Utilize a hora Universal Coordenada (UTC) para Windows e configure o tipo de arranque do serviço de hora do Windows (w32time) para **automaticamente**:

    ```
    REG ADD HKLM\SYSTEM\CurrentControlSet\Control\TimeZoneInformation /v RealTimeIsUniversal /t REG_DWORD /d 1
    sc config w32time start= auto
    ```


## <a name="configure-windows-services"></a>Configurar os serviços do Windows
5. Certifique-se de que cada um dos seguintes serviços Windows está definida para os **valores de predefinido do Windows**. São configurados com as definições de arranque indicadas na lista que se segue. Pode executar estes comandos para repor as definições de arranque:

    ```
    sc config bfe start= auto

    sc config dcomlaunch start= auto

    sc config dhcp start= auto

    sc config dnscache start= auto

    sc config IKEEXT start= auto

    sc config iphlpsvc start= auto

    sc config PolicyAgent start= demand

    sc config LSM start= auto

    sc config netlogon start= demand

    sc config netman start= demand

    sc config NcaSvc start= demand

    sc config netprofm start= demand

    sc config NlaSvc start= auto

    sc config nsi start= auto

    sc config RpcSs start= auto

    sc config RpcEptMapper start= auto

    sc config termService start= demand

    sc config MpsSvc start= auto

    sc config WinHttpAutoProxySvc start= demand

    sc config LanmanWorkstation start= auto

    sc config RemoteRegistry start= auto
    ```


## <a name="configure-remote-desktop-configuration"></a>Configurar a configuração de ambiente de trabalho remoto
6. Se existirem quaisquer certificados autoassinados associados à escuta do protocolo de ambiente de trabalho remoto (RDP), removê-los:

    ```
    REG DELETE "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp\SSLCertificateSHA1Hash”
    ```

    Para obter mais informações sobre como configurar os certificados escutar RDP, consulte o artigo [Escuta certificado configurações no Windows Server](https://blogs.technet.microsoft.com/askperf/2014/05/28/listener-certificate-configurations-in-windows-server-2012-2012-r2/)

7. Configure os valores de [KeepAlive](https://technet.microsoft.com/library/cc957549.aspx) do serviço RDP:

    ```
    REG ADD "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v KeepAliveEnable /t REG_DWORD  /d 1 /f

    REG ADD "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v KeepAliveInterval /t REG_DWORD  /d 1 /f

    REG ADD "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp" /v KeepAliveTimeout /t REG_DWORD /d 1 /f
    ```

8. Configure o modo de autenticação para o serviço RDP:

    ```
    REG ADD "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v UserAuthentication /t REG_DWORD  /d 1 /f

    REG ADD "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v SecurityLayer /t REG_DWORD  /d 1 /f

    REG ADD "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v fAllowSecProtocolNegotiation /t REG_DWORD  /d 1 /f
    ```

9. Ative o serviço de RDP adicionando as seguintes subchaves de registo:

    ```
    REG ADD "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v fDenyTSConnections /t REG_DWORD  /d 0 /f
    ```


## <a name="configure-windows-firewall-rules"></a>Configurar regras de Firewall do Windows
10. Permitir WinRM através de três perfis de firewall (domínio, privado e público) e ativar o serviço PowerShell Remote:

    ```
    Enable-PSRemoting -force
    ```

11. Certifique-se de que as seguintes regras de firewall do sistema operativo convidado estão no local:

    - Entrada

    ```
    netsh advfirewall firewall set rule dir=in name="File and Printer Sharing (Echo Request - ICMPv4-In)" new enable=yes

    netsh advfirewall firewall set rule dir=in name="Network Discovery (LLMNR-UDP-In)" new enable=yes

    netsh advfirewall firewall set rule dir=in name="Network Discovery (NB-Datagram-In)" new enable=yes

    netsh advfirewall firewall set rule dir=in name="Network Discovery (NB-Name-In)" new enable=yes

    netsh advfirewall firewall set rule dir=in name="Network Discovery (Pub-WSD-In)" new enable=yes

    netsh advfirewall firewall set rule dir=in name="Network Discovery (SSDP-In)" new enable=yes

    netsh advfirewall firewall set rule dir=in name="Network Discovery (UPnP-In)" new enable=yes

    netsh advfirewall firewall set rule dir=in name="Network Discovery (WSD EventsSecure-In)" new enable=yes

    netsh advfirewall firewall set rule dir=in name="Windows Remote Management (HTTP-In)" new enable=yes

    netsh advfirewall firewall set rule dir=in name="Windows Remote Management (HTTP-In)" new enable=yes
    ```

    - Entrada e saída

    ```
    netsh advfirewall firewall set rule group="Remote Desktop" new enable=yes

    netsh advfirewall firewall set rule group="Core Networking" new enable=yes
    ```

    - Saída

    ```
    netsh advfirewall firewall set rule dir=out name="Network Discovery (LLMNR-UDP-Out)" new enable=yes

    netsh advfirewall firewall set rule dir=out name="Network Discovery (NB-Datagram-Out)" new enable=yes

    netsh advfirewall firewall set rule dir=out name="Network Discovery (NB-Name-Out)" new enable=yes

    netsh advfirewall firewall set rule dir=out name="Network Discovery (Pub-WSD-Out)" new enable=yes

    netsh advfirewall firewall set rule dir=out name="Network Discovery (SSDP-Out)" new enable=yes

    netsh advfirewall firewall set rule dir=out name="Network Discovery (UPnPHost-Out)" new enable=yes

    netsh advfirewall firewall set rule dir=out name="Network Discovery (UPnP-Out)" new enable=yes

    netsh advfirewall firewall set rule dir=out name="Network Discovery (WSD Events-Out)" new enable=yes

    netsh advfirewall firewall set rule dir=out name="Network Discovery (WSD EventsSecure-Out)" new enable=yes

    netsh advfirewall firewall set rule dir=out name="Network Discovery (WSD-Out)" new enable=yes
    ```


## <a name="additional-windows-configuration-steps"></a>Passos de configuração do Windows adicionais
12. Executar `winmgmt /verifyrepository` para confirmar que o Windows Management instrumentação () repositório está consistente. Se o repositório está danificado, consulte [Esta mensagem no blogue](https://blogs.technet.microsoft.com/askperf/2014/08/08/wmi-repository-corruption-or-not).

13. Certifique-se que as definições de dados de configuração de arranque (BCD) correspondem às seguintes opções:

    ```
    bcdedit /set {bootmgr} integrityservices enable

    bcdedit /set {default} device partition=C:

    bcdedit /set {default} integrityservices enable

    bcdedit /set {default} recoveryenabled Off

    bcdedit /set {default} osdevice partition=C:

    bcdedit /set {default} bootstatuspolicy IgnoreAllFailures
    ```

14. Remova quaisquer filtros de Interface de transporte do controlador adicionais, tal como software que analisa os pacotes TCP.
15. Para certificar-se de que o disco está saudável e consistentes, execute o `CHKDSK /f` comando.
16. Desinstale quaisquer outras software de terceiros e controlador relacionados com componentes físicos ou outras tecnologias de virtualização.
17. Certifique-se de uma aplicação de terceiros não estiver a utilizar porta 3389. Esta porta é utilizada para o serviço RDP no Azure. Pode utilizar o `netstat -anob` comando para verificar as portas que estiver a utilizar as aplicações.
18. Se o Windows VHD que pretende carregar um controlador de domínio, siga [estes passos extra](https://support.microsoft.com/kb/2904015) para preparar o disco.
19. Reinicie a VM para se certificar de que o Windows está ainda Saudável possa ser contactado utilizando a ligação RDP.
20. Repor a palavra-passe de administrador local atual e certifique-se de que pode utilizar esta conta para iniciar sessão no Windows através da ligação RDP.  Esta permissão de acesso é controlada pelo objeto de política "Permitir a iniciar sessão através dos serviços de ambiente de trabalho remoto". Este objeto está localizado em "Computador configuração definições \ Políticas atribuição de direitos."


## <a name="install-windows-updates"></a>Instalar atualizações do Windows
22. Instale as atualizações mais recentes para Windows. Se não for possível, certifique-se de que estão instaladas as atualizações do seguintes:

    - [KB3137061](https://support.microsoft.com/kb/3137061) Microsoft Azure VMs não recuperar a partir de uma falha na rede e ocorrem problemas de danos os ficheiros de dados

    - [KB3115224](https://support.microsoft.com/kb/3115224) Melhoramentos de fiabilidade para VMs que estiver a executar o num sistema anfitrião Windows Server 2012 R2 ou Windows Server 2012

    - [KB3140410](https://support.microsoft.com/kb/3140410) MS16-031: Atualização de segurança para o Microsoft Windows para elevação de privilégios endereço: 8 de Março de 2016

    - [KB3063075](https://support.microsoft.com/kb/3063075) Muitos ID 129 eventos são registados quando executa uma máquina de virtual do Windows Server 2012 R2 no Microsoft Azure

    - [KB3137061](https://support.microsoft.com/kb/3137061) Microsoft Azure VMs não recuperar a partir de uma falha na rede e ocorrem problemas de danos os ficheiros de dados

    - [KB3114025](https://support.microsoft.com/kb/3114025) Desempenho lento quando aceder ao armazenamento de ficheiros Azure a partir do Windows 8.1 ou Server 2012 R2

    - [KB3033930](https://support.microsoft.com/kb/3033930) Correcção aumenta o limite de 64 mil intervalos de tempo de RIO por processo para o serviço Windows Azure

    - [KB3004545](https://support.microsoft.com/kb/3004545) Não é possível aceder máquinas virtuais alojadas serviços de hospedagem Azure através de uma ligação VPN no Windows

    - [KB3082343](https://support.microsoft.com/kb/3082343) Conectividade de publicação em local VPN é perdida quando utilizam o Windows Server 2012 R2 RRAS Azure túneis VPN do site para o site

    - [KB3140410](https://support.microsoft.com/kb/3140410) MS16-031: Atualização de segurança para o Microsoft Windows para elevação de privilégios endereço: 8 de Março de 2016

    - [KB3146723](https://support.microsoft.com/kb/3146723) MS16-048: Descrição da atualização de segurança para CSRSS: 12 de Abril de 2016
    - [KB2904100](https://support.microsoft.com/kb/2904100) Sistema fixa durante o disco e/s no Windows<a id="step23"></a>
23. Se pretende criar uma imagem para implementar vários computadores da mesma, terá de generalizar a imagem ao executar `sysprep` antes de carregar o VHD para Azure. Não precisa para executar o `sysprep` para utilizar um VHD especializado. Para obter mais informações sobre como criar uma imagem GRG, consulte os artigos seguintes:

    - [Criar uma imagem VM a partir de um VM Azure existente utilizando o modelo de implementação do Gestor de recursos](virtual-machines-windows-create-vm-generalized.md)
    - [Criar uma imagem VM a partir de um VM Azure existente utilizando o modem de implementação clássico](virtual-machines-windows-classic-capture-image.md)
    - [Suporte de Sysprep para funções de servidor](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles)



## <a name="suggested-extra-configurations"></a>Configurações extra sugeridas

As seguintes definições que afetam a VHD carregar. No entanto, recomendamos vivamente que tem-los configurado.

- Instale o [Azure máquinas virtuais agente](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Depois de instalar o agente, pode ativar a extensões VM. As extensões de VM implementam a maior parte da funcionalidade crítica que pretende utilizar com o seu VMs como repor palavras-passe, configurar RDP e muitas outras pessoas.

- O registo de informação pode ser úteis na resolução de problemas de falha de sistema do Windows. Ativar a coleção de registo de informações de estado:

    ```
    REG ADD "HKLM\SYSTEM\CurrentControlSet\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 2 /f`

    REG ADD "HKLM\SOFTWARE\Microsoft\Windows\Windows Error Reporting\LocalDumps" /v DumpFolder /t REG_EXPAND_SZ /d "c:\CrashDumps" /f

    REG ADD "HKLM\SOFTWARE\Microsoft\Windows\Windows Error Reporting\LocalDumps" /v DumpCount /t REG_DWORD /d 10 /f

    REG ADD "HKLM\SOFTWARE\Microsoft\Windows\Windows Error Reporting\LocalDumps" /v DumpType /t REG_DWORD /d 2 /f

    sc config wer start= auto
    ```

- Depois da VM é criada no Azure, configure o ficheiro de paginação do tamanho do sistema definido na unidade d:

    ```
    REG ADD "HKLM\SYSTEM\CurrentControlSet\Control\Session Manager\Memory Management" /t REG_MULTI_SZ /v PagingFiles /d "D:\pagefile.sys 0 0" /f
    ```


## <a name="next-steps"></a>Próximos passos

- [Carregar uma imagem do Windows VM para Azure para implementações do Gestor de recursos](virtual-machines-windows-upload-image.md)
