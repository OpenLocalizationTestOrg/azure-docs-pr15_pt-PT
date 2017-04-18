<properties
    pageTitle="Resolução de problemas do Microsoft Azure pilha | Microsoft Azure"
    description="Azure pilha de resolução de problemas."
    services="azure-stack"
    documentationCenter=""
    authors="heathl17"
    manager="byronr"
    editor=""/>

<tags
    ms.service="azure-stack"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/13/2016"
    ms.author="helaw"/>

# <a name="microsoft-azure-stack-troubleshooting"></a>Resolução de problemas do Microsoft Azure pilha

Este artigo fornece informações de resolução de problemas comuns para Azure pilha.

A melhor experiência, certifique-se de que o seu ambiente de implementação cumpre todos os [requisitos](azure-stack-deploy.md) e [preparações](azure-stack-run-powershell-script.md) antes de instalar. 

As recomendações para a resolução de problemas que são descritos nesta secção derivem das várias origens e poderão ou poderão não resolver o problema específico. Se Ocorreu um problema não documentado, certifique-se verificar o [Fórum do Azure pilha MSDN](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack) para obter suporte adicional e informações adicionais.

Exemplos de código são fornecidos como está e não não possível garantir os resultados esperados. Esta secção está sujeitas edições frequentes e atualizações estão implementadas melhorias para o produto.

  

## <a name="known-issues"></a>Problemas conhecidos

 - Poderá ver os seguintes não terminar erros durante a implementação, que afetam o sucesso implementação:
     - "Não é reconhecido o termo 'C:\WinRM\Start-Logging.ps1'"
     - "EceAction invocar: não é possível indexar para uma matriz nula" 
     - "InvokeEceAction: não é possível vincular argumento ao parâmetro 'Mensagem' porque é uma cadeia vazia."
 - Poderá ver implementação falhar no passo 60.61.93 com um erro "Aplicação com identificador 'URI' não encontrado". Este comportamento é devido a forma como as aplicações são registadas no Azure Active Directory.  Se receber este erro, continue para [voltar a executar o script de instalação](azure-stack-rerun-deploy.md) a partir do passo 60.61.93 até implementação está concluída.
 - Irá ver as que o recurso de **Conjunto de disponibilidade** no mercado é apresentada na categoria **VirtualMachine processador** – este aspeto é apenas um problema cosmético.
 - Quando criar uma nova máquina virtual no portal do, no passo **Noções básicas** , a opção de armazenamento assume a predefinição SSD.  Esta definição tem de ser alterada para disco ou no **tamanho do** passo de implementação de VM, não verá tamanhos de VM disponíveis para selecionar e continuar a implementação. 
 - Irá ver por predefinição na VM MAS CON01 já não são instalados AzureRM PowerShell módulos (no TP1 este foi denominado ClientVM). Este comportamento é por predefinição, porque existe um método alternativo para [instalar estes módulos e ligar](azure-stack-connect-powershell.md).  
 - Irá ver as que o fornecedor de recurso **Microsoft.Insights** não está registado automaticamente para subscrições do inquilino. Se gostaria ver dados de monitorização para uma VM implementada como um inquilino, execute o seguinte comando do PowerShell (depois de [instalar e ligar-se](azure-stack-connect-powershell.md) como um inquilino): 
       
        Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Insights 

 - Irá ver exportar funcionalidade no portal para grupos de recursos, no entanto, sem texto é apresentado e disponível para exportar.      
 - Pode iniciar uma implementação de recursos de armazenamento maiores que quota disponível.  Falha esta implementação e os recursos de conta serão suspensa.  Existem duas opções remediação disponíveis:
     - Administrador de serviços pode aumentar a quota, apesar das alterações não entre em vigor imediatamente e frequentemente demorar a uma hora a serem propagadas.
     - Administrador de serviços pode criar um plano do suplemento com quota adicional que o inquilino, em seguida, pode adicionar à subscrição.
 - Ao utilizar o portal para criar VMs em ambientes de pilha de Azure com identidade no 'Azure - China', não verá os tamanhos de VM disponíveis para selecionar no passo de implementação de VM **tamanho** e não conseguirão continuar a implementação.
 - Poderá ver uma falha de implementação no portal do, quando a VM realmente tem implementado com êxito.
 - Quando elimina um plano, oferta ou subscrição, VMs não podem ser eliminadas.
 - Irá ver as extensões de VM no mercado.
 - Não é possível implementar uma VM a partir de uma imagem VM guardada.
 - Inquilinos poderão ver os serviços que não são incluídos na sua subscrição.  Quando tentarem implementar estes recursos inquilinos, recebem um erro.  Exemplo: Subscrição inquilino inclui apenas recursos de armazenamento.  Inquilino irá ver a opção para criar outros recursos como VMs.  Neste cenário, quando tenta um inquilino implementar uma VM recebem uma mensagem a indicar que a VM não pode ser criada. 
 - Ao instalar o TP2, não deve activar anfitrião do sistema operativo no VHD fornecido onde vai executar o script de configuração do Azure pilha ou poderá receber um erro mensagens Windows indicação vai expirar em breve.


