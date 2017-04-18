<properties
    pageTitle="Ligar o Gestor de configuração da análise de registo | Microsoft Azure"
    description="Este artigo mostra os passos para ligar o Gestor de configuração da análise de registo e comece a análise dos dados."
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
    ms.topic="article"
    ms.date="08/29/2016"
    ms.author="banders"/>

# <a name="connect-configuration-manager-to-log-analytics"></a>Ligar o Gestor de configuração da análise de registo

Pode ligar o Gestor de configuração do Centro de sistema para a análise de registo no OMS aos dados de coleção de dispositivo de sincronização. Isto torna disponível em OMS dados a partir da sua implementação do Gestor de configuração.

Existem um número de passos necessários para ligar o Gestor de configuração da OMS, por isso Eis uma rápida execução do processo de global:

1. No Portal de gestão do Azure, registe-se Gestor de configuração, como uma aplicação de aplicação Web e/ou Web API e certifique-se de que tem o ID de cliente e chave secreta do cliente do registo a partir do Azure Active Directory. Consulte o artigo [portal de utilização para criar a aplicação do Active Directory e principal que podem aceder a recursos de serviço](../resource-group-create-service-principal-portal.md) para obter informações detalhadas sobre como concluir este passo.
2. No Azure Portal de gestão, [forneça Gestor de configuração (a aplicação web registados) com permissão para aceder a OMS](#provide-configuration-manager-with-permissions-to-oms).
3. No Gestor de configuração, [Adicionar uma ligação utilizando o Assistente de ligação de OMS adicionar](#add-an-oms-connection-to-configuration-manager).
4. No Gestor de configuração, pode [atualizar as propriedades de ligação](#update-oms-connection-properties) se a palavra-passe ou um cliente de chave secreta nunca expira ou é perdida.
5. Com informações a partir do portal OMS, [transfira e instale o Microsoft monitorização Agent](#download-and-install-the-agent) no computador com ligação de Gestor de configuração de serviço aponte a função de sistema do site. O agente envia dados de Gestor de configuração para OMS.
6. No OMS [coleções de sites a partir do Gestor de configuração de importar](#import-collections) como grupos do computador.
7. No OMS, ver dados a partir do Gestor de configuração como [grupos do computador](log-analytics-computer-groups.md).

Pode ler mais sobre como ligar o Gestor de configuração à OMS nos [dados a partir do Gestor de configuração para o conjunto de aplicações do Microsoft operações de gestão de sincronização](https://technet.microsoft.com/library/mt757374.aspx).



## <a name="provide-configuration-manager-with-permissions-to-oms"></a>Fornecer Gestor de configuração com permissões para OMS

O procedimento seguinte fornece Portal de gestão do Azure com permissões de acesso ao OMS. Especificamente, tem de conceder a *função Contribuinte* para os utilizadores do grupo de recursos. Por sua vez, que permite que o Portal de gestão do Azure ligar o Gestor de configuração para OMS.

>[AZURE.NOTE] Tem de especificar permissões para OMS para o Gestor de configuração. Caso contrário, receberá uma mensagem de erro quando utiliza o Assistente de configuração no Gestor de configuração.


1. Abra o [portal do Azure](https://portal.azure.com/) e clique em **Procurar** > **Registo Analytics (OMS)** para abrir o pá de análise de registo (OMS).  
2. No pá **Registo Analytics (OMS)** , clique em **Adicionar** para abrir a **Área de trabalho OMS** pá.  
  ![Pá OMS](./media/log-analytics-sccm/sccm-azure01.png)
3. No pá **OMS área de trabalho** , forneça as seguintes informações e, em seguida, clique em **OK**.
  - **OMS área de trabalho**
  - **Subscrição**
  - **Grupo de recursos**
  - **Localização**
  - **Preços de camadas**  
    ![Pá OMS](./media/log-analytics-sccm/sccm-azure02.png)  

    >[AZURE.NOTE] O exemplo acima cria um novo grupo de recursos. O grupo de recursos só é utilizado para fornecer o Gestor de configuração com permissões para a área de trabalho OMS neste exemplo.

4. Clique em **Procurar** > **grupos de recursos** para abrir o pá de **grupos de recursos** .
5. Na pá **grupos de recursos** , clique no grupo de recursos que criou acima para abrir o &lt;nome do grupo de recursos&gt; pá definições.  
  ![Pá de definições do grupo de recursos](./media/log-analytics-sccm/sccm-azure03.png)
6. No &lt;nome do grupo de recursos&gt; pá de definições, clique em controlo de acesso (IAM) para abrir o &lt;nome do grupo de recursos&gt; pá de utilizadores.  
  ![Pá de utilizadores do grupo de recursos](./media/log-analytics-sccm/sccm-azure04.png)  
7. No &lt;nome do grupo de recursos&gt; pá de utilizadores, clique em **Adicionar** para abrir o pá **adicionar acesso** .
8. Na pá **adicionar acesso** , clique em **selecionar uma função**e, em seguida, selecione a função **Contribuinte** .  
  ![Selecionar uma função](./media/log-analytics-sccm/sccm-azure05.png)  
9. Clique em **Adicionar utilizadores**, selecione o utilizador do Gestor de configuração, clique em **Seleccionar**e, em seguida, clique em **OK**.  
  ![Adicionar utilizadores](./media/log-analytics-sccm/sccm-azure06.png)  


## <a name="add-an-oms-connection-to-configuration-manager"></a>Adicionar uma ligação de OMS ao Gestor de configuração

Para adicionar uma ligação de OMS, o seu ambiente do Gestor de configuração tem de ter uma [ligação de um serviço aponte](https://technet.microsoft.com/library/mt627781.aspx) configurado para o modo online.

1. Na **Administração de** área de trabalho do Gestor de configuração, selecione **OMS conexão**. Esta ação abre o **Assistente de ligação de OMS adicionar**. Selecione **seguinte**.

2. No ecrã **Geral** , confirme que efetuou as seguintes ações e que pode ter detalhes para cada item, em seguida, selecione **seguinte**.
  1. No Portal de gestão do Azure, já se registou o Gestor de configuração do como uma aplicação de aplicação Web e/ou Web API e que tem o [ID de cliente do registo](../active-directory/active-directory-integrating-applications.md).
  2. No Portal de gestão do Azure, criou uma tecla secreta de aplicação para a aplicação registada no Azure Active Directory.  
  3. No Portal de gestão do Azure, que forneceu a aplicação web registado com permissão para aceder a OMS.  
  ![Ligação à página OMS assistente geral](./media/log-analytics-sccm/sccm-console-general01.png)

3. No ecrã do **Azure Active Directory** , configure as definições de ligação para OMS ao fornecer o seu **inquilino** , **ID de cliente** e **Chave de palavra-passe de cliente** , em seguida, selecione **seguinte**.  
  ![Ligação à página OMS assistente Azure Active Directory](./media/log-analytics-sccm/sccm-wizard-tenant-filled03.png)

4. Se todos os outros procedimentos que fez com êxito, em seguida, as informações no ecrã de **Configuração da ligação OMS** aparecerá automaticamente nesta página. Deverão aparecer informações para as definições de ligação para a sua **subscrição do Azure** , **grupo de recursos Azure** e **Área de trabalho de conjunto de aplicações de gestão de operações**.  
  ![Ligação à página OMS assistente OMS ligação](./media/log-analytics-sccm/sccm-wizard-configure04.png)

5. O assistente liga-se para o serviço OMS utilizando as informações que tenha de entrada. Selecione as coleções de ficheiros do dispositivo que pretende sincronizar com OMS e, em seguida, clique em **Adicionar**.  
  ![Selecione coleções de sites](./media/log-analytics-sccm/sccm-wizard-add-collections05.png)

6. Verificar as definições de ligação no ecrã de **Resumo** , em seguida, selecione **seguinte**. No ecrã de **progresso** mostra o estado da ligação, em seguida, deverá **concluída**.

>[AZURE.NOTE] OMS tem de ligar para o site de camada superior na sua hierarquia. Se ligar OMS a um site principal autónomo e, em seguida, adicionar um site de administração central para o seu ambiente, terá de eliminar e recrie a ligação OMS dentro de nova hierarquia.

Depois de ter ligado Gestor de configuração para OMS, pode adicionar ou remover coleções de sites e ver as propriedades da ligação OMS.

## <a name="update-oms-connection-properties"></a>Atualizar as propriedades de ligação OMS

Se uma palavra-passe ou um cliente de chave secreta nunca expira ou é perdida, terá de atualizar manualmente as propriedades da ligação OMS.

1. No Gestor de configuração, navegue até aos **Serviços em nuvem** , em seguida, selecione **OMS conexão** para abrir a página de **Propriedades da ligação OMS** .
2. Nesta página, clique no separador do **Azure Active Directory** para ver o seu **inquilino**, **ID de cliente**, **expiração chave secreta de cliente**. **Verifique se** a **chave secreta cliente** se esta tiver expirado.


## <a name="download-and-install-the-agent"></a>Transferir e instalar o agente

1. No portal do OMS, [Transfira o ficheiro de configuração de agente de OMS](log-analytics-windows-agents.md#download-the-agent-setup-file-from-oms).
2. Utilize um dos seguintes métodos para instalar e configurar o agente no computador com a função de sistema do Gestor de configuração serviço ligação ponto de site:
  - [Instalar o agente de utilizar o programa de configuração](log-analytics-windows-agents.md#install-the-agent-using-setup)
  - [Instalar o agente utilizando a linha de comandos](log-analytics-windows-agents.md#install-the-agent-using-the-command-line)
  - [Instalar o agente de utilizar DSC no Azure automatização](log-analytics-windows-agents.md#install-the-agent-using-dsc-in-azure-automation)


## <a name="import-collections"></a>Importar coleções de sites

Após ter adicionada uma ligação de OMS para o Gestor de configuração e instalado o agente no computador com ligação de Gestor de configuração de serviço aponte a função de sistema do site, o passo seguinte é importar a partir do Gestor de configuração no OMS como grupos de computador de coleções de sites.

Depois de importação estiver ativada, as informações de associação de coleções de sites são obtidas a cada 3 horas para manter os membros de coleções de sites atual. Pode optar por desactivar importação em qualquer altura.

1. No portal do OMS, clique em **Definições**.
2. Clique no separador de **Grupos de computador** e, em seguida, clique no separador **SCCM** .
3. Seleccionar **associações de coleções de sites do Gestor de configuração de importar** e, em seguida, clique em **Guardar**.  
  ![Grupos de computador - separador SCCM](./media/log-analytics-sccm/sccm-computer-groups01.png)

## <a name="view-data-from-configuration-manager"></a>Ver dados a partir do Gestor de configuração

Após ter adicionada uma ligação de OMS para o Gestor de configuração e instalado o agente no computador com a função de sistema do Gestor de configuração serviço ligação ponto de site, dados do agente de são enviados para OMS. No OMS, suas coleções de Gestor de configuração são apresentadas como [grupos de computador](log-analytics-computer-groups.md). Pode ver os grupos a partir da página de **Gestor de configuração** em **Grupos de computador** nas **Definições**.

Depois de importar as coleções de ficheiros, pode ver detectados quantos computadores com membros de coleções de sites. Também pode ver o número de coleções de sites que foram importados.

![Grupos de computador - separador SCCM](./media/log-analytics-sccm/sccm-computer-groups02.png)

Quando clica em qualquer uma, é aberta a pesquisa e apresenta em todos os dos grupos importados ou todos os computadores que pertencem a cada grupo. Utilizar o [Registo de pesquisa](log-analytics-log-searches.md), pode iniciar uma análise aprofundada para os dados de Gestor de configuração.

## <a name="next-steps"></a>Próximos passos

- Utilize a [Pesquisa de registo](log-analytics-log-searches.md) para ver informações detalhadas sobre os seus dados do Gestor de configuração.
