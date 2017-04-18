<properties 
   pageTitle="StorSimple localmente afixados volumes FAQ | Microsoft Azure"
   description="Fornece respostas às perguntas mais frequentes sobre os volumes de StorSimple localmente afixado."
   services="storsimple"
   documentationCenter="NA"
   authors="manuaery"
   manager="syadav"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="08/16/2016"
   ms.author="manuaery" />

# <a name="storsimple-locally-pinned-volumes-frequently-asked-questions-faq"></a>StorSimple localmente afixados volumes: Perguntas mais frequentes (FAQ)

## <a name="overview"></a>Descrição geral

Seguem-se perguntas e respostas que pode ter ao criar um volume StorSimple localmente afixado, converter um volume em camadas para um volume localmente afixado (e vice-versa), ou criar cópias de segurança e restaurar um localmente afixado volume.

Perguntas e respostas são dispostas das categorias seguintes

- Criar um volume localmente afixado
- Cópias de segurança localmente afixada
- Converter um volume em camadas para um volume localmente afixado
- Restaurar um volume localmente afixado
- Falha ao longo de um volume localmente afixada

## <a name="questions-about-creating-a-locally-pinned-volume"></a>Perguntas sobre a criação de um volume localmente afixado

**Q.** O que é o tamanho máximo de um volume localmente afixado que posso pode criar nos dispositivos 8000 série?

**A** aprovisionar volumes localmente afixados 8,5 TB ou volumes em camadas no dispositivo 8100 até 200 TB. No dispositivo 8600 maior, pode aprovisionar o volumes localmente afixados 22,5 TB ou em camadas volumes até 500 TB.

**Q.** Recentemente actualizado meu dispositivo 8100 Update 2, sendo quando tenta criar um volume localmente afixado, o tamanho máximo disponível apenas 6 TB e não 8,5 TB. Por que motivo não consigo criar um volume de 8,5 TB?

**A** aprovisionar volumes localmente afixados até 8.5 TB ou camadas volumes de até 200 TB no dispositivo 8100. Se o seu dispositivo tem já camadas volumes, em seguida, o espaço disponível para a criação de um volume localmente afixado será proporcionalmente inferior este limite máximo. Por exemplo, se já foram aprovisionado 100 TB de volumes em camadas no seu dispositivo 8100 (que é metade da capacidade em camadas), em seguida, o tamanho máximo de um local volume que pode criar no dispositivo 8100 será correspondente reduzido a 4 TB (aproximadamente metade ao máximo localmente afixados capacidade de volume).

Porque algum espaço no local no dispositivo é utilizado para alojar o conjunto de trabalho de volumes em camadas, o espaço disponível para a criação de um localmente afixado volume é reduzido se o dispositivo tem camadas volumes. Por outro lado, criar um volume localmente afixado proporcionalmente reduz o espaço disponível para volumes em camadas. A tabela seguinte resume a capacidade disponível em camadas nos dispositivos 8100 e 8600 quando são criados volumes localmente afixados.

|Capacidade de aprovisionada volumes localmente afixada|Capacidade disponível para ser aprovisionada para volumes em camadas - 8100|Capacidade disponível para ser aprovisionada para volumes em camadas - 8600|
|-----|------|------|
|0 | 200 TB | 500 TB |
|1 TB | 176.5 TB | 477.8 TB|
|4 TB | 105.9 TB | 411.1 TB |
|8.5 TB | 0 TB | 311.1 TB|
|10 TB | DISP | 277.8 TB |
|15 TB | DISP | 166.7 TB |
|22.5 TB | DISP | 0 TB |


**Q.** Porque é que a criação de volume localmente afixada uma operação de execução longa? 

**RESPOSTAS.** Volumes localmente afixados configurações thickly. Para criar espaço nas camadas locais do dispositivo, alguns dados a partir de existentes volumes em camadas podem ser enviados para a nuvem durante o processo de aprovisionamento. E uma vez que esta situação depende do tamanho do volume a ser aprovisionado, os dados existentes no seu dispositivo e a largura de banda disponível na nuvem, o tempo despendido para criar um local volume pode ser várias horas.

**Q.** Quanto tempo demora para criar um localmente afixado volume?

**RESPOSTAS.** Uma vez que volumes localmente afixados configurações thickly, alguns dados existentes a partir de volumes em camadas poderão enviados para a nuvem durante o processo de aprovisionamento. Por conseguinte, o tempo despendido para criar um volume localmente afixado depende vários fatores, incluindo o tamanho do volume, os dados no seu dispositivo e a largura de banda disponível. Num dispositivo recentemente instalado que tenha sem volumes, o tempo para criar um localmente afixado volume é de cerca de 10 minutos por terabyte de dados. No entanto, a criação de volumes locais poderá demorar algumas horas com base em fatores acima explicados num dispositivo que está a ser utilizado.

