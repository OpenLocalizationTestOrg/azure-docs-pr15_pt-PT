<properties
   pageTitle="Segurança de dados do Centro de segurança do Azure | Microsoft Azure"
   description="Este documento explica como os dados são geridos e protegidos no Centro de segurança do Azure."
   services="security-center"
   documentationCenter="na"
   authors="YuriDio"
   manager="swadhwa"
   editor=""/>

<tags
   ms.service="security-center"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/25/2016"
   ms.author="yurid"/>

# <a name="azure-security-center-data-security"></a>Segurança de dados do Centro de segurança do Azure
Para ajudar os clientes prevenir, detetar e responder a ameaças, Centro de segurança do Azure recolhe e processa dados sobre os recursos Azure, incluindo informações de configuração, metadados, registos de eventos, uma falha de sistema ficheiros de informação e muito mais. Oferecemos fortes compromissos para proteger a privacidade e segurança de dados. Microsoft cumpre as diretrizes segurança e conformidade estritamente — a partir de codificação de funcionamento de um serviço. 

Este artigo explica como os dados são geridos e protegidos no Centro de segurança do Azure.

## <a name="data-sources"></a>Origens de dados

Centro de segurança do Azure analisa dados a partir das seguintes origens:

- Serviços de Azure: Lê informações sobre a configuração dos serviços Azure implementou por comunicar com o fornecedor de recursos desse serviço.
- O tráfego de rede: Lê amostradas metadados de tráfego da infraestrutura da Microsoft, tal como origem/destino IP/porta, tamanho do pacote, de rede e protocolo de rede.
- Soluções de parceiros: Recolhe alertas de segurança de soluções de parceiros integradas, tais como firewalls e antimalware soluções. Estes dados são armazenados numa armazenamento do Centro de segurança do Azure, atualmente localizado nos Estados Unidos.
- Máquinas virtuais: Centro de segurança do Azure pode recolher informações de configuração e informações sobre eventos de segurança, tal como eventos do Windows e de auditoria registos, IIS registos, mensagens do sistema e falha de sistema de ficheiros de informação a partir do seu máquinas virtuais utilizando agentes de recolha de dados. Consulte a secção "Gerir recolha de dados" abaixo para obter detalhes adicionais.  

Além disso, as informações sobre o estado de funcionamento de segurança, alertas de segurança e recomendações são armazenadas no armazenamento do Centro de segurança do Azure, atualmente localizado nos Estados Unidos. Esta informação poderá incluir informações relacionados com a configuração e eventos de segurança recolhidos a partir do seu máquinas virtuais, conforme necessário para fornecer-lhe o alerta de segurança, recomendação ou estado de funcionamento de segurança.

## <a name="data-protection"></a>Proteção de dados

**Separação de dados**: os dados são mantidos logicamente separados cada componente em todo o serviço. Todos os dados está etiquetado por organização. Este etiquetagem persistir durante o ciclo de vida de dados e é aplicada em cada camada do serviço. Além disso, os dados recolhidos a partir do seu máquinas virtuais são armazenados na sua conta de armazenamento (s).

