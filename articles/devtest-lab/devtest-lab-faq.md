<properties
    pageTitle="Azure DevTest Labs FAQ | Microsoft Azure"
    description="Encontre respostas às perguntas comuns do Azure DevTest Labs"
    services="devtest-lab,virtual-machines"
    documentationCenter="na"
    authors="tomarcher"
    manager="douge"
    editor=""/>

<tags
    ms.service="devtest-lab"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/13/2016"
    ms.author="tarcher"/>

# <a name="azure-devtest-labs-faq"></a>Azure DevTest Labs perguntas mais frequentes

Este artigo responde a algumas perguntas mais comuns sobre Azure DevTest Labs.

[AZURE.INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="general"></a>Geral
- [O que acontece se não for respondida aqui minha pergunta?](#what-if-my-question-isnt-answered-here)
- [Por que motivo devo utilizar o Azure DevTest Labs?](#why-should-i-use-azure-devtest-labs) 
- [O que significa "preocupações, Self-serviços"?](#what-does-quotworry-free-self-servicequot-mean)
- [Como utilizar o Azure DevTest Labs?](#how-can-i-use-azure-devtest-labs) 
- [Como estou faturada para Azure DevTest Labs?](#how-am-i-billed-for-azure-devtest-labs) 
 
## <a name="security"></a>Segurança 
- [Quais são os níveis de segurança diferente em Azure DevTest laboratório?](#what-are-the-different-security-levels-in-azure-devtest-labs) 
- [Como posso criar uma função para permitir que os utilizadores efetuar uma tarefa específica?](#how-do-i-create-a-role-to-allow-users-to-perform-a-specific-task) 
 
## <a name="cicd-integration--automation"></a>Integração de CI/CD e de automatização 
 
- [Azure DevTest Labs integrar com o meu toolchain CI/CD?](#does-azure-devtest-labs-integrate-with-my-cicd-toolchain) 
 
## <a name="virtual-machines"></a>Máquinas virtuais 
 
- [Por que motivo não consigo ver determinados VMs na pá máquinas virtuais do Azure que visualizar no Azure DevTest Labs?](#why-cant-i-see-certain-vms-in-the-azure-virtual-machines-blade-that-i-see-within-azure-devtest-labs) 
- [O que é a diferença entre imagens personalizadas e as fórmulas?](#what-is-the-difference-between-custom-images-and-formulas) 
- [Como posso criar várias VMs partir do mesmo modelo de uma vez?](#how-do-i-create-multiple-vms-from-the-same-template-at-once) 
- [Como posso mover meu VMs Azure existente para o meu laboratório Azure DevTest Labs?](#how-do-i-move-my-existing-azure-vms-into-my-azure-devtest-labs-lab) 
- [Pode anexar vários discos para minha VMs?](#can-i-attach-multiple-disks-to-my-vms) 
- [Como posso automatizar o processo de carregamento de ficheiros VHD para criar imagens personalizadas?](#how-do-i-automate-the-process-of-uploading-vhd-files-to-create-custom-images) 
- [Como posso automatizar o processo de eliminar todas as VMs no meu laboratório?](#how-can-i-automate-the-process-of-deleting-all-the-vms-in-my-lab)
 
## <a name="artifacts"></a>Artefactos 
 
- [O que são artefactos?](#what-are-artifacts) 
 
## <a name="lab-configuration"></a>Configuração do laboratório 
 
- [Como posso criar um laboratório a partir de um modelo de Gestor de recursos do Azure?](#how-do-i-create-a-lab-from-an-azure-resource-manager-template) 
- [Por que motivo o meu VMs são criados no diferentes grupos de recursos com nomes arbitrários? Pode mudar o nome ou modificar estes grupos de recursos?](#why-are-my-vms-created-in-different-resource-groups-with-arbitrary-names-can-i-rename-or-modify-these-resource-groups) 
- [Quantos labs pode criar em mesma subscrição?](#how-many-labs-can-i-create-under-the-same-subscription)
- [Quantos VMs pode criar por laboratório?](#how-many-vms-can-i-create-per-lab)
- [Como posso partilhar uma ligação direta para minha laboratório?](#how-do-i-share-a-direct-link-to-my-lab)
- [O que é uma conta Microsoft?](#what-is-a-microsoft-account)
 
## <a name="troubleshooting"></a>Resolução de problemas 
 
- [Meu artefacto falhou durante a criação de VM. Como resolvê-lo?](#my-artifact-failed-during-vm-creation-how-do-i-troubleshoot-it) 
- [Por que motivo não está meu virtual rede existente guardar corretamente?](#why-isnt-my-existing-virtual-network-saving-properly)  

### <a name="what-if-my-question-isnt-answered-here"></a>O que acontece se não for respondida aqui minha pergunta?
Se a sua pergunta não está listada aqui, diga-nos e iremos ajudá-lo a encontrar uma resposta.

- Publique uma questão no [tópico Disqus](#comments) no final deste FAQ e participar com a equipa do Azure Cache e outros membros da Comunidade acerca deste artigo.
- Para chegar uma audiência maior, publicar uma pergunta no [Fórum MSDN do Azure DevTest Labs](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureDevTestLabs)e participar com a equipa do Azure DevTest Labs e outros membros da Comunidade.
- Para tornar um pedido de funcionalidade, submeta os pedidos e ideias para o [Azure DevTest Labs utilizador voz](https://feedback.azure.com/forums/320373-azure-devtest-labs).

### <a name="why-should-i-use-azure-devtest-labs"></a>Por que motivo devo utilizar o Azure DevTest Labs? 
Azure DevTest Labs pode guardar o tempo de equipa e o dinheiro. Os programadores podem criar os seus próprios ambientes, utilizando a várias bases diferentes e utilizar artefactos para implementar e configurar as aplicações de rapidamente. Utilizar imagens personalizadas e fórmulas, máquinas virtuais podem ser guardadas como modelos e reproduzidas facilmente. Além disso, labs oferecem várias políticas configuráveis que permitem aos administradores laboratório reduzir resíduos e gerir ambientes de equipa. Estas políticas incluem automática-encerramento, limiar de custo, máximos VMs por utilizador e os tamanhos máximos de VM. Para obter uma explicação de forma mais aprofundada da Azure DevTest Labs, leia a [Descrição geral](devtest-lab-overview.md) ou veja o [vídeo introdutório](/documentation/videos/videos/what-is-azure-devtest-labs). 

### <a name="what-does-worry-free-self-service-mean"></a>O que significa "preocupações, Self-serviços"?
"Preocupações, Self-Service" significa que os programadores e verificadores criar os seus próprios ambientes conforme necessário, e os administradores têm a segurança de saber o que ajuda a Azure DevTest Labs minimizar percam e controlar o custo. Os administradores podem especificar quais tamanhos VM são permitidos, o número máximo de VMs, e quando VMs iniciados e encerrar. Azure DevTest Labs também torna mais fácil monitorizar custos e definir alertas para estar a par da como recursos no laboratório estão a ser utilizados. 

### <a name="how-can-i-use-azure-devtest-labs"></a>Como utilizar o Azure DevTest Labs? 
Azure DevTest Labs é útil sempre exigir Dev Center ou testar ambientes e que pretende reproduzi-las rapidamente e/ou geri-los com a opção Guardar políticas de custo. 

Eis alguns cenários que dos nossos clientes utilizam Azure DevTest Labs por: 

- Gerir ambientes Dev Center e teste num único local, recorrendo políticas para reduzir o custo e as imagens personalizadas para partilhar constrói toda a equipa.
- Desenvolver uma aplicação utilizando imagens personalizadas para guardar o estado de disco ao longo as fases do desenvolvimento.
- Monitorizar o custo em relação de progresso. 
- Criar ambientes de teste em massa para testar a qualidade assurance.
- Utilizar artefactos e fórmulas para configurar e reproduzir uma aplicação em vários ambientes facilmente. 
- Distribuir VMs para hackathons (trabalho de colaboração de Dev Center ou teste) e, em seguida, facilmente retirar aprovisionamento-los quando termina o evento. 

### <a name="how-am-i-billed-for-azure-devtest-labs"></a>Como estou faturada para Azure DevTest Labs? 
Azure DevTest Labs é um serviço gratuito, que significa que criar labs e configurar as políticas, modelos e artefactos não gratuito. Pode pagar apenas para os recursos Azure utilizados no seu labs, tais como máquinas virtuais, contas de armazenamento e redes virtuais. Para mais informações sobre o custo de recursos de laboratório, leia sobre [preços do Azure DevTest Labs](https://azure.microsoft.com/pricing/details/devtest-lab/). 

### <a name="what-are-the-different-security-levels-in-azure-devtest-labs"></a>Quais são os níveis de segurança diferente em Azure DevTest laboratório?  
Acesso de segurança é determinado pela [Azure Role-Based acesso controlo RBCA ()](../active-directory/role-based-access-built-in-roles.md). Para compreender como funciona o access, é útil para compreender as diferenças entre uma permissão, uma função e um âmbito conforme definido pelo RBAC.

- A **permissão** - uma permissão é um acesso definido para uma acção específica. Por exemplo, uma permissão poderão ser máquinas de acesso de leitura a todas as virtual. 
- **Função** - uma função é um conjunto de permissões que podem ser agrupadas e atribuída a um utilizador. Por exemplo, "subscrição proprietário de um" tem acesso a todos os recursos dentro de uma subscrição. 
- **Âmbito** - um âmbito é um nível na hierarquia do recurso Azure. Por exemplo, pode ser um âmbito de um grupo de recursos ou um único laboratório ou toda a subscrição. 
 
No âmbito da Azure DevTest Labs, existem dois tipos de funções de administrador a definir permissões de utilizador: utilizador de laboratório e laboratório proprietário.

- **Proprietário de laboratório** - proprietário de um laboratório tem acesso a quaisquer recursos dentro o laboratório. Por conseguinte, eles podem modificar as políticas, ler e escrever qualquer VMs, alterar a rede virtual e assim sucessivamente. 
- **Utilizador laboratório** - um utilizador laboratório pode ver todos os recursos de laboratório, tais como VMs, políticas e redes virtuais, mas não podem modificar políticas ou qualquer VMs criados por outros utilizadores. Também é possível criar funções personalizadas em Azure DevTest laboratório e pode aprender a fazer no artigo, [conceder permissões de utilizador para as políticas de laboratório específico](devtest-lab-grant-user-permissions-to-specific-lab-policies.md). 
 
Uma vez que âmbitos são hierárquicos, quando um utilizador com permissões a um determinado âmbito, estes são concedidas automaticamente essas permissões em cada âmbito de níveis inferiores abrangido. Por exemplo, se um utilizador é atribuído a função do proprietário da subscrição, em seguida, tiverem acesso a todos os recursos numa subscrição. Estes recursos incluem todas as máquinas virtuais, todas as redes virtuais e todos os labs. Assim sendo, um proprietário de subscrição herda automaticamente a função do proprietário laboratório. No entanto, o oposto não é verdade. Proprietário de um laboratório tem acesso a um laboratório, que é um âmbito que o nível de subscrição inferior. Por conseguinte, proprietário de um laboratório não está a ver máquinas virtuais ou redes virtuais ou quaisquer recursos que estejam fora do ambiente de teste. 

### <a name="how-do-i-create-a-role-to-allow-users-to-perform-a-specific-task"></a>Como posso criar uma função para permitir que os utilizadores efetuar uma tarefa específica?
Um artigo abrangente sobre como criar funções personalizadas e atribuir permissões a essa função pode ser encontrado aqui. Eis um exemplo de um script que cria a função "DevTest Labs avançadas" utilizador, que tem permissão para iniciar e parar todos os VMs no laboratório:
 
    $policyRoleDef = Get-AzureRmRoleDefinition "DevTest Labs User" 
    $policyRoleDef.Actions.Remove('Microsoft.DevTestLab/Environments/*') 
    $policyRoleDef.Id = $null 
    $policyRoleDef.Name = "DevTest Labs Advance User" 
    $policyRoleDef.IsCustom = $true 
    $policyRoleDef.AssignableScopes.Clear() 
    $policyRoleDef.AssignableScopes.Add("subscriptions/<subscription Id>") 
    $policyRoleDef.Actions.Add("Microsoft.DevTestLab/labs/virtualMachines/Start/action") 
    $policyRoleDef.Actions.Add("Microsoft.DevTestLab/labs/virtualMachines/Stop/action") 
    $policyRoleDef = New-AzureRmRoleDefinition -Role $policyRoleDef  
 
### <a name="does-azure-devtest-labs-integrate-with-my-cicd-toolchain"></a>Azure DevTest Labs integrar com o meu toolchain CI/CD? 
Se estiver a utilizar VSTS, existe uma [extensão de tarefas do Azure DevTest Labs](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks) permite-lhe automatizar a sua pipeline de lançamento em Azure DevTest laboratório. Algumas das utilizações desta extensão incluem:

- Criar e implementar uma VM automaticamente e configurá-la com a compilação mais recente, utilizar as tarefas Azure ficheiro de cópia ou PowerShell VSTS. 
- O estado de um VM a capturar automaticamente depois de teste para reproduzir um erro sobre a mesma VM para o inquérito ainda mais. 
- Eliminar a VM no final do pipeline de lançamento quando já não é necessária. 

Fornecem as seguintes mensagens do blogue de orientação e informações sobre como utilizar a extensão VSTS:
 
- [Azure DevTest Labs – extensão VSTS](https://blogs.msdn.microsoft.com/devtestlab/2016/06/15/azure-devtest-labs-vsts-extension/) 
- [Implementar uma nova VM numa AzureDevTestLab existente a partir do VSTS](http://www.visualstudiogeeks.com/blog/DevOps/Deploy-New-VM-To-Existing-AzureDevTestLab-From-VSTS) 
- [Utilizar a gestão de lançamento do VSTS para implementações contínuas para AzureDevTestLabs](http://www.visualstudiogeeks.com/blog/DevOps/Use-VSTS-ReleaseManagement-to-Deploy-and-Test-in-AzureDevTestLabs) 
 
Para outras toolchains CI/CD, todos os cenários anteriormente mencionados que podem ser obtidos através a extensão de tarefas VSTS podem ser do mesmo modo obtidos através de implementar [modelos de Gestor de recursos do Azure](https://github.com/Azure/azure-devtestlab/tree/master/ARMTemplates) utilizando [os cmdlets do PowerShell do Azure](../resource-group-template-deploy.md) e [.NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Management.DevTestLabs/). Também pode utilizar [REST APIs para DevTest Labs](http://aka.ms/dtlrestapis) para integrar com o seu toolchain.  

### <a name="why-cant-i-see-certain-vms-in-the-azure-virtual-machines-blade-that-i-see-within-azure-devtest-labs"></a>Por que motivo não consigo ver determinados VMs na pá máquinas virtuais do Azure que visualizar no Azure DevTest Labs?
Quando é criada uma VM em Azure DevTest laboratório, é dado permissão para aceder a esse VM. É possível para visualizá-lo, tanto na pá labs e o pá **máquinas virtuais** . Utilizadores na função DevTest Labs podem ver todas as máquinas virtuais criadas no laboratório através de pá de **todas as máquinas virtuais** do ambiente de teste. No entanto, os utilizadores na função DevTest Labs não são automaticamente concedidos acesso de leitura para recursos VM que outros utilizadores tenham criado. Por conseguinte, essas VMs não são apresentadas na pá **máquinas virtuais** . 

### <a name="what-is-the-difference-between-custom-images-and-formulas"></a>O que é a diferença entre imagens personalizadas e as fórmulas? 
Uma imagem personalizada é um VHD (disco rígido virtual), enquanto que a fórmula for uma imagem que pode configurar com definições adicionais que podem guardar e reproduzir. Uma imagem personalizada poderá ser preferível se pretender criar rapidamente várias ambientes com a mesma imagem básica, imutáveis. Uma fórmula pode ser melhor se pretende reproduzir a configuração do seu VM com os bits mais recentes, virtual/sub-rede de uma rede ou um tamanho específico. Para obter uma explicação de forma mais aprofundada, consulte o artigo, [imagens personalizadas comparar e fórmulas em DevTest laboratório](devtest-lab-comparing-vm-base-image-types.md). 
 
### <a name="how-do-i-create-multiple-vms-from-the-same-template-at-once"></a>Como posso criar várias VMs partir do mesmo modelo de uma vez? 
Pode utilizar a [extensão de tarefas VSTS](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks) ou [gerar um modelo de Gestor de recursos do Azure](devtest-lab-add-vm-with-artifacts.md#save-arm-template) enquanto cria uma VM e [Implementar o modelo de Gestor de recursos do Azure a partir do Windows PowerShell](../resource-group-template-deploy.md). 
 
### <a name="how-do-i-move-my-existing-azure-vms-into-my-azure-devtest-labs-lab"></a>Como posso mover meu VMs Azure existente para o meu laboratório Azure DevTest Labs? 
Vamos está a estruturar uma solução para mover diretamente VMs para Azure DevTest Labs, mas atualmente pode copiar os VMs existentes para Azure DevTest Labs da seguinte forma: 

1. Copie o ficheiro VHD da sua VM existente utilizando este [script do Windows PowerShell](https://github.com/Azure/azure-devtestlab/blob/master/Scripts/CopyVHDFromVMToLab.ps1) 
1. [Criar a imagem personalizada](devtest-lab-create-template.md) no interior do laboratório Azure DevTest Labs. 
1. Criar uma VM no laboratório a partir de uma imagem personalizada 
 
### <a name="can-i-attach-multiple-disks-to-my-vms"></a>Pode anexar vários discos para minha VMs? 
Como anexar vários discos a VMs é suportada.  
 
### <a name="how-do-i-automate-the-process-of-uploading-vhd-files-to-create-custom-images"></a>Como posso automatizar o processo de carregamento de ficheiros VHD para criar imagens personalizadas? 
Existem duas opções:

- [Azure AzCopy](../storage/storage-use-azcopy.md#blob-upload) podem ser utilizados para copiar ou carregar ficheiros VHD para a conta de armazenamento associada o laboratório.
- [Explorador de armazenamento do Microsoft Azure](../vs-azure-tools-storage-manage-with-storage-explorer.md) é uma aplicação autónoma que é executada no Windows, os x e Linux.   
 
Para localizar a conta de armazenamento de destino associada a sua laboratório, siga estes passos:

1. Inicie sessão no [portal do Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040). 
1. Selecione **Grupos de recursos** a partir do painel da esquerda. 
1. Localize e selecione o grupo de recursos associado com o laboratório. 
1. No pá **Descrição geral** , selecione uma das contas de armazenamento. 
1. Selecione **Blobs**.
1. Procure os carregamentos na lista. Se não existe nenhum, regresse ao passo #4 e tente outra conta de armazenamento.
1. Utilize o **URL** , como o destino no comando AzCopy.


### <a name="how-can-i-automate-the-process-of-deleting-all-the-vms-in-my-lab"></a>Como posso automatizar o processo de eliminar todas as VMs no meu laboratório?

Para além de eliminar VMs a partir do seu laboratório no portal do Azure, pode eliminar todas as VMs no seu laboratório utilizando um script PowerShell. No exemplo seguinte, basta modifique os valores de parâmetros sob o comentário de **valores para alterar** . Pode obter a `subscriptionId`, `labResourceGroup`, e `labName` valores a partir de pá laboratório no portal do Azure. 


    # Delete all the VMs in a lab
    
    # Values to change
    $subscriptionId = "<Enter Azure subscription ID here>"
    $labResourceGroup = "<Enter lab's resource group here>"
    $labName = "<Enter lab name here>"

    # Login to your Azure account
    Login-AzureRmAccount
    
    # Select the Azure subscription that contains the lab. This step is optional
    # if you have only one subscription.
    Select-AzureRmSubscription -SubscriptionId $subscriptionId
    
    # Get the lab that contains the VMs to delete.
    $lab = Get-AzureRmResource -ResourceId ('subscriptions/' + $subscriptionId + '/resourceGroups/' + $labResourceGroup + '/providers/Microsoft.DevTestLab/labs/' + $labName)
    
    # Get the VMs from that lab.
    $labVMs = Get-AzureRmResource | Where-Object { 
              $_.ResourceType -eq 'microsoft.devtestlab/labs/virtualmachines' -and
              $_.ResourceName -like "$($lab.ResourceName)/*"}
    
    # Delete the VMs.
    foreach($labVM in $labVMs)
    {
        Remove-AzureRmResource -ResourceId $labVM.ResourceId -Force
    }




### <a name="what-are-artifacts"></a>O que são artefactos? 
Artefactos são personalizáveis elementos que podem ser utilizados para implementar o seu bits mais recentes ou as ferramentas do seu Dev Center para uma VM. Estão anexados às sua VM durante a criação com alguns cliques simples e, depois da VM está aprovisionada, erros implementar e configurar o seu VM. Existem vários artefactos pré-existentes no nosso [público repositório de Github](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts), mas pode também facilmente [autor o seus próprio artefactos](devtest-lab-artifact-author.md). 

### <a name="how-do-i-create-a-lab-from-an-azure-resource-manager-template"></a>Como posso criar um laboratório a partir de um modelo de Gestor de recursos do Azure? 
Fornecemos um [repositório de Github dos modelos de Gestor de recursos do Azure laboratório](https://github.com/Azure/azure-devtestlab/tree/master/ARMTemplates) que pode implementar como-está ou modificar para criar modelos personalizados para o seu labs. Cada um dos seguintes modelos tem uma ligação que pode clicar para implementar o laboratório como-está sob o seu próprio subscrição Azure, ou pode personalizar o modelo e [implementar utilizando o PowerShell ou clip de Azure](../resource-group-template-deploy.md).
 
### <a name="why-are-my-vms-created-in-different-resource-groups-with-arbitrary-names-can-i-rename-or-modify-these-resource-groups"></a>Por que motivo o meu VMs são criados no diferentes grupos de recursos com nomes arbitrários? Pode mudar o nome ou modificar estes grupos de recursos? 
Grupos de recursos são criados desta forma na ordem de Azure DevTest Labs gerir as permissões de utilizador e o acesso ao máquinas virtuais. Enquanto pode mover a VM para outro grupo de recursos com o seu nome pretendido, ao fazê-lo por isso, não é recomendado. Estamos a trabalhar sobre como melhorar esta experiência para permitir uma maior flexibilidade.   
 
### <a name="how-many-labs-can-i-create-under-the-same-subscription"></a>Quantos labs pode criar em mesma subscrição? 
Não existe nenhum limite específico no número de labs que podem ser criados por subscrição. No entanto, os recursos utilizados estão limitados por subscrição. Pode obter informações sobre os [limites e as quotas de impostas subscrições Azure](../azure-subscription-service-limits.md) e [como aumentar estes limites](https://azure.microsoft.com/blog/azure-limits-quotas-increase-requests). 
 
### <a name="how-many-vms-can-i-create-per-lab"></a>Quantos VMs pode criar por laboratório? 
Não existe nenhum limite específico no número de VMs que podem ser criados por laboratório. No entanto, neste momento o laboratório suporta apenas cerca de 40 VMs a executar ao mesmo tempo no armazenamento padrão e 25 VMs a ser executada em simultâneo em armazenamento premium. Estamos atualmente a trabalhar no aumentando estes limites. 

### <a name="how-do-i-share-a-direct-link-to-my-lab"></a>Como posso partilhar uma ligação direta para minha laboratório?

Para partilhar uma ligação direta para os seus utilizadores laboratório pode executar o procedimento seguinte.

1. Navegue para o laboratório no portal do Azure.
2. Copie o URL de laboratório a partir do browser e partilhá-lo com os seus utilizadores laboratório. 

>[AZURE.NOTE] Se os seus utilizadores laboratório são os utilizadores externos com uma [conta MSA](#what-is-a-microsoft-account) e que não pertencem do Active directory da sua empresa, poderá recebem um erro ao navegar para a hiperligação fornecida. Se recebem um erro, indique-lhes clique no nome no canto superior direito do portal do Azure e selecione a pasta onde o laboratório existe a partir da secção de **diretório** do menu.

### <a name="what-is-a-microsoft-account"></a>O que é uma conta Microsoft?

Uma conta Microsoft está a utilizar para quase tudo o que fazer com o Microsoft dispositivos e serviços. É um endereço de e-mail e palavra-passe que utiliza para iniciar sessão Skype, Outlook.com, OneDrive, Windows Phone e Xbox LIVE – e significa: os ficheiros, fotografias, contactos e definições podem seguir a qualquer dispositivo. 

>[AZURE.NOTE] Conta Microsoft utilizado para ser chamado "Windows Live ID".
 
### <a name="my-artifact-failed-during-vm-creation-how-do-i-troubleshoot-it"></a>Meu artefacto falhou durante a criação de VM. Como resolvê-lo? 
Referir-se a mensagem no blogue [como resolver problemas com a falhar artefactos no AzureDevTestLabs](http://www.visualstudiogeeks.com/blog/DevOps/How-to-troubleshoot-failing-artifacts-in-AzureDevTestLabs) - escrito por um dos nossos MVPs - para aprender a obter registos em relação à sua artefacto falhado. 
 
### <a name="why-isnt-my-existing-virtual-network-saving-properly"></a>Por que motivo não está meu virtual rede existente guardar corretamente?  
Uma possibilidade é que o seu nome de rede virtual contém períodos. Se Sim, tente remover os períodos ou substitui-los com hífenes e, em seguida, tente guardar novamente a rede virtual.
