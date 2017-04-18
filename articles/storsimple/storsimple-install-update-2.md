<properties
   pageTitle="Instalar o Update 2 no seu dispositivo StorSimple | Microsoft Azure"
   description="Explica como instalar o StorSimple 8000 série Update 2 no seu dispositivo de série StorSimple 8000."
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
   ms.date="09/21/2016"
   ms.author="alkohli" />

# <a name="install-update-2-on-your-storsimple-device"></a>Instalar o Update 2 no seu dispositivo StorSimple

## <a name="overview"></a>Descrição geral

Neste tutorial explica como instalar o Update 2 num dispositivo StorSimple a executar uma versão anterior do software através do portal do Azure clássico. O tutorial também abrange os passos necessários para a atualização, quando um gateway está configurado numa interface de rede diferente de 0 de dados do dispositivo StorSimple e está a tentar atualizar a partir de uma versão anterior à atualização do software 1.

Actualização 2 inclui atualizações de software de dispositivo, LSI controlador actualizações e disco firmware atualizações. O software de dispositivo e actualizações de LSI são actualizações não desorganização e podem ser aplicadas através do portal do Azure clássico. As atualizações de firmware disco são actualizações desorganização e só podem ser aplicadas através da interface do Windows PowerShell do dispositivo.

> [AZURE.IMPORTANT]

> -  Poderá não conseguir ver Update 2 imediatamente, dado que recomendamos fazer uma implementação faseada das atualizações. Verificar a existência de atualizações em alguns dias novamente como esta atualização ficará disponível mais rapidamente.
> - Um conjunto de manuais e automáticas verificações pré é efetuado antes da instalação para determinar o estado de funcionamento do dispositivo em termos hardware estado e conectividade de rede. Estas verificações pré são executadas apenas se aplicar as atualizações a partir do Azure portal clássico.
> - Recomendamos que instale as atualizações de software e controlador através do portal do Azure clássica. Apenas devem ir para a interface do Windows PowerShell do dispositivo (para instalar atualizações) se a verificação de gateway anterior à atualização falhar no portal. As atualizações poderão demorar 7 de 4 horas para instalar (incluindo as atualizações do Windows). Tem de estar instaladas as atualizações do modo de manutenção através da interface do Windows PowerShell do dispositivo. Como as atualizações do modo de manutenção são desorganização atualizações, estes irão resultar num período de tempo para baixo para o seu dispositivo.
> - Se executar o Gestor de instantâneo StorSimple opcional, certifique-se de que tiver atualizado sua versão do Gestor de instantâneo para Update 2 antes de atualizar o dispositivo.

[AZURE.INCLUDE [storsimple-preparing-for-update](../../includes/storsimple-preparing-for-updates.md)]

## <a name="install-update-2-via-the-azure-classic-portal"></a>Instalar o Update 2 através do portal clássico do Azure

Execute os seguintes passos para atualizar o seu dispositivo para [Atualizar 2](storsimple-update2-release-notes.md).


> [AZURE.NOTE]
Actualização 2 permite à Microsoft separar informações adicionais de diagnóstico do dispositivo. Como resultado, quando a nossa equipa de operações identifica os dispositivos que estão a ter problemas, estamos melhor equipados para recolher informações a partir do dispositivo e diagnosticar problemas. Ao aceitar Update 2, permitir-nos fornecer este suporte pro-activos.

[AZURE.INCLUDE [storsimple-install-update2-via-portal](../../includes/storsimple-install-update2-via-portal.md)]

12. Certifique-se de que o dispositivo está em execução **StorSimple 8000 série Update 2 (6.3.9600.17673)**. **Última atualização data** também deve ser alterada. Também verá que estão disponíveis atualizações de modo de manutenção (esta mensagem poderá continuar a ser apresentados até 24 horas depois de instalar as atualizações).

    Atualizações de modo de manutenção estão desorganização atualizações que resultam em tempo de inatividade do dispositivo e só podem ser aplicadas através da interface do Windows PowerShell do seu dispositivo. Em alguns casos quando estiver a executar 1.2 de atualização, o firmware disco já poderá atualizado, caso em que não precisa de instale as atualizações de modo a manutenção.

13. Transfira as atualizações do modo de manutenção utilizando os passos indicados [para transferir correcções](#to-download-hotfixes) para procurar e transferir KB3121899, as atualizações de firmware instalações do disco (as outras atualizações já deverão estar instaladas por agora).

13. Siga os passos indicados no [instale e verifique se correcções de modo de manutenção](#to-install-and-verify-maintenance-mode-hotfixes) para instalar as atualizações de modo a manutenção.


## <a name="install-update-2-as-a-hotfix"></a>Instalar o Update 2 como correcção

Utilize este procedimento se falhar a verificação de gateway, quando tento instalar as atualizações através do portal do Azure clássica. A verificação falhar à medida que tem um gateway atribuído a uma interface de rede 0 não dados e o dispositivo está a executar uma versão de software antes de 1 de atualização.

As versões de software que podem ser actualizadas utilizando o método de correcção são atualização 0,1, atualização 0,2 e atualização 0,3, atualização 1, 1.1 de atualização e 1.2 de atualização. O método de correcção envolve os três passos seguintes:

- Transfira as correcções a partir do catálogo do Microsoft Update.
- Instalar e verifique se as correcções modo normal.
- Instalar e verifique se a correcção de modo a manutenção.

Para instalar Update 2 como correcção, tem de transferir e instalar as seguintes correcções:

| Ordem  | KB        | Descrição                    | Tipo de actualização  |
|--------|-----------|-------------------------|------------- |
| 1      | KB3121901 | Atualização de software         |  Normal     |
| 2      | KB3121900 | Controlador de LSI              |  Normal     |
| 3      | KB3080728 | Corrigir Storport </br> Windows Server 2012 R2 |  Normal     |
| 4      | KB3090322 | Corrigir spaceport </br> Windows Server 2012 R2 |  Normal     |
| 5      | KB3121899 | Firmware disco           | Manutenção  |


> [AZURE.IMPORTANT]
>
> - Se o seu dispositivo está a executar a versão de lançamento (das versões DG), contacte o [Suporte da Microsoft](storsimple-contact-microsoft-support.md) para ajudá-lo com a atualização.
> - Este procedimento tem de ser executado apenas uma vez para aplicar Update 2. Pode utilizar o portal do Azure clássico para aplicar atualizações subsequentes.
> - Instalação de cada correcção pode demorar cerca de 20 minutos a concluir. Tempo total da instalação é perto 2 horas.
> - Antes de utilizar este procedimento para aplicar a atualização, certifique-se de que ambos os controladores do dispositivo estão online e todos os componentes de hardware estão em bom Estados.

Execute os passos seguintes para aplicar esta atualização como correcção.

[AZURE.INCLUDE [storsimple-install-update2-hotfix](../../includes/storsimple-install-update2-hotfix.md)]

[AZURE.INCLUDE [storsimple-install-troubleshooting](../../includes/storsimple-install-troubleshooting.md)]



## <a name="next-steps"></a>Próximos passos

Saiba mais sobre a [versão de Update 2](storsimple-update2-release-notes.md).