## <a name="deployment"></a>Implementação

### <a name="deployment-failure"></a>Falha de implementação
Se ocorrer uma falha durante a instalação, o instalador do Azure pilha permite-lhe continuar a uma falha na instalação ao seguir os [passos de implementação de voltar a executar](azure-stack-rerun-deploy.md).

### <a name="at-the-end-of-the-deployment-the-powershell-session-is-still-open-and-doesnt-show-any-output"></a>No final da implementação, a sessão de PowerShell ainda está aberta e não mostra qualquer saída

Este comportamento é provavelmente apenas o resultado do comportamento predefinido de uma janela de comando do PowerShell, quando tiver sido selecionada. A implementação de conceito tem realmente foi concluída com êxito mas o script foi colocada em pausa quando seleciona a janela. Pode verificar que este é o caso, procurando pela palavra "select" na barra de título da janela do comando.  Prima a tecla ESC para anular a seleção de-lo e, deve ser apresentada a mensagem de conclusão depois de-lo.

## <a name="templates"></a>Modelos

### <a name="azure-template-wont-deploy-to-azure-stack"></a>Modelo do Azure não implementar a pilha de Azure

Certifique-se de que:

- O modelo tem de utilizar um serviço do Microsoft Azure que já se encontra disponível ou na pré-visualização na pilha de Azure.
- As APIs utilizadas para um recurso específico são suportadas pela instância do Azure pilha local e que está a filtrar uma localização válida ("local" no Azure pilha técnico pré-visualização (TP) 2, vs "Leste dos EUA" ou "Sul Índia" no Azure).
- Leia [Este artigo](https://github.com/Azure/AzureStack-QuickStart-Templates/blob/master/README.md) sobre os cmdlets AzureRmResourceGroupDeployment de teste, qual capturas pequenas diferenças na sintaxe de Gestor de recursos do Azure.

Também pode utilizar os modelos de pilha de Azure fornecidos no [repositório GitHub](http://aka.ms/AzureStackGitHub/) para o ajudar a começar a utilizar.

## <a name="virtual-machines"></a>Máquinas virtuais

### <a name="after-starting-my-microsoft-azure-stack-poc-host-all-my-tenants-vms-are-gone-from-hyper-v-manager-and-come-back-automatically-after-waiting-a-bit"></a>Depois de iniciar o anfitrião do meu conceito de pilha do Microsoft Azure, todos os meus inquilinos VMs desaparece do Gestor de Hyper-V e voltar atrás automaticamente após um pouco a aguardar?

Como o sistema de surgir voltar a subsystem de armazenamento e a necessidade de RPs para determinar consistência. O tempo necessário depende do hardware e da especificação para eletrónico a ser utilizado, mas pode ser algum tempo após reiniciar o computador do anfitrião do inquilino VMs voltar atrás e será reconhecido.

### <a name="i-have-deleted-some-virtual-machines-but-still-see-the-vhd-files-on-disk-is-this-behavior-expected"></a>Ter eliminado algumas máquinas virtuais, mas continuar a ver os ficheiros VHD no disco. Este comportamento esperado

Sim, este é o comportamento esperado. Foi concebido desta forma porque:

- Quando elimina uma VM, VHDs não são eliminados. Discos são separados recursos no grupo de recursos.
- Quando uma conta de armazenamento é eliminada, a eliminação é visível imediatamente através do Azure Gestor de recursos (portal, PowerShell), mas os discos podem conter ainda são mantidos no armazenamento até que a recolha de lixo é executado.

Se vir VHDs "órfão", é importante saber se fazem parte da pasta para uma conta de armazenamento que foi eliminada. Se a conta de armazenamento não foi eliminada, é normal que ainda estão lá.

Pode ler mais sobre como configurar o limiar de retenção no [Gerir contas de armazenamento](azure-stack-manage-storage-accounts.md).

## <a name="installation-steps"></a>Passos de instalação
As seguintes informações sobre os passos de instalação do Azure pilha poderão ser útil para fins de resolução de problemas:  

| Índice remissivo | Nome | Descrição|
| ----- | ----- | -----|
|0,11 | DADOS (DEP) Validar máquinas físicas | Validar o hardware e configuração do SO em nós físicas. |
| 0,12 | DADOS (DEP) Configurar a rede máquinas física para conceito | Configurar parâmetros de rede virtual e interfaces. |
| 0.14 | DADOS (DEP) Implementar o domínio | Implemente o Active Directory na máquina Virtual. |
| 0,15 | DADOS (DEP) Configurar o servidor de domínio | Configure o servidor de domínio com grupos de segurança etc. |
| 0,16 | DADOS (DEP) Configurar máquina física | Configure a rede, aderir ao domínio e administradores local do programa de configuração. |
| 0.18 | (PARAR) Configurar Cluster de armazenamento | Criar cluster de armazenamento, criar um servidor de conjunto de dados e ficheiro de armazenamento. |
| 0.19 | (CPI) Infraestrutura de ferro de configuração | Configure os pré-requisitos para a implementação de ferro. |
| 0.21 | (LÍQUIDA) Configuração BGP e NAT | Instala BGP e NAT - necessário apenas para um nó. |
| 0.22 | (LÍQUIDA) Configurar NAT e do servidor de horas | Sincroniza o servidor de horas e configura entradas NAT. |
| 40.41 | (CPI) Criar VMs convidado | Crie a gestão VMs. |
| 40.42 | (FBI) Configurar o PowerShell JEA | Configure o PowerShell JEA para todas as funções. |
| 40.43 | (FBI) Configurar o Azure autoridade de certificação de pilha | Instala autoridade de certificação de pilha Azure. |
| 40.44 | (FBI) Configurar autoridade de certificação de pilha Azure | Configura autoridade de certificação de pilha Azure. |
| 40.45 | (LÍQUIDA) Configurar o NC no VMs | Instala NC num VMs convidado |
| 40.46 | (LÍQUIDA) Configurar o NC no VMs | Configurar o NC no VMs convidado |
| 40.47 | (LÍQUIDA) Configurar o convidado VMs | Configure a gestão VMs com NC ACL. |
| 60.61.81 | (FBI) Implementar pilha Azure ferro simultaneamente serviços - FabricRing pré-requisito | Cria VIP para FabricRing |
| 60.61.82 | (FBI) Implementar os serviços de pilha Azure ferro em simultâneo - implementar ferro em simultâneo Cluster | Instala e configura o Azure pilha ferro em simultâneo Cluster. |
| 60.61.83 | (FBI) Implementar extensões de administrador para fornecedores de recursos | Instalar as extensões de administrador para fornecedores de recursos |
| 60.61.84 | (ACS) Configure o armazenamento Azure consistentes no nível de nó. | Instala e configura o Azure consistentes armazenamento no nível de nó. |
| 60.61.85 | (ACS) Configure o armazenamento Azure consistentes no nível cluster. | Instala e configura o Azure consistentes armazenamento no nível cluster. |
| 60.61.86 | (FBI) Implementar pilha Azure ferro em simultâneo controlador serviços - pré-requisito | Pré-requisitos para InfraServiceController |
| 60.61.87 | (FBI) Implementar pilha Azure ferro em simultâneo controlador serviços - pré-requisito | Pré-requisitos para CPI |
| 60.61.88 | (FBI) Implementar pilha Azure ferro em simultâneo controlador serviços - pré-requisito | Pré-requisitos para ASAppGateway |
| 60.61.89 | (FBI) Implementar pilha Azure ferro em simultâneo controlador serviços - pré-requisito | Pré-requisitos para controlador de armazenamento |
| 60.61.90 | (FBI) Implementar pilha Azure ferro em simultâneo controlador serviços - pré-requisito | Pré-requisitos para HealthMonitoring |
| 60.61.91 | (FBI) Implementar pilha Azure ferro em simultâneo controlador serviços - pré-requisito | Pré-requisitos da Comissão Económica |
| 60.61.92 | (FBI) Implementar pilha Azure ferro em simultâneo controlador serviços - pré-requisito | Pré-requisitos para PMM |
| 60.61.93 | (Katal) Criar AzureStack serviço principais | Crie aplicações de Azure Graph e principais de serviço no AAD. |
| 60.61.94 | (LÍQUIDA) Configuração GW VMs | Instala GW no VMs convidado. |
| 60.61.95 | (LÍQUIDA) Configurar GW VMs | Configura GW VMs o convidado. |
| 60.61.96 | (LÍQUIDA) Implementar iDNS em anfitriões | Implementar iDNS em anfitriões de infraestrutura |
| 60.61.97 | (LÍQUIDA) Configurar iDNS | Configurar iDNS função |
| 60.61.98 | (FBI) Configuração WSUS VMs | Instala o servidor WSUS no VMs convidado. |
| 60.61.99 | (FBI) Configurar WSUS VMs | Configura servidor WSUS VMs o convidado. |
| 60.61.100 | (FBI) Configuração do Azure SQL VMs | Instala o Azure SQL server no VMs convidado |
| 60.61.101 | (Katal) Pré-requisitos para foi VMs de configuração. | Configura os pré-requisitos para a Microsoft Azure pilha no VMs convidado. |
| 60.61.102 | (Katal) Configuração foi VMs | Instala o Microsoft Azure pilha no VMs convidado. |
| 60.120.121 | (FBI) Implementar fornecedores de recursos e controladores | Controladores e instalações recurso fornecedores |
| 60.120.121 | (FBI) Implementar fornecedores de recursos e controladores | Controladores e instalações recurso fornecedores |
| 60.120.121 | (FBI) Implementar fornecedores de recursos e controladores | Controladores e instalações recurso fornecedores |
| 60.120.121 | (FBI) Implementar controladores de fornecedores de recursos e | Controladores e instalações recurso fornecedores |
| 60.120.121 | (FBI) Implementar controladores de fornecedores de recursos e | Fornecedores de recurso instalações e controladores de |
| 60.120.121 | (FBI) Implementar controladores de fornecedores de recursos e | Fornecedores de recurso instalações e controladores de |
| 60.120.121 | (FBI) Implementar fornecedores de recursos e controladores | Controladores e instalações recurso fornecedores |
| 60.120.121 | (FBI) Implementar fornecedores de recursos e controladores | Controladores e instalações recurso fornecedores |
| 60.120.122 | (FBI) Configuração do controlador de | Configura controladores |
| 60.120.123 | (Katal) Configurar foi VMs | Configura Microsoft Azure pilha VMs o convidado. |
| 60.120.124 | (Katal) Pilha Azure AAD configuração. | Configura pilha Azure com Azure AD. |
| 60.120.125 | (Katal) Instalar o ADFS | Instala serviços de Federação do Active Directory (ADFS) |
| 60.120.126 | (Katal) Instalar o ADFS/Graph | Instalações pilha Azure Graph |
| 60.120.127 | (Katal) Configurar o ADFS | Configura serviços de Federação do Active Directory (ADFS) |
| 60.140.141 | (FBI) Configurar SRP | Configura o fornecedor de recursos de armazenamento |
| 60.140.142 | (ACS) Configure o armazenamento Azure consistentes. | Configura armazenamento Azure consistentes. |
| 60.140.143 | (FBI) Criar contas de armazenamento | Crie todas as contas de armazenamento para ser utilizado por fornecedores diferentes. |
| 60.140.144 | (FBI) Registe-se a utilização no SRP | Registe-se a utilização do fornecedor de armazenamento. |
| 60.140.145 | (CPI) Migrar VMs criados, anfitriões e Cluster para CPI | Migra objectos de criada VMs, anfitriões e Cluster para CPI |
| 60.140.146 | (FBI) Configurar o Windows Defender | Configura o Windows Defender |
| 60.160.161 | (SEG) Configurar o agente de monitorização | Configura o agente de monitorização |
| 60.160.162 | (FBI) NRP pré-requisito | Pré-requisitos de NRP instalações |
| 60.160.163 | (FBI) Implementação NRP | Instalações NRP  |
| 60.160.164 | (FBI) Configuração de NRP | Configura NRP |
| 60.160.165 | (FBI) CRP pré-requisito | Pré-requisitos de CRP instalações |
| 60.160.166 | (FBI) Implementação CRP | Instalações CRP |
| 60.160.167 | (FBI) Configuração de CRP | Configura CRP |
| 60.160.168 | (FBI) FRP pré-requisito | Pré-requisitos de FRP instalações |
| 60.160.169 | (FBI) Implementação de FRP | Instalações FRP  |
| 60.160.170 | (FBI) Configuração de FRP | Configura FRP |
| 60.160.174 | (FBI) URP pré-requisito | Pré-requisitos de URP instalações |
| 60.160.175 | (FBI) Implementação de URP | Instalações URP  |
| 60.160.176 | (FBI) Configuração de URP | Configura URP |
| 60.160.171 | (FBI) Pré-requisito de conformidade | Pré-requisitos de conformidade instalações |
| 60.160.172 | (FBI) Implementação de conformidade | Instalações conformidade  |
| 60.160.173 | (FBI) Configuração de conformidade | Configura conformidade |
| 60.160.177 | (KV) KeyVault pré-requisito | Pré-requisitos de KeyVault instalações |
| 60.160.178 | (KV) Implementação de KeyVault | Instalações KeyVault  |
| 60.160.179 | (KV) Configuração de KeyVault | Configura KeyVault | 
| 60.190.191 | (FBI) Configurar o Gallery | Configurar o Gallery |
| 60.190.192 | (FBI) Configurar os serviços de ferro em simultâneo | Configurar os serviços de ferro em simultâneo |
| 60.221 | (FBI) Configuração da consola VMs | Instala consola do servidor no VMs convidado. |
| 60.222 | (FBI) Configuração da consola VMs | Mova o conteúdo DVM na consola VM. |
| 251. | Preparar para futura anfitrião for reiniciado | Definir a política de reiniciar o computador |


## <a name="next-steps"></a>Próximos passos

[Perguntas mais frequentes](azure-stack-FAQ.md)
