<properties
   pageTitle="Falhas recuperação e dispositivo activação pós-falha para a sua matriz de Virtual StorSimple"
   description="Saiba mais sobre como activação pós-falha sua matriz de Virtual StorSimple."
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
   ms.date="06/07/2016"
   ms.author="alkohli"/>

# <a name="disaster-recovery-and-device-failover-for-your-storsimple-virtual-array"></a>Falhas recuperação e dispositivo activação pós-falha para a sua matriz de Virtual StorSimple


## <a name="overview"></a>Descrição geral

Este artigo descreve a recuperação de falhas para o Microsoft Azure StorSimple Virtual matriz (também conhecido como o StorSimple no local virtual dispositivo), incluindo os passos detalhados necessários para falhar sobre para outro dispositivo virtual eventualidade de um. Uma activação pós-falha permite-lhe migrar os dados a partir de um dispositivo de *origem* no Centro de dados para outro dispositivo de *destino* localizado na mesma ou noutra localização geográfica. O dispositivo activação pós-falha é para todo o dispositivo. Durante a falha na ligação, os dados de nuvem para o dispositivo de origem forem alterados que é o proprietário para que o dispositivo de destino.

Dispositivo activação é orquestrada através da funcionalidade de recuperação (DR) falhas e é iniciada a partir da página de **dispositivos** . Esta página organiza todos os dispositivos de StorSimple ligados ao seu serviço de Gestor de StorSimple. Para cada dispositivo, o nome amigável, estado, capacidade aprovisionada e máxima, tipo e modelo são apresentadas.

![](./media/storsimple-ova-failover-dr/image15.png)

Este artigo é aplicável às matrizes Virtual StorSimple apenas. Falha ao longo de um dispositivo de 8000 série, aceda a [activação e recuperação de falhas do seu dispositivo StorSimple](storsimple-device-failover-disaster-recovery.md).


## <a name="what-is-disaster-recovery"></a>O que é a recuperação de falhas?

Num cenário de recuperação (DR) falhas, o dispositivo primário deixa de funcionar. Esta situação, pode mover os dados da nuvem associados ao dispositivo falhado para outro dispositivo utilizando o dispositivo primário como a *origem* e especificando noutro dispositivo como o *destino*. Este processo é referido como a *activação pós-falha*. Durante a falha na ligação, as partilhas partir do dispositivo de origem ou de todos os volumes alterar a propriedade e são transferidas para o dispositivo de destino. Sem filtro dos dados é permitida.

DR é considerado um restauro de dispositivo completo utilizando o térmico com base em mapas tiering e registo. Um mapa térmico definido pelo atribuir um valor de térmico para os dados com base em ler e escrever padrões. Este térmico mapear, em seguida, camadas os blocos de dados térmico mais baixos na nuvem pela primeira vez mantendo os blocos de dados de alta térmico (mais utilizado) na camada local. Durante uma DR, mapa térmico é utilizado para restaurar e rehydrate os dados a partir da nuvem. O dispositivo obtém todos os volumes/acções da última cópia de segurança recente (conforme determinado internamente) e executa um restauro dessa cópia de segurança. Todo o processo de DR é orquestrado pelo dispositivo.


## <a name="prerequisites-for-device-failover"></a>Pré-requisitos para activação pós-falha de dispositivo


### <a name="prerequisites"></a>Pré-requisitos

Para falha na ligação qualquer dispositivo, devem ser cumpridos os pré-requisitos seguintes:

- O dispositivo de origem tem de estar num estado **Deactivated** .

- O dispositivo de destino tem de ser apresentada como **ativo** no portal do Azure clássico. Tem de aprovisionamento de um dispositivo virtual do destino da capacidade iguais ou superior. Em seguida, deve utilizar web local da IU para configurar e registar com êxito o dispositivo virtual.

    > [AZURE.IMPORTANT] Não tente configurar o dispositivo virtual registado através do serviço, clicando em **configuração de dispositivo completo**. Configuração do dispositivo deve ser executada através do serviço.

- O dispositivo de origem e destino tem de ser do mesmo tipo. Apenas pode falhar ao longo de um dispositivo virtual configurado como um servidor do ficheiro para outro servidor do ficheiro. O mesmo se VERDADEIRO para um servidor iSCSI.

