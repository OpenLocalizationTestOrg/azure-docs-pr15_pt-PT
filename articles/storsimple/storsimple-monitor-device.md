<properties 
   pageTitle="Monitorizar o seu dispositivo StorSimple | Microsoft Azure"
   description="Descreve como utilizar o serviço do Gestor de StorSimple para monitorizar o desempenho e/s, utilização das capacidades, débito da rede e desempenho de dispositivo."
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
   ms.date="08/16/2016"
   ms.author="alkohli" />

# <a name="use-the-storsimple-manager-service-to-monitor-your-storsimple-device"></a>Utilizar o serviço do Gestor de StorSimple para monitorizar o seu dispositivo StorSimple 

## <a name="overview"></a>Descrição geral

Pode utilizar o serviço do Gestor de StorSimple para monitorizar a dispositivos específicos dentro da sua solução StorSimple. Pode criar gráficos personalizados com base no desempenho e/s, utilização das capacidades, débito da rede e métricas de desempenho do dispositivo. 

Para ver as informações de monitorização para um dispositivo específico, no portal do Azure clássico, selecione o serviço do Gestor de StorSimple. Clique no separador **Monitor** e, em seguida, selecione a partir da lista de dispositivos. A página **Monitor** contém as seguintes informações.

## <a name="io-performance"></a>Desempenho e/s 

Métricas de faixas de **Desempenho e/s** relacionados com o número de leitura e operações de escrita entre ambos as interfaces do iniciador de iSCSI em servidor de anfitrião do dispositivo ou o dispositivo e e na nuvem. Este desempenho pode ser medido para um volume específico, um contentor de volume específico ou todos os contentores de volume.

O gráfico abaixo mostra a e/s para o iniciador para o seu dispositivo para todos os volumes para um dispositivo de produção. Métricas representadas são ler e escrever bytes por segundo, ler e escrever operações es por segundo e leitura e escrita latências.

![Desempenho IO a partir do iniciador para o dispositivo](./media/storsimple-monitor-device/StorSimple_IO_Performance_For_InitiatorTODevice_For_AllVolumesM.png)

Para o mesmo dispositivo, as operações e/s são representadas para os dados a partir do dispositivo na nuvem para todos os contentores de volume. Neste dispositivo, os dados são apenas na camada linear e nada tem espalhado na nuvem. Não existem operações leitura / escrita ocorrer a partir do dispositivo para a nuvem. Portanto, picos no gráfico são com um intervalo de 5 minutos, que corresponde à frequência com que o heartbeat der entre o dispositivo e o serviço. 

![Desempenho IO a partir do dispositivo para o cloud](./media/storsimple-monitor-device/StorSimple_IO_Performance_For_DeviceTOCloud_For_AllVolumeContainersM.png)


Para o mesmo dispositivo, um instantâneo de nuvem foi redirecionado para os dados de volume começando na 2:00 pm. Isto resultou em dados a partir do dispositivo fluir na nuvem. Lê gravações foram servidas na nuvem, este duração. O gráfico de IO mostra um pico nas várias métricas correspondente ao tempo quando o instantâneo foi tirado. 

![Desempenho es para o dispositivo para o cloud após instantâneo na nuvem](./media/storsimple-monitor-device/StorSimple_IO_Performance_For_DeviceTOCloud_For_AllVolumeContainers2M.png)


## <a name="capacity-utilization"></a>Utilização da capacidade 

**Utilização das capacidades** controla métricas relacionados com a quantidade de espaço de armazenamento de dados que é utilizado pelo volumes, contentores de volume ou dispositivo. Pode criar relatórios com base em utilização capacidade do seu armazenamento principal, o armazenamento na nuvem ou o armazenamento de dispositivo. Utilização da capacidade pode ser medida com um volume específico, um contentor de volume específico ou todos os contentores de volume.


Nuvem primária, e a capacidade de armazenamento dispositivo pode ser descrita da seguinte forma:

###<a name="primary-storage-capacity-utilization"></a>Utilização das capacidades armazenamento principal
 
Estes gráficos mostram a quantidade de dados escritas StorSimple volumes antes dos dados são deduplicated e comprimidos. Pode ver a utilização da primária armazenamento por todos os volumes ou para um único volume.

