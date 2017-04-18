<properties
   pageTitle="Instalar actualizações 3 no seu dispositivo StorSimple | Microsoft Azure"
   description="Explica como instalar StorSimple 8000 série atualização 3 no seu dispositivo de série StorSimple 8000."
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
   ms.date="10/05/2016"
   ms.author="alkohli" />

# <a name="install-update-3-on-your-storsimple-device"></a>Instalar actualizações 3 no seu dispositivo StorSimple

## <a name="overview"></a>Descrição geral

Neste tutorial explica como instalar actualizações 3 num dispositivo StorSimple a executar uma versão anterior do software através do portal clássico do Azure e utilizando o método de correcção. O método de correcção é utilizado quando um gateway está configurado numa interface de rede diferente de 0 de dados do dispositivo StorSimple e está a tentar atualizar a partir de uma versão anterior à atualização do software 1.

Actualização 3 inclui o software do dispositivo, controlador LSI e firmware, Storport e Spaceport atualizações. Se atualizar o Update 2 ou uma versão anterior, também será necessário para aplicar iSCSI, WMI e em certos casos, firmware atualizações do disco. O software do dispositivo, WMI, iSCSI, LSI controlador, Spaceport e Storport correções são actualizações não desorganização e podem ser aplicadas através do portal do Azure clássico. As atualizações de firmware disco são actualizações desorganização e só podem ser aplicadas através da interface do Windows PowerShell do dispositivo. 

> [AZURE.IMPORTANT]

> - Um conjunto de manuais e automáticas verificações pré é efetuado antes da instalação para determinar o estado de funcionamento do dispositivo em termos hardware estado e conectividade de rede. Estas verificações pré são executadas apenas se aplicar as atualizações a partir do Azure portal clássico.
> - Recomendamos que instale as atualizações de software e controlador através do portal do Azure clássica. Apenas devem ir para a interface do Windows PowerShell do dispositivo (para instalar atualizações) se a verificação de gateway anterior à atualização falhar no portal. Dependendo da versão que está a atualizar, as atualizações poderão demorar horas de 2,5 1,5 para instalar. Tem de estar instaladas as atualizações do modo de manutenção através da interface do Windows PowerShell do dispositivo. Como as atualizações do modo de manutenção são desorganização atualizações, estes irão resultar num período de tempo para baixo para o seu dispositivo.
> - Se executar o Gestor de instantâneo StorSimple opcional, certifique-se de que tiver atualizado sua versão do Gestor de instantâneo para Update 2 antes de atualizar o dispositivo.

[AZURE.INCLUDE [storsimple-preparing-for-update](../../includes/storsimple-preparing-for-updates.md)]

## <a name="install-update-3-via-the-azure-classic-portal"></a>Instalar actualizações 3 através do portal clássico do Azure

Execute os seguintes passos para atualizar o seu dispositivo para [Atualizar 3](storsimple-update3-release-notes.md).


> [AZURE.NOTE]
Se está a aplicar Update 2 ou posterior (incluindo atualização 2.1), Microsoft poderão separar informações adicionais de diagnóstico do dispositivo. Como resultado, quando a nossa equipa de operações identifica os dispositivos que estão a ter problemas, estamos melhor equipados para recolher informações a partir do dispositivo e diagnosticar problemas. Ao aceitar Update 2 ou posterior, permitem-nos fornecer este suporte pro-activos.

[AZURE.INCLUDE [storsimple-install-update2-via-portal](../../includes/storsimple-install-update2-via-portal.md)]

12. Certifique-se de que o dispositivo está em execução **StorSimple 8000 série atualização 3 (6.3.9600.17759)**. **Última atualização data** também deve ser alterada. 

    Se estiver a atualizar a partir de uma versão anterior ao Update 2, também verá que estão disponíveis as atualizações do modo de manutenção (esta mensagem poderá continuar a ser apresentados até 24 horas depois de instalar as atualizações).

    Atualizações de modo de manutenção estão desorganização atualizações que resultam em tempo de inatividade do dispositivo e só podem ser aplicadas através da interface do Windows PowerShell do seu dispositivo. Em alguns casos quando estiver a executar 1.2 de atualização, o firmware disco já poderá atualizado, caso em que não precisa de instale as atualizações de modo a manutenção.

    Se estiver a atualizar a partir do Update 2 ou posterior, o seu dispositivo deverá agora estar atualizado. Também pode ignorar os passos restantes.

