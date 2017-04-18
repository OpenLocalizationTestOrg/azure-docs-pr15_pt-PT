<properties 
   pageTitle="Dashboard de serviço do Gestor de StorSimple - matriz Virtual | Microsoft Azure"
   description="Descreve o dashboard de serviço do Gestor de StorSimple e explica como utilizá-la para monitorizar o estado de funcionamento da sua matriz de Virtual StorSimple."
   services="storsimple"
   documentationCenter=""
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="04/07/2016"
   ms.author="alkohli" />

# <a name="use-the-storsimple-manager-service-dashboard-for-the-storsimple-virtual-array"></a>Utilizar o dashboard de serviço do Gestor de StorSimple para a matriz Virtual StorSimple

## <a name="overview"></a>Descrição geral

Página de dashboard do Gestor de StorSimple serviço fornece uma vista de resumo das StorSimple Virtual matrizes (também conhecido como StorSimple no local dispositivos virtuais ou dispositivos virtuais) que estão ligadas para o serviço de Gestor de StorSimple, realce aqueles que necessitam de atenção um administrador de sistema. Neste tutorial apresenta a página do dashboard, explica o conteúdo do dashboard e a função e descreve as tarefas que pode executar a partir desta página.

![Dashboard de serviço](./media/storsimple-ova-service-dashboard/dashboard1.png)

Dashboard de serviço de Gestor de StorSimple apresenta as seguintes informações:

- Na área de **gráfico** na parte superior da página, pode ver as métricas relevantes para os seus dispositivos virtuais. Pode ver o armazenamento principal utilizado em todos os dispositivos virtuais, bem como o armazenamento em nuvem média consumida por dispositivos virtuais através de um período de tempo. Utilize os controlos no canto superior direito do gráfico para especificar a utilização de referências absoluta ou relativa e para ver uma escala de 1 semana, 1 mês, 3-mês ou ano de 1 hora. Utilizar o controlo de atualização ![atualizar controlo](./media/storsimple-ova-service-dashboard/refresh-control.png) para atualizar o gráfico.

- **Descrição geral de utilização** mostra o armazenamento principal que é aprovisionado e média consumido por todos os dispositivos virtuais relativamente ao armazenamento total disponível em todos os dispositivos virtuais. **Provisioned** refere-se para a quantidade de armazenamento que está preparado e atribuído para ser utilizado, **utilizados** refere-se para a utilização da partilhas ou volumes como visualizados pelos iniciadores que estão ligados aos dispositivos virtuais e **capacidade de máx.** mostra a capacidade máxima de todos os dispositivos virtuais.

- A área de **alertas** fornece um instantâneo de todos os alertas ativos em todos os dispositivos virtuais, agrupados por gravidade dos alertas. Clicar no nível de gravidade abre a página de **alertas** , confinada a mostrar esses alertas. Na página **alertas** , pode clicar num alerta individual para ver detalhes adicionais sobre que o alerta, incluindo as ações recomendadas. Também pode limpar o alerta, se tiver sido resolvido o problema.

- A área de **tarefas** fornece um instantâneo de tarefas recentes em todos os dispositivos virtuais que estão ligados no seu serviço. Existem ligações que pode utilizar para ver tarefas que se encontram atualmente em curso e aquelas que foi concluída com êxito ou falha em últimas 24 horas. 

- A área **vista rápida** no lado direito da página fornece informações úteis, como o estado do serviço, número total de virtuais dispositivos ligados ao serviço, a localização do serviço e detalhes da subscrição que está associado com o serviço. Também existe uma ligação para o registo de operações. Clique na ligação para ver uma lista de todas as operações de serviço do Gestor de StorSimple concluída. 

Pode utilizar a página do dashboard service StorSimple Manager para iniciar as seguintes tarefas:

- Obter a chave de registo do serviço.
- Visualize os registos de operação.

## <a name="get-the-service-registration-key"></a>Obter a chave de registo do serviço

A chave de registo do serviço é utilizada para registar um dispositivo virtual StorSimple com o serviço do Gestor de StorSimple, para que o dispositivo é apresentado no portal do Azure clássico para ainda mais ações de gestão. A tecla é criada no primeiro dispositivo virtual e partilhada com os restantes dispositivos virtuais. 

Clicar em **Chave de registo** (na parte inferior da página) abre a caixa de diálogo **Chave de registo do serviço** , onde pode copiar a chave de registo atual do serviço para a área de transferência ou gerar a chave de registo do serviço.

![chave de registo](./media/storsimple-ova-service-dashboard/service-dashboard3.png)

Voltar a gerar a chave não vai afetar os dispositivos virtuais registados anteriormente:-afeta apenas os dispositivos virtuais que estão registados com o serviço depois da tecla é geradas novamente.

Para mais informações sobre como obter a chave de registo do serviço, aceda a [obter a chave de registo do serviço](storsimple-ova-manage-service.md#get-the-service-registration-key).

## <a name="view-the-operations-logs"></a>Ver os registos de operações

Pode ver os registos de operação ao clicar na ligação de registos de operação disponíveis no painel de **vista rápida** do dashboard. Isto leva-o até à página de serviços de gestão, onde pode filtrar e ver os registos específicos no seu serviço de Gestor de StorSimple.

![Registo de operações](./media/storsimple-ova-service-dashboard/ops-log.png)

## <a name="next-steps"></a>Próximos passos

Saiba como [utilizar a web local da IU para administrar a sua matriz de Virtual StorSimple](storsimple-ova-web-ui-admin.md).