<properties
   pageTitle="Transferir o que é o proprietário de uma subscrição do Azure | Microsoft Azure"
   description="Como transferir uma subscrição do Azure a outro utilizador e algumas perguntas mais frequentes (FAQ) sobre o processo"
   services=""
   documentationCenter=""
   authors="genlin"
   manager="stevenpo"
   editor=""
   tags="billing,top-support-issue"/>

<tags
   ms.service="billing"
   ms.workload="na"
   ms.tgt_pltfrm="na"
   ms.devlang="na"
   ms.topic="article"
   ms.date="10/10/2016"
   ms.author="genli"/>

# <a name="transferring-ownership-of-an-azure-subscription"></a>Transferir o que é o proprietário de uma subscrição do Azure

Faça:

- Precisa de mão sobre faturação que é o proprietário da sua subscrição do Azure a outra pessoa?
- Pretende alterar a conta utilizada para se inscrever Azure? Talvez utilizado sua Account Microsoft mas destinar a utilizar o seu trabalho ou escola em vez disso, a conta?
- Pretende mover a sua subscrição do Azure a partir de um diretório para outro?
- Possui Azure e no Office 365 no inquilinos diferentes e pretende consolidar?

Pode agora fazê-lo facilmente no Microsoft Azure conta Centro - para repartição, MSDN, pacote de ação ou BizSpark subscrições.  Adicionámos a capacidade de transferir a sua subscrição para outro utilizador. Por outras palavras, agora pode alterar o administrador de conta na qualquer repartição, MSDN, pacote de ação ou BizSpark subscrição que é o proprietário, independentemente do qual país funcionar em. Agora suportamos a transferência de compras do Azure Marketplace para estes tipos de subscrição também.

