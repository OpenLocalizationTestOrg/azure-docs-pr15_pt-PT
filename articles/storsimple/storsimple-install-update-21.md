<properties
   pageTitle="Instale a atualização 2.2 no seu dispositivo de StorSimple | Microsoft Azure"
   description="Explica como instalar StorSimple 8000 série atualização 2.2 no seu dispositivo de série StorSimple 8000."
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="08/02/2016"
   ms.author="alkohli" />

# <a name="install-update-22-on-your-storsimple-device"></a>Instale a atualização 2.2 no seu dispositivo de StorSimple

## <a name="overview"></a>Descrição geral

Neste tutorial explica como instalar a atualização 2.2 num dispositivo StorSimple a executar uma versão anterior do software através do portal clássico do Azure e utilizando o método de correcção. O método de correcção é utilizado quando um gateway está configurado numa interface de rede diferente de 0 de dados do dispositivo StorSimple e está a tentar atualizar a partir de uma versão anterior à atualização do software 1.

Actualização 2.2 inclui o software de dispositivo, WMI e iSCSI atualizações. Se a atualizar a partir da versão 2.1, apenas a actualização de software para o dispositivo terá de ser aplicada. Se a atualizar a partir de uma versão de 2 anterior à atualização, também será exigido para aplicar LSI controlador, Spaceport, Storport e disco firmware atualizações. O software do dispositivo, WMI, iSCSI, LSI controlador, Spaceport e Storport correções são actualizações não desorganização e podem ser aplicadas através do portal do Azure clássico. As atualizações de firmware disco são actualizações desorganização e só podem ser aplicadas através da interface do Windows PowerShell do dispositivo. 

> [AZURE.IMPORTANT]

> - Um conjunto de manuais e automáticas verificações pré é efetuado antes da instalação para determinar o estado de funcionamento do dispositivo em termos hardware estado e conectividade de rede. Estas verificações pré são executadas apenas se aplicar as atualizações a partir do Azure portal clássico.
> - Recomendamos que instale as atualizações de software e controlador através do portal do Azure clássica. Apenas devem ir para a interface do Windows PowerShell do dispositivo (para instalar atualizações) se a verificação de gateway anterior à atualização falhar no portal. Dependendo da versão que está a atualizar, as atualizações poderão demorar horas de 2,5 1,5 para instalar. Tem de estar instaladas as atualizações do modo de manutenção através da interface do Windows PowerShell do dispositivo. Como as atualizações do modo de manutenção são desorganização atualizações, estes irão resultar num período de tempo para baixo para o seu dispositivo.
> - Se executar o Gestor de instantâneo StorSimple opcional, certifique-se de que tiver atualizado sua versão do Gestor de instantâneo para atualização 2.2 antes de atualizar o dispositivo.

[AZURE.INCLUDE [storsimple-preparing-for-update](../../includes/storsimple-preparing-for-updates.md)]

## <a name="install-update-22-via-the-azure-classic-portal"></a>Instalar a atualização 2.2 através do portal clássico do Azure

Execute os seguintes passos para atualizar o seu dispositivo para [Atualizar 2.2](storsimple-update21-release-notes.md).


> [AZURE.NOTE]
Se está a aplicar Update 2 ou posterior (incluindo atualização 2.1), Microsoft poderão separar informações adicionais de diagnóstico do dispositivo. Como resultado, quando a nossa equipa de operações identifica os dispositivos que estão a ter problemas, estamos melhor equipados para recolher informações a partir do dispositivo e diagnosticar problemas. Ao aceitar Update 2 ou posterior, permitem-nos fornecer este suporte pro-activos.

[AZURE.INCLUDE [storsimple-install-update2-via-portal](../../includes/storsimple-install-update2-via-portal.md)]

12. Certifique-se de que o dispositivo está em execução **StorSimple 8000 série atualização 2.2 (6.3.9600.17708)**. **Última atualização data** também deve ser alterada. 

    Se estiver a atualizar a partir de uma versão anterior ao Update 2, também verá que estão disponíveis as atualizações do modo de manutenção (esta mensagem poderá continuar a ser apresentados até 24 horas depois de instalar as atualizações).

    Atualizações de modo de manutenção estão desorganização atualizações que resultam em tempo de inatividade do dispositivo e só podem ser aplicadas através da interface do Windows PowerShell do seu dispositivo. Em alguns casos quando estiver a executar 1.2 de atualização, o firmware disco já poderá atualizado, caso em que não precisa de instale as atualizações de modo a manutenção.

    Se estiver a atualizar a partir do Update 2, o seu dispositivo deverá agora estar atualizado. Também pode ignorar os passos restantes.

