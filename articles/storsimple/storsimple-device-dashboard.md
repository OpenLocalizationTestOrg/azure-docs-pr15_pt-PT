<properties
   pageTitle="Utilize o dashboard do dispositivo de Gestor de StorSimple | Microsoft Azure"
   description="Descreve o dashboard de dispositivo de serviço do Gestor de StorSimple e como utilizá-lo para visualizar métricas de armazenamento e iniciadores ligadas e encontrar o número de série e IQN."
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

# <a name="use-the-storsimple-manager-device-dashboard"></a>Utilize o dashboard de dispositivo StorSimple Gestor

## <a name="overview"></a>Descrição geral

Dashboard de dispositivo de Gestor de StorSimple dá-lhe uma descrição geral de informações para um dispositivo StorSimple específico, contrariamente ao dashboard de serviço, que lhe fornece informações sobre todos os dispositivos incluídos na sua solução do Microsoft Azure StorSimple.

![Página do dashboard de dispositivo](./media/storsimple-device-dashboard/StorSimple_DeviceDashbaord1M.png)

O dashboard contém as seguintes informações:

- **Área do gráfico** – pode ver as métricas de armazenamento relevantes na área de gráfico na parte superior do dashboard. Neste gráfico, pode ver métricas para o armazenamento total principal (a quantidade de dados escritos por anfitriões para o seu dispositivo) e o armazenamento em nuvem total média consumida por dispositivo durante um período de tempo.

     Neste contexto, refere-se até à quantidade total de dados escritos por anfitrião do *armazenamento principal* e podem ser divididos por tipo de volume: *armazenamento em camadas primário* inclui ambos os dados armazenados localmente e camadas de dados para a nuvem; *primária localmente afixados armazenamento* inclui apenas os dados armazenados localmente. *Armazenamento em nuvem*, outro lado, é uma medida da quantia total dos dados armazenados na nuvem. Isto inclui dados em camadas e cópias de segurança. Note que dados armazenados na nuvem são deduplicated e comprimidos, Considerando que armazenamento primário indica a quantidade de armazenamento utilizado antes dos dados são deduplicated e comprimidos. (Pode comparar estes dois números para obter uma ideia da taxa de compressão.) Para ambas as principal e armazenamento em nuvem, os valores mostrados irá basear-se a frequência de controlo, configurar. Por exemplo, se optar por uma frequência de uma semana, em seguida, o gráfico vai mostrar dados para cada dia da semana anterior.

     Pode configurar o gráfico da seguinte forma:

     - Para ver a quantidade de armazenamento em nuvem consumida ao longo do tempo, selecione a opção **Utilizado de armazenamento na NUVEM** . Para ver o armazenamento total que foi escrito ao anfitrião do, selecione as opções de **Primária CAMADAS armazenamento utilizado** e **Primária LOCALMENTE AFIXADOS armazenamento utilizado** . Na ilustração, ambas as opções estão selecionadas; Por conseguinte, o gráfico apresenta as quantidades de armazenamento na nuvem e armazenamento principal. Tenha em atenção que qualquer armazenamento principal utilizado antes de instalar Update 2 é representado por da linha **Principal CAMADAS armazenamento utilizado** .
     - Utilize o menu pendente no canto superior direito do gráfico para especificar um período de tempo de 1 semana, 1 mês, 3-mês ou ano de 1. Repare que o gráfico de nível superior é atualizado apenas uma vez por dia e, consequentemente, vai refletir as totais o dia anterior.

     Para mais informações, consulte o artigo [utilizar o serviço do Gestor de StorSimple para monitorizar o seu dispositivo StorSimple](storsimple-monitor-device.md).

- **Descrição geral de utilização** -na área de **Descrição geral de utilização** , pode ver a quantidade de armazenamento principal utilizado, a quantidade de armazenamento aprovisionada e a capacidade de armazenamento máximo para o seu dispositivo. Ao comparar estes números de utilização para a quantidade máxima de armazenamento disponível, pode ver rapidamente se precisar de obter armazenamento adicional. Note que esta descrição geral é atualizado a cada 15 minutos e, devido a diferença de atualização frequência, poderá mostrar números diferentes daqueles mostrado na área do gráfico acima, que é atualizada diariamente. Para mais informações, consulte o artigo [utilizar o serviço do Gestor de StorSimple para monitorizar o seu dispositivo StorSimple](storsimple-monitor-device.md).


