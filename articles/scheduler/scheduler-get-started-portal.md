<properties
 pageTitle="Introdução ao Azure Scheduler no Azure portal | Microsoft Azure"
 description="Introdução ao Azure Scheduler no portal do Azure"
 services="scheduler"
 documentationCenter=".NET"
 authors="derek1ee"
 manager="kevinlam1"
 editor=""/>
<tags
 ms.service="scheduler"
 ms.workload="infrastructure-services"
 ms.tgt_pltfrm="na"
 ms.devlang="dotnet"
 ms.topic="hero-article"
 ms.date="08/10/2016"
 ms.author="deli"/>

# <a name="get-started-with-azure-scheduler-in-azure-portal"></a>Introdução ao Azure Scheduler no portal do Azure

É fácil criar tarefas agendadas no Azure Programador de tarefas. Neste tutorial, irá aprender a criar uma tarefa. Também irá aprender capacidades de monitorização e gestão do programador.

## <a name="create-a-job"></a>Criar uma tarefa

1.  Inicie sessão no [Azure portal](https://portal.azure.com/).  

2.  Clique em **+ Novo** > escreva _Programador_ na caixa de pesquisa > selecione **Scheduler** nos resultados > clique em **Criar**.

     ![][marketplace-create]

3.  Vamos criar uma tarefa que simplesmente acertos http://www.microsoft.com/ num pedido GET. No ecrã de **Tarefa de programador** , introduza as seguintes informações:

    1.  **Nome:**`getmicrosoft`  

    2.  **Subscrição:** A sua subscrição do Azure   

    3.  **Da coleção de cargo:** Selecione uma coleção de tarefa existente ou clique em **Criar novo** > introduza um nome.

4.  Em seguida, nas **Definições de ação**, defina os seguintes valores:

    1.  **Tipo de ação:**` HTTP`  

    2.  **Método:**`GET`  

    3.  **URL:**` http://www.microsoft.com`  

      ![][action-settings]

5.  Por fim, vamos definir uma agenda. A tarefa pode ser definido como uma única tarefa, mas vamos escolha uma agenda de periodicidade:

    1. **Periodicidade**:`Recurring`

    2. **Iniciar**: a data de hoje

    3. **Que se repita cada**:`12 Hours`

    4. **Terminar a**: dois dias da data de hoje da data  

      ![][recurrence-schedule]

6.  Clique em **Criar**

## <a name="manage-and-monitor-jobs"></a>Gerir e monitorizar tarefas

Assim que uma tarefa é criada, esta aparece no dashboard de Azure principal. Clique na tarefa e numa nova janela abre com os seguintes separadores:

1.  Propriedades  

2.  Definições de ação  

3.  Agenda  

4.  Histórico

5.  Utilizadores

    ![][job-overview]

### <a name="properties"></a>Propriedades

Estas propriedades só de leitura descrevem os metadados de gestão para a tarefa de programador.

   ![][job-properties]


### <a name="action-settings"></a>Definições de ação

Clicar num projecto no ecrã **tarefas** permite-lhe configurar essa tarefa. Permite-lhe configurar definições avançadas, se não a configurou-los no Assistente de criação rápida.

Para todos os tipos de ação, pode alterar a política de repetição e a ação de erro.

Para HTTP e HTTPS tipos de acção de tarefa, pode alterar o método para qualquer verbo HTTP permitido. Também pode adicionar, eliminar ou alterar os cabeçalhos e informações de autenticação básica.

Para tipos de ação de fila de armazenamento, pode alterar a conta de armazenamento, nome de fila de espera, token de SA e corpo.

Bus ação para tipos de serviço, pode alterar o espaço de nomes, caminho tópico/fila, definições de autenticação, tipo de transporte, propriedades da mensagem e o corpo da mensagem.

   ![][job-action-settings]

### <a name="schedule"></a>Agenda

Permite-lhe reconfigurar a agenda, se pretender alterar a agenda que criou no Assistente de criação rápida.

Esta é uma oportunidade para construir [complexas agendas e periodicidade avançada no seu trabalho](scheduler-advanced-complexity.md)

Pode alterar a data de início e hora, a agenda de periodicidade e final data e hora (se a tarefa é periódica.)

   ![][job-schedule]


### <a name="history"></a>Histórico

No separador **Histórico** apresenta métricas selecionadas para cada execução da tarefa no sistema de para a tarefa selecionada. Estes métricas fornecem valores em tempo real sobre o estado de funcionamento do seu programador de:

1.  Estado  

2.  Detalhes  

3.  Tentativas de repetição

4.  Ocorrência: 1º, 2º, 3º, etc.

5.  Hora de início de execução  

6.  Hora de fim da execução

   ![][job-history]

Pode clicar num executar para ver os **Detalhes do histórico**, incluindo a resposta inteira para cada execução. Esta caixa de diálogo também permite-lhe copiar a resposta à área de transferência.

   ![][job-history-details]

### <a name="users"></a>Utilizadores

Azure baseado em funções acesso controlo RBCA () permite a gestão de acesso extensivamente do Scheduler do Azure. Para saber como utilizar o separador de utilizadores, consulte [Azure Role-Based o controlo de acesso](../active-directory/role-based-access-control-configure.md)


## <a name="see-also"></a>Consulte também

 [O que é o Scheduler?](scheduler-intro.md)

 [Scheduler conceitos, terminologia e hierarquia entidade](scheduler-concepts-terms.md)

 [Planos e faturação no Azure Programador de tarefas](scheduler-plans-billing.md)

 [Como construir complexas agendas e periodicidade avançada com o programador do Azure](scheduler-advanced-complexity.md)

 [Referência do Scheduler REST API](https://msdn.microsoft.com/library/mt629143)

 [Os cmdlets do PowerShell do Programador de referência](scheduler-powershell-reference.md)

 [Scheduler alta disponibilidade e fiabilidade](scheduler-high-availability-reliability.md)

 [Limites de programador, predefinições e códigos de erro](scheduler-limits-defaults-errors.md)

 [Autenticação de saída de programador](scheduler-outbound-authentication.md)


[marketplace-create]: ./media/scheduler-get-started-portal/scheduler-v2-portal-marketplace-create.png
[action-settings]: ./media/scheduler-get-started-portal/scheduler-v2-portal-action-settings.png
[recurrence-schedule]: ./media/scheduler-get-started-portal/scheduler-v2-portal-recurrence-schedule.png
[job-properties]: ./media/scheduler-get-started-portal/scheduler-v2-portal-job-properties.png
[job-overview]: ./media/scheduler-get-started-portal/scheduler-v2-portal-job-overview-1.png
[job-action-settings]: ./media/scheduler-get-started-portal/scheduler-v2-portal-job-action-settings.png
[job-schedule]: ./media/scheduler-get-started-portal/scheduler-v2-portal-job-schedule.png
[job-history]: ./media/scheduler-get-started-portal/scheduler-v2-portal-job-history.png
[job-history-details]: ./media/scheduler-get-started-portal/scheduler-v2-portal-job-history-details.png


[1]: ./media/scheduler-get-started-portal/scheduler-get-started-portal001.png
[2]: ./media/scheduler-get-started-portal/scheduler-get-started-portal002.png
[3]: ./media/scheduler-get-started-portal/scheduler-get-started-portal003.png
[4]: ./media/scheduler-get-started-portal/scheduler-get-started-portal004.png
[5]: ./media/scheduler-get-started-portal/scheduler-get-started-portal005.png
[6]: ./media/scheduler-get-started-portal/scheduler-get-started-portal006.png
[7]: ./media/scheduler-get-started-portal/scheduler-get-started-portal007.png
[8]: ./media/scheduler-get-started-portal/scheduler-get-started-portal008.png
[9]: ./media/scheduler-get-started-portal/scheduler-get-started-portal009.png
[10]: ./media/scheduler-get-started-portal/scheduler-get-started-portal010.png
[11]: ./media/scheduler-get-started-portal/scheduler-get-started-portal011.png
[12]: ./media/scheduler-get-started-portal/scheduler-get-started-portal012.png
[13]: ./media/scheduler-get-started-portal/scheduler-get-started-portal013.png
[14]: ./media/scheduler-get-started-portal/scheduler-get-started-portal014.png
[15]: ./media/scheduler-get-started-portal/scheduler-get-started-portal015.png