**Q.** Pretende criar um volume localmente afixado. Existem qualquer práticas recomendadas que é necessário ter em mente?

**RESPOSTAS.** Volumes localmente afixados são adequados para das cargas de trabalho que requerem garantias locais de dados sempre e são sensíveis a maiúsculas e na nuvem latências. Enquanto considerar a utilização da volumes locais para qualquer uma das cargas de trabalho, tenha em atenção dos seguintes procedimentos:

- Volumes localmente afixados configurações thickly e criação de volumes locais impactos o espaço disponível para volumes em camadas. Por conseguinte, sugerimos que comece com volumes de tamanho mais pequeno e Dimensionar para cima, como os aumentos requisito de armazenamento.

- Aprovisionamento de volumes locais é uma operação de execução longa que poderá envolvam conduza dados existentes a partir de volumes em camadas para a nuvem. Como resultado, que se poderá deparar desempenho reduzido nestes volumes.

- Aprovisionamento de volumes locais é uma operação demorada. O tempo real envolvido depende vários fatores: o tamanho do volume a ser aprovisionado, dados no seu dispositivo e a largura de banda disponível. Se não tiver cópia volumes de existentes na nuvem, a criação de volume é mais lenta. Sugerimos que instantâneos nuvem de volumes de existentes antes de aprovisionar o local volume.
 
- Pode converter volumes em camadas existentes volumes localmente afixados e esta conversão envolve aprovisionamento de espaço no dispositivo para o volume localmente afixado resultante (para além de os incluir para baixo em camadas dados [se qualquer] a partir da nuvem). Novamente, esta é uma operação de execução longa que depende fatores que discutimos acima. Sugerimos que faça uma cópia de segurança os volumes existentes antes de conversão conforme o processo será ainda mais lento se volumes existentes não cópia de segurança abrange. O dispositivo também poderá deparar reduzido desempenho durante este processo.
    