13. Transfira as atualizações do modo de manutenção utilizando os passos indicados [para transferir correcções](#to-download-hotfixes) para procurar e transferir KB3121899, as atualizações de firmware instalações do disco (as outras atualizações já deverão estar instaladas por agora).

13. Siga os passos indicados no [instale e verifique se correcções de modo de manutenção](#to-install-and-verify-maintenance-mode-hotfixes) para instalar as atualizações de modo a manutenção. 

  

## <a name="install-update-3-as-a-hotfix"></a>Instalar actualizações 3 como uma correcção

Utilize este procedimento se falhar a verificação de gateway, quando tento instalar as atualizações através do portal do Azure clássica. A verificação falhar à medida que tem um gateway atribuído a uma interface de rede 0 não dados e o dispositivo está a executar uma versão de software antes de 1 de atualização.

As versões de software que podem ser actualizadas utilizando o método de correcção são:

- Atualizar 0,1, 0,2, 0,3
- Atualizar a 1, 1.1, 1.2
- Atualizar 2, 2.1, 2.2 

> [AZURE.IMPORTANT]
>
> - Se o seu dispositivo está a executar a versão de lançamento (das versões DG), contacte o [Suporte da Microsoft](storsimple-contact-microsoft-support.md) para ajudá-lo com a atualização.

O método de correcção envolve os três passos seguintes:

1.  Transfira as correcções a partir do catálogo do Microsoft Update.

2.  Instalar e verifique se as correcções modo normal.

3.  Instalar e verifique se a correcção do modo de manutenção (apenas quando atualizar o software de 2 anterior à atualização).


#### <a name="download-updates-for-your-device"></a>Transfira as atualizações para o seu dispositivo

**Se o seu dispositivo está a ser executado de atualização 2.1 ou 2.2**, tem de transferir e instalar as seguintes correcções pela ordem prescrita:

| Ordem  | KB        | Descrição                    | Tipo de actualização  | Instalar o tempo |
|--------|-----------|-------------------------|------------- |-------------|
| 1.      | KB3186843 | Atualização de software & #42;  |  Normal <br></br>Não desorganização     | ~ 45 minutos |
| 2.      | KB3186859 | Controlador de LSI e firmware             |  Normal <br></br>Não desorganização      | ~ 20 minutos |
| 3.      | KB3121261 | Corrigir Storport e Spaceport </br> Windows Server 2012 R2 |  Normal <br></br>Não desorganização      | ~ 20 minutos |

& #42;  Actualização *nota, software consiste em duas ficheiros binários: atualização de software do dispositivo precedido com `all-hcsmdssoftwareupdate` e o agente Cis e a Mds precedido com `all-cismdsagentupdatebundle`. A atualização de software de dispositivo tem de estar instalada antes do agente Cis e a Mds. Também tem de reiniciar o controlador de ativo através de `Restart-HcsController` cmdlet depois de aplicar o agente Cis e a Mds atualizar (e antes de aplicar o restante atualiza).* 


**Se o seu dispositivo está a atualizar 0,1, 0,2, 0,3, 1.0, 1.1, 1.2 ou 2.0**, tem de transferir e instalar as seguintes correcções para além de software, controlador LSI e atualizações firmware (mostradas na tabela anterior), pela ordem prescrita:

| Ordem  | KB        | Descrição                    | Tipo de actualização  | Instalar o tempo |
|--------|-----------|-------------------------|------------- |-------------|
| 4.      | KB3146621 | pacote iSCSI | Normal <br></br>Não desorganização  | ~ 20 minutos |
| 5.      | KB3103616 | Pacote WMI |  Normal <br></br>Não desorganização      | ~ minutos 12 |


<br></br>

**Se o seu dispositivo está a ser executado versões 0,2, 0,3, 1.0, 1.1 e 1.2**, poderá também tem de instalar as atualizações de firmware do disco na parte superior de todas as atualizações apresentadas nas tabelas anteriores. Pode verificar se tem as atualizações de firmware disco ao executar o `Get-HcsFirmwareVersion` cmdlet. Se estiver a utilizar estas versões firmware: `XMGG`, `XGEG`, `KZ50`, `F6C2`, `VR08`, em seguida, não tem de instalar estas atualizações.


| Ordem  | KB        | Descrição                    | Tipo de actualização  | Instalar o tempo |
|--------|-----------|-------------------------|------------- |-------------|
| 6.      | KB3121899 | Firmware disco              |  Manutenção <br></br>Desorganização      | ~ 30 minutos |
 
<br></br>

> [AZURE.IMPORTANT]
>
> - Este procedimento tem de ser executado apenas uma vez para aplicar a atualização 3. Pode utilizar o portal do Azure clássico para aplicar atualizações subsequentes.
> - Se a atualizar a partir de atualização 2.2, a hora de instalação total é perto 1.1 horas.
> - Antes de utilizar este procedimento para aplicar a atualização, certifique-se de que tanto os controladores do dispositivo estão online e todos os componentes de hardware estão em bom Estados.

Execute os passos seguintes para transferir e instalar as correcções.

[AZURE.INCLUDE [storsimple-install-update3-hotfix](../../includes/storsimple-install-update3-hotfix.md)]

[AZURE.INCLUDE [storsimple-install-troubleshooting](../../includes/storsimple-install-troubleshooting.md)]

## <a name="next-steps"></a>Próximos passos

Saiba mais sobre a [versão 3 de atualização](storsimple-update3-release-notes.md).
