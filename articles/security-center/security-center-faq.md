<properties
   pageTitle="Perguntas mais frequentes (FAQ sobre) o Centro de segurança Azure | Microsoft Azure"
   description="Estas FAQs respondem a questões sobre o Centro de segurança do Azure."
   services="security-center"
   documentationCenter="na"
   authors="TerryLanfear"
   manager="MBaldwin"
   editor=""/>

<tags
   ms.service="security-center"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/27/2016"
   ms.author="terrylan"/>

# <a name="azure-security-center-frequently-asked-questions-faq"></a>Perguntas mais frequentes (FAQ sobre) o Centro de segurança do Azure

Este artigo responde a questões sobre Azure Centro de segurança, um serviço que o ajuda a impedir que, detetar e responder a ameaças com maior visibilidade do e controlo sobre a segurança dos seus recursos do Microsoft Azure.

## <a name="general-questions"></a>Perguntas gerais

### <a name="what-is-azure-security-center"></a>O que é o Centro de segurança do Azure?
Centro de segurança do Azure ajuda-o a evitar, detetar e responder a ameaças com maior visibilidade do e controlo sobre a segurança dos seus recursos Azure. Fornece a segurança integrada monitorização e gestão de políticas entre subscrições do seu, ajuda a Deteta ameaças caso contrário, aceda não ser e funciona com uma Ecossistema vasto de soluções de segurança.

