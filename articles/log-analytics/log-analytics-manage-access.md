<properties
    pageTitle="Gerir o acesso a análise de registo | Microsoft Azure"
    description="Gerir o acesso ao registo de análise utilizando uma variedade de tarefas administrativas no utilizadores, contas, áreas de trabalho OMS e contas do Azure."
    services="log-analytics"
    documentationCenter=""
    authors="bandersmsft"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="log-analytics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="08/16/2016"
    ms.author="banders"/>

# <a name="manage-access-to-log-analytics"></a>Gerir o acesso a análise de registo

Para gerir o acesso ao registo de análise, irá utilizar uma variedade de tarefas administrativas no utilizadores, contas, áreas de trabalho OMS e contas do Azure. Para criar uma nova área de trabalho no conjunto de aplicações de gestão de operações (OMS), optar por nome de uma área de trabalho, associá-la com a sua conta e escolher uma localização geográfica. Uma área de trabalho é, essencialmente, um contentor que inclui informações da conta e informações de configuração simples para a conta. Utilizador ou outros membros da sua organização podem utilizar vários OMS as áreas de trabalho para gerir diferentes conjuntos de dados que são recolhidos de todas ou partes da sua infraestrutura de TI.

O artigo [Introdução ao registo de análise](log-analytics-get-started.md) mostra-lhe como aceder rapidamente para cima e em execução e o resto deste artigo descreve detalhadamente mais algumas das ações terá de gerir o acesso a OMS.

Apesar de não poderá ter de executar todas as tarefas de gestão, primeiro, vamos abordar todas as tarefas frequentemente utilizadas que poderá utilizar nas secções seguintes:

- Determinar o número de áreas de trabalho que necessita
- Gerir contas e utilizadores
- Adicionar um grupo a uma área de trabalho existente
- Ligar uma área de trabalho existente a uma subscrição do Azure
- Atualizar uma área de trabalho para um plano de dados paga
- Alterar o tipo de plano de dados
- Adicionar uma organização do Azure Active Directory para uma área de trabalho existente
- Fechar a área de trabalho OMS

## <a name="determine-the-number-of-workspaces-you-need"></a>Determinar o número de áreas de trabalho que necessita

Uma área de trabalho é um recurso Azure e é um contentor onde dados são recolhidos, agregados, analisados e apresentados no portal do OMS.

É possível criar várias OMS registo Analytics as áreas de trabalho e para os utilizadores têm acesso a um ou mais áreas de trabalho. Em geral que pretende minimizar o número de áreas de trabalho como isto irá permitir-lhe consultar e correlacionar ao longo da maioria dos dados. Esta secção descreve quando pode ser útil criar mais do que uma área de trabalho.

Hoje em dia, fornece uma área de trabalho de análise de registo:

- Uma localização geográfica para armazenamento de dados
- Granularidade para faturação
- Isolamento de dados

Com base nas características acima, poderá querer criar várias áreas de trabalho se:

- Estiver numa empresa global e precisa de dados armazenados em regiões determinadas razões dados soberania ou conformidade.
- Está a utilizar o Azure e pretende evitar taxas de transferência de dados de saída ao ter uma área de trabalho de análise de registo na mesma região como dos recursos Azure que gere.
- Que pretende atribuir encargos a diferentes departamentos ou grupos de negócio com base na sua utilização. Quando criar uma área de trabalho para cada departamento ou grupo negócios, o seu extrato de fatura e a utilização Azure mostra os encargos para cada área de trabalho separadamente.
- Se um fornecedor de serviços gerida e precisa para manter os dados de análise do registo de cada cliente gerir isolados a partir de dados de outro cliente.
- Gerir vários clientes e pretende que cada cliente ou departamento ou grupo negócios para ver os seus próprios dados, mas não os dados para outros clientes ou departamentos ou grupos de negócio.

Ao utilizar agentes para recolher dados, pode configurar cada agente para um relatório para a área de trabalho necessária.

Se estiver a utilizar o Gestor de operações do Centro de sistema, cada grupo de gestão do Gestor de operações pode ser ligado com apenas uma área de trabalho. Pode instalar o Microsoft monitorização Agent em computadores geridas pelo Gestor de operações e que o relatório de agente para Gestor de operações e uma área de trabalho de análise de registo diferente.

### <a name="workspace-information"></a>Informações de área de trabalho