> [AZURE.NOTE] Para alterar a sua subscrição para uma oferta diferente, consulte o artigo [mudar a sua subscrição Azure oferta de outra](billing-how-to-switch-azure-offer.md) para obter mais informações. Se precisar de mais ajuda em qualquer ponto neste artigo, consulte [contactar o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para obter o seu problema resolvido rapidamente.

## <a name="how-to-transfer-ownership-of-an-azure-subscription"></a>Como transferir o que é o proprietário de uma subscrição do Azure

> [AZURE.VIDEO transfer-an-azure-subscription]

1.  Inicie sessão em login.Partner.microsoftonline <https://account.windowsazure.com/Subscriptions>. Tem de ser o administrador de conta para executar uma transferência de propriedade. Para obter mais informações sobre como determinar quem é o administrador da conta da subscrição, consulte [Perguntas mais frequentes](#faq).

2.  Selecione a subscrição para o qual transferir.

3.  Clique na opção de **Transferência de subscrição** .

    ![No separador conta Azure subscrições](./media/billing-subscription-transfer/image1.png)

4.  Siga os pedidos para especificar o destinatário.

    ![Caixa de diálogo de subscrição de transferência](./media/billing-subscription-transfer/image2.PNG)

5.  O destinatário irão receber automaticamente um e-mail com uma ligação de aceitação.

    ![E-mail de transferência de subscrição para o destinatário](./media/billing-subscription-transfer/image3.png)

6.  O destinatário clica na ligação e segue as instruções, incluindo a introduzir as respetivas informações de pagamento.

    ![Primeira página do web subscrição transferência](./media/billing-subscription-transfer/image4.png)

    ![Segunda página de web de transferência de subscrição](./media/billing-subscription-transfer/image5.png)

7. Sucesso! A subscrição será agora transferida.

<a id="faq"></a>
## <a name="frequently-asked-questions-faq"></a>Perguntas mais frequentes (FAQ)

-   **Como posso saber quem é o administrador da conta da subscrição?**

    Pode confirmar que é o administrador de conta da subscrição da seguinte forma:

    1. Inicie sessão no [portal do Azure](https://portal.azure.com).
    2. No menu concentrador, selecione a **subscrição**.
    3. Selecione a subscrição que pretende verificar e, em seguida, selecione **Definições**.
    4. Selecione **Propriedades**. O administrador da conta da subscrição será apresentado na caixa **Conta de administrador** .  

-   **Uma transferência de subscrição resulta em qualquer tempo de inatividade do serviço?**

    Não existe nenhuma impacto ao serviço. Isto eficazmente cancela a subscrição da conta corrente de administrador e cria uma nova conta do destinatário, mas associa os serviços de Azure subjacentes a nova subscrição. O ID da subscrição permanece a mesma.

-   **Como utilizo este mecanismo para alterar o diretório de subscrição?**-   
    Uma subscrição do Azure é criada no diretório que pertence o administrador de conta. Sim, para poder alterar diretório, basta transferir a subscrição para uma conta de utilizador no directório de destino. Quando esse utilizador conclui os passos para aceitar a transferência, a subscrição será movido automaticamente para o directório de destino.

-   **Se posso tomar sobre faturação que é o proprietário de uma subscrição a partir de outra organização, irá continuar a ter acesso aos meus recursos?**

    Se a subscrição é transferida para outro inquilino, os utilizadores associados ao inquilino anterior irão perder o acesso à subscrição. Mesmo que um utilizador não for um administrador de serviços ou administrador Co deixem, poderá ainda tiverem acesso à subscrição através de outros mecanismos de segurança. Estes incluem:
    - Certificados de gestão concedam ao utilizador os direitos de administrador para recursos de subscrição. Para obter mais informações, consulte o artigo [criar e carregar um certificado de gestão do Azure](https://msdn.microsoft.com/library/azure/gg551722.aspx)
    -   Teclas de acesso para serviços como o armazenamento. Para obter mais informações, consulte o artigo [vista, cópia e teclas de acesso de armazenamento gerar](storage-create-storage-account.md#view-copy-and-regenerate-storage-access-keys)
    -   Credenciais de acesso remotas para serviços como o máquinas virtuais do Azure

    Não se trata de uma lista completa. O destinatário deverá tomar em consideração atualizar qualquer segredos associados ao serviço se for necessário restringir o acesso aos seus recursos. A maior parte dos recursos podem ser atualizados da seguinte forma:

    1.   Aceda ao portal do Azure: [ *https://portal.azure.com*](https://portal.azure.com)

    2.    Clique em Procurar tudo -&gt; todos os recursos

    3.    Selecione o recurso. Esta ação abre a pá do recurso.

    4.    No pá recurso, clique em **Definições**. Aqui pode ver e atualizar segredos existentes.


-   **Se posso transferir a subscrição no meio do ciclo de faturação, é que o pagamento destinatário para a faturação inteira do ciclo de?**

    O remetente é responsável por pagamento para qualquer utilização que foi comunicado por excesso para o ponto de que a transferência está concluída. O destinatário é o responsável pela utilização comunicada desde a hora da transferência e posteriores. Poderão existir algumas a utilização que substituiu antes de transferência, mas foi comunicada posteriormente. Serão incluída na fatura do destinatário.

-   **O destinatário tem acesso a utilização e histórico de faturação?**

    Neste momento, a única informação revelada para o destinatário é a quantidade de fatura última (ou o saldo atual, se a subscrição tiver sido transferida antes da primeira fatura foi gerada). O resto do histórico de faturação e a utilização não transferir com a subscrição.

-   **A oferta pode ser alterada durante uma transferência?**

    A oferta tem de permanecer a mesma. Para alterar a sua oferta, terá de [contactar o suporte](http://go.microsoft.com/fwlink/?LinkID=619338).

-   **Pode transferir uma subscrição para uma conta de utilizador no outro país/região?**

    Não, neste momento que não é suportada. Conta de utilizador do destinatário tem de ser do mesmo país.

-   **O destinatário pode utilizar um mecanismo de pagamento diferente?**

    Sim. Existem limitações aqui: agora a histórico de faturação da subscrição é dividida em duas contas. Mas a vantagem é pode fazê-lo sem ter de [contactar o suporte](http://go.microsoft.com/fwlink/?LinkID=619338).

-   **O método de pagamento vai ser afectado após posso transferidas uma subscrição do Azure?**

    Para aceitar uma transferência de subscrição, um cartão de crédito ou o método de pagamento semelhante tem de ser fornecido para pagar a subscrição. Por exemplo, se o Rodrigo transfere uma subscrição para a Teresa e Inês aceita a transferência, Inês também devem fornecer um método de pagamento que ela irá utilizar para pagar a subscrição. Depois da transferência estiver concluída, o Rodrigo já não será cobrado pela subscrição que ele transferidos para Teresa.

## <a name="next-steps-after-accepting-ownership-of-a-subscription"></a>Próximos passos depois de o aceitar que é o proprietário de uma subscrição

1. Está agora o administrador da conta. Rever e actualizar o administrador de serviços e Coadministradores. Geri administradores no [portal clássica Azure](https://manage.windowsazure.com) ao aceder a definições. [Saiba mais](http://go.microsoft.com/fwlink/?LinkID=533293).
2. Também pode utilizar o controlo de acesso baseado em funções (RBCA) para a sua subscrição e serviços. Visite o [Azure portal](https://portal.azure.com) [saber mais sobre RBAC](http://go.microsoft.com/fwlink/?LinkID=544802)
3. Atualize as credenciais associadas dos serviços esta subscrição. Estes incluem:
    - Certificados de gestão concedam ao utilizador os direitos de administrador para recursos de subscrição. Para obter mais informações, consulte o artigo [criar e carregar uma gestão de certificado para Azure](https://msdn.microsoft.com/library/azure/gg551722.aspx)
    -   Teclas de acesso para serviços como o armazenamento. Para obter mais informações, consulte o artigo [vista, cópia e teclas de acesso de armazenamento gerar](storage-create-storage-account.md#view-copy-and-regenerate-storage-access-keys)
    -   Credenciais de acesso remotas para serviços como o máquinas virtuais do Azure
4. Atualizar a faturaçãohttps alertas para esta subscrição, no [Centro de conta Azure](https://account.windowsazure.com/Subscriptions)  [Saiba mais](http://go.microsoft.com/fwlink/?LinkID=533292)
5.  Se estiver a trabalhar com um parceiro, considere atualizar o ID de parceiro esta subscrição. Pode fazê-lo no [Centro de conta Azure](https://account.windowsazure.com/Subscriptions).

> [AZURE.NOTE] Se ainda tiver ainda mais frequentes, consulte [contactar o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para obter o seu problema resolvido rapidamente.
