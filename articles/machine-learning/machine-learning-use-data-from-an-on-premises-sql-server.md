<properties
pageTitle="Utilizar dados a partir de uma base de dados do SQL Server no local em aprender máquina | Azure"
description="Utilize dados a partir de uma base de dados do SQL Server no local para executar a análise avançadas com Azure máquina aprendizagem."
services="machine-learning"
documentationCenter=""
authors="garyericson"
manager="jhubbard"
editor="cgronlun"/>

<tags
ms.service="machine-learning"
ms.workload="data-services"
ms.tgt_pltfrm="na"
ms.devlang="na"
ms.topic="article"
ms.date="09/16/2016"
ms.author="garye;krishnan"/>

# <a name="perform-advanced-analytics-with-azure-machine-learning-using-data-from-an-on-premises-sql-server-database"></a>Efetuar análise avançadas com formação de máquina Azure através de dados a partir de uma base de dados do SQL Server no local


Muitas vezes as empresas que funcionam com dados no local, optar por tirar partido da escala e agilidade da nuvem para os respetivos das cargas de trabalho de aprendizagem automática. Mas não pretender interromper o respetivo atual processos de negócio e fluxos de trabalho ao mover os seus dados no local na nuvem. Formação do Azure máquina suporta agora ler os seus dados de uma base de dados do SQL Server no local e, em seguida, formação e pontuação um modelo com estes dados. Já não tiver manualmente copiar e sincronizar os dados entre na nuvem e o seu servidor no local. Em vez disso, o módulo **Importar dados** no Azure máquina aprendizagem Studio agora pode ler diretamente da sua base de dados do SQL Server no local para a sua formação e pontuação tarefas. 

Este artigo fornece uma descrição geral de como penetração no local dados do SQL server para Azure máquina aprendizagem. Parte do princípio de que estiver familiarizado com os conceitos de formação do Azure máquina como áreas de trabalho, módulos, conjuntos de dados, experiências, *etc*...

> [AZURE.NOTE] Esta funcionalidade não está disponível para as áreas de trabalho gratuitas. Para mais informações sobre preços de aprendizagem automática e de camadas, consulte o artigo [Preços do Azure máquina aprendizagem](https://azure.microsoft.com/pricing/details/machine-learning/).

<!-- --> 

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]


## <a name="install-the-microsoft-data-management-gateway"></a>Instalar o Microsoft Data Management Gateway

Para aceder a uma base de dados do SQL Server no local no Azure máquina aprendizagem tem de transferir e instalar o Microsoft Data Management Gateway. Quando configurar a ligação do gateway no computador aprendizagem Studio terá a oportunidade de transferir e instalar o gateway utilizando a caixa de diálogo **transferência e registar dados gateway** descrita abaixo.