No portal do OMS, pode ver a informação de área de trabalho e escolha se pretende receber informações da Microsoft.

#### <a name="view-workspace-information"></a>Ver as informações de área de trabalho

1. No OMS, clique no mosaico **Definições** .
2. Clique no separador **contas** .
3. Clique no separador **Informações da área de trabalho** .  
  ![Informações de área de trabalho](./media/log-analytics-manage-access/workspace-information.png)

## <a name="manage-accounts-and-users"></a>Gerir contas e utilizadores

Cada área de trabalho pode ter várias contas de utilizador associadas com o mesmo e, cada conta de utilizador (conta Microsoft ou institucional) pode ter acesso a várias áreas de trabalho OMS.

Por predefinição, a conta Microsoft ou institucional utilizados para criar a área de trabalho torna-se o administrador da área de trabalho. O administrador, em seguida, pode convidar contas Microsoft adicionais ou selecione os utilizadores a partir do Azure Active Directory.

Atribuir pessoas o acesso à área de trabalho OMS é controlada em 2 locais:

- No Azure, pode utilizar o controlo de acesso baseado em funções para fornecer acesso para a subscrição do Azure e os recursos Azure associados. Também é utilizado para acesso PowerShell e REST API.
- No portal do OMS, o acesso a apenas o portal OMS - não a subscrição Azure associado.

Se conceder acesso a pessoas para o portal OMS mas não para a subscrição Azure que está ligada a, em seguida, os mosaicos de solução automatização, cópia de segurança e recuperação de Site não mostrar quaisquer dados aos utilizadores quando estes início de sessão no portal do OMS.

Para permitir que todos os utilizadores ver os dados estas soluções, certifique-se de que tenham, pelo menos, o **leitor de** aceder para no cofre conta automatização, Cofre de cópia de segurança e recuperação de sites que está ligado à área de trabalho OMS.   

### <a name="managing-access-to-log-analytics-using-the-azure-portal"></a>Gerir o acesso ao registo de análise utilizando o portal Azure

Se conceder acesso a pessoas à área de trabalho registo Analytics utilizando Azure permissões, no portal do Azure por exemplo, em seguida, os mesmos utilizadores podem aceder ao registo Analytics portal. Se os utilizadores no portal do Azure, são podem navegar para o portal OMS ao clicar na tarefa de **OMS Portal** quando vê o recurso de área de trabalho de análise de registo.

Alguns pontos a ter em conta sobre o portal do Azure:

- Não é *o controlo de acesso baseado em funções*. Se tiver permissões de acesso de *leitor* no portal do Azure para a área de trabalho de análise de registo, em seguida, pode efetuar alterações através do portal OMS. O portal OMS tem um conceito de administrador, contribuinte e utilizador só de leitura. Se a conta que está assinada-com estiver no Azure Active Directory associado à área de trabalho será um administrador no portal do OMS, caso contrário, será um contribuinte.

- Quando que inicie sessão no portal do OMS utilizando http://mms.microsoft.com, em seguida, por predefinição, verá a lista **Selecione uma área de trabalho** . Contém apenas as áreas de trabalho que foram adicionadas utilizando o portal OMS. Para ver as áreas de trabalho tiver acesso a com subscrições do Azure e, em seguida, precisa de especificar um inquilino como parte do URL. Por exemplo:

  `mms.microsoft.com/?tenant=contoso.com`O identificador de inquilino é frequentemente essa última parte do endereço de e-mail iniciar sessão com.

- Se a conta que iniciar sessão com uma conta no inquilino Azure Active Directory, que é normalmente o caso, a menos que está a iniciar sessão como um CSP, em seguida, será um *administrador* no portal do OMS. Se a sua conta não estiver no inquilino Azure Active Directory, em seguida, será um *utilizador* no portal do OMS.

- Se pretender navegue diretamente para um portal de que tem acesso ao utilizar permissões Azure, em seguida, é necessário especificar o recurso como parte do URL. É possível obter este URL através do PowerShell.

  Por exemplo, `(Get-AzureRmOperationalInsightsWorkspace).PortalUrl`.

  O URL terá o seguinte aspeto:`https://eus.mms.microsoft.com/?tenant=contoso.com&resource=%2fsubscriptions%2faaa5159e-dcf6-890a-a702-2d2fee51c102%2fresourcegroups%2fdb-resgroup%2fproviders%2fmicrosoft.operationalinsights%2fworkspaces%2fmydemo12`


