<properties
   pageTitle="Instalar a atualização 1.2 no seu dispositivo StorSimple | Microsoft Azure"
   description="Explica como instalar StorSimple 8000 série atualização 1.2 no seu dispositivo de série StorSimple 8000."
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
   ms.date="08/22/2016"
   ms.author="alkohli" />

# <a name="install-update-12-on-your-storsimple-device"></a>Instalar a atualização 1.2 no seu dispositivo StorSimple

## <a name="overview"></a>Descrição geral

Neste tutorial explica como instalar a atualização 1.2 num dispositivo StorSimple que esteja a executar uma versão de software antes de 1 de atualização. O tutorial também abrange os passos adicionais necessários para a atualização, quando um gateway está configurado numa interface de rede diferente de 0 de dados do dispositivo StorSimple.

Actualização 1.2 inclui atualizações de software de dispositivo, LSI controlador atualizações e disco firmware atualizações. O software e LSI controlador actualizações são actualizações não desorganização e podem ser aplicadas através do portal do Azure clássico. As atualizações de firmware disco são actualizações desorganização e só podem ser aplicadas através da interface do Windows PowerShell do dispositivo.

Dependendo de qual a versão do seu dispositivo está em execução, pode determinar se atualizar 1.2 serão aplicadas. Pode verificar a versão do software do seu dispositivo ao navegar para a secção **vista rápida** do seu dispositivo **Dashboard**.

</br>

| Se executar a versão de software …   | O que acontece no portal do?                              |
|---------------------------------|--------------------------------------------------------------|
| Lançamento - das versões DG                    | Se estiver a executar a versão de lançamento das versões (DG), não são aplicadas esta atualização. Utilize a [contactar o suporte da Microsoft](storsimple-contact-microsoft-support.md) para atualizar o seu dispositivo.|
| Atualizar 0,1                      | Portal aplica-se 1.2 de atualização.                                |
| Atualizar 0,2 mais próximo                      | Portal aplica-se 1.2 de atualização.                                |
| Atualizar 0,3                      | Portal aplica-se 1.2 de atualização.                                |
| Atualização 1                        | Esta atualização não estará disponível.                           |
| Atualizar 1.1                      | Esta atualização não estará disponível.                           |

</br>

> [AZURE.IMPORTANT]

> -  Poderá não conseguir ver atualização 1.2 imediatamente, dado que recomendamos fazer uma implementação faseada das atualizações. Verificar a existência de atualizações em alguns dias novamente como esta atualização ficará disponível mais rapidamente.
> - Esta atualização inclui um conjunto de manuais e automáticas verificações pré para determinar o estado de funcionamento do dispositivo em termos hardware estado e conectividade de rede. Estas verificações pré são executadas apenas se aplicar as atualizações a partir do Azure portal clássico.
> - Recomendamos que instale as atualizações de software e controlador através do portal do Azure clássica. Apenas devem ir para a interface do Windows PowerShell do dispositivo (para instalar atualizações) se a verificação de gateway anterior à atualização falhar no portal. As atualizações poderão demorar horas de 5 a 10 para instalar (incluindo as atualizações do Windows). Tem de estar instaladas as atualizações do modo de manutenção através da interface do Windows PowerShell do dispositivo. Como as atualizações do modo de manutenção são desorganização atualizações, estes irão resultar num período de tempo para baixo para o seu dispositivo.

[AZURE.INCLUDE [storsimple-preparing-for-update](../../includes/storsimple-preparing-for-updates.md)]

## <a name="install-update-12-via-the-azure-classic-portal"></a>Instalar a atualização 1.2 através do portal clássico do Azure

Execute os seguintes passos para atualizar o seu dispositivo para [Atualizar 1.2](storsimple-update1-release-notes.md). Utilize este procedimento só se tiver um gateway configurado na interface de 0 rede de dados no seu dispositivo.

[AZURE.INCLUDE [storsimple-install-update2-via-portal](../../includes/storsimple-install-update2-via-portal.md)]

12. Certifique-se de que o dispositivo está em execução **StorSimple 8000 série atualização 1.2 (6.3.9600.17584)**. **Última atualização data** também deve ser alterada. Também verá que estão disponíveis atualizações de modo de manutenção (esta mensagem poderá continuar a ser apresentados até 24 horas depois de instalar as atualizações).

    Atualizações de modo de manutenção estão desorganização atualizações que resultam em tempo de inatividade do dispositivo e só podem ser aplicadas através da interface do Windows PowerShell do seu dispositivo.

    ![Página de manutenção] (./media/storsimple-install-update-1/InstallUpdate12_10M.png "Página de manutenção")