13. Transfira as atualizações do modo de manutenção utilizando os passos indicados [para transferir correcções](#to-download-hotfixes) para procurar e transferir KB3121899, as atualizações de firmware instalações do disco (as outras atualizações já deverão estar instaladas por agora).

13. Siga os passos indicados no [instale e verifique se correcções de modo de manutenção](#to-install-and-verify-maintenance-mode-hotfixes) para instalar as atualizações de modo a manutenção. 

  

## <a name="install-update-22-as-a-hotfix"></a>Instalar a atualização 2.2 como correcção

Utilize este procedimento se falhar a verificação de gateway, quando tento instalar as atualizações através do portal do Azure clássica. A verificação falhar à medida que tem um gateway atribuído a uma interface de rede 0 não dados e o dispositivo está a executar uma versão de software antes de 1 de atualização.

As versões de software que podem ser actualizadas utilizando o método de correcção são:

- Atualizar 0,1, 0,2, 0,3
- Atualizar a 1, 1.1, 1.2
- Update 2, 2.1 

> [AZURE.IMPORTANT]
>
> - Se o seu dispositivo está a executar a versão de lançamento (das versões DG), contacte o [Suporte da Microsoft](storsimple-contact-microsoft-support.md) para ajudá-lo com a atualização.

O método de correcção envolve os três passos seguintes:

- Transfira as correcções a partir do catálogo do Microsoft Update.
- Instalar e verifique se as correcções modo normal.
- Instalar e verifique se a correcção do modo de manutenção (apenas quando atualizar o software de 2 anterior à atualização).

#### <a name="download-updates-for-a-device-running-update-21-software"></a>Transfira as atualizações para um dispositivo executar software 2.1 de actualização

**Se o seu dispositivo está a atualizar 2.1**, tem de transferir apenas a dispositivo atualização de software KB3179904. Instale apenas o ficheiro binário precedido com 'hcsmdssoftwareudpate tudo'. Não instale Cis e a atualização do agente da MDS precedido com `all-cismdsagentupdatebundle`. Se não conseguir fazê-lo irá resultar num erro. Esta é uma atualização não desorganização, não vai ser interrompida IO e o dispositivo não terão qualquer tempo de inatividade.


#### <a name="download-updates-for-a-device-running-update-2-software"></a>Transfira as atualizações para um dispositivo executar software Update 2

**Se o seu dispositivo está a ser executado Update 2**, tem de transferir e instalar as seguintes correcções pela ordem prescrita:

| Ordem  | KB        | Descrição                    | Tipo de actualização  | Instalar o tempo |
|--------|-----------|-------------------------|------------- |-------------|
| 1.      | KB3179904 | Atualização de software & #42;  |  Normal <br></br>Não desorganização     | ~ 45 minutos |
| 2.      | KB3146621 | pacote iSCSI | Normal <br></br>Não desorganização  | ~ 20 minutos |
| 3.      | KB3103616 | Pacote WMI |  Normal <br></br>Não desorganização      | ~ minutos 12 |


 & #42;  Actualização *nota, software consiste em duas ficheiros binários: atualização de software do dispositivo precedido com `all-hcsmdssoftwareupdate` e o agente Cis e a Mds precedido com `all-cismdsagentupdatebundle`. A atualização de software de dispositivo tem de estar instalada antes do agente Cis e a Mds. Também tem de reiniciar o controlador de active através de `Restart-HcsController` cmdlet depois de aplicar o agente Cis e a MDS atualizar (e antes de aplicar o restante atualiza).* 

#### <a name="download-updates-for-a-device-running-pre-update-2-software"></a>Transfira as atualizações para um dispositivo executar software 2 anterior à atualização

**Se o seu dispositivo está a ser executado versões 0,2, 0,3, 1.0 e 1.1**, tem de transferir e instalar o LSI controlador e firmware atualizar para além de software, iSCSI e WMI atualizações. Esta atualização já está instalada se estiver a executar 1.2 atualizar ou 2. 
 
| Ordem  | KB        | Descrição                    | Tipo de actualização  | Instalar o tempo |
|--------|-----------|-------------------------|------------- |-------------|
| 4.      | KB3121900 | Controlador de LSI e firmware             |  Normal <br></br>Não desorganização      | ~ 20 minutos |


<br></br>
**Se o seu dispositivo está a ser executado versões 0,2, 0,3, 1.0, 1.1 e 1.2**, tem de transferir e instalar o Spaceport e a fix Storport. Estes já estão instaladas se estiver a executar Update 2.

| Ordem  | KB        | Descrição                    | Tipo de actualização  | Instalar o tempo |
|--------|-----------|-------------------------|------------- |-------------|
| 5.      | KB3090322 | Corrigir spaceport </br> Windows Server 2012 R2 |  Normal <br></br>Não desorganização      | ~ 20 minutos |
| 6.      | KB3080728 | Corrigir Storport </br> Windows Server 2012 R2 |  Normal <br></br>Não desorganização      | ~ 20 minutos |



<br></br>
Poderá também tem de instalar as atualizações de firmware do disco. Pode verificar se tem as atualizações de firmware disco ao executar o `Get-HcsFirmwareVersion` cmdlet. Se estiver a utilizar estas versões firmware: `XMGG`, `XGEG`, `KZ50`, `F6C2`, `VR08`, em seguida, não tem de instalar estas atualizações.


| Ordem  | KB        | Descrição                    | Tipo de actualização  | Instalar o tempo |
|--------|-----------|-------------------------|------------- |-------------|
| 7.      | KB3121899 | Firmware disco              |  Manutenção <br></br>Desorganização      | ~ 30 minutos |
 
<br></br>

> [AZURE.IMPORTANT]
>
> - Este procedimento tem de ser executado apenas uma vez para aplicar a atualização 2.2. Pode utilizar o portal do Azure clássico para aplicar atualizações subsequentes.
> - Se atualizar o Update 2, a hora de instalação total é perto 1,5 horas.
> - Antes de utilizar este procedimento para aplicar a atualização, certifique-se de que tanto os controladores do dispositivo estão online e todos os componentes de hardware estão em bom Estados.

Execute os passos seguintes para transferir e instalar as correcções.

[AZURE.INCLUDE [storsimple-install-update21-hotfix](../../includes/storsimple-install-update21-hotfix.md)]

[AZURE.INCLUDE [storsimple-install-troubleshooting](../../includes/storsimple-install-troubleshooting.md)]

## <a name="next-steps"></a>Próximos passos

Saiba mais sobre a [atualização 2.1 lançamento](storsimple-update21-release-notes.md).
