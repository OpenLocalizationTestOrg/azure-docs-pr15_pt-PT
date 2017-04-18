<properties
   pageTitle="Processamento de erros de automatização Azure | Microsoft Azure"
   description="Este artigo fornece erro básico processamento passos para resolver problemas e corrija erros comuns de automatização do Azure."
   services="automation"
   documentationCenter=""
   authors="mgoedtel"
   manager="stevenka"
   editor="tysonn"
   tags="top-support-issue"
   keywords="Erro de automatização, processamento de erros"/>
<tags
   ms.service="automation"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="07/06/2016"
   ms.author="sngun; v-reagie"/>

# <a name="error-handling-tips-for-common-azure-automation-errors"></a>Sugestões para erros comuns de automatização do Azure de processamento de erros

Este artigo explica alguns dos erros Azure automatização comuns que poderá deparar e sugere possíveis passos de processamento de erros.

## <a name="troubleshoot-authentication-errors-when-working-with-azure-automation-runbooks"></a>Resolução de problemas de autenticação quando trabalha com runbooks de automatização do Azure  

### <a name="scenario-sign-in-to-azure-account-failed"></a>Cenário: Iniciar sessão na conta Azure falhou

**Erro:** Receber o erro "Unknown_user_type: utilizador tipo desconhecido" quando trabalha com os cmdlets adicionar AzureAccount ou AzureRmAccount de início de sessão.

**Razão para o erro:** Este erro ocorre se o nome de elementos de credenciais não é válido ou se o nome de utilizador e palavra-passe que utilizou para configurar os elementos de credenciais de automatização não são válidos.

**Sugestões de resolução de problemas:** Para determinar qual é o problema, siga os passos seguintes:  

1. Certifique-se de que não tem qualquer carateres especiais, incluindo o **@** carateres no nome de elementos de credenciais de automatização que está a utilizar para ligar ao Azure.  

2. Verifique que pode utilizar o nome de utilizador e palavra-passe que estão armazenados na credencial de automatização do Azure no seu editor ISE do PowerShell local. Pode fazê-lo executando os seguintes cmdlets no ISE do PowerShell:  

        $Cred = Get-Credential  
        #Using Azure Service Management   
        Add-AzureAccount –Credential $Cred  
        #Using Azure Resource Manager  
        Login-AzureRmAccount –Credential $Cred