**Acesso a dados**: para fornecer recomendações de segurança e investigar potenciais ameaças de segurança, pessoal da Microsoft poderá aceder às informações recolhidas ou analisados por Azure serviços, incluindo ficheiros de informação de falha de sistema. Ficheiros de informação de falha de sistema e eventos de criação de processo inadvertidamente podem incluir dados de cliente ou pessoais dados a partir do seu máquinas virtuais. Vamos adira os [Termos do Microsoft Online Services](http://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31) e a [Declaração de privacidade](https://www.microsoft.com/privacystatement/en-us/OnlineServices/Default.aspx), que estado que Microsoft não irá utilizar os dados de cliente ou obtêm informações de-la para qualquer fins comerciais publicidade ou semelhantes. Apenas utilizamos os dados do cliente, conforme necessário para fornecer-lhe Azure serviços, incluindo efeitos compatíveis com o fornecer desses serviços. Manter todos os direitos a dados do cliente.

**Utilização de dados**: a Microsoft utiliza padrões e intelligence ameaça visto através de múltiplos inquilinos para melhorar os nossos capacidades prevenção e detecção; Vamos fazê-lo em conformidade com os compromissos de privacidade descritos na nossa [Declaração de privacidade](https://www.microsoft.com/privacystatement/en-us/OnlineServices/Default.aspx).

**Localização de dados**: for especificada uma conta de armazenamento para cada região que estão a ser executado máquinas virtuais. Permite-lhe armazenar dados na mesma região como a máquina virtual a partir do qual os dados são recolhidos. Estes dados, incluindo ficheiros de informação de falha de sistema, serão armazenados forma persistente na sua conta de armazenamento. O serviço também armazena informações sobre os alertas de segurança, incluindo os alertas do soluções de parceiros integrada, recomendações e estado de funcionamento de segurança no armazenamento do Centro de segurança do Azure, atualmente localizado nos Estados Unidos.

## <a name="managing-data-collection-from-virtual-machines"></a>Gestão de recolha de dados a partir do máquinas virtuais

Quando escolhe ativar o Centro de segurança do Azure, recolha de dados está ativada para cada uma das suas subscrições. Pode desativar a recolha de dados na secção "Política de segurança" do Dashboard do Centro de segurança do Azure. Quando a recolha de dados estiver ativada, o Centro de segurança do Azure disposições o agente de monitorização do Azure em todos os existentes suportadas máquinas virtuais e quaisquer novos ficheiros que são criados. A extensão de monitorização de segurança do Azure verifica a existência de relacionadas com segurança vários configurações e eventos controla-lo no [Rastreio de evento para o Windows](https://msdn.microsoft.com/library/windows/desktop/bb968803.aspx) (ETW). Além disso, o sistema operativo irá elevar eventos de registo de eventos durante a execução do computador a executar. Exemplos de tais dados são: tipo de sistema operativo e versão, operativo registos do sistema (registos de eventos do Windows), com o processos, nome do computador, endereços IP, tem sessão iniciada no utilizador e o ID do inquilino. O agente de monitorização do Azure lê entradas de registo de eventos e ETW controla e copia-los à sua conta de armazenamento para uma análise. 

Uma conta de armazenamento está especificada para cada região em que tenha máquinas virtuais em execução, onde os dados recolhidos a partir do máquinas virtuais no que está armazenado mesma região. Isto torna mais fácil manter os dados na mesma área geográfica para fins de soberania de dados e privacidade. Pode configurar contas de armazenamento para cada região na secção "Política de segurança" do Dashboard do Centro de segurança do Azure.

O agente de monitorização do Azure também copia os ficheiros de informação de falha de sistema para a sua conta de armazenamento.  Centro de segurança do Azure efémeras cópias dos seus ficheiros de informações de estado de falha de sistema de recolhe e analisa-os para fornecer provas de tentativas de exploração e compromete efetuada com êxito.  Centro de segurança do Azure executa esta análise dentro da mesma região geográfica como a conta de armazenamento e elimina as cópias efémeras quando a análise está concluída.

Pode desativar a recolha de dados máquinas virtuais em qualquer altura, que irá remover quaisquer agentes de monitorização instalado anteriormente ao centro de segurança do Azure.


## <a name="see-also"></a>Consulte também

Neste documento, o que aprendeu como dados são geridos e protegidos no Centro de segurança do Azure. Para saber mais sobre o Centro de segurança do Azure, consulte o artigo:

- [Guia de operações de planeamento de centro de segurança do Azure e](security-center-planning-and-operations-guide.md) — obter informações sobre como planear e compreenda as considerações de estrutura para adotar o Centro de segurança do Azure.
- [Estado de funcionamento de segurança no Centro de segurança do Azure de monitorização](security-center-monitoring.md) — Saiba como monitorizar o estado de funcionamento dos seus recursos Azure
- [Gerir e a responder a alertas de segurança no Centro de segurança do Azure](security-center-managing-and-responding-alerts.md) — Saiba como gerir e responder a alertas de segurança
- [Monitorização soluções de parceiros com o Centro de segurança do Azure](security-center-partner-solutions.md) — Saiba como monitorizar o estado de funcionamento de soluções de parceiros.
- [Perguntas mais frequentes do Centro de segurança do Azure](security-center-faq.md) — localizar perguntas mais frequentes sobre como utilizar o serviço
- [Blogue de segurança do Azure](http://blogs.msdn.com/b/azuresecurity/) — localizar mensagens de blogue sobre Azure segurança e conformidade
