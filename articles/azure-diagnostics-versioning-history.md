<properties
    pageTitle="Histórico de versões diagnósticos Azure"
    description="Explicação das alterações em versões diferentes do Azure diagnósticos como enviada com diferentes versões do Microsoft Azure SDK."
    services="multiple"
    documentationCenter=".net"
    authors="rboucher"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="multiple"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="02/12/2016"
    ms.author="robb"/>


# <a name="microsoft-azure-diagnostics-version-history"></a>Histórico de versões de diagnósticos do Microsoft Azure

Nunca diagnósticos do Azure? Consulte o artigo [Descrição geral do Azure diagnóstico](azure-diagnostics.md).

Cada versão do Azure SDK normalmente é fornecido com uma nova versão do Azure diagnósticos. A tabela abaixo descreve as versões SDK do Azure e diagnósticos de Azure associadas com a versão SDK.



Versão do Azure SDK | Versão de diagnóstico Azure | Modelo
--- | --- | ---
1. x      | 1.0 | Plug-in
2.0 para 2.4| 1.0 | "
2,5      | 1.2 | extensão
2.6      | 1.3 | "
2.7      | 1.4 | "
2,8      | 1,5 | "


A versão mais recente está 1,5, que é fornecido com o Azure SDK 2,8. A versão da extensão do Azure diagnóstico que vem incluído com o SDK só é utilizada para cenários emulador local. Qualquer implementada aplicação utiliza automaticamente a versão mais recente quando a ser executado no Azure, independentemente que versão do SDK a aplicação é criado com. No entanto, a menos que instalar o SDK do Azure mais recente, poderá não ter todas as ferramentas que ajudá-lo utilizam as novas funcionalidades.

Várias funcionalidades e alterações descritas abaixo.

## <a name="azure-sdk-28"></a>Azure SDK 2,8
Azure SDK 2,8 adicionada a capacidade para enviar dados diagnóstico para [Informações de aplicação](./application-insights/app-insights-cloudservices.md) , tornando mais fácil diagnosticar problemas através da aplicação, bem como o nível de sistema e infraestrutura.

## <a name="azure-26-diagnostics-changes"></a>Alterações do Azure 2.6 diagnóstico

Para os projectos de serviço de nuvem do Azure SDK 2.6 no Visual Studio, as seguintes alterações foram efetuadas. (Estas alterações também se aplica a versões posteriores do Azure SDK.)

- Agora, o local emulador suporta diagnósticos. Isto significa que pode recolher dados de diagnóstico e certifique-se de que a aplicação está a criar os seus rastreios direita enquanto está a desenvolver e testar no Visual Studio. A cadeia de ligação `UseDevelopmentStorage=true` permite a recolha de dados de diagnóstico enquanto estiver a executar o seu projeto do serviço de nuvem no Visual Studio utilizando o emulador armazenamento Azure. Todos os dados de diagnóstico são recolhidas na conta de armazenamento (armazenamento de desenvolvimento).

- A cadeia de ligação de conta de armazenamento de diagnóstico (Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString) é armazenada mais uma vez no ficheiro de configuração (.cscfg) de serviço. No Azure SDK 2,5 a conta de armazenamento de diagnóstico foi especificada no ficheiro diagnostics.wadcfgx.

Existem algumas diferenças entre como a cadeia de ligação trabalhou no Azure SDK 2.4 e anterior e como funciona no Azure SDK 2.6 e versões posteriores.

- No Azure SDK 2.4 e anteriores, a cadeia de ligação foi utilizada como um tempo de execução do plug-in do diagnóstico para obter as informações de conta de armazenamento para transferir os registos de diagnóstico.

- No Azure SDK 2.6 e posterior, a cadeia de ligação de diagnóstico é utilizada pelo Visual Studio para configurar a extensão de diagnóstico com as informações de conta de armazenamento adequado durante a publicação. A cadeia de ligação permite-lhe definir as contas de armazenamento diferente para configurações de serviços diferente que Visual Studio irá utilizar quando de publicação. No entanto, uma vez que o plug-in de diagnóstico já não se encontra disponível (depois de Azure SDK 2,5), o ficheiro .cscfg por si só é possível ativar a extensão de diagnóstico. Tem de ativar a extensão separadamente através de ferramentas, como o Visual Studio ou PowerShell.

- Para simplificar o processo de configurar a extensão de diagnóstico com o PowerShell, a saída do pacote a partir do Visual Studio também contém a configuração do pública XML para a extensão de diagnóstico para cada função. Visual Studio utiliza a cadeia de ligação de diagnóstico para preencher as informações de conta de armazenamento presentes na configuração do pública. Os ficheiros de config público são criados na pasta extensões e siga o padrão de PaaSDiagnostics. <RoleName>. PubConfig.xml. Qualquer implementações do PowerShell com base podem utilizar este padrão para mapear cada configuração para uma função.