- **Alertas** – a área de **alertas** contém uma descrição geral de alertas para o seu dispositivo. Alertas estão agrupados por gravidade e for fornecida uma contagem do número de alertas em cada nível de gravidade. Clicar em gravidade alerta abre uma vista de âmbito no separador alertas para mostrar-lhe apenas os alertas desse nível de gravidade para este dispositivo de.

- **Tarefas de** – a área de **projectos** mostra-lhe o resultado de atividade de tarefas recentes. Isto pode assegurar que o sistema está a funcionar como esperado, ou -pode permitir que o precisa fazer uma ação lentes. Para ver mais informações sobre tarefas concluídas recentemente, clique em **tarefas com êxito as últimas 24 horas**.

- A área **vista rápida** no lado direito do dashboard fornece informações úteis como modelo de dispositivo, número de série, estado, descrição e o número de volumes.

Também pode configurar activação pós-falha e ver iniciadores ligadas a partir do dashboard de dispositivo.

Sejam as tarefas comuns que podem ser efetuadas nesta página:

- Ver iniciadores ligadas

- Encontrar o número de série do dispositivo

- Encontrar o destino de dispositivo IQN

## <a name="view-connected-initiators"></a>Ver iniciadores ligadas

Pode ver os iniciadores iSCSI que estão ligados ao seu dispositivo ao clicar na ligação **Ver ligados iniciadores** fornecida na área **vista rápida** do seu dashboard de dispositivo. Esta página fornece uma lista em forma de tabela dos iniciadores que ligou com êxito ao seu dispositivo. Para cada iniciador, consulte:

- O iSCSI qualificado nome (IQN) do iniciador ligado.

- O nome do registo de controlo de acesso (ACR) que permite que este iniciador ligada.

- O endereço IP do iniciador ligado.

- As interfaces de rede que o iniciador está ligado no seu dispositivo de armazenamento. Estes podem variar a partir de dados 0 e 5 de dados.

- Todos os volumes que é permitido o iniciador ligado para aceder a acordo com a configuração de ACR atual.

Se Consulte iniciadores inesperados nesta lista ou se não conseguir ver aqueles esperado, reveja a sua configuração ACR. Pode ligar a um máximo de 512 iniciadores para o seu dispositivo.

## <a name="find-the-device-serial-number"></a>Encontrar o número de série do dispositivo

Poderá ter o número de série do dispositivo quando configurar Microsoft i / (o) no dispositivo. Execute os passos seguintes para encontrar o número de série do dispositivo.

#### <a name="to-find-the-device-serial-number"></a>Para encontrar o número de série do dispositivo

1. Navegue para **dispositivos** > **Dashboard**.

2. No painel direito do dashboard, localize a área **vista rápida** .

3. Deslocar para baixo e localize o número de série.

## <a name="find-the-device-target-iqn"></a>Encontrar o destino de dispositivo IQN

Poderá ter de destino de dispositivo IQN quando configura o desafio autenticação CHAP (Handshake Protocol) no seu dispositivo StorSimple. Execute os passos seguintes para encontrar o destino de dispositivo IQN.

### <a name="to-find-the-device-target-iqn"></a>Para encontrar o destino de dispositivo IQN

1. Navegue para **dispositivos** > **Dashboard**.

1. No painel direito do dashboard, localize a área **vista rápida** .

1. Deslocar para baixo e localize o destino IQN.

## <a name="next-steps"></a>Próximos passos

- Saiba mais sobre o [dashboard de serviço do Gestor de StorSimple](storsimple-service-dashboard.md).
- Saiba mais sobre como [utilizar o serviço do Gestor de StorSimple para administrar o seu dispositivo StorSimple](storsimple-manager-service-administration.md).