13. Transfira as atualizações do modo de manutenção utilizando os passos indicados [para transferir correcções]( #to-download-hotfixes) para procurar e transferir KB3063416, as atualizações de firmware instalações do disco (as outras atualizações já deverão estar instaladas por agora).

13. Siga os passos indicados no [instale e verifique se correcções de modo de manutenção](#to-install-and-verify-maintenance-mode-hotfixes) para instalar as atualizações de modo a manutenção.

14. No portal do Azure clássico, navegue para a página de **Manutenção** e na parte inferior da página, clique em **Pesquisar atualizações** para verificar as atualizações do Windows e, em seguida, clique em **Instalar atualizações**. Terminar após de todas as atualizações são instaladas com êxito.



## <a name="install-update-12-on-a-device-that-has-a-gateway-configured-for-a-non-data-0-network-interface"></a>Instalar a atualização 1.2 num dispositivo que tenha um gateway configurado para uma interface de rede 0 sem dados

Deve utilizar este procedimento só se falhar a verificação de gateway, quando tento instalar as atualizações através do portal do Azure clássica. A verificação falhar à medida que tem um gateway atribuído a uma interface de rede 0 não dados e o dispositivo está a executar uma versão de software antes de 1 de atualização. Se o seu dispositivo não tiver um gateway numa interface de rede 0 não dados, pode atualizar o seu dispositivo diretamente a partir do portal de clássico Azure. Consulte o artigo [instalar atualizar 1.2 através do portal do Azure clássico](#install-update-1.2-via-the-azure-classic-portal).

As versões de software que podem ser actualizadas utilizando este método são Update 0,1, atualização 0,2 e Update 0,3.


> [AZURE.IMPORTANT]
>
> - Se o seu dispositivo está a executar a versão de lançamento (das versões DG), contacte o [Suporte da Microsoft](storsimple-contact-microsoft-support.md) para ajudá-lo com a atualização.
> - Este procedimento tem de ser executado apenas uma vez para aplicar a atualização 1.2. Pode utilizar o portal do Azure clássico para aplicar atualizações subsequentes.

Se o dispositivo está a ser executado anterior à atualização 1 software e que tem um gateway definido para uma interface de rede diferente de 0 de dados, pode aplicar 1.2 atualização das seguintes duas formas:

- **Opção 1**: Transferir a actualização e aplicá-la através de `Start-HcsHotfix` cmdlet a partir da interface do Windows PowerShell do dispositivo. Este é o método recomendado. **Não utilize este método para aplicar a atualização 1.2 se o seu dispositivo está a executar Update 1.0 ou 1.1 de atualização.**

- **Opção 2**: remover a configuração de gateway e instale a atualização diretamente a partir do portal de clássico Azure.


Nas secções seguintes, são fornecidas instruções detalhadas para cada um dos seguintes procedimentos.

## <a name="option-1-use-windows-powershell-for-storsimple-to-apply-update-12-as-a-hotfix"></a>Opção 1: Utilizar o Windows PowerShell para StorSimple aplicar a atualização 1.2 como correcção

Deve utilizar este procedimento apenas se estiver a executar o atualização 0,1, 0,2, 0,3 e se a verificação de gateway falhou quando tento instalar atualizações a partir do Azure portal clássico. Se estiver a executar software das versões lançamento (DG), contacte o [Suporte da Microsoft](storsimple-contact-microsoft-support.md) para atualizar o seu dispositivo.

Para instalar a atualização 1.2 como correcção, tem de transferir e instalar as seguintes correcções:

| Ordem  | KB        | Descrição             | Tipo de actualização  |
|--------|-----------|-------------------------|------------- |
| 1      | KB3063418 | Atualização de software         |  Normal     |
| 2      | KB3043005 | Atualização do controlador de LSI SA |  Normal     |
| 3      | KB3063416 | Firmware disco           | Manutenção  |

Antes de utilizar este procedimento para aplicar a atualização, certifique-se que:

- Ambos os controladores do dispositivo estão disponíveis online.

Execute os passos seguintes para aplicar a atualização 1.2. **As atualizações poderá demorar cerca de 2 horas a concluir (aproximadamente 30 minutos para software, 30 minutos para controlador, 45 minutos para firmware disco).**

[AZURE.INCLUDE [storsimple-install-update-option1](../../includes/storsimple-install-update-option1.md)]


## <a name="option-2-use-the-azure-classic-portal-to-apply-update-12-after-removing-the-gateway-configuration"></a>Opção 2: Utilizar o portal do clássico Azure para aplicar a atualização 1.2 depois de remover a configuração de gateway

Este procedimento só se aplica a dispositivos de StorSimple que estiver a executar uma versão de software antes de 1 de atualização e tem um gateway configurar numa interface de rede que não seja dados 0. Terá de desmarcar a definição de gateway antes de aplicar a atualização.

A atualização pode demorar algumas horas para concluir. Se os anfitriões são em sub-redes diferentes, remover a configuração de gateway nas interfaces iSCSI poderá resultar em tempo de inatividade. Recomendamos que configure dados 0 para o tráfego de iSCSI para reduzir o tempo de inatividade.

Execute os passos seguintes para desativar a interface de rede com o gateway e, em seguida, aplicar a atualização.

[AZURE.INCLUDE [storsimple-install-update-option2](../../includes/storsimple-install-update-option2.md)]

[AZURE.INCLUDE [storsimple-install-troubleshooting](../../includes/storsimple-install-troubleshooting.md)]


## <a name="next-steps"></a>Próximos passos

Saiba mais sobre a [versão 1.2 de atualização](storsimple-update1-release-notes.md).
