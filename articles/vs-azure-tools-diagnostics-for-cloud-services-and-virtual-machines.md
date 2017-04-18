<properties
   pageTitle="Configuração de diagnóstico para serviços em nuvem Azure e máquinas virtuais | Microsoft Azure"
   description="Descreve como configurar as informações de diagnóstico para depuração de serviços de Azure cloude e máquinas virtuais (VMs) no Visual Studio."
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="08/15/2016"
   ms.author="tarcher" />

# <a name="configuring-diagnostics-for-azure-cloud-services-and-virtual-machines"></a>Configuração de diagnóstico para serviços em nuvem Azure e máquinas virtuais

Quando precisar de resolver problemas de um serviço na nuvem Azure ou máquina virtual Azure, pode configurar mais facilmente diagnósticos do Azure utilizando o Visual Studio. Diagnósticos do Azure para capturar dados de sistema e registo do máquinas virtuais e instâncias de máquina virtual que executam o seu serviço de nuvem e transferem de que os dados para uma conta de armazenamento da sua escolha. Consulte o artigo [Ativar diagnósticos do registo para web apps no Azure aplicação de serviço](./app-service-web/web-sites-enable-diagnostic-log.md) para obter mais informações acerca dos diagnósticos do registo no Azure.

Este tópico mostra como ativar e configurar o Azure diagnósticos no Visual Studio, ambos antes e depois de implementação, e em máquinas virtuais do Azure. -Lo também mostra-lhe como selecionar os tipos de informações de diagnóstico para recolher e sobre como ver as informações depois de serem recolhido.

Pode configurar diagnósticos do Azure das seguintes formas:

- Pode alterar as definições de configuração de diagnóstico através da caixa de diálogo de **Configuração de diagnósticos** no Visual Studio. As definições são guardadas num ficheiro denominado diagnostics.wadcfgx (diagnostics.wadcfg no Azure SDK 2.4 ou anterior). Em alternativa, pode modificar diretamente o ficheiro de configuração. Se atualizar manualmente o ficheiro, as alterações à configuração serão aplicada da próxima vez implementar a nuvem do serviço para Azure ou executar o serviço no emulador.

- Utilize o **Explorador de nuvem** ou **Servidor Explorador** no Visual Studio para alterar as definições de diagnóstico para um serviço de nuvem em execução ou máquina virtual.

## <a name="azure-26-diagnostics-changes"></a>Alterações de diagnósticos do Azure 2.6

Para os projectos Azure SDK 2.6 no Visual Studio, as seguintes alterações foram efetuadas. (Estas alterações também se aplica a versões posteriores do Azure SDK.)

- Agora, o local emulador suporta diagnósticos. Isto significa que pode recolher dados de diagnóstico e certifique-se de que a aplicação está a criar os rastreios direita enquanto está a desenvolver e testar no Visual Studio. A cadeia de ligação `UseDevelopmentStorage=true` permite a recolha de dados de diagnóstico enquanto estiver a executar o seu projeto do serviço de nuvem no Visual Studio utilizando o emulador armazenamento Azure. Todos os dados de diagnóstico são recolhidas na conta de armazenamento (armazenamento de desenvolvimento).

- A cadeia de ligação de conta de armazenamento de diagnóstico (Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString) é armazenada mais uma vez no ficheiro de configuração (.cscfg) de serviço. No Azure SDK 2,5 a conta de armazenamento de diagnóstico foi especificada no ficheiro diagnostics.wadcfgx.

Existem algumas diferenças entre como a cadeia de ligação trabalhou no Azure SDK 2.4 e anterior e como funciona no Azure SDK 2.6 e versões posteriores.

- No Azure SDK 2.4 e anteriores, a cadeia de ligação foi utilizada como um tempo de execução o plug-in de diagnóstico para obter as informações de conta de armazenamento para transferir os registos de diagnóstico.

- No Azure SDK 2.6 e posterior, a cadeia de ligação de diagnóstico é utilizada pelo Visual Studio para configurar a extensão de diagnóstico com as informações de conta de armazenamento adequado durante a publicação. A cadeia de ligação permite-lhe definir as contas de armazenamento diferente para configurações de serviços diferente que Visual Studio irá utilizar quando de publicação. No entanto, uma vez que o plug-in de diagnóstico já não se encontra disponível (depois de Azure SDK 2,5), o ficheiro .cscfg por si só é possível ativar a extensão de diagnóstico. Tem de ativar a extensão separadamente através de ferramentas, como o Visual Studio ou PowerShell.

- Para simplificar o processo de configurar a extensão de diagnóstico com o PowerShell, a saída do pacote a partir do Visual Studio também contém a configuração do pública XML para a extensão de diagnóstico para cada função. Visual Studio utiliza a cadeia de ligação de diagnóstico para preencher as informações de conta de armazenamento presentes na configuração do pública. Os ficheiros de config público são criados na pasta extensões e siga o padrão de PaaSDiagnostics. &lt;Nome da função >. PubConfig.xml. Qualquer implementações do PowerShell com base podem utilizar este padrão para mapear cada configuração para uma função.

