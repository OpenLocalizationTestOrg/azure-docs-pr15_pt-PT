<properties
   pageTitle="Ver operações de implementação com o portal | Microsoft Azure"
   description="Descreve como utilizar o portal do Azure para detetar erros de implementação do Gestor de recursos."
   services="azure-resource-manager,virtual-machines"
   documentationCenter=""
   tags="top-support-issue"
   authors="tfitzmac"
   manager="timlt"
   editor="tysonn"/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-multiple"
   ms.workload="infrastructure"
   ms.date="06/15/2016"
   ms.author="tomfitz"/>

# <a name="view-deployment-operations-with-azure-portal"></a>Operações de implementação de vista com o Portal do Azure

> [AZURE.SELECTOR]
- [Portal](resource-manager-troubleshoot-deployments-portal.md)
- [PowerShell](resource-manager-troubleshoot-deployments-powershell.md)
- [Clip Azure](resource-manager-troubleshoot-deployments-cli.md)
- [REST API](resource-manager-troubleshoot-deployments-rest.md)

Pode ver as operações para uma implementação através do portal do Azure. Poderá ser mais interessado em visualizar as operações quando tiver recebido um erro durante a implementação para que este artigo foca-se sobre a visualização de operações tem ocorrido uma falha. O portal disponibiliza uma interface que permite-lhe encontrar os erros e determinar correções potenciais facilmente.

[AZURE.INCLUDE [resource-manager-troubleshoot-introduction](../includes/resource-manager-troubleshoot-introduction.md)]

## <a name="use-deployment-operations-to-troubleshoot"></a>Utilizar as operações de implementação para resolução de problemas

Para ver as operações de implementação, utilize os seguintes passos:

1. Para o grupo de recursos envolvido na implementação, repare que o estado da última implementação. Pode selecionar este estado para obter mais detalhes.

    ![Estado de implementação](./media/resource-manager-troubleshoot-deployments-portal/deployment-status.png)

2. Irá ver o histórico de implementação recentes. Selecione a implementação que falhou.

    ![Estado de implementação](./media/resource-manager-troubleshoot-deployments-portal/select-deployment.png)

3. Selecione **Ocorreu uma falha. Clique aqui para obter mais detalhes** para ver uma descrição da razão pela qual a implementação falhou. Na imagem abaixo, o registo DNS não é exclusivo.  

    ![Ver falha de implementação](./media/resource-manager-troubleshoot-deployments-portal/view-error.png)

    Esta mensagem de erro deverão ser suficientes para começar a resolução de problemas. No entanto, se precisar de mais detalhes sobre as tarefas que foram concluídas, pode ver as operações conforme mostrado nos passos seguintes.

4. Pode ver todas as operações de implementação no pá a **implementação** . Selecione qualquer operação para ver mais detalhes.

    ![operações de vista](./media/resource-manager-troubleshoot-deployments-portal/view-operations.png)

    Neste caso, verá que a conta de armazenamento, rede virtual e conjunto de disponibilidade foram criadas com êxito. Ocorreu uma falha com o endereço IP público e outros recursos não foram tentados.

5. Pode ver eventos para a implementação ao selecionar **eventos**.

    ![Ver eventos](./media/resource-manager-troubleshoot-deployments-portal/view-events.png)

6. Ver todos os eventos para a implementação e selecione uma para obter mais detalhes.

    ![ver os eventos](./media/resource-manager-troubleshoot-deployments-portal/see-all-events.png)

## <a name="use-audit-logs-to-troubleshoot"></a>Registos de auditoria de utilização para resolução de problemas

[AZURE.INCLUDE [resource-manager-audit-limitations](../includes/resource-manager-audit-limitations.md)]

Para ver erros para uma implementação, utilize os seguintes passos:

1. Ver os registos de auditoria para um grupo de recursos ao selecionar **Os registos de auditoria**.

    ![Selecione registos de auditoria](./media/resource-manager-troubleshoot-deployments-portal/select-audit-logs.png)

2. No pá **Registos de auditoria** , irá ver um resumo das operações recentes para todos os grupos de recursos na sua subscrição. Inclui uma representação gráfica da hora e o estado das operações e uma lista das operações.

    ![Mostrar acções](./media/resource-manager-troubleshoot-deployments-portal/audit-summary.png)

3. Pode filtrar a vista dos registos de auditoria focar-se em condições específicas. Seleccione o **filtro** no topo da pá a **registos de auditoria** .

    ![registos de filtro](./media/resource-manager-troubleshoot-deployments-portal/filter-logs.png)

4. Pá **filtro** , selecione as condições para restringir a vista dos registos de auditoria a apenas essas operações que pretende ver. Por exemplo, pode filtrar operações para apresentar apenas as erros do grupo de recursos.

    ![definir as opções de filtro](./media/resource-manager-troubleshoot-deployments-portal/set-filter.png)

5. Pode continuar a filtrar operações ao definir um intervalo de tempo. A imagem seguinte filtra a vista para um determinado timespan de 20 minutos.

    ![definir a hora](./media/resource-manager-troubleshoot-deployments-portal/select-time.png)

6. Pode selecionar qualquer uma das operações na lista. Escolha a operação que contém o erro que pretende pesquisar.

    ![Selecione operação](./media/resource-manager-troubleshoot-deployments-portal/select-operation.png)
  
7. Irá ver todos os eventos para essa operação. Repare os **IDS de correlação** no resumo. Este ID é utilizado para controlar eventos relacionados. Pode ser útil quando trabalhar com o suporte técnico para resolver um problema. Pode selecionar qualquer um dos eventos para ver os detalhes sobre o evento.

    ![Selecione o evento](./media/resource-manager-troubleshoot-deployments-portal/select-event.png)

8. Irá ver detalhes sobre o evento. Em particular, paga a atenção para as **Propriedades** para obter informações sobre o erro.

    ![Mostrar detalhes de registo de auditoria](./media/resource-manager-troubleshoot-deployments-portal/audit-details.png)

O filtro aplicado para o registo de auditoria é retido da próxima vez que visualiza-lo, por isso, poderá ter de alterar esses valores para alargar a sua vista das operações.

## <a name="next-steps"></a>Próximos passos

- Para obter ajuda com resolver erros de implementação específica, consulte o artigo [resolver erros comuns ao implementar recursos para Azure com o Gestor de recursos do Azure](resource-manager-common-deployment-errors.md).
- Para saber como utilizar os registos de auditoria para monitorizar a outros tipos de ações, consulte o artigo [Auditar operações com o Gestor de recursos](resource-group-audit.md).
- Para validar a sua implementação antes de executá-lo, consulte o artigo [Implementar um grupo de recursos com o modelo de Gestor de recursos do Azure](resource-group-template-deploy.md).