- A cadeia de ligação no ficheiro .cscfg também é utilizada pelo Azure portal para aceder aos dados de diagnóstico para que possam aparecer em separador de **monitorização** . A cadeia de ligação é necessário para configurar o serviço para mostrar dados de monitorização verbosas no portal.

### <a name="migrating-projects-to-azure-sdk-26-and-later"></a>Migrar projectos para Azure SDK 2.6 e versões posteriores

Quando migrar a partir do Azure SDK 2,5 ao Azure SDK 2.6 ou posterior, se tiver uma conta de armazenamento de diagnóstico especificada no ficheiro .wadcfgx, em seguida,-lo irá permanecer aí. Para tirar partido da flexibilidade da utilizando as contas de armazenamento diferente para configurações de armazenamento diferente, terá de adicionar manualmente a cadeia de ligação ao seu projeto. Se está a migrar um projeto a partir do Azure SDK 2.4 ou anterior para o Azure SDK 2.6, as cadeias de ligação diagnóstico são preservadas. No entanto, tenha em atenção as alterações no como cadeias de ligação são tratadas no Azure SDK 2.6 conforme especificado na secção anterior.

### <a name="how-visual-studio-determines-the-diagnostics-storage-account"></a>Como o Visual Studio determina a conta de armazenamento de diagnóstico

- Se uma cadeia de ligação de diagnóstico for especificada no ficheiro .cscfg, o Visual Studio utiliza-o para configurar a extensão de diagnóstico ao publicar e, quando gerar os ficheiros de xml configuração público durante embalagem.

- Se não for especificada nenhum cadeia de ligação de diagnóstico no ficheiro .cscfg, em seguida, Visual Studio reverte para utilizando a conta de armazenamento especificada no ficheiro de .wadcfgx para configurar a extensão de diagnóstico quando publicar e gerar os ficheiros de xml configuração público quando embalagem.

- A cadeia de ligação de diagnóstico no ficheiro .cscfg tem precedência sobre a conta de armazenamento no ficheiro .wadcfgx. Se uma cadeia de ligação de diagnóstico for especificada no ficheiro .cscfg, Visual Studio utiliza que e ignora a conta de armazenamento no .wadcfgx.

### <a name="what-does-the-update-development-storage-connection-strings-checkbox-do"></a>O que faz as "atualização desenvolvimento armazenamento cadeias de ligação..." caixa de verificação fazer?

A caixa de verificação **Atualizar cadeias de ligação do armazenamento de desenvolvimento de diagnóstico e de colocação em cache com credenciais da conta de armazenamento do Microsoft Azure durante a publicação do Microsoft Azure** fornece-lhe um meio cómodo como atualizar quaisquer cadeias de ligação de conta de armazenamento de desenvolvimento com a conta de armazenamento Azure especificada durante a publicação.

Por exemplo, imaginemos Selecione esta caixa de verificação e a cadeia de ligação diagnóstico especifica `UseDevelopmentStorage=true`. Quando publicar o projeto no Azure, o Visual Studio irá atualizar automaticamente a cadeia de ligação de diagnóstico com a conta de armazenamento que especificou no Assistente de publicação. No entanto, se uma conta de armazenamento real foi especificada como a cadeia de ligação de diagnóstico, em seguida, essa conta é utilizada.

## <a name="diagnostics-functionality-differences-between-azure-sdk-24-and-earlier-and-azure-sdk-25-and-later"></a>Diferenças de funcionalidade diagnóstico entre o Azure SDK 2.4 e anterior e Azure SDK 2,5 e posterior

Se estiver a atualizar o seu projeto a partir do Azure SDK 2.4 ao Azure SDK 2,5 ou posterior, devem ter em atenção as diferenças de funcionalidade diagnósticos seguintes.

- **APIs de configuração são preteridos** – programação configuração dos diagnósticos do está disponível no Azure SDK 2.4 ou versões anteriores, mas é preterida no Azure SDK 2,5 e versões posteriores. Se a sua configuração diagnósticos está definida no código, terá de reconfigurar esses definições de raiz do projeto na ordem dos diagnósticos do migradas para continuar a trabalhar. O ficheiro de configuração de diagnóstico para Azure SDK 2.4 é diagnostics.wadcfg e diagnostics.wadcfgx para Azure SDK 2,5 e versões posteriores.

- **Só podem ser configurados diagnósticos para aplicações de serviço de nuvem ao nível da função, não ao nível de instância.**

- **Sempre que implementar a aplicação, a configuração de diagnóstico é atualizada** – Isto pode causar problemas de paridade se alterar a sua configuração diagnósticos a partir do Explorador de servidor e, em seguida, voltar a implementar a aplicação.

- **No Azure SDK 2,5 e posterior, falha copia está configuradas no ficheiro de configuração de diagnóstico, não no código** – se tiver informações de estado de falha de sistema configuradas no código, terá de transferir manualmente a configuração de código para o ficheiro de configuração, porque as informações de estado da falha de sistema não são transferidas durante a migração para Azure SDK 2.6.
