<properties
   pageTitle="Começar a trabalhar com a integração do registo Azure | Microsoft Azure"
   description="Saiba como instalar o serviço de integração de registo Azure e integrar registos do armazenamento Azure, registos de auditoria Azure e alertas do Centro de segurança do Azure."
   services="security"
   documentationCenter="na"
   authors="TomShinder"
   manager="MBaldwin"
   editor="TerryLanfear"/>

<tags
   ms.service="security"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/24/2016"
   ms.author="TomSh"/>

# <a name="get-started-with-azure-log-integration-preview"></a>Começar a trabalhar com a integração do registo Azure (pré-visualização)

Integração do registo Azure permite-lhe integrar registos não processados por parte dos seus recursos Azure os sistemas de gestão de evento (SIEM) e informações de segurança no local. Esta integração fornece um dashboard unificado para todos os seus ativos, no local ou na nuvem, para que possa agregar, correlacionar, analisar e alertar para eventos de segurança associados com as suas aplicações.

Neste tutorial explica como instalar a integração do registo Azure e integrar registos a partir do armazenamento Azure, registos de auditoria Azure e alertas do Centro de segurança do Azure. Tempo estimado para concluir este tutorial é uma hora.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, tem de ter o seguinte procedimento:

- Uma máquina (no local ou na nuvem) para instalar o serviço de integração de registo Azure. Nesta máquina tem de executar um sistema operativo Windows de 64 bits com o .net 4.5.1 instalado. Nesta máquina chama-se o **Azlog integrador**.
- Subscrição do Azure. Se não tiver uma, pode inscrever-se para uma [conta gratuita](https://azure.microsoft.com/free/).
- Azure diagnóstico ativado para a sua máquinas virtuais Azure (VMs). Para activar diagnósticos para serviços em nuvem, consulte o artigo [Ativar o Azure diagnósticos no Azure serviços em nuvem](../cloud-services/cloud-services-dotnet-diagnostics.md). Para ativar diagnósticos para uma VM Azure a executar o Windows, consulte o artigo [Utilizar o PowerShell para activar diagnósticos do Azure na máquina Virtual a executar o Windows](../virtual-machines/virtual-machines-windows-ps-extensions-diagnostics.md).
- Conectividade a partir do integrador Azlog ao armazenamento Azure e para autenticar e autorizar à subscrição Azure.
- Nos registos de Azure VM, tem de estar instalado no integrador Azlog o agente SIEM (por exemplo, Splunk Universal reencaminhador, agente Coleccionador de eventos do Windows do HP ArcSight ou IBM QRadar WinCollect).

## <a name="deployment-considerations"></a>Considerações de implementação

Pode executar várias instâncias do integrador Azlog se o evento de volume é audível. Balanceamento de carga de contas de armazenamento do Azure diagnósticos para Windows *(WAD)* e o número de subscrições para fornecer às instâncias deve ser baseado no seu capacidade.

Num computador processador de 8 (principal), uma única ocorrência de Azlog integrador pode processar cerca de 24 milhões de eventos por dia (~1M/hour).

Num computador processador de 4 (principal), uma única ocorrência de Azlog integrador pode processar cerca de 1,5 milhões de eventos por dia (~62.5K/hour).

## <a name="install-azure-log-integration"></a>Instalar a integração do registo Azure

Transfira a [integração de iniciar sessão Azure](https://www.microsoft.com/download/details.aspx?id=53324).

O serviço de integração de registo Azure recolhe dos dados de telemetria no computador onde está instalado.  Dados de telemetria recolhidos são:

- Exceções que ocorrer durante a execução de integração de registo Azure
- Métricas sobre o número de consultas e eventos processados
- Estatísticas sobre qual Azlog.exe estão a ser utilizadas opções de linha de comandos

> [AZURE.NOTE] Pode desativar a recolha de dados de telemetria desmarcando esta opção.

## <a name="integrate-azure-vm-logs-from-your-azure-diagnostics-storage-accounts"></a>Integrar registos Azure VM de contas armazenamento diagnósticos do Azure

1. Verifique os pré-requisitos listados acima para garantir a sua conta de armazenamento WAD está a recolher registos antes de continuar a integração do Azure registo. Não siga os passos seguintes se a sua conta de armazenamento WAD não está a recolher registos.

2. Abra a linha de comandos e o **cd** para **c:\Program Files\Microsoft Azure registo integração**.

3. Execute o comando

        azlog source add <FriendlyNameForTheSource> WAD <StorageAccountName> <StorageKey>

      Substitua o nome da conta de armazenamento Azure configurado para receber eventos de diagnósticos a partir do seu VM StorageAccountName.

        azlog source add azlogtest WAD azlog9414 fxxxFxxxxxxxxywoEJK2xxxxxxxxxixxxJ+xVJx6m/X5SQDYc4Wpjpli9S9Mm+vXS2RVYtp1mes0t9H5cuqXEw==

      Se gostaria de id da subscrição para mostrar XML de evento, acrescente o ID da subscrição para o nome amigável:

        azlog source add <FriendlyNameForTheSource>.<SubscriptionID> WAD <StorageAccountName> <StorageKey>

4. Aguarde 30 e 60 minutos (poderá demorar tempo, desde que uma hora) e, em seguida, visualizar os eventos que são extraídos da conta de armazenamento. Para ver, abra **Visualizador de eventos > registos do Windows > reencaminhadas eventos** no integrador Azlog.

5. Certifique-se de que a conexão SIEM padrão instalada no computador está configurada para escolha eventos a partir da pasta **Reencaminhadas eventos** e encaminhá-los à sua instância SIEM. Rever a configuração específica SIEM para configurar e ver os registos de integração de.

## <a name="what-if-data-is-not-showing-up-in-the-forwarded-events-folder"></a>O que acontece se dados não estiver visível na pasta reencaminhadas eventos?

Se após uma hora dados são não na pasta **Eventos reencaminhado** , em seguida:

1. Verifique o computador e confirme que pode aceder a Azure. Para testar a conectividade, tente abrir o [Azure portal](http://portal.azure.com) a partir do browser.

2. Certifique-se que a conta de utilizador **azlog** tem permissão de escrita na pasta **users\azlog**.

3. Certifique-se a conta de armazenamento adicionada o comando **Adicionar origem azlog** é apresentada quando executa o comando **azlog lista de origem**.
4. Aceda a **Visualizador de eventos > registos do Windows > aplicação** para ver se há algum erro referidos a partir da integração do Azure registo.

Se ainda não vir os eventos, em seguida:

1. Transferir [o Explorador de armazenamento do Microsoft Azure](http://storageexplorer.com/).

2. Ligar para a conta de armazenamento adicionada o comando **Adicionar azlog origem**.

3. No Explorador de armazenamento do Microsoft Azure, navegue para a tabela **WADWindowsEventLogsTable** para ver se existe quaisquer dados. Caso contrário, em seguida, diagnósticos na VM não está configurado corretamente.

## <a name="integrate-azure-audit-logs-and-security-center-alerts"></a>Integrar e alertas de segurança Centro de registos de auditoria Azure

1. Abra a linha de comandos e o **cd** para **c:\Program Files\Microsoft Azure registo integração**.

2. Execute o comando

        azlog createazureid

      Este comando pede-lhe o início de sessão Azure. O comando, em seguida, cria um [Principal de serviço do Azure Active Directory](../active-directory/active-directory-application-objects.md) no Azure AD inquilinos que alojam as subscrições Azure na qual o utilizador com sessão iniciada é um administrador, um administrador de cocriação ou um proprietário. O comando falhará se o utilizador com sessão iniciada é um utilizador de convidado no Azure AD inquilino. Autenticação do Azure é feita através do Azure Active Directory (AD).  A criação de um capital de serviço de integração de Azlog cria a identidade do Azure AD que será concedida acesso de leitura a partir do Azure subscrições.

3. Execute o comando

        azlog authorize <SubscriptionID>

      Este procedimento atribui acesso de leitor da subscrição para o serviço principal criado no passo 2. Se não especificar um SubscriptionID, em seguida, tenta atribuir a função de leitor principal de serviço para todas as subscrições para o qual tenha acesso.

        azlog authorize 0ee9d577-9bc4-4a32-a4e8-c29981025328

      > [AZURE.NOTE] Poderá ver avisos se executar o comando **autorizar** imediatamente após o comando **createazureid** . Existe algum latência entre quando a conta do Azure AD é criada e quando a conta está disponível para utilização. Se Aguarde cerca de 10 segundos depois de executar o comando **createazureid** para executar o comando **autorizar** , deverá ver não estes avisos.

4. Verificar as seguintes pastas para confirmar se os ficheiros de JSON de registo de auditoria estão lá:

  - **c:\Users\azlog\AzureResourceManagerJson**
  - **c:\Users\azlog\AzureResourceManagerJsonLD**

5. Verificar as seguintes pastas para confirmar que existem de alertas do Centro de segurança no-las:

  - **c:\Users\azlog\ AzureSecurityCenterJson**
  - **c:\Users\azlog\AzureSecurityCenterJsonLD**

6. Aponte o conector de reencaminhador de ficheiro SIEM padrão para a pasta adequada encaminhamento os dados para a instância SIEM. Poderá ter alguns mapeamentos de campo baseados no produto SIEM que estiver a utilizar.

Se tiver questões sobre a integração de registo do Azure, envie um e-mail para [AzSIEMteam@microsoft.com](mailto:AzSIEMteam@microsoft.com)

## <a name="next-steps"></a>Próximos passos

Neste tutorial, aprendeu como instalar a integração do registo Azure e integrar registos a partir do armazenamento Azure. Para saber mais, consulte o seguinte:

- [Integração de registo do Microsoft Azure para registos Azure (pré-visualização)](https://www.microsoft.com/download/details.aspx?id=53324) – Centro de transferências para obter detalhes, requisitos de sistema e instruções de instalação sobre a integração do Azure registo.
- [Introdução à integração de registo Azure](security-azure-log-integration-overview.md) – este documento apresenta-lhe a integração do registo Azure, das suas capacidades chaves e como funciona.
- [Passos de configuração de parceiro](https://blogs.msdn.microsoft.com/azuresecurity/2016/08/23/azure-log-siem-configuration-steps/) – esta mensagem de blogue mostra-lhe como configurar integração de registo Azure para trabalhar com soluções de parceiros Splunk, HP ArcSight e IBM QRadar.
- [Registo azure integração perguntas mais frequentes (FAQ)](security-azure-log-integration-faq.md) - perguntas mais frequentes sobre esta responde a questões sobre a integração do Azure registo.
- [Alertas do Centro de segurança de integração com o Azure sessão integração](../security-center/security-center-integrating-alerts-with-log-integration.md) – este documento mostra-lhe como sincronizar os alertas do Centro de segurança, juntamente com os eventos de segurança de máquina virtual recolhidos pelo diagnósticos do Azure e registos de auditoria Azure, com o seu registo analytics ou solução SIEM.
- [Funcionalidades novas para diagnósticos do Azure e registos de auditoria Azure](https://azure.microsoft.com/blog/new-features-for-azure-diagnostics-and-azure-audit-logs/) – esta mensagem de blogue apresenta para registos de auditoria Azure e outras funcionalidades que o ajudam a obter informações para as operações dos seus recursos Azure.
