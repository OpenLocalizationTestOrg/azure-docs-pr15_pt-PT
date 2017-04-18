<properties
   pageTitle="Gerir power cluster no armazém de dados do SQL Azure (Azure portal) | Microsoft Azure"
   description="Azure tarefas portais para gerir calcular power. Recursos de cluster de escala, ajustando DWUs. Ou, interromper e retomar cluster recursos poupar nos custos."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="barbkess"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="08/22/2016"
   ms.author="barbkess;sonyama"/>

# <a name="manage-compute-power-in-azure-sql-data-warehouse-azure-portal"></a>Gerir power cluster no armazém de dados do SQL Azure (Azure portal)

> [AZURE.SELECTOR]
- [Descrição geral](sql-data-warehouse-manage-compute-overview.md)
- [Portal](sql-data-warehouse-manage-compute-portal.md)
- [PowerShell](sql-data-warehouse-manage-compute-powershell.md)
- [RESTO](sql-data-warehouse-manage-compute-rest-api.md)
- [TSQL](sql-data-warehouse-manage-compute-tsql.md)


Desempenho de escala por escalar para fora de calcular recursos e da memória para satisfazer os pedidos de alteração da sua carga de trabalho. Guarde os custos por recursos anterior dimensionamento durante as horas de que não sejam ponta ou colocar em pausa cluster completamente. 

Este conjunto de tarefas utiliza o portal do Azure para:

- Cluster de escala
- Cluster de colocar em pausa
- Cluster de currículo

Para mais informações, consulte o artigo [Gerir calcular descrição geral][].

<a name="scale-performance-bk"></a>
<a name="scale-compute-bk"></a>

## <a name="scale-compute-power"></a>Power cluster de escala

[AZURE.INCLUDE [SQL Data Warehouse scale DWUs description](../../includes/sql-data-warehouse-scale-dwus-description.md)]

Para alterar cluster recursos:

1. Abra o [Azure portal][], abra a base de dados e clique em **escala**.

    ![Clique em escala][1]

1. No pá escala, mova o controlo de deslize para a esquerda ou direita para alterar a definição de DWU.

    ![Mover o controlo de deslize][2]

1. Clique em **Guardar**. É apresentada uma mensagem de confirmação. Clique em **Sim** para confirmar ou **não** para cancelar.

    ![Clique em Guardar][3]

<a name="pause-compute-bk"></a>

## <a name="pause-compute"></a>Cluster de colocar em pausa

[AZURE.INCLUDE [SQL Data Warehouse pause description](../../includes/sql-data-warehouse-pause-description.md)]

Para interromper uma base de dados:

1. Abra o [portal do Azure][] e abra a base de dados. Repare que o estado está **Online**. 

    ![Estado online][6]

1. Para suspender cluster e recursos de memória, clique em **Pausa**e, em seguida, uma mensagem de confirmação é apresentada. Clique em **Sim** para confirmar ou **não** para cancelar.

    ![Confirmar a colocar em pausa][7]

1. Enquanto armazém de dados SQL está a iniciar a base de dados, o estado é **Interromper**.
2. Quando o estado está **em pausa**, a operação de colocar em pausa é feita e já não está a ser cobrado por DWUs.

    ![Estado de colocar em pausa][4]

<a name="resume-compute-bk"></a>

## <a name="resume-compute"></a>Cluster de currículo

[AZURE.INCLUDE [SQL Data Warehouse resume description](../../includes/sql-data-warehouse-resume-description.md)]Para retomar a uma base de dados:

1. Abra o [portal do Azure][] e abra a base de dados. Repare que o estado está **em pausa**. 

    ![Base de dados de colocar em pausa][4]

1. Para retomar a base de dados clique em **Iniciar**e, em seguida, é apresentada uma mensagem de confirmação. Clique em **Sim** para confirmar ou **não** para cancelar.

    ![Confirmar o currículo][5]

1. Enquanto armazém de dados SQL está a iniciar a base de dados, o estado é "Resuming".
2. Quando o estado está **online**, a base de dados está pronto.

    ![Estado online][6]

<a name="next-steps-bk"></a>

## <a name="next-steps"></a>Próximos passos
Para mais informações, consulte o artigo [Descrição geral da gestão][].

<!--Image references-->
[1]: ./media/sql-data-warehouse-manage-compute-portal/click-scale.png
[2]: ./media/sql-data-warehouse-manage-compute-portal/move-slider.png
[3]: ./media/sql-data-warehouse-manage-compute-portal/click-save.png
[4]: ./media/sql-data-warehouse-manage-compute-portal/resume-database.png
[5]: ./media/sql-data-warehouse-manage-compute-portal/resume-confirm.png
[6]: ./media/sql-data-warehouse-manage-compute-portal/pause-database.png
[7]: ./media/sql-data-warehouse-manage-compute-portal/pause-confirm.png

<!--Article references-->
[Descrição geral da gestão]: ./sql-data-warehouse-overview-manage.md
[Gerir a descrição geral de cluster]: ./sql-data-warehouse-manage-compute-overview.md

<!--MSDN references-->


<!--Other Web references-->

[Portal do Azure]: http://portal.azure.com/
