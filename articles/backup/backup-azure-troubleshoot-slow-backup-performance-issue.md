<properties
   pageTitle="Resolver problemas de cópia de segurança lenta dos ficheiros e pastas cópia de segurança do Azure | Microsoft Azure"
   description="Fornece orientações de resolução de problemas para o ajudar a diagnosticar a causa de problemas de desempenho de cópia de segurança do Azure"
   services="backup"
   documentationCenter=""
   authors="genlin"
   manager="jimpark"
   editor=""/>

<tags
    ms.service="backup"
    ms.workload="storage-backup-recovery"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/13/2016"
    ms.author="genli"/>

# <a name="troubleshoot-slow-backup-of-files-and-folders-in-azure-backup"></a>Resolver problemas de cópia de segurança lenta dos ficheiros e pastas cópia de segurança do Azure

Este artigo fornece orientações resolução de problemas para o ajudar a diagnosticar a causa do desempenho lento de cópia de segurança para ficheiros e pastas quando estiver a utilizar o Azure cópia de segurança. Quando utiliza o agente de cópia de segurança do Azure cópias de ficheiros, o processo de cópia de segurança poderá demorar mais tempo que o esperado. Este atraso pode ser causado por um ou mais dos seguintes procedimentos:

-   [Existem congestionamentos de desempenho no computador que está a ser cópias de segurança.](#cause1)
-   [Outro processo ou o software antivírus está a interferir com o processo de cópia de segurança do Azure.](#cause2)
-   [O agente de cópia de segurança está em execução uma máquina virtual Azure (VM).](#cause3)  
-   [Está a cópias um grande número (milhões) de ficheiros.](#cause4)

Antes de iniciar a resolução de problemas, recomendamos que transferir e instalar o [agente de cópia de segurança do Azure mais recente](http://aka.ms/azurebackup_agent). Oferecemos frequentes atualizações para o agente de cópia de segurança para corrigir problemas de vários, adicionar funcionalidades e melhorar o desempenho.

Também recomendamos que reveja as [FAQ do serviço de cópia de segurança do Azure](backup-azure-backup-faq.md) para se certificar de que não está a ter qualquer um dos problemas de configuração de comuns.

[AZURE.INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

<a id="cause1"></a>
## <a name="cause-performance-bottlenecks-on-the-computer"></a>Problema: Congestionamentos de desempenho no computador

Congestionamentos no computador que está a ser cópias de segurança podem causar atrasos. Por exemplo, a capacidade do computador para ler ou escrever disco ou em largura de banda disponível para enviar dados através da rede, pode causar congestionamentos.

O Windows fornece uma ferramenta incorporada que chama [Monitor de desempenho](https://technet.microsoft.com/magazine/2008.08.pulse.aspx) (desempenho) para detectar estes congestionamentos.

Aqui estão algumas contadores de desempenho e intervalos que podem ser útil diagnosticar congestionamentos para ideais cópias de segurança.

| Contador  | Estado  |
|---|---|
|Disco lógico (disco físico) – % inactivo   | • 100% idle 50% idle = Saudável</br>• 49% idle a 20% idle = aviso ou o Monitor</br>• 19% idle para 0% idle = crítica ou Out of especificação|
|  Disco lógico (disco físico) – Avg %. Disco Sec leitura ou escrita |  • ms 0,001 para 0.015 ms = Saudável</br>• ms 0.015 para ms 0,025 = aviso ou o Monitor</br>• 0.026 ms ou já = crítico ou Out of especificação|
|  Disco lógico (disco físico) – comprimento de fila de disco atual (para todas as instâncias) | 80 pedidos de mais de 6 minutos |
| Memória – que não sejam de Memória Bytes|• Menos de 60% de agrupamento de consumidas = Saudável<br>• 61 a 80% de agrupamento de consumidas = aviso ou o Monitor</br>• Superior a 80% agrupamento consumido = crítica ou fora do especificação|
| Memória – de Memória Bytes |• Menos de 60% de agrupamento de consumidas = Saudável</br>• 61 a 80% de agrupamento de consumidas = aviso ou o Monitor</br>• Superior a 80% agrupamento consumido = crítica ou fora do especificação|
| Memória – Megabytes disponíveis| • 50% de memória livre disponível ou mais = saudável</br>• 25% de memória livre disponível = Monitor</br>• 10% de memória livre disponível = aviso</br>• Menos de 100 MB ou % de 5 de memória livre disponível = crítica ou fora do especificação|
|Processador –\%processador de tempo (todas as instâncias)|• Menos de 60% consumidas = Saudável</br>• 61 90% consumidas = Monitor ou atenção</br>• 91 a 100% consumidas = crítica|


> [AZURE.NOTE] Se determinar que a infraestrutura de é o culpado, recomendamos que desfragmentar discos regularmente para um melhor desempenho.

<a id="cause2"></a>
## <a name="cause-another-process-or-antivirus-software-interfering-with-azure-backup"></a>Causa: Outro processo ou software de antivírus interferir com cópia de segurança do Azure

Vamos visualizou várias instâncias onde outros processos no sistema Windows tenham negativa afectadas desempenho do processo de agente de cópia de segurança do Azure. Por exemplo, se utilizar o agente de cópia de segurança do Azure e outro programa para agregar dados ou se o software antivírus está em execução e tem um cadeado em ficheiros para ser cópias de segurança, o múltiplo bloqueios no ficheiros poderão causar contenção. Esta situação, poderá falhar a cópia de segurança ou a tarefa poderá demorar mais tempo do que o esperado.

Neste cenário, a melhor recomendação é desativar o outro programa cópia de segurança para ver se o tempo de cópia de segurança para o agente de cópia de segurança do Azure é alterado. Normalmente, certificando-se de que várias tarefas de cópia de segurança não estiver a executar ao mesmo tempo é suficiente para as impedir de afetar entre si.

Para programas de antivírus, recomendamos que excluir os seguintes ficheiros e as localizações:

- C:\Program Files\Microsoft Azure recuperação serviços Agent\bin\cbengine.exe como um processo
- C:\Program Files\Microsoft Azure recuperação serviços Agent\ pastas
- Localização de rascunho (se não estiver a utilizar a localização padrão)

<a id="cause3"></a>
## <a name="cause-backup-agent-running-on-an-azure-virtual-machine"></a>Problema: Agente de cópia de segurança em execução numa máquina virtual Azure

Se estiver a executar o agente de cópia de segurança numa VM, desempenho será mais lento do que quando executá-la num computador físico. Isto é esperado devido a limitações IOPS.  No entanto, pode otimizar o desempenho ao mudar as unidades de dados que estão a ser cópia de segurança ao armazenamento de Premium do Azure. Estamos a trabalhar no corrigir este problema e a fix estarão disponível num lançamento futuro.

<a id="cause4"></a>
## <a name="cause-backing-up-a-large-number-millions-of-files"></a>Problema: Cópias de um grande número (milhões) de ficheiros

Mover um grande volume de dados irá demorar mais tempo a mover um volume mais pequeno de dados. Em alguns casos, hora de cópia de segurança está relacionada com não só o tamanho dos dados, mas o número de ficheiros ou pastas. Este é especialmente true quando estão a ser cópias de segurança dos milhões de pequenos ficheiros (alguns bytes para alguns quilobytes).

Este comportamento ocorre porque enquanto estiver cópias de segurança dos dados e movê-lo para Azure, Azure em simultâneo é catalogação os ficheiros. Em alguns cenários raros, a operação de catálogo poderá demorar mais tempo que o esperado.

Os seguintes indicadores podem ajudá-lo a compreender o congestionamento e trabalhar em conformidade nos próximos passos:

- **IU é a mostrar o progresso de transferência de dados**. Os dados ainda estão a ser transferidos. A largura de banda de rede ou o tamanho dos dados poderá estar a provocar atrasos.

- **IU não está a mostrar o progresso de transferência de dados**. Abra os registos que se encontra no C:\Microsoft Azure recuperação serviços Agent\Temp e, em seguida, selecione para a entrada de FileProvider::EndData nos registos do. Esta entrada se trata de que a transferência de dados tiver terminado e que se passa a operação de catálogo. Não cancele as tarefas de cópia de segurança. Em vez disso, aguarde que um pouco já a operação de catálogo concluir. Se o problema persistir, contacte o [suporte do Azure](https://portal.azure.com/#create/Microsoft.Support).