Quando visualiza os gráficos de utilização do armazenamento primário volume capacidade para todos os volumes versus cada um dos volumes individuais e somar dados principais em ambos os casos, poderão existir um erro de correspondência entre os dois números. Os dados principais total em todos os volumes não podem adicionar para o total da soma dos dados primários dos volumes individuais. Isto pode ser devido a um dos seguintes procedimentos:

- **Incluído para todos os volumes de dados de instantâneo**: Este comportamento é visualizado apenas se estiver a executar versão anterior ao atualizar 3. Os dados primários apresentados para todos os volumes são a soma dos dados principais para cada volume e os dados de instantâneo. Os dados primários apresentados para um determinado volume corresponde ao apenas a quantidade de dados atribuídas em volume (e não inclui os dados de instantâneo volume correspondentes).

    Também pode ser explicado pela seguinte equação:

    *Dados principal (todos os volumes) = soma (dados principal (volume i) + tamanho dos dados instantâneo (volume i))*
    
    *onde, dados principal (volume i) = tamanho dos dados primários atribuídos volume i*
 
    Se os instantâneos são eliminados através do serviço, a eliminação é feita modo assíncrono em segundo plano. Poderá demorar algum tempo para o tamanho de dados do volume ser atualizados na sequência a eliminação de instantâneo. 

    Se executar a atualização 3 ou posterior, os dados de instantâneo não estão incluídos nos dados de volume. E a utilização da primária é calculada da seguinte forma:

    * Dados principal (todos os volumes) = soma (dados primária (volume i)
    
    *onde, dados principal (volume i) = tamanho dos dados primários atribuídos volume i*
 
- **Volumes com monitorização desativado incluídos em todos os volumes**: Se tiver volumes no seu dispositivo para o qual monitorização está desativada, os dados de monitorização para estes volumes individuais só estarão disponíveis nos gráficos. No entanto, os dados para todos os volumes no gráfico incluem os volumes para a qual monitorização está desativado. 
 
- **Eliminado volumes com live associados as cópias de segurança incluídas para todos os volumes**: se volumes que contêm dados instantâneo são eliminados, mas os instantâneos associados ainda existem, em seguida, poderá ver um erro de correspondência.

- **Volumes de eliminados incluídos para todos os volumes**: em alguns casos, volumes antigos poderão existir apesar de estes tiverem sido eliminadas. Não é visualizado o efeito de eliminação e o dispositivo pode mostrar inferior capacidade disponível. Tem de Support da Microsoft para remover estes volumes de contacto.

Os gráficos seguintes mostram a utilização da capacidade de armazenamento principal de um dispositivo StorSimple antes e depois de um instantâneo de nuvem foi tirado. Como este é apenas os dados de volume, um instantâneo de nuvem não deverá alterar o armazenamento principal. Como pode ver, o gráfico mostra sem diferença na utilização da capacidade principal como resultado de tirar um instantâneo da nuvem. Limite da nuvem iniciado em cerca 2:00 pm nesse dispositivo.

![Utilização das capacidades primária antes de instantâneo na nuvem](./media/storsimple-monitor-device/StorSimple_PrimaryCapacityUtil_For_AllVolumes2M.png)

![Utilização das capacidades primária após instantâneo na nuvem](./media/storsimple-monitor-device/StorSimple_PrimaryCapacityUtil_For_AllVolumes1M.png)

Se estiver a executar o atualizar 2 ou superior, pode dividir a utilização da capacidade de armazenamento primário por um volume individual, todos os volumes, todos os volumes em camadas e todos os volumes localmente afixados conforme apresentado abaixo. Dividir para baixo por todos os volumes localmente afixados permite-lhe determinar rapidamente a quantidade da camada local é utilizada para cima.

![Utilização das capacidades principal para todos os volumes localmente afixados](./media/storsimple-monitor-device/localvolumes.png)


###<a name="cloud-storage-capacity-utilization"></a>Utilização de capacidade de armazenamento na nuvem

Estes gráficos mostram a quantidade de armazenamento em nuvem utilizado. Estes dados são deduplicated e comprimidos. Este valor inclui instantâneos na nuvem que podem conter dados que não são reflectidos na qualquer volume principal e são mantidos para fins de retenção legado ou necessários. Pode comparar a página principal e na nuvem ilustrações de consumo de armazenamento para obter uma ideia da taxa de redução de dados, embora o número não serão exato. Os gráficos seguintes mostram a utilização de capacidade de armazenamento na nuvem de um dispositivo StorSimple antes e depois de um instantâneo de nuvem foi tirado. Limite da nuvem iniciado em cerca 2:00 pm nesse dispositivo e pode ver a utilização da capacidade de nuvem captura configuração ao mesmo tempo, aumentando a partir do 5.73 MB para 4.04 GB.

![Utilização das capacidades nuvem antes de instantâneo na nuvem](./media/storsimple-monitor-device/StorSimple_CloudCapacityUtil_For_AllVolumeContainers2M.png)

![Utilização das capacidades nuvem após instantâneo na nuvem](./media/storsimple-monitor-device/StorSimple_CloudCapacityUtil_For_AllVolumeContainers1M.png)


###<a name="device-storage-capacity-utilization"></a>Utilização de capacidade de armazenamento do dispositivo

Estes gráficos mostram a utilização da total para o dispositivo, que será a utilização do armazenamento primária mais do que uma vez que inclui a camada linear SSD. Este camada contiver um valor de dados que também existem no dispositivo do outras camadas. A capacidade na camada linear SSD desligado e ligada para que quando novos dados é recebida, os dados antigos são movidos para a camada de disco (momento em que é deduplicated e comprimido) e, posteriormente, para a nuvem.

Ao longo do tempo, utilização das capacidades de principal e a utilização da capacidade de dispositivo muito provavelmente aumentará em conjunto até ao início dos dados da camadas para a nuvem. Nesse momento, a utilização da capacidade de dispositivo provavelmente começará a patamar, mas a utilização da capacidade primária aumentará como escritos mais dados.

Os gráficos seguintes mostram a utilização da capacidade de armazenamento principal de um dispositivo StorSimple antes e depois de um instantâneo de nuvem foi tirado. Limite da nuvem começou na 2:00 pm e a utilização da capacidade de dispositivo começaram diminuindo nesse momento. A utilização de capacidade de armazenamento do dispositivo passou para baixo de 11.58 GB para 7.48 GB. Isto indica que provavelmente os dados não comprimidos na camada SSD linear foram deduplicated, comprimidos e movidos para a camada de disco. Tenha em atenção que, se o dispositivo já tiver uma grande quantidade de dados em camadas SSD tanto disco, não poderá ver esta diminuição. Neste exemplo, o dispositivo tem uma pequena quantidade de dados.

![Utilização das capacidades dispositivo antes de instantâneo na nuvem](./media/storsimple-monitor-device/StorSimple_DeviceCapacityUtil2M.png)

![Utilização das capacidades dispositivo depois instantâneo na nuvem](./media/storsimple-monitor-device/StorSimple_DeviceCapacityUtil1M.png)


## <a name="network-throughput"></a>Débito da rede

**Débito da rede** controla métricas relacionados com a quantidade de dados transferidos a partir as interfaces de rede do iniciador de iSCSI no servidor anfitrião e o dispositivo e entre o dispositivo e na nuvem. Pode monitorizar esta métrica para cada uma das interfaces de rede iSCSI no seu dispositivo.

Os gráficos seguintes mostram o débito da rede para dados 0 e 4 de dados, ambas as interfaces de rede GbE 1 no seu dispositivo. Nesta instância dados 0 foram nuvem preparados Considerando que 4 de dados foi ativado iSCSI. Pode ver a entrada e o tráfego de saída para o seu dispositivo StorSimple. A linha simples no gráfico a partir do 3:24 pm é devidos para o facto de que recolhemos dados em 5 minutos e deve ser ignoradas. 

![Débito da rede para Data4](./media/storsimple-monitor-device/StorSimple_NetworkThroughput_Data0M.png)

![Débito da rede para Data4](./media/storsimple-monitor-device/StorSimple_NetworkThroughput_Data4M.png)


## <a name="device-performance"></a>Desempenho do dispositivo 

**Desempenho do dispositivo** controla métricas relacionadas com o desempenho do seu dispositivo. O gráfico seguinte mostra as estatísticas de utilização da CPU para um dispositivo de produção.

![Utilização da CPU do dispositivo](./media/storsimple-monitor-device/StorSimple_DeviceMonitor_DevicePerformance1M.png)

## <a name="next-steps"></a>Próximos passos

- Saiba como [utilizar o dashboard de dispositivo de serviço do Gestor de StorSimple](storsimple-device-dashboard.md).

- Saiba como [utilizar o serviço do Gestor de StorSimple para administrar o seu dispositivo StorSimple](storsimple-manager-service-administration.md).