- A cadeia de ligação no ficheiro .cscfg também é utilizada pelo [Azure portal](http://go.microsoft.com/fwlink/p/?LinkID=525040) para aceder aos dados de diagnóstico para que possam aparecer em separador de **monitorização** . A cadeia de ligação é necessário para configurar o serviço para mostrar dados de monitorização verbosas no portal.

## <a name="migrating-projects-to-azure-sdk-26-and-later"></a>Migrar projectos para Azure SDK 2.6 e versões posteriores

Quando migrar a partir do Azure SDK 2,5 ao Azure SDK 2.6 ou posterior, se tiver uma conta de armazenamento de diagnóstico especificada no ficheiro .wadcfgx, em seguida,-lo irá permanecer aí. Para tirar partido da flexibilidade da utilizando as contas de armazenamento diferente para configurações de armazenamento diferente, terá de adicionar manualmente a cadeia de ligação ao seu projeto. Se está a migrar um projeto a partir do Azure SDK 2.4 ou anterior para o Azure SDK 2.6, as cadeias de ligação de diagnóstico são preservadas. No entanto, tenha em atenção as alterações no como cadeias de ligação são tratadas no Azure SDK 2.6 conforme especificado na secção anterior.

### <a name="how-visual-studio-determines-the-diagnostics-storage-account"></a>Como o Visual Studio determina a conta de armazenamento de diagnóstico

- Se uma cadeia de ligação de diagnóstico for especificada no ficheiro .cscfg, o Visual Studio utiliza-o para configurar a extensão de diagnóstico ao publicar e, quando gerar os ficheiros de xml configuração público durante embalagem.

- Se não for especificada nenhum cadeia de ligação de diagnóstico no ficheiro .cscfg, em seguida, Visual Studio reverte para utilizando a conta de armazenamento especificada no ficheiro de .wadcfgx para configurar a extensão de diagnóstico quando publicar e gerar os ficheiros de xml configuração público quando embalagem.

- A cadeia de ligação de diagnóstico no ficheiro .cscfg tem precedência sobre a conta de armazenamento no ficheiro .wadcfgx. Se uma cadeia de ligação de diagnóstico for especificada no ficheiro .cscfg, Visual Studio utiliza que e ignora a conta de armazenamento no .wadcfgx.

### <a name="what-does-the-update-development-storage-connection-strings-checkbox-do"></a>O que faz as "atualização desenvolvimento armazenamento cadeias de ligação..." caixa de verificação fazer?

A caixa de verificação **Atualizar cadeias de ligação do armazenamento de desenvolvimento de diagnóstico e de colocação em cache com credenciais da conta de armazenamento do Microsoft Azure durante a publicação do Microsoft Azure** fornece-lhe um meio cómodo como atualizar quaisquer cadeias de ligação de conta de armazenamento de desenvolvimento com a conta de armazenamento Azure especificada durante a publicação.

Por exemplo, imaginemos Selecione esta caixa de verificação e a cadeia de ligação de diagnóstico especifica `UseDevelopmentStorage=true`. Quando publicar o projeto no Azure, o Visual Studio irá atualizar automaticamente a cadeia de ligação de diagnóstico com a conta de armazenamento que especificou no Assistente de publicação. No entanto, se uma conta de armazenamento real foi especificada como a cadeia de ligação de diagnóstico, em seguida, essa conta é utilizada.

## <a name="diagnostics-functionality-differences-between-azure-sdk-24-and-earlier-and-azure-sdk-25-and-later"></a>Diferenças de funcionalidade diagnósticos entre o Azure SDK 2.4 e anterior e Azure SDK 2,5 e posterior

Se estiver a atualizar o seu projeto a partir do Azure SDK 2.4 ao Azure SDK 2,5 ou posterior, devem ter em atenção as diferenças de funcionalidade diagnósticos seguintes.

- **APIs de configuração são preteridos** – programação configuração dos diagnósticos do está disponível no Azure SDK 2.4 ou versões anteriores, mas é preterida no Azure SDK 2,5 e versões posteriores. Se a sua configuração diagnósticos está definida no código, terá de reconfigurar esses definições de raiz do projeto na ordem dos diagnósticos do migradas para continuar a trabalhar. O ficheiro de configuração de diagnóstico para Azure SDK 2.4 é diagnostics.wadcfg e diagnostics.wadcfgx para Azure SDK 2,5 e versões posteriores.

- **Só podem ser configurados diagnósticos para aplicações de serviço de nuvem ao nível da função, não ao nível de instância.**

- **Sempre que implementar a aplicação, a configuração de diagnóstico é atualizada** – Isto pode causar problemas de paridade se alterar a sua configuração diagnósticos a partir do Explorador de servidor e, em seguida, voltar a implementar a aplicação.

- **No Azure SDK 2,5 e posterior, falha copia está configuradas no ficheiro de configuração de diagnóstico, não no código** – se tiver informações de estado de falha de sistema configuradas no código, terá de transferir manualmente a configuração de código para o ficheiro de configuração, porque as informações de estado da falha de sistema não são transferidas durante a migração para Azure SDK 2.6.

## <a name="enable-diagnostics-in-cloud-service-projects-before-deploying-them"></a>Activar diagnósticos em projetos de serviço de nuvem antes de implementá-los

No Visual Studio, pode escolher recolher dados de diagnóstico para funções que são executados no Azure, quando executa o serviço no emulador antes de implementá-lo. Todas as alterações às definições dos diagnósticos no Visual Studio são guardadas no ficheiro de configuração de diagnostics.wadcfgx. Estas definições de configuração especificam a conta de armazenamento onde são guardados os dados de diagnóstico quando implementar o seu serviço de nuvem.

### <a name="to-enable-diagnostics-in-visual-studio-before-deployment"></a>Para ativar diagnósticos no Visual Studio antes da implementação

1. No menu de atalho para a função que lhe interessa, selecione **Propriedades**e, em seguida, selecione o separador **configuração** na janela de **Propriedades** a função.

1. Na secção **Diagnósticos** , certifique-se de que a caixa de verificação **Ativar diagnósticos** está selecionada.

    ![Aceder a opção ativar diagnósticos](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC796660.png)

1. Selecione o botão de reticências (…) para especificar a conta de armazenamento onde pretende que os dados de diagnóstico para serem armazenados. A conta de armazenamento que escolher será a localização onde os dados de diagnóstico estão armazenados.

    ![Especificar a conta de armazenamento para utilizar](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC796661.png)

1. Na caixa de diálogo **Criar cadeia de ligação de armazenamento** , especifique se pretende ligar utilizando o Azure armazenamento emulador, uma subscrição do Azure, ou introduzidos manualmente as credenciais.

    ![Caixa de diálogo de conta de armazenamento](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC796662.png)

  - Se escolher o Microsoft opção Azure emulador de armazenamento, a cadeia de ligação está definida como UseDevelopmentStorage = true.

  - Se escolher a sua opção de subscrição, pode escolher a subscrição Azure que pretende utilizar e o nome da conta. Pode escolher o botão Gerir contas para gerir as suas subscrições Azure.

  - Se escolher a opção de credenciais introduzida manualmente, lhe for pedido para introduzir o nome e a chave da conta Azure que pretende utilizar.

1. Selecione o botão **Configurar** para ver a caixa de diálogo de **configuração de diagnósticos** . Cada separador (exceto **gerais** e **Registo de diretórios**) representa uma origem de dados de diagnóstico que pode recolher. No separador predefinido, em **Geral**, oferece-lhe as seguintes opções de recolha de dados de diagnóstico: **apenas os erros**, **todas as informações**e **plano personalizada**. A opção predefinida, **apenas os erros**, leva a quantidade de armazenamento, pelo menos, uma vez que não transferência de avisos ou mensagens de rastreio. A opção de informações de todas as transfere a maior parte das informações e é, por conseguinte, a opção mais dispendiosa em termos de armazenamento.

    ![Ativar o Azure diagnóstico e de configuração](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC758144.png)

1. Neste exemplo, selecione a opção de **plano personalizada** para que pode personalizar os dados recolhidos.

1. A caixa de **Quota de disco em MB** Especifica a quantidade de espaço que pretende atribuir na sua conta de armazenamento para os dados de diagnóstico. Pode alterar o valor predefinido, se pretender.

1. Em cada separador de dados de diagnóstico que pretende recolher, selecione o **Activar a transferência de <log type> ** caixa de verificação. Por exemplo, se quiser recolher registos de aplicação, selecione a caixa de verificação **Ativar a transferência de registos da aplicação** no separador de **Registos da aplicação** . Além disso, especificar quaisquer outras informações necessárias por cada tipo de dados de diagnóstico. Consulte a secção **configurar origens de dados de diagnóstico** mais adiante neste tópico para obter informações de configuração em cada separador.

1. Depois de ter ativado coleção de todos os dados de diagnóstico que pretende, selecione o botão **OK** .

1. Execute o projeto de serviço de nuvem Azure no Visual Studio como costuma fazer. Ao utilizar a aplicação, as informações de registo é activado são guardadas na conta de armazenamento Azure que especificou.

## <a name="enable-diagnostics-in-azure-virtual-machines"></a>Activar diagnósticos em máquinas virtuais do Azure

No Visual Studio, pode escolher recolher dados de diagnóstico para máquinas virtuais Azure.

### <a name="to-enable-diagnostics-in-azure-virtual-machines"></a>Para ativar diagnósticos em máquinas virtuais do Azure

1. No **Explorador do servidor**, escolha o nó Azure e, em seguida, ligue à sua subscrição do Azure, se ainda não estiver ligado.

1. Expanda o nó **máquinas virtuais** . Pode criar uma nova máquina virtual ou selecione uma que já lá.

1. No menu de atalho para a máquina virtual que lhe interessa, selecione **Configurar**. Isto mostra a caixa de diálogo de configuração de máquina virtual.

    ![Configurar uma máquina virtual Azure](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC796663.png)

1. Se ainda não estiver instalado, adicione a extensão de diagnósticos do agente de monitorização Microsoft. Esta extensão permite-lhe recolher dados de diagnóstico para a máquina virtual Azure. Na lista de extensões instalado, selecione selecionar um menu pendente de extensão disponíveis e, em seguida, selecione diagnósticos do agente de monitorização Microsoft.

    ![Instalar uma extensão de máquina virtual Azure](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC766024.png)

    >[AZURE.NOTE] Outras extensões de diagnóstico estão disponíveis para máquinas virtuais. Para mais informações, consulte o artigo Azure VM extensões e funcionalidades.

1. Selecione o botão **Adicionar** para adicionar a extensão e ver a respectiva caixa de diálogo de **configuração de diagnóstico** .

1. Selecione o botão **Configurar** para especificar uma conta de armazenamento e, em seguida, selecione o botão **OK** .

    Cada separador (exceto **gerais** e **Registo de diretórios**) representa uma origem de dados de diagnóstico que pode recolher.

    ![Ativar o Azure diagnóstico e de configuração](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC758144.png)

    No separador predefinido, em **Geral**, oferece-lhe as seguintes opções de recolha de dados de diagnóstico: **apenas os erros**, **todas as informações**e **plano personalizada**. A opção predefinida, **apenas os erros**, leva a quantidade de armazenamento, pelo menos, uma vez que não transferência de avisos ou mensagens de rastreio. A opção de **todas as informações** transfere a maior parte das informações e é, por conseguinte, a opção mais dispendiosa em termos de armazenamento.

1. Neste exemplo, selecione a opção de **plano personalizada** para que pode personalizar os dados recolhidos.

1. A caixa de **Quota de disco em MB** Especifica a quantidade de espaço que pretende atribuir na sua conta de armazenamento para os dados de diagnóstico. Pode alterar o valor predefinido, se pretender.

1. Em cada separador de dados de diagnóstico que pretende recolher, selecione o **Activar a transferência de <log type> ** caixa de verificação.

    Por exemplo, se quiser recolher registos de aplicação, selecione a caixa de verificação **Ativar a transferência de registos da aplicação** no separador de **Registos da aplicação** . Além disso, especificar quaisquer outras informações necessárias por cada tipo de dados de diagnóstico. Consulte a secção **configurar origens de dados de diagnóstico** mais adiante neste tópico para obter informações de configuração em cada separador.

1. Depois de ter ativado coleção de todos os dados de diagnóstico que pretende, selecione o botão **OK** .

1. Guarde o projeto atualizado.

    Verá uma mensagem na janela do **Registo de atividade do Microsoft Azure** que a máquina virtual foi atualizada.

## <a name="configure-diagnostics-data-sources"></a>Configurar a origens de dados de diagnóstico

Depois de ativar a recolha de dados de diagnóstico, pode escolher exatamente que origens de dados que pretende recolher e quais as informações recolhidas. Segue-se uma lista dos separadores na caixa de diálogo **configuração de diagnóstico** e significa que cada opção de configuração.

### <a name="application-logs"></a>Registos de aplicação

**Registos da aplicação** contêm informações de diagnóstico produzidas por uma aplicação web. Se quiser recolher registos da aplicação, selecione a caixa de verificação **Ativar a transferência de registos da aplicação** . Pode aumentar ou diminuir o número de minutos quando são transferidos os registos de aplicação à sua conta de armazenamento ao alterar o valor de **Transferir o período (mínimo)** . Também pode alterar a quantidade de informações capturadas no registo de ao definir o valor de nível de registo. Por exemplo, pode escolher **verboso** para obter mais informações ou selecione **crítica** para capturar apenas erros críticos. Se tiver um fornecedor de diagnóstico específico emite registos da aplicação, pode capturá-los ao Adicionar GUID o fornecedor para a caixa **GUID do fornecedor** .

  ![Registos de aplicação](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC758145.png)

  Consulte o artigo [Ativar diagnósticos do registo para web apps no Azure aplicação de serviço](./app-service-web/web-sites-enable-diagnostic-log.md) para obter mais informações sobre registos da aplicação.

### <a name="windows-event-logs"></a>Registos de eventos do Windows

Se quiser recolher registos dos eventos do Windows, selecione a caixa de verificação **Ativar a transferência de registos de eventos do Windows** . Pode aumentar ou diminuir o número de minutos quando são transferidos os registos de eventos à sua conta de armazenamento ao alterar o valor de **Transferir o período (mínimo)** . Selecione as caixas de verificação para os tipos de eventos que pretende controlar.

  ![Registos de eventos](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC796664.png)

Se estiver a utilizar o Azure SDK 2.6 ou posterior e pretende especificar uma origem de dados personalizados, introduza-o na **<Data source name>** texto caixa e, em seguida, selecione o botão **Adicionar** junto ao mesmo. A origem de dados é adicionada ao ficheiro diagnostics.cfcfg.

Se estiver a utilizar o Azure SDK 2,5 e pretende especificar uma origem de dados personalizado, pode adicionar-o para a `WindowsEventLog` secção da diagnostics.wadcfgx de ficheiros, tal como o exemplo seguinte.

```
<WindowsEventLog scheduledTransferPeriod="PT1M">
   <DataSource name="Application!*" />
   <DataSource name="CustomDataSource!*" />
</WindowsEventLog>
```
### <a name="performance-counters"></a>Contadores de desempenho

Informações sobre o desempenho contador pode ajudar a localizar congestionamentos de sistema e optimizar o desempenho do sistema e de aplicações. Para mais informações, consulte [criar e utilizar contadores do desempenho numa aplicação do Azure](https://msdn.microsoft.com/library/azure/hh411542.aspx) . Se pretender capturar contadores de desempenho, selecione a caixa de verificação **Ativar a transferência de desempenho** . Pode aumentar ou diminuir o número de minutos quando são transferidos os registos de eventos à sua conta de armazenamento ao alterar o valor de **Transferir o período (mínimo)** . Selecione as caixas de verificação para os contadores de desempenho que pretende controlar.

  ![Contadores de desempenho](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC758147.png)

Para controlar um contador de desempenho não estiver listado, introduza-a utilizando a sintaxe sugerida e, em seguida, selecione o botão **Add** . O sistema operativo na máquina virtual determina quais pode controlar os contadores de desempenho. Para obter mais informações acerca da sintaxe, consulte [especificar um caminho de contador](https://msdn.microsoft.com/library/windows/desktop/aa373193.aspx).

### <a name="infrastructure-logs"></a>Infraestrutura de registos

Se deseja capturar registos de infraestrutura, que contêm informações sobre a infraestrutura de diagnóstico Azure, o módulo de acesso remoto e o módulo RemoteForwarder, selecione a caixa de verificação **Ativar a transferência de infraestrutura de registos** . Pode aumentar ou diminuir o número de minutos quando os registos são transferidos para a sua conta de armazenamento ao alterar o valor de transferir o período (mínimo).

  ![Registos de infraestrutura de diagnóstico](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC758148.png)

  Para mais informações, consulte [Recolher dados de registo por utilizar diagnósticos do Azure](https://msdn.microsoft.com/library/azure/gg433048.aspx) .

### <a name="log-directories"></a>Diretórios de registo

Se que pretende capturar directórios de registo, que contêm os dados recolhidos de directórios de registo para pedidos de serviços de informação Internet (IIS), pedidos de falhou ou pastas que escolher, selecione a caixa de verificação **Ativar a transferência de directórios de registo** . Pode aumentar ou diminuir o número de minutos quando os registos são transferidos para a sua conta de armazenamento ao alterar o valor de **Transferir o período (mínimo)** .

Pode selecionar as caixas dos registos que pretende recolher, tais como **Registos do IIS** e **Falhou pedir** registos. Nomes de contentor de armazenamento predefinidos são fornecidos, mas pode alterar os nomes se pretender.

Além disso, pode capturar registos a partir de qualquer pasta. Basta especificar o caminho na secção **registo a partir do diretório absoluto** e, em seguida, selecione o botão de **Adicionar directório** . Os registos serão capturados para os contentores especificados.

  ![Diretórios de registo](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC796665.png)

### <a name="etw-logs"></a>ETW registos

Se utilizar o [Rastreio de evento para o Windows](https://msdn.microsoft.com/library/windows/desktop/bb968803(v=vs.85).aspx) (ETW) e pretende capturar registos ETW, selecione a caixa de verificação **Ativar a transferência de ETW registos** . Pode aumentar ou diminuir o número de minutos quando os registos são transferidos para a sua conta de armazenamento ao alterar o valor de **Transferir o período (mínimo)** .

Os eventos são capturados a partir de origens de eventos e manifestos evento que especificar. Para especificar uma origem de evento, introduza um nome na secção de **Origens de evento** e, em seguida, selecione o botão de **Adicionar origem do evento** . Da mesma forma, pode especificar um evento manifesto na secção **Manifestos evento** e, em seguida, selecione o botão **Adicionar manifesto do evento** .

  ![ETW registos](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC766025.png)

  A arquitetura ETW é suportada no ASP.NET através de classes no [System.Diagnostics.aspx] (https://msdn.microsoft.com/library/system.diagnostics (v=vs.110) espaço de nomes. O espaço de nomes do Microsoft.WindowsAzure.Diagnostics que herda e expande a norma [System.Diagnostics.aspx] (https://msdn.microsoft.com/library/system.diagnostics (v=vs.110) classes, permite a utilização de [System.Diagnostics.aspx] (https://msdn.microsoft.com/library/system.diagnostics (v=vs.110) como um quadro de registo no ambiente do Azure. Para mais informações, consulte o artigo [tomar o controlo de registo e rastreio no Microsoft Azure](https://msdn.microsoft.com/magazine/ff714589.aspx) e [Ativar diagnósticos no Azure serviços em nuvem e máquinas virtuais](./cloud-services/cloud-services-dotnet-diagnostics.md).

### <a name="crash-dumps"></a>Informações de estado da falha de sistema

Se pretender capturar informações sobre quando uma instância de função falha, selecione a caixa de verificação **Ativar a transferência de uma falha de sistema regista** . (Porque o ASP.NET processa as exceções a maior parte dos, isto é geralmente útil apenas para funções de trabalho.) Pode aumentar ou diminuir a percentagem de espaço de armazenamento Office dedicada a informações de estado as Falha ao alterar o valor de **Quota de diretório (%)** . Pode alterar o contentor de armazenamento onde as informações de estado da falha são armazenadas e pode selecionar se pretende capturar uma cópia **completa** ou **Minibarra** .

Os processos atualmente a ser registados estão listados. Selecione as caixas de verificação para os processos que pretende capturar. Para adicionar outro processo à lista, introduza o nome do processo e, em seguida, selecione o botão de **Adicionar o processo** .

  ![Informações de estado da falha de sistema](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC766026.png)

  Consulte o artigo [rastreio no Microsoft Azure e tomar o controlo de registo](https://msdn.microsoft.com/magazine/ff714589.aspx) e [Microsoft Azure diagnósticos parte 4: componentes de registo personalizados e Azure diagnósticos 1.3 alterações](http://justazure.com/microsoft-azure-diagnostics-part-4-custom-logging-components-azure-diagnostics-1-3-changes/) para obter mais informações.

## <a name="view-the-diagnostics-data"></a>Ver os dados de diagnóstico

Depois de ter recolhidos os dados de diagnóstico para um serviço na nuvem ou uma máquina virtual, pode vê-lo.

### <a name="to-view-cloud-service-diagnostics-data"></a>Para ver dados de diagnósticos do serviço na nuvem

1. Implementar o seu serviço de nuvem, como habitualmente e, em seguida, executá-la.

1. Pode ver os dados de diagnóstico num relatório de que o Visual Studio gera ou tabelas na sua conta de armazenamento. Para ver os dados num relatório, abra o **Explorador de nuvem** ou **Explorador de servidor**, abrir o menu de atalho do nó para a função que lhe interessa e, em seguida, selecione **Ver dados de diagnóstico**.

    ![Ver dados de diagnóstico](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC748912.png)

    É apresentado um relatório que apresenta os dados disponíveis.

    ![Relatório de diagnósticos do Microsoft Azure no Visual Studio](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC796666.png)

    Se os dados mais recentes não aparecerem, poderá ter de esperar para o período de transferência a decorrer.

    Selecione a ligação **Atualizar** para atualizar imediatamente os dados ou escolher um intervalo na caixa de listagem pendente **Atualização automática** para os dados atualizados automaticamente. Para exportar os dados de erros, selecione o botão **Exportar para CSV** para criar um ficheiro de valores separados por ponto e vírgula, que pode abrir numa folha de cálculo.

    No **Explorador de nuvem** ou **Server Explorer**, abra a conta de armazenamento que está associada a implementação.

1. Abra as tabelas de diagnóstico no Visualizador da tabela e, em seguida, reveja os dados que lhe recolhidas. Para obter registos do IIS e registos personalizados, pode abrir um contentor blob. Ao rever a tabela seguinte, pode encontrar o contentor de tabela ou blob que contém os dados que lhe interessa. Para além dos dados para esse ficheiro de registo, as entradas da tabela contêm EventTickCount, DeploymentId, função e RoleInstance para ajudar a identificar que máquina virtual função gerados e os dados e quando. 

  	|Dados de diagnóstico|Descrição|Localização|
  	|---|---|---|
  	|Registos de aplicação|Registos que o seu código gera ao contactar o suporte métodos a classe de System.Diagnostics.Trace de.|WADLogsTable|
  	|Registos de eventos|Estes dados são a partir dos registos de eventos do Windows nas máquinas virtuais. O Windows armazena informações nestes registos, mas aplicações e serviços também utilizá-los para relatar erros ou registar informações.|WADWindowsEventLogsTable|
  	|Contadores de desempenho|Pode recolher dados qualquer contador de desempenho que está disponível na máquina virtual. O sistema operativo fornece contadores de desempenho, que incluem muitos estatísticas, tal como a hora de utilização e processador de memória.|WADPerformanceCountersTable|
  	|Infraestrutura de registos|Estes registos são gerados a partir de infraestrutura de diagnóstico própria.|WADDiagnosticInfrastructureLogsTable|
  	|Registos do IIS|Estes registos registam pedidos web. Se o seu serviço de nuvem obtém uma quantidade de tráfego significativa, estes registos podem ser muito demorados, pelo que deverá recolher e armazenar estes dados apenas quando precisar dele.|Pode encontrar os registos de pedido de falha no contentor de BLOBs em wad iis failedreqlogs num caminho para essa implementação, função e instância. Pode encontrar registos concluídos em wad-iis-ficheiros de registo. Entradas para cada ficheiro são efetuadas na tabela WADDirectories.|
  	|Informações de estado da falha de sistema|Esta informação fornece imagens binárias do processo do seu serviço de nuvem (normalmente uma função de trabalho).|contentor de BLOBs WAD-compressão-informações de estado|
  	|Ficheiros de registo personalizados|Registos de dados predefinidos.|Pode especificar no código a localização dos ficheiros de registo personalizados na sua conta de armazenamento. Por exemplo, pode especificar um contentor BLOBs personalizado.|

1. Se os dados de qualquer tipo são truncados, pode tentar aumentar a memória intermédia para esses dados tipo ou encurtar o intervalo entre as transferências de dados a partir de máquina virtual à sua conta de armazenamento.

1. (opcional) Limpar dados a partir da conta de armazenamento, ocasionalmente, para reduzir os custos gerais de armazenamento.

1. Quando fizer uma implementação completa, o ficheiro de diagnostics.cscfg (.wadcfgx para Azure SDK 2,5) é atualizado no Azure e levantar o seu serviço de nuvem auscultador quaisquer alterações à configuração de diagnóstico. Se, em vez disso, atualizar uma implementação existente, o ficheiro .cscfg não é atualizado no Azure. Ainda pode alterar as definições de diagnósticos, no entanto, ao seguir os passos na secção seguinte. Para mais informações sobre como efetuar uma implementação completa e atualizar uma implementação existente, consulte o artigo [Aplicação Assistente de publicação do Azure](vs-azure-tools-publish-azure-application-wizard.md).

### <a name="to-view-virtual-machine-diagnostics-data"></a>Para ver os dados de diagnóstico máquina virtual

1. No menu de atalho para a máquina virtual, selecione **Ver dados de diagnóstico**.

    ![Ver dados de diagnóstico na máquina virtual Azure](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC766027.png)

    Esta ação abre a janela de **Resumo de diagnóstico** .

    ![Diagnósticos do Azure máquina virtual resumo](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC796667.png)  

    Se os dados mais recentes não aparecerem, poderá ter de esperar para o período de transferência a decorrer.

    Selecione a ligação **Atualizar** para atualizar imediatamente os dados ou escolher um intervalo na caixa de listagem pendente **Atualização automática** para os dados atualizados automaticamente. Para exportar os dados de erros, selecione o botão **Exportar para CSV** para criar um ficheiro de valores separados por ponto e vírgula, que pode abrir numa folha de cálculo.

## <a name="configure-cloud-service-diagnostics-after-deployment"></a>Configurar diagnósticos do serviço de nuvem após a implementação

Se estiver a investigar um problema com uma nuvem serviço que já está em execução, poderá pretender recolher dados especificadas não antes de que a função implementado originalmente. Neste caso, pode começar a recolher dados utilizando as definições no Explorador do servidor. Pode configurar diagnósticos para uma única ocorrência ou todas as instâncias de uma função, dependendo se abrir a caixa de diálogo de configuração de diagnósticos no menu de atalho para a instância ou a função. Se configurar o nó funções, aplicam as alterações a todas as instâncias. Se configurar o nó instância, quaisquer alterações aplicam-se apenas essa ocorrência.

### <a name="to-configure-diagnostics-for-a-running-cloud-service"></a>Para configurar diagnósticos para um serviço de nuvem em execução

1. No Explorador do servidor, expanda o nó **Serviços em nuvem** e, em seguida, expanda nós para localizar a função ou instância que pretende investigar ou ambos.

    ![Configurar diagnósticos](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC748913.png)

1. No menu de atalho para um nó instância ou um nó de função, selecione **Definições de diagnósticos de atualização**e, em seguida, escolha as definições de diagnóstico ao qual pretende recolher.

    Para obter informações sobre as definições de configuração, consulte o artigo **origens de dados de diagnóstico configurar** neste tópico. Para obter informações sobre como ver os dados de diagnóstico, consulte o artigo **ver os dados de diagnóstico** neste tópico.

    Se alterar a recolha de dados no **Explorador do servidor**, estas alterações permanecem ativas até implementar o serviço de nuvem totalmente novamente. Se utilizar a predefinição de definições de publicação, as alterações não são substituídas, uma vez que publicar a predefinição definição está a atualizar a implementação existente, em vez de o fazer uma nova implementação completa. Para certificar-se de que as definições de limpar no momento da implementação, aceda ao separador **Definições avançadas** no Assistente de publicação e desmarque a caixa de verificação **atualizar a implementação** . Quando implementar com essa caixa de verificação desmarcada, as definições de revertem para aqueles no ficheiro .wadcfgx (ou .wadcfg) como conjunto através do editor de propriedades para a função. Se atualizar a sua implementação, Azure mantém as definições antigas.

## <a name="troubleshoot-azure-cloud-service-issues"></a>Resolução de problemas de serviço de nuvem Azure

Se ocorrerem problemas com os seus projetos de serviço de nuvem, tal como uma função que fica bloqueada num estado "ocupado", repetidamente Reciclagens da, ou emitir um erro de servidor interno, existem ferramentas e técnicas que pode utilizar para diagnosticar e corrigir estes problemas. Para exemplos específicos dos problemas comuns e soluções, bem como uma descrição geral dos conceitos e ferramentas utilizadas para diagnosticar e corrigir estes erros, consulte o artigo [Azure PaaS calcular diagnósticos de dados](http://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.aspx).

## <a name="q--a"></a>As perguntas e respostas

**O que é o tamanho do intervalo de tempo e como grandes devem estar?**

Em cada instância de máquina virtual, as quotas de limitam a quantidade de dados diagnóstico pode ser armazenado no sistema de ficheiros local. Além disso, pode especificar um tamanho de memória intermédia para cada tipo de dados de diagnóstico que está disponíveis. Este tamanho da memória intermédia funciona como se fosse uma quota individual para esse tipo de dados. Ao selecionar a parte inferior da caixa de diálogo, pode determinar a quota de geral e na quantidade de memória permanece. Se especificar intervalos de tempo maiores ou mais tipos de dados, irá abordar a quota de geral. Pode alterar a quota de geral, alterando o ficheiro de configuração de diagnostics.wadcfg/.wadcfgx. Os dados de diagnóstico estão armazenados no sistema de ficheiros mesmo como dados da sua aplicação, por isso, se a sua aplicação utiliza muitas espaço em disco, não devem aumentar a quota de diagnóstico geral.

**O que é o período de transferência e quanto tempo devem estar?**

O período de transferência é a quantidade de tempo para capturar a decorrer entre dados. Após cada período de transferência, dados são movidos a partir do sistema de ficheiros local numa máquina virtual para tabelas na sua conta de armazenamento. Se a quantidade de dados que são recolhidas exceder a quota de antes do fim de um período de transferência, os dados mais antigos são eliminados. Poderá pretender diminuir o período de transferência, se está a perder dados porque os seus dados excedem o tamanho do intervalo de tempo ou a quota de geral.

**O que fuso horário são os carimbos de data / hora no?**

O fuso horário local do Centro de dados que aloja o seu serviço de nuvem do está os carimbos de data / hora. As seguintes três colunas de data/hora nas tabelas registo são utilizadas.

  - **PreciseTimeStamp** é o carimbo de hora ETW do evento. Isto é, a hora o evento tem a sessão iniciado a partir do cliente.

  - **Data/hora** é PreciseTimeStamp arredondado por defeito para o limite da frequência de carregamento. Por isso, se a sua frequência de carregamento for 5 minutos e o evento tempo 00:17:12, carimbo será 00:15:00.

  - **Data/hora** é o carimbo de data/hora em que a entidade foi criada numa tabela do Azure.

**Como gerir custos quando recolher informações de diagnóstico?**

As predefinições (**nível de registo** definido para o **erro** e **Transferir o período** definido para **1 minuto**) foram concebidas para minimizar o custo. Os custos do seu cluster aumentará se recolher dados de diagnóstico mais ou diminuir o período de transferência. Não recolha dados de mais do que precisar e não se esqueça de desativar a recolha de dados quando já não necessitar dele. Pode sempre voltar a ativá-lo, mesmo o tempo de execução, conforme mostrado na secção anterior.

**Como posso recolher registos de pedido de falha do IIS?**

Por predefinição, o IIS não recolher registos de pedido de falha. Pode configurar o IIS para recolhê-las se editar o ficheiro da Web. config para o seu perfil web.

**Não estou a obter informações de rastreio a partir de métodos de RoleEntryPoint como OnStart. O que é o problema?**

Os métodos de RoleEntryPoint chamam-se no contexto de WAIISHost.exe, não IIS. Por conseguinte, as informações de configuração na Web. config que normalmente permite rastreio não se aplica. Para resolver este problema, adicionar um ficheiro. config ao projeto funções web e o nome do ficheiro para corresponder à assemblagem de saída que contém o código de RoleEntryPoint. No project de função de web predefinido, o nome do ficheiro. config seria WAIISHost.exe.config. Em seguida, adicione as seguintes linhas para este ficheiro:

```
<system.diagnostics>
  <trace>
      <listeners>
          <add name “AzureDiagnostics” type=”Microsoft.WindowsAzure.Diagnostics.DiagnosticMonitorTraceListener”>
              <filter type=”” />
          </add>
      </listeners>
  </trace>
</system.diagnostics>
```

Agora, na janela de **Propriedades** , defina a propriedade **Copiar para directório de saída** **sempre**cópia.

## <a name="next-steps"></a>Próximos passos

Para saber mais sobre diagnósticos do registo no Azure, consulte o artigo [Ativar diagnósticos no Azure serviços em nuvem e máquinas virtuais](./cloud-services/cloud-services-dotnet-diagnostics.md) e [Ativar diagnósticos do registo para web apps no Azure aplicação de serviço](./app-service-web/web-sites-enable-diagnostic-log.md).