### <a name="how-do-i-get-azure-security-center"></a>Como obtenho o Centro de segurança do Azure?
Centro de segurança do Azure está ativado com a sua subscrição do Microsoft Azure e acedido a partir do [Azure portal](https://azure.microsoft.com/features/azure-portal/). ([Inicie sessão portal do](https://portal.azure.com), selecione **Procurar**e desloque-se para **O Centro de segurança**).  

## <a name="billing"></a>Faturação

### <a name="how-does-billing-work-for-azure-security-center"></a>Como funciona a faturação trabalho para o Centro de segurança do Azure?
Centro de segurança é oferecido no duas camadas: gratuito e padrão.

A camada gratuita permite-lhe definir políticas de segurança e receber alertas de segurança, incidentes e recomendações orientá-lo durante o processo de configuração controlos necessários. Com a camada gratuita também pode monitorizar o estado de segurança dos seus recursos Azure e soluções de parceiros integradas com a sua subscrição Azure.

A camada padrão fornece a DLP de sinal de adição de funcionalidades avançadas de camada livre: ameaça de informações da empresa, análise comportamento, análise de falha de sistema e deteção de anomalia. Uma avaliação gratuita de 90 dias da camada padrão está disponível. Para atualizar, selecione a camada preços na [política de segurança](security-center-policies.md#setting-security-policies-for-subscriptions). Para obter mais informações, consulte a [preços do Centro de segurança](security-center-pricing.md) .

## <a name="data-collection"></a>Recolha de dados

Centro de segurança recolhe dados a partir do seu máquinas virtuais para avaliar o seu estado de segurança, fornecer as recomendações de segurança e alertá-lo para ameaças. Quando pela primeira vez aceder ao centro de segurança, recolha de dados é activada em máquinas virtuais todos na sua subscrição. Recolha de dados é recomendada, mas pode optar ativamente ao [desativar a recolha de dados](#how-do-i-disable-data-collection) na política do Centro de segurança.

### <a name="how-do-i-disable-data-collection"></a>Como desactivar a recolha de dados?

Pode desativar **a recolha de dados** para uma subscrição na política de segurança em qualquer altura. ([Para iniciar sessão no portal do Azure](https://portal.azure.com), selecione **Procurar**, selecione **Centro de segurança**e selecione **política**.)  Quando seleciona uma subscrição, um novo pá abre e fornece-lhe a opção para desativar a **recolha de dados** . Selecione a opção **Eliminar agentes de** no Friso superior para remover agentes de máquinas virtuais existentes.

> [AZURE.NOTE] Políticas de segurança podem ser definidas no nível de subscrição Azure e nível de grupo de recursos, mas tem de selecionar uma subscrição para desativar a recolha de dados.

### <a name="how-do-i-enable-data-collection"></a>Como ativar a recolha de dados?
Pode ativar a recolha de dados para as suas subscrições Azure na política de segurança. Para ativar a recolha de dados, [Inicie sessão no portal do Azure](https://portal.azure.com), selecione **Procurar**, selecione **Centro de segurança**e selecione **política**. **Recolha de dados** **no** e configurar contas de armazenamento onde pretende que os dados para serem recolhidos para (consulte a pergunta "[onde os meus dados estão armazenados?](#where-is-my-data-stored)"). Quando a **recolha de dados** é ativada, recolhe automaticamente informações de configuração e eventos de segurança de todos os máquinas virtuais suportadas na subscrição.

> [AZURE.NOTE] Políticas de segurança podem ser definidas no nível de subscrição Azure e nível de grupo de recursos mas configuração de recolha de dados ocorre apenas ao nível subscrição.

### <a name="what-happens-when-data-collection-is-enabled"></a>O que acontece quando é activada recolha de dados?
Recolha de dados é ativada via o agente de monitorização do Azure e a extensão de monitorização de segurança do Azure. A extensão de monitorização de segurança do Azure verifica a existência de vários relevantes a configuração de segurança e envia-o para rastreios de [Rastreio de evento para o Windows](https://msdn.microsoft.com/library/windows/desktop/bb968803.aspx) (ETW). Além disso, o sistema operativo cria entradas de registo de eventos.  O agente de monitorização do Azure lê entradas de registo de eventos e ETW controla e copia-los à sua conta de armazenamento para uma análise.  Esta é a conta de armazenamento configurado na política de segurança. Para mais informações sobre a conta de armazenamento, consulte o artigo pergunta "[onde os meus dados estão armazenados?](#where-is-my-data-stored)"

### <a name="does-the-monitoring-agent-or-security-monitoring-extension-impact-the-performance-of-my-servers"></a>É que a extensão do agente de monitorização ou monitorização de segurança afeta o desempenho do meu servidor (es)?
O agente e a extensão consome um valor nominal de recursos do sistema e deverá ter impacto pouco sobre o desempenho.

### <a name="where-is-my-data-stored"></a>Onde estão armazenados os meus dados?
Para cada região no qual vai ter máquinas virtuais em execução, selecione a conta de armazenamento onde os dados recolhidos máquinas virtuais estão armazenados. Isto torna mais fácil manter os dados na mesma área geográfica para fins de soberania de dados e privacidade. Selecione a conta de armazenamento para uma subscrição na política de segurança. ([Para iniciar sessão no portal do Azure](https://portal.azure.com), selecione **Procurar**, selecione **Centro de segurança**e selecione **política**.) Quando clica numa subscrição, é aberta uma nova pá. Selecione **contas de armazenamento de escolher** para selecionar uma região.

> [AZURE.NOTE] Políticas de segurança podem ser definidas no nível de subscrição Azure e nível de grupo de recursos, mas selecionar uma região para a sua conta de armazenamento ocorre apenas ao nível subscrição.

Para saber mais sobre o armazenamento Azure e contas de armazenamento, consulte a [Documentação de armazenamento](https://azure.microsoft.com/documentation/services/storage/) e [contas de armazenamento de sobre Azure](../storage/storage-create-storage-account.md).

## <a name="using-azure-security-center"></a>Utilizar o Centro de segurança do Azure

### <a name="what-is-a-security-policy"></a>O que é uma política de segurança?
Uma política de segurança define o conjunto de controlos que lhe foram recomendados para recursos numa subscrição especificada ou grupo de recursos. No Centro de segurança do Azure, pode definir políticas para as suas subscrições Azure e grupos de recursos de acordo com os requisitos de segurança da sua empresa e o tipo de aplicações ou confidencialidade dos dados de cada subscrição.

Por exemplo, recursos utilizados para o desenvolvimento ou de teste poderão ter os requisitos de segurança diferente daqueles utilizado para aplicações de produção. Da mesma forma, aplicações com dados regulamentados como PII (informação identificativa) precisem de um nível superior de segurança. As políticas de segurança activadas no Centro de segurança do Azure serão a unidade de monitorização e recomendações de segurança. Para saber mais sobre as políticas de segurança, consulte [o estado de funcionamento de segurança no Centro de segurança do Azure de monitorização](security-center-monitoring.md).

> [AZURE.NOTE] Em caso de um conflito entre política de nível de subscrição e a política de nível de grupo de recursos, a política de nível de grupo de recursos terá precedência.

### <a name="who-can-modify-a-security-policy"></a>Quem pode modificar uma política de segurança?
Políticas de segurança estão configuradas para cada subscrição ou grupo de recursos. Para modificar uma política de segurança ao nível subscrição ou nível de grupo de recursos, tem de ser um proprietário ou contribuinte dessa subscrição.

Para saber como configurar uma política de segurança, consulte o artigo [definir políticas de segurança no Centro de segurança do Azure](security-center-policies.md).

### <a name="what-is-a-security-recommendation"></a>O que é uma recomendação de segurança?
Centro de segurança do Azure analisa o estado de segurança dos seus recursos Azure. Quando é identificada potenciais vulnerabilidade de segurança, são criadas recomendações. As recomendações orientá-lo durante o processo de configurar o controlo conforme seja necessário. Exemplos são:

- Aprovisionamento de antimalware para ajudar a identificar e remover software malicioso
- Configurar regras para o tráfego de controlo para máquinas virtuais e [Grupos de segurança de rede](../virtual-network/virtual-networks-nsg.md)
- Aprovisionamento de uma firewall de aplicação web para o ajudar a proteger-se contra ataques de filtragem de aplicações web
- Implementar actualizações do sistema em falta
- Endereçar configurações do sistema operativo que não correspondem os planos base recomendados

Apenas as recomendações que estão activadas no políticas de segurança são apresentadas aqui.

### <a name="how-can-i-see-the-current-security-state-of-my-azure-resources"></a>Como posso ver o estado atual de segurança dos meus recursos Azure?
Um mosaico de **Estado de funcionamento de recursos** sobre o **Centro de segurança** pá mostra a geral postura de segurança do seu ambiente dividido por máquinas virtuais, aplicações web e outros recursos. Cada recurso tem a mostrar um indicador se identificou qualquer potenciais vulnerabilidade de segurança. Clicar no mosaico do Estado de funcionamento de recursos apresenta os recursos e identifica onde é necessária atenção ou podem existir problemas.

### <a name="what-triggers-a-security-alert"></a>O que accionadores um alerta de segurança?
Centro de segurança do Azure automaticamente recolhe, analisa e Rastilhos os dados do registo os recursos do Azure, da rede e soluções de parceiros, como antimalware e firewalls. Quando são detectadas ameaças, um alerta de segurança é criado. Exemplos de deteção de:

- Comprometida máquinas virtuais comunicar com endereços IP maliciosos conhecidos
- Software maligno avançado detetado utilizando o relatório de erros do Windows
- Força bruta ataques máquinas virtuais
- Alertas de segurança de soluções de segurança integrada parceiro como antimalware ou Firewalls de aplicação Web

### <a name="whats-the-difference-between-threats-detected-and-alerted-on-by-microsoft-security-response-center-versus-azure-security-center"></a>O que é a diferença entre ameaças detetado e um alerta pela Microsoft Security Response Center versus Centro de segurança do Azure?
A segurança resposta centro MSRC (Microsoft) executa a monitorização de segurança select da rede Azure e infraestrutura e recebe queixas de informações da empresa e abuse ameaça de terceiros. Quando MSRC torna-se em atenção que os dados do cliente tem sido acedidos por uma festa ilícita ou não autorizada ou que utilize o cliente do Azure não está em conformidade com os termos para uso aceitável, um gestor incidente de segurança notifica o cliente. Normalmente, ocorre notificação ao enviar uma mensagem de e-mail para o contacto ou contactos de segurança especificado no Centro de segurança do Azure ou o proprietário da subscrição Azure se um contacto de segurança não for especificado.

Centro de segurança é um serviço Azure que continuamente monitoriza ambiente Azure do cliente e aplica-se a análise de detetar automaticamente uma vasta gama de atividade potencialmente maliciosa. Estes DLP forem apresentados como alertas de segurança no dashboard do Centro de segurança.

### <a name="how-are-permissions-handled-in-azure-security-center"></a>Como são processadas permissões no Centro de segurança do Azure?
Centro de segurança do Azure suporta o acesso baseado em funções. Para saber mais sobre o controlo de acesso baseado em funções (RBCA) no Azure, consulte o artigo [Baseada em Azure Active Directory funções o controlo de acesso](../active-directory/role-based-access-control-configure.md).

Quando um utilizador abre o Centro de segurança, apenas recomendações e serão vistos alertas relacionados com recursos que o utilizador tem acesso ao. Isto significa que os utilizadores verão apenas itens relacionados com recursos onde o utilizador está atribuído a função de leitor, contribuinte ou proprietário para a subscrição ou grupo de recursos que pertence um recurso.

Se precisar de:

- **Editar uma política de segurança**, tem de ser um proprietário ou contribuinte da subscrição.
- **Aplicar uma recomendação**, tem de ser um proprietário ou contribuinte da subscrição.
- **Ter visibilidade do Estado de segurança através de todas as suas subscrições**, tem de ser um proprietário, contribuinte ou leitor (administrador de TI, a equipa de segurança) de cada subscrição.
- **Ter visibilidade do Estado de segurança dos seus recursos**, tem de ser um grupo de recursos proprietário, contribuinte ou leitor (DevOps).

### <a name="which-azure-resources-are-monitored-by-azure-security-center"></a>Quais os recursos Azure são monitorizados pelo centro de segurança do Azure?
Centro de segurança do Azure monitoriza os seguintes recursos Azure:

- Máquinas virtuais (VMs) (incluindo [Serviços em nuvem](../cloud-services/cloud-services-choose-me.md))
- Redes virtuais Azure
- Serviço do SQL Azure
- Soluções de parceiros integrada com a sua subscrição Azure tal como uma firewall de aplicação web VMs e da [Aplicação de ambiente de serviço](../app-service/app-service-app-service-environments-readme.md)

## <a name="virtual-machines"></a>Máquinas virtuais

### <a name="what-types-of-virtual-machines-will-be-supported"></a>O que tipos de máquinas virtuais serão suportadas?
Monitorização do Estado de funcionamento de segurança e recomendações estão disponíveis para máquinas virtuais (VMs) criadas utilizando tanto a [clássica e modelos de implementação do Gestor de recursos](../azure-classic-rm.md).

VMs suportada do Windows:

- Windows Server 2008 R2
- Windows Server 2012
- Windows Server 2012 R2

VMs Linux suportados:

- Versões de Ubuntu 12.04, 14.04, 16.04
- Debian versões 7, 8
- CentOS versões 6. \*, 7.*
- Versões de vermelho chapéu Enterprise Linux (RHEL) 6. \*, 7.*
- SUSE Linux Enterprise Server (SLES) versões 11. \*, 12.*
- Versões de Linux Oracle 6. \*, 7.*

VMs em execução num serviço de nuvem também são suportados. Funções de web e trabalho em execução no produção faixas são monitorizadas dos serviços apenas na nuvem. Para saber mais sobre o serviço de nuvem, consulte o artigo [Descrição geral de serviços em nuvem](../cloud-services/cloud-services-choose-me.md).

### <a name="why-doesnt-azure-security-center-recognize-the-antimalware-solution-running-on-my-azure-vm"></a>Por que motivo o Centro de segurança do Azure não reconhece a solução antimalware em execução no meu VM Azure?

Centro de segurança do Azure tem apenas visibilidade do antimalware instalado através de extensões Azure. Por exemplo, o Centro de segurança não é possível detetar antimalware foi encontra instalado numa imagem que forneceu ou se tiver instalado antimalware no seu máquinas virtuais utilizando o seus próprio processos (como sistemas de gestão de configuração).

### <a name="why-do-i-get-the-message-missing-scan-data-for-my-vm"></a>Porque é que posso obter a mensagem "Dados de pesquisa em falta" para minha VM?

Pode demorar algum tempo (normalmente, menor que uma hora) para os dados de pesquisa preencher depois de recolha de dados é activada no Centro de segurança do Azure. Pesquisas não irão povoar para VMs num estado parado.

### <a name="why-do-i-get-the-message-vm-agent-is-missing"></a>Porque é que recebo a mensagem "VM agente está em falta?"

O agente VM tem de ser instalado no VMs para ativar a recolha de dados. O agente VM está instalado por predefinição para VMs que são implementadas do Azure Marketplace. Para obter informações sobre como instalar o agente VM em outras VMs, consulte a mensagem [VM agente e extensões](https://azure.microsoft.com/blog/vm-agent-and-extensions-part-2/)do blogue.