Obter mais informações sobre como [criar um volume localmente afixado](storsimple-manage-volumes-u2.md#add-a-volume)

**Q.** Pode criar vários volumes localmente afixados ao mesmo tempo?

**RESPOSTAS.** Sim, mas quaisquer tarefas de criação e de expansão de volume localmente afixada são processados sequencialmente.

Volumes localmente afixados configurações thickly e isto requer que a criação de espaço local no dispositivo (que poderá resultar em dados existentes a partir de volumes em camadas para ser enviados para a nuvem durante o processo de aprovisionamento). Por isso, se uma tarefa de aprovisionamento estiver em curso, outras tarefas de criação de local volume serão ser em fila de espera até essa tarefa estiver concluída.

Da mesma forma, se estiver a ser expandido um volume local existente ou um volume em camadas está a ser convertido num volume localmente afixado, em seguida, a criação de um novo volume localmente afixada é em fila de espera até que a tarefa anterior seja concluída. Expandir o tamanho de um volume localmente afixado envolve a expansão do espaço existente local para esse volume. Conversão a partir de um em camadas para localmente afixados volume envolve também a criação de espaço local para o resultante localmente afixados volume. No tanto destas operações, criação ou expansão de espaço local é um valor longo a executar o trabalho.

Pode ver estas tarefas na página do serviço Azure StorSimple Gestor de **tarefas** . A tarefa que está a ser processada ativamente continuamente é atualizada para refletir o progresso de aprovisionamento de espaço. As tarefas restantes do volume localmente afixada é assinalado como em execução, mas o progresso está parado e são recolhidos pela ordem em que foram em fila de espera.

**Q.** Eliminei um localmente afixado volume. Por que motivo não consigo ver o espaço regenerado refletido no espaço disponível quando tento criar um novo volume? 

**RESPOSTAS.** Se eliminar um volume localmente afixado, o espaço disponível para novos volumes pode não ser atualizado imediatamente. O serviço do Gestor de StorSimple atualiza o local espaço disponível (Product Key) a cada hora. Sugerimos que aguardar uma hora antes de tentar criar o novo volume.

**Q.** Volumes localmente afixados são suportados no aparelho nuvem?

**RESPOSTAS.** Volumes localmente afixados não são suportados no aparelho na nuvem (8010 e 8020 dispositivos anteriormente designados o dispositivo virtual StorSimple).

**Q.** Pode utilizar os cmdlets do Azure PowerShell para criar e gerir volumes localmente afixados? 

**RESPOSTAS.** Não, não é possível criar volumes localmente afixados através do Azure PowerShell cmdlets (qualquer volume que criar através do Azure PowerShell é camada). Sugerimos também que não utilize os cmdlets do Azure PowerShell para modificar as propriedades de um volume localmente afixada, tal como este terá efeito indesejado modificar o tipo de volume para em camadas.

## <a name="questions-about-backing-up-a-locally-pinned-volume"></a>Perguntas sobre como criar cópias de um volume localmente afixado

**Q.** Estão instantâneos locais de volumes localmente afixados suportados?

**RESPOSTAS.** Sim, pode tirar instantâneos locais do seu volumes localmente afixados. No entanto, recomendamos vivamente que regularmente cópia os volumes localmente afixados instantâneos na nuvem para se certificar de que os seus dados estão protegidos na eventualidade de uma falhas.

**Q.** Existem qualquer diretrizes para gerir instantâneos locais para volumes localmente afixados?

**RESPOSTAS.** Frequentes instantâneos locais juntamente com uma taxa alto de vasilha de dados do volume localmente afixada poderão causar espaço local no dispositivo para ser consumida rapidamente e resultar em dados a partir de volumes em camadas a ser enviados para a nuvem. Sugerimos, por conseguinte, que pode minimiza o número de instantâneos locais.

**Q.** Recebi um aviso a indicar que poderão ser invalidados meu locais instantâneos dos volumes localmente afixados. Quando pode isto acontece?

**RESPOSTAS.** Frequentes instantâneos locais juntamente com uma taxa alto de vasilha de dados do volume localmente afixada poderão causar espaço local no dispositivo para ser consumida rapidamente. Se as camadas locais do dispositivo fortemente forem utilizadas, uma falha na nuvem adicional poderá resultar num dispositivo a tornar-se completo e escritas recebidas para o volume poderão resultar em anulação dos instantâneos (tal como sem espaço não existe para atualizar os instantâneos para consultar os blocos de dados que tenham sido substituídos mais antigos). Dessa situação escritas para o volume irão continuar a ser fornecido, mas os instantâneos locais podem ser inválidos. Não existe nenhuma impacto na sua instantâneos nuvem existente.

É o aviso de alerta a notificá-lo que esta situação pode surgir e certifique-se de que endereço da mesma forma atempada ao rever as agendas de instantâneos local tirar um instantâneo local menos frequente ou eliminar mais antigos instantâneos locais que já não são necessários.

Se os locais instantâneos são invalidados, receberá um alerta de informações notificá-lo de que os instantâneos locais para a política de cópia de segurança específico tem sido invalidados juntamente com a lista de selos de tempo dos locais instantâneos que foram invalidados. Estes instantâneos que serão eliminados automaticamente e já não poderão para visualizá-los na página de **Catálogos de cópia de segurança** no portal do Azure clássica.

## <a name="questions-about-converting-a-tiered-volume-to-a-locally-pinned-volume"></a>Perguntas sobre a conversão de um volume em camadas para um volume localmente afixado

**Q.** Estou a observar o algumas lentidão no dispositivo enquanto converter um volume em camadas para um localmente afixado volume. Por que motivo é que isto acontece? 

**RESPOSTAS.** O processo de conversão envolve dois passos: 

  1. Aprovisionamento de espaço no dispositivo para o mais cedo-para--convertido em localmente afixada volume.
  2. Garantias de quaisquer dados em camadas a ser transferidas a partir da nuvem para se certificar de local.

Ambos os passos são longos executar operações que são dependentes do tamanho do volume a ser convertido, os dados no dispositivo e largura de banda disponível. À medida que alguns dados a partir de existentes volumes em camadas poderão ajustar na nuvem, como parte do processo de aprovisionamento, o seu dispositivo poderá deparar reduzido desempenho durante este período de tempo. Além disso, o processo de conversão pode ser mais lento se:

- Não foi cópia volumes existentes na nuvem; para que sugerimos que os volumes antes de iniciar uma conversão de cópia de segurança.

- Políticas de optimização da largura de banda foram aplicadas, que podem restringir a largura de banda disponível para a nuvem; Recomendamos que, por conseguinte, ter um Mbps 40 dedicado ou mais uma ligação para a nuvem.

- O processo de conversão pode demorar algumas horas devido as vários factores acima; explicados Por conseguinte, sugerimos que efetuar esta operação durante as horas que não sejam picos ou no fim de uma semana para evitar o impacto na consumidores de fim.

Obter mais informações sobre como [converter um volume em camadas para um volume localmente afixado](storsimple-manage-volumes-u2.md#change-the-volume-type)

**Q.** Pode cancelar a operação de conversão de volume?

**RESPOSTAS.** Não, não pode cancelar a operação de conversão iniciada uma vez. Tal como é abordado na pergunta anterior, tenha em atenção dos potenciais problemas de desempenho que poderá encontrar durante o processo e siga os procedimentos recomendados listados em cima quando planear a sua conversão.

**Q.** O que acontece aos meus volume se a operação de conversão falhar?

**RESPOSTAS.** Conversão de volume poderá falhar devido a problemas de conectividade de nuvem. O dispositivo eventualmente poderá deixar o processo de conversão depois de uma série de tentativas para trazer para baixo em camadas dados a partir da nuvem. Cenário, o tipo de volume irão continuar a ser o tipo de volume de origem antes de conversão, e:

- Um alerta crítico será elevado ao notificá-lo a falha na conversão de volume. Obter mais informações sobre [alertas relacionados com volumes localmente afixados](storsimple-manage-alerts.md#locally-pinned-volume-alerts)

- Se estiver a converter uma em camadas para um volume localmente afixado, o volume irão continuar a apresentar as propriedades de um volume em camadas como dados poderão ainda se encontram na nuvem. Sugerimos que resolver os problemas de conectividade e, em seguida, repita a operação de conversão.
 
- Da mesma forma, quando a conversão de localmente afixada para um volume em camadas falha, apesar do volume será marcado como um volume localmente afixado,-lo irá funcionar como um em camadas volume (uma vez que poderiam ter passado dados na nuvem). No entanto, este continuará a ocupar espaço nas camadas locais do dispositivo. Este espaço só estarão disponível para outros volumes localmente afixados. Sugerimos que repita esta operação para se certificar de que a conversão de volume é concluída e o espaço no dispositivo local pode ser recuperado.

## <a name="questions-about-restoring-a-locally-pinned-volume"></a>Perguntas sobre como restaurar um volume localmente afixado

**Q.** São volumes localmente afixados restaurados instantaneamente?

**RESPOSTAS.** Sim, volumes localmente afixados são restaurados instantaneamente. Assim que as informações de metadados para o volume são movidas a partir da nuvem, como parte da operação de restaurar, o volume estiver online e pode ser acedido por anfitrião do. No entanto, as garantias locais para o volume de dados não será apresentados até que tenha sido transferida todos os dados a partir da nuvem e que se possa deparar reduzido desempenho nestes volumes para a duração de restaurar.

**Q.** Quanto tempo demora para restaurar um localmente afixado volume?

**RESPOSTAS.** Os volumes localmente afixados são restaurados instantaneamente e colocados online assim que as informações de metadados de volume são obtidas a partir da nuvem, enquanto os dados do volume continua a ser transferido em segundo plano. Este parte último da operação de restaurar – obter novamente as garantias locais para os dados do volume é uma operação de execução longa e poderá demorar algumas horas para todos os dados ser efetuadas local novamente. O tempo despendido para concluir a mesma depende fatores de múltiplos, como o tamanho do volume a ser restaurado e a largura de banda disponível. Se tiver sido eliminado o volume original que está a ser restaurado, tempo adicional serão tidos para criar o espaço local no dispositivo, como parte da operação de restaurar.

**Q.** É necessário restaurar o meu volume localmente afixada existente para um instantâneo mais antigo (realizado quando o volume foi camado). Será o volume restaurado como camadas neste caso?

**RESPOSTAS.** Não, o volume será restaurado como um localmente afixado volume. Apesar das datas de instantâneo ao tempo quando o volume foi camado, enquanto a restaurar volumes existentes, StorSimple utiliza sempre o tipo de volume no disco tal como se encontra atualmente.

**Q.** Posso expandido meu volume localmente afixada recentemente, mas é necessário agora restaurar os dados para uma hora em que o volume foi tamanho mais pequeno. Irá restaurar redimensionar o volume atual e será necessário expandir o tamanho do volume uma vez que o restauro está concluído?

**RESPOSTAS.** Sim, o restauro irá redimensionar o volume e será necessário alargar o tamanho do volume depois da restaurar.

**Q.** Pode alterar o tipo de um volume durante o restauro?

**A.** Não, não é possível alterar o tipo de volume durante o restauro.

- Volumes que tenham sido eliminados são restaurados como o tipo de armazenados no instantâneo.

- Volumes existentes são restaurados com base no respetivo tipo atual, independentemente do tipo de armazenado no limite da (consulte as duas perguntas anterior).
 
**Q.** É necessário restaurar a minha volume localmente afixada, mas escolheu um ponto de incorreto no instantâneo de tempo. Pode cancelar a operação de restauro atual?

**RESPOSTAS.** Sim, pode cancelar uma operação de restauro em curso. O estado do volume vai ser revertido para o estado no início do restauro. No entanto, qualquer escritas que foram feitas o volume enquanto o restauro estava em curso serão perdidas.

**Q.** Posso a uma operação de restauro das minhas volumes localmente afixados e agora vejo um instantâneo no meu catálogo de registo de tarefas pendentes posso não recollect criar. Isto finalidade desse?

**RESPOSTAS.** Este é o instantâneo temporário que é criado antes da operação de restauro e é utilizado para anulação no caso da restaurar é cancelada ou falha. Não elimine este instantâneo; -serão automaticamente eliminado quando o restauro estiver concluído. Este comportamento pode ocorrer se a tarefa de restaurar apenas localmente tem afixados volumes ou uma mistura de volumes localmente afixados e em camadas. Se a tarefa de restauro inclui apenas os volumes em camadas, este comportamento não irá ocorrer.

**Q.** Pode clonar um localmente afixado volume?

**RESPOSTAS.** Sim, pode. No entanto, o volume localmente afixado será possível clonar como um volume em camadas por predefinição. Obter mais informações sobre como [clonar um volume localmente afixado](storsimple-clone-volume-u2.md)

## <a name="questions-about-failing-over-a-locally-pinned-volume"></a>Perguntas sobre uma falha ao longo de um volume localmente afixada

**Q.** Precisa de falhar ao longo do meu dispositivo para outro dispositivo físico. Vai meu volumes localmente afixados falhar superior à medida que localmente afixados ou camadas?

**RESPOSTAS.** Dependendo da versão de software do dispositivo de destino, volumes localmente afixados irão ser Ocorreu uma falha ao longo do como:

- Localmente afixados se o dispositivo de destino está a ser executado StorSimple 8000 série atualizar 2
- Camadas se o dispositivo de destino está a ser executado StorSimple 8000 série atualizar 1. x
- Camadas se o dispositivo de destino for aparelho na nuvem (atualização de versão do software 2 ou atualizar 1. x)

Obter mais informações sobre [activação e DR do localmente afixados volumes através de versões](storsimple-device-failover-disaster-recovery.md#device-failover-across-software-versions)

**Q.** Volumes localmente afixados são restaurados instantaneamente durante a recuperação de falhas (DR)?

**RESPOSTAS.** Sim, volumes localmente afixados são restaurados instantaneamente durante activação pós-falha. Assim que as informações de metadados para o volume são movidas a partir da nuvem, como parte da operação de activação pós-falha, o volume estiver online no dispositivo de destino e pode ser acedido por anfitrião do. Entretanto, os dados do volume irão continuar a transferir em segundo plano e que se possa deparar desempenho reduzido nestes volumes para a duração da falha geral.

**Q.** Posso ver a tarefa de activação pós-falha concluída, como posso controlar o progresso de volume localmente afixada que estiver a ser restaurada no dispositivo destino?

**RESPOSTAS.** Durante uma operação de activação pós-falha, a tarefa de activação pós-falha está marcada como concluir uma vez por todas os volumes no conjunto de activação pós-falha foram instantaneamente restaurados e colocados online no dispositivo de destino. Isto inclui qualquer volumes localmente afixados que poderão ter sido falhados sobre; No entanto, garantias locais dos dados só estará disponíveis quando tenha sido transferida todos os dados para o volume. Pode controlar este progresso para cada volume localmente afixada que foi falhou início ao monitorizar as tarefas de restaurar correspondentes que são criadas como parte da falha geral. Estas tarefas individuais restaurar só serão criadas para volumes localmente afixados.

**Q.** Pode alterar o tipo de um volume durante activação pós-falha?

**RESPOSTAS.** Não, não é possível alterar o tipo de volume durante uma activação pós-falha. Se está a falhar sobre para outro dispositivo físico que está a ser executado StorSimple 8000 série são atualizadas 2, os volumes vai ser Ocorreu uma falha ao longo com base no tipo de volume armazenado no limite da. Quando a falhar sobre qualquer outra versão do dispositivo, consulte a pergunta acima no tipo de volume após uma activação pós-falha.

**Q.** Pode falhar ao longo de um contentor de volume com volumes localmente afixados ao aparelho nuvem?

**RESPOSTAS.** Sim, pode. Os volumes localmente afixados vai ser Ocorreu uma falha ao longo do como volumes em camadas. Obter mais informações sobre [activação e DR do localmente afixados volumes através de versões](storsimple-device-failover-disaster-recovery.md#considerations-for-device-failover)