Também pode instalar o Data Management Gateway antecedência ao transferir e executar o pacote de configuração MSI a partir do [Centro de transferências da Microsoft](https://www.microsoft.com/download/details.aspx?id=39717). Escolha a versão mais recente, selecionar 32 bits ou 64 bits conforme adequado para o seu computador. O MSI pode também ser utilizado para atualizar um Data Management Gateway existente para a versão mais recente, com todas as definições de preservados.

O gateway tem os pré-requisitos seguintes:

- As versões do sistema operativo Windows suportadas são Windows 7, Windows 8/8.1, Windows 10, Windows Server 2008 R2, Windows Server 2012 e Windows Server 2012 R2.
- A configuração recomendada para máquina de gateway é, pelo menos, GHz 2, 4 núcleos, 8 GB de RAM e disco 80 GB.
- Se o computador anfitrião hibernação, o gateway não será possível responder a pedidos de dados. Por isso, configure um plano de alimentação adequada no computador antes de instalar o gateway. A instalação do gateway apresenta uma mensagem, se o computador está configurado para hibernação.
- Uma vez que ocorre atividade de cópia com uma frequência específica, a utilização de recursos (CPU, memória) no computador também segue o mesmo padrão com pico e idle horas. Utilização de recursos também depende fortemente a quantidade de dados a ser movidos. Quando estão várias tarefas de cópia em curso irá observar subir durante as horas de utilização de recursos. Enquanto a configuração mínima listada acima for tecnicamente suficiente, poderá querer ter uma configuração com mais recursos de que a configuração mínima dependendo da sua carga específica para movimento de dados.

Deverá tomar em consideração o seguinte quando configurar e utilizar um data Management Gateway:

- Pode instalar apenas uma instância do Data Management Gateway num único computador.
- Pode utilizar um único gateway para várias origens de dados no local.
- Pode ligar a várias gateways em computadores diferentes à mesma origem de dados no local.
- Configurar um gateway para apenas uma área de trabalho de cada vez. Gateways não podem ser partilhadas por áreas de trabalho neste momento.
- Pode configurar várias gateways para uma área de trabalho única. Por exemplo, poderá querer utilizar um gateway que está ligado à sua origens de dados de teste durante o desenvolvimento e um gateway de produção quando estiver pronto para operationalize.
- O gateway não precisa de estar ligado na mesma máquina como origem de dados, mas manter-se mais perto da origem de dados reduz o tempo para o gateway ligar à origem de dados. Recomendamos que instale o gateway num computador que seja diferente da que aloja a origem de dados no local para que o gateway e origem de dados não competir para recursos.
- Se já tiver um gateway instalado no seu computador funcionar cenários do Power BI ou Azure fábrica de dados, instale um gateway separado para Azure máquina aprendizagem noutro computador. 

    > [AZURE.NOTE] Não pode executar o Data Management Gateway e Power BI Gateway no mesmo computador.

- Tem de utilizar o Data Management Gateway para Azure máquina formação, mesmo se estiver a utilizar o Azure ExpressRoute para outros dados. Deve tratar sua origem de dados como uma origem de dados no local (que esteja protegido por uma firewall) mesmo quando utiliza ExpressRoute e utilize o Data Management Gateway para estabelecer ligação entre aprendizagem automática e a origem de dados. 

Pode encontrar informações detalhadas sobre a pré-requisitos de instalação, passos de instalação e sugestões de resolução de problemas no artigo, [mover dados entre origens no local e na nuvem com o Data Management Gateway](../data-factory/data-factory-move-data-between-onprem-and-cloud.md#considerations-for-using-data-management-gateway), começando na secção, [Considerações para utilizar o Data Management Gateway](../data-factory/data-factory-move-data-between-onprem-and-cloud.md#considerations-for-using-data-management-gateway).

## <a name="span-idusing-the-data-gateway-step-by-step-walk-classanchorspan-idtoc450838866-classanchorspanspaningress-data-from-your-on-premises-sql-server-database-into-azure-machine-learning"></a><span id="using-the-data-gateway-step-by-step-walk" class="anchor"><span id="_Toc450838866" class="anchor"></span></span>Dados de penetração a partir da sua base de dados do SQL Server no local para o Azure máquina aprendizagem


Neste tutorial, irá configurar um data Management Gateway uma área de trabalho do Azure máquina formação, configurá-lo e, em seguida, ler os dados a partir de uma base de dados do SQL Server no local.

> [AZURE.TIP] Antes de começar, desativar o Bloqueador de janelas de pop-up do seu browser para `studio.azureml.net`. Se estiver a utilizar o browser Google Chrome, transfira e instale um dos vários plug-ins disponíveis na loja Web do Google Chrome [Clique uma vez por aplicação extensão](https://chrome.google.com/webstore/search/clickonce?_category=extensions).

### <a name="step-1-create-a-gateway"></a>Passo 1: Criar um gateway

O primeiro passo é criar e configurar o gateway para aceder à sua base de dados do SQL no local.

1. Inicie sessão no [Azure máquina aprendizagem Studio](https://studio.azureml.net/Home/) e selecione a área de trabalho que pretende trabalhar.

2. Clique em pá as **Definições** no lado esquerdo e, em seguida, clique no separador **GATEWAYS de dados** na parte superior.

3. Clique em **Novo GATEWAY de dados** na parte inferior do ecrã.

    ![](media/machine-learning-use-data-from-an-on-premises-sql-server/new-data-gateway-button.png)

4. Na caixa de diálogo **Novo gateway de dados** , introduza o **Nome do Gateway** e, opcionalmente, adicione uma **Descrição**. Clique na seta no canto inferior direita para ir para o próximo passo da configuração.

    ![](media/machine-learning-use-data-from-an-on-premises-sql-server/new-data-gateway-dialog-enter-name.png)

5. Na transferência e registo dados gateway caixa de diálogo, copie a chave de registo de GATEWAY para a área de transferência.

    ![](media/machine-learning-use-data-from-an-on-premises-sql-server/download-and-register-data-gateway.png)

6. <span id="note-1" class="anchor"></span>Se ainda não tiver transferido e instalado o Microsoft Data Management Gateway, em seguida, clique em **Transferir o Data management gateway**. Isto leva-o para o Microsoft Download Center onde pode selecionar a versão do gateway que precisar, transfira-o e instalá-lo. Pode encontrar informações detalhadas sobre a pré-requisitos de instalação, passos de instalação e sugestões de resolução de problemas nas secções início do artigo [mover dados entre origens no local e na nuvem com o Data Management Gateway](../data-factory/data-factory-move-data-between-onprem-and-cloud.md).

7. Depois do gateway é instalado, o Gestor de configuração de Gateway de gestão de dados é aberto e é apresentada a caixa de diálogo **registar o gateway** . Cole a **Chave de registo de Gateway** que copiou para a área de transferência e clique em **Registe-se**.

8. Se já tiver um gateway instalado, execute o Gestor de configuração de Gateway de gestão de dados, clique em **Alterar chave**, cole a  **Chave de registo de Gateway** que copiou para a área de transferência e clique em **OK**.

9. Quando a instalação estiver concluída, é apresentado o diálogo **registar o gateway** para Microsoft configuração Gestor data Management Gateway. Cole a chave de registo de GATEWAY que copiou para a área de transferência acima e clique em **Registe-se**.

    ![](media/machine-learning-use-data-from-an-on-premises-sql-server/data-gateway-configuration-manager-register-gateway.png)

10. A configuração de gateway está concluída, quando os seguintes valores são definidos no separador **base** no Microsoft configuração Gestor data Management Gateway:

    - **Nome do gateway** e o **nome da instância** estão definidas para o nome do gateway.

    - **Registo** está definido para **registado**.

    - **Estado** está definido para **iniciado**.

    - Barra de estado na parte inferior apresenta **ligado ao serviço de nuvem de Gateway de gestão de dados** , juntamente com uma marca de verificação verde.

     ![](media/machine-learning-use-data-from-an-on-premises-sql-server/data-gateway-configuration-manager-registered.png)

     Azure máquina aprendizagem Studio também é atualizado quando o registo é efetuada com êxito.

    ![](media\machine-learning-use-data-from-an-on-premises-sql-server\gateway-registered.png)

11. Na caixa de diálogo **Transferir e registar o gateway de dados** , clique na marca de verificação para concluir a configuração. A página de **Definições** apresenta o estado do gateway como "Online". O painel do lado direito encontrará estado e outras informações úteis.

    ![](media\machine-learning-use-data-from-an-on-premises-sql-server\gateway-status.png)

12. No Gestor de configuração de Gateway de gestão de dados de Microsoft mudar para o separador de **certificado** . O certificado especificado neste separador é utilizado para encriptar/desencriptar credenciais para o arquivo de dados no local que especificou no portal. Este é o certificado predefinido gerado. A Microsoft recomenda a alteração deste para o seu próprio certificado que pode fazer cópia de segurança no sistema de gestão de certificado. Clique em **Alterar** para utilizar em vez disso, o seu próprio certificado.

    ![](media\machine-learning-use-data-from-an-on-premises-sql-server\data-gateway-configuration-manager-certificate.png)

13. (opcional) Se pretende ativar o registo verboso para resolver problemas com o gateway, no Microsoft dados Management Gateway Gestor de configuração mudar para o separador de **diagnóstico** e selecione a opção **Ativar registo verboso para fins de resolução de problemas** . As informações de registo podem ser encontradas no Visualizador de eventos do Windows sob a **registos de serviços e aplicações**  - &gt; nó do **Data Management Gateway** . Também pode utilizar o separador de **diagnóstico** para testar a ligação a uma origem de dados no local com o gateway.

    ![](media\machine-learning-use-data-from-an-on-premises-sql-server\data-gateway-configuration-manager-verbose-logging.png)

Este procedimento conclui o gateway configurar o processo no Azure máquina aprendizagem.
Agora está pronto para utilizar os seus dados no local.

Pode criar e configurar o várias gateways no Studio para cada área de trabalho. Por exemplo, poderá ter um gateway que pretende ligar a origens de dados do teste durante o desenvolvimento e um gateway diferentes para as origens de dados de produção. Formação do Azure máquina dá-lhe a flexibilidade para configurar o várias gateways consoante o seu ambiente empresarial. Atualmente, não é possível partilhar um gateway entre as áreas de trabalho e apenas um gateway pode ser instalado num único computador. Para obter mais considerações ao instalar o gateway, consulte o artigo [Considerações para utilizar o Data Management Gateway](../data-factory/data-factory-move-data-between-onprem-and-cloud.md#considerations-for-using-data-management-gateway) no artigo, [mover dados entre origens no local e na nuvem com o Data Management Gateway](../data-factory/data-factory-move-data-between-onprem-and-cloud.md).

### <a name="step-2-use-the-gateway-to-read-data-from-an-on-premises-data-source"></a>Passo 2: Utilizar o gateway para ler dados a partir de uma origem de dados no local

Depois de configurar o gateway, pode adicionar um módulo de **Importar dados** para uma experiência que as entradas os dados a partir da base de dados do SQL Server no local.

1.  No computador aprendizagem Studio, selecione o separador **EXPERIÊNCIAS** , clique em **+ Novo** no canto inferior esquerdo e selecione **Experiência em branco** (ou selecione uma das várias experiências de exemplo disponíveis).

2.  Localize e arraste o módulo **Importar dados** para a tela de experiência.

3.  Clique em **Guardar como** , por baixo da tela. Introduza "Azure máquina formação no local SQL Server Tutorial" para o nome de experiência, selecione a área de trabalho e clique na marca de verificação **OK** .

    ![](media\machine-learning-use-data-from-an-on-premises-sql-server\experiment-save-as.png)

4.  Clique no módulo **Importar dados** para o selecionar e, em seguida, no painel de **Propriedades** à direita da tela, selecione "Base de dados do SQL no local" na lista pendente **origem de dados** .

5.  Selecione o **gateway de dados** instalado e registado. Pode configurar o gateway noutro selecionando "(adicionar novo Gateway de dados...)".

    ![](media\machine-learning-use-data-from-an-on-premises-sql-server\import-data-select-on-premises-data-source.png)

6.  Introduza o **nome de servidor de base de dados** do SQL e o **nome da base de dados**, juntamente com a **consulta de base de dados** SQL que pretende executar.

7.  Clique em **Enter valores** em **nome de utilizador e palavra-passe** e introduza as suas credenciais de base de dados. Pode utilizar autenticação integrada do Windows ou a autenticação do SQL Server, consoante a forma como o SQL Server no local está configurado.

    ![](media\machine-learning-use-data-from-an-on-premises-sql-server\database-credentials.png)
    
    A mensagem "valores necessários" será alterado para "conjunto de valores" com uma marca de verificação verde. Só precisa de introduzir as credenciais de uma vez, a menos que altere as informações de base de dados ou a palavra-passe. Formação do Azure máquina utiliza o certificado que forneceu quando instalou o gateway para encriptar as credenciais na nuvem. Azure nunca irá armazenar credenciais no local sem encriptação.

    ![](media\machine-learning-use-data-from-an-on-premises-sql-server\import-data-properties-entered.png)

8.  Clique em **Executar** para executar a experiência.

Assim que a experiência é concluída a executar o pode visualizar os dados importados a partir da base de dados ao clicar na porta de saída do módulo **Importar dados** e selecionar **visualizar**.

Assim que acabar de desenvolver a sua experiência, pode implementar e operationalize o seu modelo. Utilizar o serviço de execução do lote, dados a partir da base de dados no local do SQL Server configurada no módulo **Importar dados** serão ler e utilizados para pontuação. Apesar de poder utilizar o serviço de resposta pedir para pontuação dados no local, a Microsoft recomenda a utilização do [suplemento do Excel](machine-learning-excel-add-in-for-web-services.md) em vez disso. Atualmente, escrever um SQL Server no local base de dados através de **Exportar dados** não é suportada no seu experiências ou serviços web publicada.