### <a name="managing-users-in-the-oms-portal"></a>Gerir utilizadores no portal do OMS

Gerir utilizadores e grupo no separador **Gerir utilizadores** no separador **contas** na página de definições. Não existem, pode efetuar as tarefas nas secções seguintes.  

![Gerir utilizadores](./media/log-analytics-manage-access/setup-workspace-manage-users.png)

#### <a name="add-a-user-to-an-existing-workspace"></a>Adicionar um utilizador para uma área de trabalho existente

Utilize os seguintes passos para adicionar um utilizador ou grupo para uma área de trabalho OMS. O utilizador ou grupo estão poderão ver e agir em todos os alertas que estão associados esta área de trabalho.

>[AZURE.NOTE] Se pretender adicionar um utilizador ou grupo a partir de uma conta organizacional do Azure Active Directory, primeiro tem de garantir que tiver associadas a sua conta OMS com o seu domínio do Active Directory. Consulte o artigo [Adicionar uma Azure Active Directory organização para uma área de trabalho existente](#add-an-azure-active-directory-organization-to-an-existing-workspace).

1. No OMS, clique no mosaico **Definições** .
2. Clique no separador **contas** e, em seguida, clique no separador **Gerir utilizadores** .
3. Na secção **Gerir utilizadores** , selecione o tipo de conta para adicionar: **Conta institucional**, **Conta Microsoft**, **Suporte da Microsoft**.
    - Se optar por Account Microsoft, escreva o endereço de e-mail de utilizador associado a Account Microsoft.
    - Se optar por conta institucional, pode introduzir parte do utilizador ou o alias de e-mail ou nome do grupo e será apresentada uma lista de utilizadores e grupos. Selecione um utilizador ou grupo.
    - Utilizar o Microsoft Support para dar um Support da Microsoft efectuar a engenharia acesso temporário à área de trabalho para o ajudar com a resolução de problemas.

    >[AZURE.NOTE] Para obter os melhores resultados, limitar o número de grupos do Active Directory associados a uma única conta OMS para três — um para administradores, uma para os contribuintes e um para os utilizadores só de leitura. Utilizar grupos mais pode afetar o desempenho do registo de análise.

5. Selecione o tipo de utilizador ou grupo para adicionar: **administrador**, **Contribuinte**ou **Utilizador só de leitura** .  
6. Clique em **Adicionar**.

  Se estiver a adicionar uma conta Microsoft, é enviado um convite para a área de trabalho de aderir ao e-mail que forneceu. Depois do utilizador segue as instruções no convite para participar OMS, o utilizador pode ver os alertas e as informações de conta para esta conta OMS e poderão ver as informações de utilizador no separador **contas** da página **Definições** .
  Se estiver a adicionar uma conta institucional, o utilizador será possível aceder à análise de registo imediatamente.  
  ![e-mail de convite](./media/log-analytics-manage-access/setup-workspace-invitation-email.png)

#### <a name="edit-an-existing-user-type"></a>Editar um tipo de utilizador existente

Pode alterar a função de conta para um utilizador associado com a sua conta OMS. Tem as seguintes opções de função:

 - *Administrador*: pode gerir utilizadores, ver e agir em todos os alertas e adicionar e remover servidores

 - *Contribuinte*: pode ver e agir em todos os alertas e adicionar e remover servidores

 - *Utilizador só de leitura*: os utilizadores marcados como só de leitura não poderão para:
   1. Adicionar/remover soluções. A Galeria de soluções estiver oculto.
   2. Adicionar/modificar/remover os mosaicos no **Meu Dashboard**.
   3. Visualizar as páginas de **Definições** . As páginas estão ocultas.
   4. No pesquisa vista, obter configuração, guardado as pesquisas e alertas de tarefas estão ocultas.


#### <a name="to-edit-an-account"></a>Para editar uma conta

1. No OMS, clique no mosaico **Definições** .
2. Clique no separador **contas** e, em seguida, clique no separador **Gerir utilizadores** .
3. Selecione a função para o utilizador ao qual pretende alterar.
2. Na caixa de diálogo de confirmação, clique em **Sim**.

### <a name="remove-a-user-from-a-oms-workspace"></a>Remover um utilizador a partir de uma área de trabalho OMS

Utilize os passos seguintes para remover um utilizador a partir de uma área de trabalho OMS. Tenha em atenção que esta não fechar área de trabalho o utilizador. Em vez disso, remove a associação entre esse utilizador e a área de trabalho. Se um utilizador está associado a várias áreas de trabalho, esse utilizador continuarão conseguir iniciar sessão no OMS e ver outras áreas de trabalho.

1. No OMS, clique no mosaico **Definições** .
2. Clique no separador **contas** e, em seguida, clique no separador **Gerir utilizadores** .
3. Clique em **Remover** junto ao nome do utilizador ao qual pretende remover.
4. Na caixa de diálogo de confirmação, clique em **Sim**.


### <a name="add-a-group-to-an-existing-workspace"></a>Adicionar um grupo a uma área de trabalho existente

1.  Siga os passos 1 -4 no "Para adicionar um utilizador para uma área de trabalho existente", acima.
2.  Em **Escolher utilizador/grupo**, selecione o **grupo**.
    ![Adicionar um grupo a uma área de trabalho existente](./media/log-analytics-manage-access/add-group.png)
3.  Introduza o nome a apresentar ou endereço de E-Mail para o grupo que pretende adicionar.
4.  Selecione o grupo nos resultados da lista e, em seguida, clique em **Adicionar**.

## <a name="link-an-existing-workspace-to-an-azure-subscription"></a>Ligar uma área de trabalho existente a uma subscrição do Azure

É possível criar uma área de trabalho a partir do Web site da [microsoft.com/oms](https://microsoft.com/oms) .  No entanto, determinadas existem limites para estas áreas de trabalho, o máximo notáveis a ser um limite de 500MB/dia dos carregamentos de dados de se estiver a utilizar uma conta gratuita. Para efetuar alterações a esta área de trabalho terá de *ligar a área de trabalho existente para uma subscrição do Azure*.

>[AZURE.IMPORTANT] Para poder ligar uma área de trabalho, a conta Azure já tem de ter acesso à área de trabalho que pretende ligar.  Por outras palavras, a conta que utiliza para aceder ao portal Azure tem de ser **a mesma** como a conta que utiliza para aceder à área de trabalho OMS. Se não for o caso, consulte o artigo [Adicionar um utilizador para uma área de trabalho existente](#add-a-user-to-an-existing-workspace).

### <a name="to-link-a-workspace-to-an-azure-subscription-in-the-oms-portal"></a>Para ligar uma área de trabalho a uma subscrição do Azure no portal do OMS

Para poder ligar uma área de trabalho a uma subscrição do Azure no portal do OMS, o utilizador tiver sessão iniciada no já tem de ter uma conta Azure paga. A área de trabalho que esteja a utilizar ativamente recebe uma ligação para a conta Azure.

1. No OMS, clique no mosaico **Definições** .
2. Clique no separador **contas** e, em seguida, clique no separador **Azure subscrição e o plano de dados** .
3. Clique no plano de dados que pretende utilizar.
4. Clique em **Guardar**.  
  ![planos de subscrição e dados](./media/log-analytics-manage-access/subscription-tab.png)

O novo plano de dados é apresentado no Friso, portal OMS na parte superior da sua página web.

![Friso OMS](./media/log-analytics-manage-access/data-plan-changed.png)

### <a name="to-link-a-workspace-to-an-azure-subscription-in-the-azure-portal"></a>Para ligar uma área de trabalho a uma subscrição do Azure no portal do Azure

1.  Inicie sessão no [portal do Azure](http://portal.azure.com).
2.  Procure o **Registo de análise (OMS)** e, em seguida, selecione.
3.  Irá ver a sua lista de áreas de trabalho existentes. Clique em **Adicionar**.  
    ![lista de áreas de trabalho](./media/log-analytics-manage-access/manage-access-link-azure01.png)
4.  Em **OMS área de trabalho**, clique em **ou ligar a existente**.  
    ![ligar a existente](./media/log-analytics-manage-access/manage-access-link-azure02.png)
5.  Clique em **Configurar obrigatório definições**.  
    ![configurar definições necessárias](./media/log-analytics-manage-access/manage-access-link-azure03.png)
6.  Verá a lista de áreas de trabalho que ainda não estão ligados à sua conta Azure. Selecione uma área de trabalho.  
    ![Selecione as áreas de trabalho](./media/log-analytics-manage-access/manage-access-link-azure04.png)
7.  Se for necessário, pode alterar valores para os seguintes itens:
    - Subscrição
    - Grupo de recursos
    - Localização
    - Preços de camadas  
        ![alterar os valores](./media/log-analytics-manage-access/manage-access-link-azure05.png)
8.  Clique em **Criar**. A área de trabalho está agora ligada à sua conta Azure.

>[AZURE.NOTE] Se não vir a área de trabalho que pretende ligar, em seguida, a sua subscrição Azure não tem acesso à área de trabalho OMS criado utilizando o Web site OMS.  Terá de conceder acesso a esta conta a partir de dentro da sua área de trabalho OMS utilizando o Web site OMS. Para fazê-lo, consulte o artigo [Adicionar um utilizador para uma área de trabalho existente](#add-a-user-to-an-existing-workspace).



## <a name="upgrade-a-workspace-to-a-paid-data-plan"></a>Atualizar uma área de trabalho para um plano de dados paga

Existem os dados da área de trabalho de três planear tipos OMS: **livre**, **padrão**e **Premium**.  Se estiver a um plano de *livre* , poderá ter clicada o remate de dados de 500 MB.  Terá de atualizar a área de trabalho para um ***plano repartição*** para recolher dados que ultrapassem este limite. Em qualquer altura pode converter o seu tipo de plano.  Para mais informações sobre preços OMS, consulte o artigo [Detalhes do preços](https://www.microsoft.com/en-us/server-cloud/operations-management-suite/pricing.aspx).

>[AZURE.IMPORTANT] Só podem ser alterados planos de área de trabalho se estiverem *ligados* a uma subscrição Azure.  Se criou a área de trabalho no Azure ou se tiver *já* ligada a área de trabalho, pode ignorar esta mensagem.  Se criou a área de trabalho com o [Web site OMS](http://www.microsoft.com/oms), terá de seguir os passos na [ligação uma área de trabalho existente para uma subscrição do Azure](#link-an-existing-workspace-to-an-azure-subscription).

### <a name="using-entitlements-from-the-oms-add-on-for-system-center"></a>Com direitos a partir do suplemento OMS sistema Centro para

O suplemento OMS para o Centro de sistema fornece uma elegibilidade para o plano de Premium de análise de registo de OMS, descrito em [OMS preços](https://www.microsoft.com/en-us/server-cloud/operations-management-suite/pricing.aspx).

Quando comprar o suplemento OMS para o Centro de sistema, o suplemento OMS é adicionado como um direito no contrato de centro de sistema. Pode efetuar qualquer Azure subscrição que é criada ao abrigo deste contrato utilizar do direito. Esta opção permite-lhe, por exemplo, ter várias áreas de trabalho OMS que utilizam o direito do suplemento OMS.

Para se certificar de que a utilização da área de trabalho do OMS está aplicada ao seu direitos a partir do suplemento OMS, terá:

1. Ligar a área de trabalho OMS a uma subscrição do Azure que faz parte de acordo empresarial que inclui a compra de suplemento OMS e a utilização de subscrição Azure
2. Selecione o plano de Premium para a área de trabalho

Quando a rever a sua utilização no portal do Azure ou OMS, não verá os direitos de suplemento OMS. No entanto, pode ver direitos no Portal do Enterprise.  

Se precisar de alterar a subscrição Azure que a área de trabalho OMS para este funcionário, pode utilizar o cmdlet do Azure PowerShell [AzureRmResource mover](https://msdn.microsoft.com/library/mt652516.aspx) .

### <a name="using-azure-commitment-from-an-enterprise-agreement"></a>Utilizar o compromisso Azure a partir de um acordo empresarial

Se optar por utilizar autónomo preços para componentes OMS, irá pagar separadamente para cada componente de OMS e a utilização serão apresentados na sua fatura Azure.

Se tiver uma consolidação monetária Azure a inscrição de empresa para o qual as suas subscrições Azure estão ligadas, qualquer a utilização da análise do registo será automaticamente débito contra qualquer consolidar monetário restante.

Se precisar de alterar a subscrição Azure que a área de trabalho OMS está associada a pode utilizar o cmdlet do Azure PowerShell [AzureRmResource mover](https://msdn.microsoft.com/library/mt652516.aspx) .  



### <a name="to-change-a-workspace-to-a-paid-data-plan"></a>Para alterar uma área de trabalho para um plano de dados paga

1.  Inicie sessão no [portal do Azure](http://portal.azure.com).
2.  Procure o **Registo de análise (OMS)** e, em seguida, selecione.
3.  Irá ver a sua lista de áreas de trabalho existentes. Selecione uma área de trabalho.  
    ![lista de áreas de trabalho](./media/log-analytics-manage-access/manage-access-change-plan01.png)
4.  Em **Definições**, clique em **níveis de preços**.  
    ![preços de camadas](./media/log-analytics-manage-access/manage-access-change-plan02.png)
5.  Em **preços camada**, selecione um plano de dados e, em seguida, clique em **Selecionar**.  
    ![Selecione o plano](./media/log-analytics-manage-access/manage-access-change-plan03.png)
6.  Quando atualizar a vista no portal do Azure, verá **os preços camada** actualizados para o plano que selecionou.  
    ![actualização de preços de camadas](./media/log-analytics-manage-access/manage-access-change-plan04.png)

Agora pode recolher dados que ultrapassem o remate de dados "gratuito".


## <a name="add-an-azure-active-directory-organization-to-an-existing-workspace"></a>Adicionar uma organização do Azure Active Directory para uma área de trabalho existente

Pode associar a área de trabalho de análise de registo (OMS) a um domínio do Azure Active Directory. Permite-lhe adicionar utilizadores a partir do Active Directory diretamente para a área de trabalho OMS sem necessidade de uma conta Microsoft em separado.

Quando criar a área de trabalho a partir do portal do Azure ou ligar a área de trabalho a uma subscrição do Azure Azure Active Directory será ligado como a sua conta institucional.

Quando cria a área de trabalho a partir do portal OMS que ser-lhe-á pedido para criar uma ligação para uma subscrição do Azure e uma conta institucional.

### <a name="to-add-an-azure-active-directory-organization-to-an-existing-workspace"></a>Para adicionar uma organização do Azure Active Directory para uma área de trabalho existente

1. Na página definições no OMS, clique em **contas** e, em seguida, clique no separador **Informações de área de trabalho** .  
2. Reveja as informações sobre contas organizacionais e, em seguida, clique em **Adicionar organização**.  
    ![adicionar a organização](./media/log-analytics-manage-access/manage-access-add-adorg01.png)
3. Introduza as informações de identidade para o administrador do seu domínio do Azure Active Directory. Mais tarde, verá uma confirmação de que a área de trabalho está ligada ao seu domínio do Azure Active Directory.
    ![confirmação da área de trabalho ligada](./media/log-analytics-manage-access/manage-access-add-adorg02.png)

>[AZURE.NOTE] Assim que a sua conta estiver ligada a uma conta institucional, ligar não pode ser removida ou alterada.

## <a name="close-your-oms-workspace"></a>Fechar a área de trabalho OMS

Quando fecha uma área de trabalho OMS, todos os dados relacionados com a área de trabalho é eliminado a partir do serviço OMS dentro de 30 dias de fechar a área de trabalho.

Se for um administrador e não existem vários utilizadores associados a área de trabalho, a associação entre esses utilizadores e a área de trabalho é interrompida. Se os utilizadores estão associados a outras áreas de trabalho, em seguida, eles podem continuar a utilizar OMS com essas outras áreas de trabalho. No entanto, se não estão associadas a outras áreas de trabalho, em seguida, que serão necessário criar uma nova área de trabalho para utilizar OMS.

### <a name="to-close-an-oms-workspace"></a>Para fechar uma área de trabalho OMS

1. No OMS, clique no mosaico **Definições** .
2. Clique no separador **contas** e, em seguida, clique no separador **Informações de área de trabalho** .
3. Clique em **Fechar área de trabalho**.
4. Selecione uma dos motivos para fechar a área de trabalho ou introduza um motivo diferente na caixa de texto.
5. Clique em **Fechar área de trabalho**.

## <a name="next-steps"></a>Próximos passos

- Consulte o artigo [computadores com o Windows ligar-se para a análise de registo](log-analytics-windows-agents.md) para adicionar agentes e recolher dados.
- [Soluções de adicionar o registo de análise a partir da Galeria de soluções](log-analytics-add-solutions.md) para adicionar a funcionalidade e reunir dados.
- [Configurar as definições de proxy e a firewall no registo de análise](log-analytics-proxy-firewall.md) se a sua organização utiliza uma firewall ou servidor proxy para que agentes possam comunicar com o serviço de análise de registo.
