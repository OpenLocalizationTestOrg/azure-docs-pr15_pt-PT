<properties
   pageTitle="Como criar um bilhetes de suporte para armazém de dados SQL | Microsoft Azure"
   description="Como criar um bilhetes de suporte no armazém de dados do SQL Azure."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sonyam"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="09/01/2016"
   ms.author="sonyama;barbkess"/>

# <a name="how-to-create-a-support-ticket-for-sql-data-warehouse"></a>Como criar um bilhetes de suporte para armazém de dados SQL
 
Se está a ter problemas com o seu armazém de dados SQL, consulte Criar um suporte permissão para que a nossa equipa de engenharia pode ajudá-lo.

## <a name="create-a-support-ticket"></a>Criar um bilhetes de suporte

1. Abra o [Azure portal][].

2. No ecrã principal, clique no mosaico **Ajuda + suporte** .

    ![Ajuda + suporte](./media/sql-data-warehouse-get-started-create-support-ticket/help-support.png)

3. Sobre a ajuda + pá de suporte, clique em **Criar pedido de suporte**.

    ![Novo pedido de suporte](./media/sql-data-warehouse-get-started-create-support-ticket/create-support-request.png)
    
    <a name="request-quota-change"></a> 

4. Seleccione o **pedido do tipo**.

    ![Tipo de pedido](./media/sql-data-warehouse-get-started-create-support-ticket/request-type.png)
    
    >[AZURE.NOTE]  Por predefinição, cada SQL server (por exemplo, myserver.database.windows.net) tem um **DTU Quota** de 45,000. Esta quota é simplesmente um limite de segurança. Pode aumentar a sua quota através da criação de um bilhetes de suporte e ao selecionar a *Quota de* como o tipo de pedido. Para calcular o DTU necessidades, multiplique o 7.5 pelo total [que DWU][] conforme necessário. Por exemplo, que gostaria de alojar duas DW6000s num servidor SQL, em seguida, deverá pedir uma quota DTU de 90,000.  Pode ver o consumo de DTU atual a partir do pá de servidor do SQL no portal. Bases de dados em pausa e retomadas contam para a quota DTU. 

5. Selecione a **subscrição** que aloja a base de dados com o problema que está a denunciar.

    ![Subscrição](./media/sql-data-warehouse-get-started-create-support-ticket/subscription.png)

6. Selecione **Armazém de dados SQL** como o recurso.

    ![Recurso](./media/sql-data-warehouse-get-started-create-support-ticket/resource.png)

7. Selecione o seu [plano de suporte do Azure][].

    - Suporte de **faturação, gestão de quota e de subscrição** está disponível em todos os níveis de suporte.
    - Suporte de **quebra fix** é fornecido através de [Programador][], [padrão][], [Professional direta][] ou suporte [Premier][] . Quebra de corrigir problemas são os problemas que surgirem pelos clientes ao usar o Azure onde não existe uma expetativa razoável que Microsoft causou o problema.
    - **Orientação do programador** e **Serviços de aconselhamento** estão disponíveis em níveis de suporte [Profissional direta][] e [Premier][] . 
    
    Se tiver um plano de suporte de Premier, também pode comunicar armazém de dados SQL relacionados com problemas no [portal online Microsoft Premier][].  Consulte o artigo [planos de suporte do Azure][Azure suporte plano] para saber mais sobre os diversos planos de suporte, incluindo o âmbito, tempos de resposta, preços, etc.  Para perguntas mais frequentes sobre Azure suporte, consulte [Azure FAQs de suporte][].  

    ![Plano de suporte](./media/sql-data-warehouse-get-started-create-support-ticket/support-plan.png)

8. Selecione o **tipo de problema** e **categoria**.

    ![Categoria do tipo de problema](./media/sql-data-warehouse-get-started-create-support-ticket/problem-type-category.png)

9. Descreva o problema e escolha o nível de impacto empresarial.

    ![Descrição do problema](./media/sql-data-warehouse-get-started-create-support-ticket/problem-description.png)

10. As **informações de contacto** para este bilhetes de suporte serão previamente preenchido. Actualize esta opção se for necessário.

    ![Informações de contacto](./media/sql-data-warehouse-get-started-create-support-ticket/contact-info.png)

11. Clique em **Criar** para submeter o pedido de suporte.


## <a name="monitor-a-support-ticket"></a>Monitorizar um bilhetes de suporte

Depois de ter submetido do pedido de suporte, a equipa de suporte Azure irá contactá-lo. Para verificar o estado do pedido e detalhes, clique em **Gerir os pedidos de suporte** no dashboard.

![Verificar o Estado](./media/sql-data-warehouse-get-started-create-support-ticket/check-status.png)

## <a name="other-resources"></a>Outros recursos

Para além disso, pode ligar com a Comunidade do armazém de dados SQL na [Pilha de conteúdo adicional][] ou no [Fórum MSDN do Azure SQL dados armazém][].

<!--Image references--> 

<!--Article references--> 
[DWU]: ./sql-data-warehouse-overview-what-is.md#data-warehouse-units

<!--MSDN references--> 

<!--Other web references--> 
[Portal do Azure]: https://portal.azure.com/
[Plano de suporte do Azure]: https://azure.microsoft.com/support/plans/?WT.mc_id=Support_Plan_510979/  
[Para programadores]: https://azure.microsoft.com/support/plans/developer/  
[Padrão]: https://azure.microsoft.com/support/plans/standard/  
[Direct profissional]: https://azure.microsoft.com/support/plans/prodirect/  
[Premier]: https://azure.microsoft.com/support/plans/premier/  
[Perguntas mais frequentes do suporte Azure]: https://azure.microsoft.com/support/faq/
[Portal online Microsoft Premier]: https://premier.microsoft.com/
[Texto em excesso de pilha]: https://stackoverflow.com/questions/tagged/azure-sqldw/
[Fórum do MSDN de armazém de dados do SQL Azure]: https://social.msdn.microsoft.com/Forums/home?forum=AzureSQLDataWarehouse/