- Para servidor de ficheiros DR, recomendamos que participar o dispositivo de destino para o mesmo domínio como que da origem de modo a que as permissões de partilha são automaticamente resolvidas. Apenas a activação pós-falha para um dispositivo de destino no mesmo domínio é suportada neste lançamento.

### <a name="other-considerations"></a>Outras considerações

- Recomendamos que tome todos os volumes ou partilhas no dispositivo origem offline.

- Se for um activação planeada pós-falha, recomendamos que utiliza uma cópia de segurança do dispositivo e prossiga com a activação pós-falha para minimizar as perdas de dados. Se for um activação não planeado pós-falha, a cópia de segurança mais recente será utilizada para restaurar o dispositivo.

- Os dispositivos de destino disponíveis para DR são dispositivos que tem a capacidade de polegadas ou mesmo em comparação com o dispositivo de origem. Os dispositivos que estão ligados ao seu serviço mas não satisfizer os critérios de espaço suficiente não estarão disponíveis como dispositivos de destino.

### <a name="dr-prechecks"></a>DR prechecks

Antes de começa o DR, prechecks são executadas no dispositivo. Estas verificações ajudam a garantir que não há erros irão ocorrer quando DR começa. Os prechecks incluem:

- Validar a conta de armazenamento

- Verificar a conectividade da nuvem para Azure

- Verificar o espaço disponível no dispositivo de destino

- Verificar se um dispositivo de origem do servidor de iSCSI tem os nomes ACR válidos, IQN (não que excedam os carateres de comprimento 220) e palavra-passe CHAP (12 e 16 carateres de comprimento) associados com os volumes

Se qualquer as prechecks acima falhar, não é possível continuar com o DR. Tem de resolver esses problemas e, em seguida, volte a tentar DR.

Depois do DR é concluído com êxito, o que é o proprietário dos dados da nuvem no dispositivo origem é transferido para o dispositivo de destino. O dispositivo de origem, em seguida, já não se encontra disponível no portal. Acesso a todas as volumes/partilhas no dispositivo origem está bloqueado e o dispositivo de destino fica ativo.

> [AZURE.IMPORTANT]
> 
> Apesar do dispositivo já não se encontra disponível, a máquina virtual que aprovisionado no sistema anfitrião ainda está a consumir recursos. Assim que a DR estiver concluído com êxito, pode eliminar esta máquina virtual do seu sistema anfitrião.

## <a name="fail-over-to-a-virtual-array"></a>Sobre a uma matriz virtual falhar

Recomendamos que tem outra matriz de Virtual StorSimple aprovisionado, configuradas através da web local IU e registado com o serviço do Gestor de StorSimple antes de executar este procedimento.


> [AZURE.IMPORTANT]
> 
> - Não são permitidas provocam a falha ao longo de um dispositivo de série StorSimple 8000 para um dispositivo virtual 1200.
> - Pode falhar sobre a partir de um dispositivo de virtual Federal Information Processing padrão (FIPS) ativado implementado no portal de administração pública para um dispositivo virtual no Azure portal clássico. O inverso também é verdadeiro.

Execute os passos seguintes para restaurar o dispositivo para um dispositivo virtual do StorSimple de destino.

1. Tomar volumes/partilhas offline no anfitrião do. Consulte as instruções específicas do sistema operativo – no anfitrião do colocar volumes/partilhas offline. Se não já offline, terá de ter todas as volumes/partilhas offline no dispositivo ao aceder a **dispositivos > partilhas** (ou **dispositivo > Volumes**). Selecione um partilhar/volume e clique em **colocar offline** na parte inferior da página. Quando lhe for pedido de confirmação, clique em **Sim**. Repita este processo para todos os partilhas/volumes no dispositivo.

2. Na página de **dispositivos** , selecione o dispositivo de origem para activação pós-falha e clique em **desativar**. 
    ![](./media/storsimple-ova-failover-dr/image16.png)

3. Será pedido de confirmação. A desativação de dispositivo é um processo permanente que não pode ser anulado. Também ser lembrado para tirar os partilhas/volumes offline no anfitrião do.

    ![](./media/storsimple-ova-failover-dr/image18.png)