3. Se a autenticação falhar localmente, isto significa que não tenha configurado o suas credenciais do Azure Active Directory corretamente. Referir-se a mensagem de blogue [Authenticating para Azure utilizando o Azure Active Directory](https://azure.microsoft.com/blog/azure-automation-authenticating-to-azure-using-azure-active-directory/) para obter a conta do Azure Active Directory corretamente configurada.  


### <a name="scenario-unable-to-find-the-azure-subscription"></a>Cenário: Não é possível localizar a subscrição do Azure

**Erro:** Receber o erro "a subscrição denominada ``<subscription name>`` não foi encontrado" quando trabalha com os cmdlets selecione AzureSubscription ou selecione AzureRmSubscription.

**Razão para o erro:** Este erro ocorre se o nome da subscrição não é válido ou se o utilizador do Azure Active Directory que está a tentar obter os detalhes da subscrição não estiver configurado como um administrador da subscrição.

**Sugestões de resolução de problemas:** Para determinar se corretamente foram autenticados para Azure e tenha acesso a subscrição que está a tentar para selecionar, siga os passos seguintes:  

1. Certifique-se de que execute a **Adicionar AzureAccount** antes de executar o cmdlet **Selecione AzureSubscription** .  

2. Se continuar a ver esta mensagem de erro, modificar o seu código adicionando o cmdlet **Get-AzureSubscription** seguir o cmdlet **AzureAccount adicionar** e, em seguida, execute o código.  Agora, verifique se o resultado de obter AzureSubscription contém os detalhes da subscrição.  
    * Se não vir qualquer detalhes da subscrição no resultado, isto significa que a subscrição não estiver ainda inicializada.  
    * Se vir os detalhes da subscrição no resultado, confirme que está a utilizar o nome da subscrição correto ou o ID com o cmdlet **Selecione AzureSubscription** .   


### <a name="scenario-authentication-to-azure-failed-because-multi-factor-authentication-is-enabled"></a>Cenário: Autenticação para Azure falhou porque autenticação multifator é activada

**Erro:** Receber o erro "Adicionar AzureAccount: AADSTS50079: é necessária inscrição de autenticação forte (prova de segurança)" ao autenticar para Azure com o seu nome de utilizador Azure e a palavra-passe.

**Razão para o erro:** Se tiver autenticação multifator na sua conta do Azure, não pode utilizar um utilizador do Azure Active Directory para autenticar Azure.  Em vez disso, tem de utilizar um certificado ou um serviço principal para autenticar para Azure.

**Sugestões de resolução de problemas:** Para utilizar um certificado com os cmdlets de gestão de serviço do Azure, referir-se a [criar e adicionar um certificado para gerir serviços Azure.](http://blogs.technet.com/b/orchestrator/archive/2014/04/11/managing-azure-services-with-the-microsoft-azure-automation-preview-service.aspx) Para utilizar um principal de serviço com cmdlets do Gestor de recursos do Azure, referir-se para a [criação de capital de serviço através do portal Azure](./resource-group-create-service-principal-portal.md) e [autenticar um principal de serviço com o Gestor de recursos do Azure.](./resource-group-authenticate-service-principal.md)


## <a name="troubleshoot-common-errors-when-working-with-runbooks"></a>Resolver erros comuns ao trabalhar com runbooks

### <a name="scenario-runbook-fails-because-of-deserialized-object"></a>Cenário: Livro execuções falhar devido a objeto

**Erro:** O livro de execuções falha com o erro "não é possível vincular parâmetro ``<ParameterName>``. Não é possível converter o ``<ParameterType>`` valor do tipo Deserialized ``<ParameterType>`` para escrever ``<ParameterType>``".

**Razão para o erro:** Se o seu livro de execuções for um fluxo de trabalho do PowerShell, armazena complexos objetos num formato para poder persistir estado da sua livro execuções se o fluxo de trabalho está suspensa.  

**Sugestões de resolução de problemas:**  
Qualquer uma das três seguintes soluções irão corrigir este problema:

1. Se são tubagens objectos complexos a partir de um cmdlet para outro, molde estes cmdlets uma InlineScript.  
2. Passe o nome ou o valor que necessita do objeto complexo em vez do objeto de todo a passar.  

3. Utilize um livro de execuções do PowerShell em vez de um livro de execuções do PowerShell fluxo de trabalho.  


### <a name="scenario-runbook-job-failed-because-the-allocated-quota-exceeded"></a>Cenário: Falha da tarefa de livro execuções porque a atribuída quota excedida

**Erro:** A tarefa de livro execuções falha com o erro "a quota para o trabalho total mensal tempo de execução foi atingida para esta subscrição".

**Razão para o erro:** Este erro ocorre quando a execução do projecto exceda a quota de gratuita 500 minutos para a sua conta. Esta quota aplica-se a todos os tipos de execução de tarefas do projecto como testar uma tarefa, iniciar uma tarefa a partir do portal, executar uma tarefa utilizando webhooks e agendamento de uma tarefa para executar utilizando o portal de Azure ou no seu centro de dados. Para obter mais informações sobre preços do automatização consulte [preços de automatização](https://azure.microsoft.com/pricing/details/automation/).

**Sugestões de resolução de problemas:** Se pretender utilizar mais de 500 minutos de processamento por mês terá de alterar a sua subscrição a partir do Free camada para a camada básica. Pode atualizar para a camada básica ao efetuar os seguintes passos:  

1. Inicie sessão na sua subscrição do Azure  
2. Selecione a conta de automatização que pretende atualizar  
3. Clique em **Definições** > **camada de preços e a utilização** > **preços camada**  
4. No pá **Escolher a comparar camada** , selecione **básicas**    


### <a name="scenario-cmdlet-not-recognized-when-executing-a-runbook"></a>Cenário: Cmdlet não é reconhecido durante a execução de um livro de execuções

**Erro:** A tarefa de livro execuções falha com o erro "``<cmdlet name>``: O termo ``<cmdlet name>`` não é reconhecido como o nome de um cmdlet, função, ficheiro de script ou programa operável."

**Razão para o erro:** Este erro é causado quando o motor de PowerShell não consegue localizar o cmdlet que estiver a utilizar no seu livro de execuções.  Isto pode ser porque o módulo que contém o cmdlet está em falta a conta, existe um conflito de nomes com um nome de livro execuções, ou o cmdlet também existe no outro módulo e automatização não é possível resolver o nome.

**Sugestões de resolução de problemas:** Qualquer uma das seguintes soluções corrige o problema:  

- Verifique que introduziu o nome do cmdlet corretamente.  

- Certificar-se de que o cmdlet existe na sua conta de automatização e que não existirem conflitos. Para verificar se o cmdlet é apresentar, abra um livro de execuções no modo de edição e procure o cmdlet que pretende localizar na biblioteca ou executar **comando Get ``<CommandName>`` **.  Depois de ter validado que o cmdlet está disponível para a conta e que não existirem nome conflitos com outros cmdlets ou runbooks, adicioná-lo para a tela e certifique-se de que está a utilizar um parâmetro válido definir no seu livro de execuções.  

- Se tiver um conflito de nomes e o cmdlet está disponível em dois módulos diferentes, pode resolver esta situação utilizando o nome para o cmdlet completamente qualificado. Por exemplo, pode utilizar **ModuleName\CmdletName**.  

- Se está a executar o livro execuções no local num grupo de trabalho híbrido, em seguida, certifique-se de que o módulo/cmdlet está instalado no computador que aloja o trabalhador híbrido.


### <a name="scenario-a-long-running-runbook-consistently-fails-with-the-exception-the-job-cannot-continue-running-because-it-was-repeatedly-evicted-from-the-same-checkpoint"></a>Cenário: Um livro de execuções longa forma consistente falha com a exceção: "a tarefa não é possível continuar a ser executado porque-repetidamente foi retirado do mesmo ponto de verificação".

**Razão para o erro:** Este é pelo comportamento de estrutura devido a monitorização de "Partilhar da feira da ciência" dos processos dentro de automatização de Azure, suspender automaticamente um livro de execuções, se for executado já 3 horas. No entanto, a mensagem de erro devolvida não fornece "e agora" opções. Um livro de execuções pode ser suspenso para um número de razões. Suspender acontecer principalmente devido a erros. Por exemplo, uma exceção não identificada num livro de execuções, uma falha na rede ou uma falha de sistema no livro execuções trabalhador a executar o livro de execuções, todas as causará livro de execuções ser suspensa e iniciar a partir do seu ponto de verificação último quando retomada.

**Sugestões de resolução de problemas:** A solução documentada para evitar este problema é utilizar os pontos de verificação num fluxo de trabalho.  Para saber mais referir-se aos [Fluxos de trabalho de PowerShell de aprendizagem](automation-powershell-workflow.md#Checkpoints).  Pode encontrar uma explicação mais detalhada dos "Partilhar da feira da ciência" e o ponto de verificação neste artigo de blogue [Pontos de verificação utilizar no Runbooks](https://azure.microsoft.com/en-us/blog/azure-automation-reliable-fault-tolerant-runbook-execution-using-checkpoints/).


## <a name="troubleshoot-common-errors-when-importing-modules"></a>Resolver erros comuns ao importar módulos

### <a name="scenario-module-fails-to-import-or-cmdlets-cant-be-executed-after-importing"></a>Cenário: Módulo falha importar ou cmdlets não pode ser executados depois de importar

**Erro:** Um módulo falha importar ou importado com êxito, mas não os cmdlets do são extraídos.

**Razão para o erro:** Alguns dos motivos comuns que um módulo com êxito poderá não importar para Azure automatização são:  

- A estrutura não corresponder a estrutura de que necessita de automatização-lo a estar.  

- O módulo é dependente outro módulo que não tenha sido implementado à sua conta de automatização.  

- O módulo falta respectivas dependências na pasta.  

- O cmdlet **Novo AzureRmAutomationModule** está a ser utilizado para carregar o módulo, e não ter dado o caminho completo armazenamento ou não ter sido carregado o módulo utilizando um URL acessível publicamente.  

**Sugestões de resolução de problemas:**  
Qualquer uma das seguintes soluções corrige o problema:  

- Certifique-se de que o módulo segue o seguinte formato:  
ModuleName.Zip **->** nomemódulo ou número de versão **->** (ModuleName.psm1, ModuleName.psd1)

- Abra o ficheiro .psd1 e veja se o módulo dependente de.  Caso isso aconteça, carregue estes módulos para a conta de automatização.  

- Certifique-se de que quaisquer DLLs referenciada estão presentes na pasta módulo.  


## <a name="troubleshoot-common-errors-when-working-with-desired-state-configuration-dsc"></a>Resolver erros comuns quando trabalhar com configuração de estado pretendida (DSC)  

### <a name="scenario-node-is-in-failed-status-with-a-not-found-error"></a>Cenário: Nó está no estado falhado com um erro "Não encontrado"

**Erro:** O nó tem um relatório com estado **falhou** e que contém o erro "tentativa de obter a ação a partir do servidor https://``<url>``//accounts/``<account-id>``/Nodes(AgentId=``<agent-id>``)/GetDscAction falhou porque uma configuração válida ``<guid>`` não foi encontrado."

**Razão para o erro:** Este erro ocorre normalmente quando o nó é atribuído a um nome de configuração (por exemplo, ABC) em vez de um nome de configuração de nó (por exemplo, ABC. Servidorweb).  

**Sugestões de resolução de problemas:**  

- Certifique-se de que está a atribuir o nó com "nome de configuração de nó" e não o "configuração nome".  

- Pode atribuir uma configuração de nó para um nó através do portal Azure ou com um cmdlet do PowerShell.
    - Para atribuir uma configuração de nó para um nó através do portal Azure, abra pá **DSC nós** , em seguida, selecione um nó e clique no botão **atribuir nó Configuração** .  
    - Para atribuir uma configuração de nó a um nó utilizando o cmdlet do PowerShell, utilize o cmdlet **Set-AzureRmAutomationDscNode**


### <a name="scenario--no-node-configurations-mof-files-were-produced-when-a-configuration-is-compiled"></a>Cenário: Sem configurações de nó (ficheiros MOF) foram produzidas quando uma configuração é compilada

**Erro:** A tarefa de compilação DSC suspende com o erro: "compilação foi concluída com êxito, mas não .mofs de configuração de nó foram geradas".

**Razão para o erro:** Quando a seguinte expressão que é avaliada a palavra-chave de **nó** na configuração DSC $null, em seguida, sem configurações nó vai ser produzida.    

**Sugestões de resolução de problemas:**  
Qualquer uma das seguintes soluções corrige o problema:  

- Certifique-se de que a expressão junto a palavra-chave de **nó** na definição de configuração não está a avaliar para $null.  
- Se está a passar ConfigurationData ao compilar a configuração, certifique-se de que está a passar os valores de esperados a configuração requer a partir do [ConfigurationData](automation-dsc-compile.md#configurationdata).


### <a name="scenario--the-dsc-node-report-becomes-stuck-in-progress-state"></a>Cenário: O relatório de nó DSC fica bloqueada: estado "em curso"

**Erro:** Agente de DSC não exporta "Foi encontrada nenhuma instância com valores de propriedade indicados."

**Razão para o erro:** Tiver atualizado o seu versão WMF e ter danificado WMI.  

**Sugestões de resolução de problemas:** Siga as instruções na mensagem no blogue [DSC problemas e limitações conhecidos](https://msdn.microsoft.com/powershell/wmf/limitation_dsc) para corrigir o problema.


### <a name="scenario--unable-to-use-a-credential-in-a-dsc-configuration"></a>Cenário: Não é possível utilizar uma credencial numa configuração DSC

**Erro:** A tarefa de compilação DSC foi suspensa com o erro: "propriedade credenciais do tipo de processamento de erros de System.InvalidOperationException ``<some resource name>``: converter e armazenar uma palavra-passe encriptada como texto simples só são permitido se PSDscAllowPlainTextPassword está definida como verdadeiro".

**Razão para o erro:** Tenha utilizado uma credencial numa configuração, mas não fornecer inicial **ConfigurationData** para definir **PSDscAllowPlainTextPassword** como verdadeiro para cada configuração de nó.  

**Sugestões de resolução de problemas:**  
- Certifique-se passar no bom **ConfigurationData** para definir **PSDscAllowPlainTextPassword** como verdadeiro para cada configuração de nó mencionada na configuração. Para obter mais informações, consulte [ativos na Azure automatização DSC](automation-dsc-compile.md#assets).


## <a name="next-steps"></a>Próximos passos

Se tiver seguido os passos de resolução de problemas acima e precisar de ajuda adicional em qualquer ponto neste artigo, pode:

- Obtenha ajuda do Azure especialistas. Submeter o seu problema para o [fóruns MSDN Azure ou pilha de excesso.](https://azure.microsoft.com/support/forums/)

- Ficheiro de um incidente de suporte Azure. Aceda ao [site de suporte do Azure](https://azure.microsoft.com/support/options/) e clique em **obter suporte** ao abrigo do **suporte técnico e de faturação**.

- Publicar um pedido de Script no [Centro de Script](https://azure.microsoft.com/documentation/scripts/) se está a procurar uma solução de livro execuções Azure automatização ou um módulo de integração.

- Publicar feedback ou funcionalidade pedidos para Azure automatização no [Voz do utilizador](https://feedback.azure.com/forums/34192--general-feedback).