3. Após a confirmação, a desativação será iniciado. Após a desativação é concluída com êxito, será notificado.

    ![](./media/storsimple-ova-failover-dr/image19.png)

4. Na página de **dispositivos** , o estado do dispositivo agora será alterado para **Deactivated**.

    ![](./media/storsimple-ova-failover-dr/image20.png)

5. Selecione o dispositivo desativado e na parte inferior da página, clique em **activação pós-falha**.

6. No Assistente de activação pós-falha confirmar que é aberta para cima, faça o seguinte:

    1. Na lista pendente de dispositivos disponíveis, selecione um **dispositivo de destino.** Apenas os dispositivos que tem uma capacidade suficiente são apresentados na lista pendente.

    2. Reveja os detalhes associados com o dispositivo de origem, como o nome do dispositivo, capacidade total e os nomes dos partilhas que irão Ocorreu uma falha ao longo do.

        ![](./media/storsimple-ova-failover-dr/image21.png)

7. Verifique **que concordo que activação pós-falha é uma operação permanente e assim que a activação pós-falha é concluída com êxito, o dispositivo de origem será eliminado**.

8. Clique no ícone de verificação ![](./media/storsimple-ova-failover-dr/image1.png).


9. Uma tarefa de activação pós-falha será iniciada e será notificado. Clique em **tarefa da vista** para monitorizar a activação pós-falha.

    ![](./media/storsimple-ova-failover-dr/image22.png)

10. Na página **tarefas** , verá uma tarefa de activação pós-falha criada para o dispositivo de origem. Esta tarefa executa os prechecks DR.

    ![](./media/storsimple-ova-failover-dr/image23.png)

    Depois dos prechecks DR estarem efetuada com êxito, a tarefa de activação pós-falha irá expandir a restaurar tarefas para cada partilhar/volume que existe no seu dispositivo de origem.

    ![](./media/storsimple-ova-failover-dr/image24.png)

11. Depois de concluída a activação pós-falha, vá para a página de **dispositivos** .

    um. Selecione o dispositivo virtual StorSimple que foi utilizado como o dispositivo de destino para o processo de activação pós-falha.

    b. Aceda à página **partilhas** (ou **Volumes** se servidor iSCSI). Agora deverão estar listadas todas as partilhas (volumes) a partir do dispositivo antigo.
    
    ![](./media/storsimple-ova-failover-dr/image25.png)

![](./media/storsimple-ova-failover-dr/video_icon.png)**Vídeo disponível**

Este vídeo demonstra como pode falhar ao longo de um dispositivo virtual StorSimple no local para outro dispositivo virtual.

> [AZURE.VIDEO storsimple-virtual-array-disaster-recovery]

## <a name="business-continuity-disaster-recovery-bcdr"></a>Recuperação de falhas de continuidade do negócio (BCDR)

Um cenário de recuperação (BCDR) de falhas de continuidade do negócio ocorre quando o Centro de dados Azure inteiro deixa de funcionar. Isto pode afectar seu serviço de StorSimple gestor e os dispositivos de StorSimple associados.

Se existirem StorSimple dispositivos que foram registados antes de uma falhas ocorreram, poderão ter nestes dispositivos StorSimple a ser eliminada. Depois de falhas, pode recriar e configurar os dispositivos.

## <a name="errors-during-dr"></a>Erros durante a DR

**Falha de conectividade de nuvem durante DR**

Se a conectividade de nuvem é interrompida depois de ter começado a DR e antes da restaurar dispositivo estiver concluída, o DR falhará e será notificado. O dispositivo de destino que foi utilizado para DR, em seguida, é assinalado como *inutilizável.* O mesmo dispositivo de destino não pode ser utilizado, em seguida, para DRs futuras.

**Nenhum dos dispositivos compatíveis de destino**

Se os dispositivos de destino disponíveis não possui espaço suficiente, verá um erro para o efeito que não existem dispositivos compatíveis de destino.

**Falhas precheck**

Se um das prechecks não for cumprido, em seguida, irá ver falhas precheck.

## <a name="next-steps"></a>Próximos passos

Saiba mais sobre como [administrar a sua matriz de Virtual StorSimple através da IU da web local](storsimple-ova-web-ui-admin.md).
