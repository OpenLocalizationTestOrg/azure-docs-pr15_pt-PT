<properties
   pageTitle="Segurança de conjunto de aplicações de gestão de operações e segurança dos dados de auditoria solução | Microsoft Azure"
   description="Este documento explica como os dados são geridos e protegidos em segurança de conjunto de aplicações de gestão de operações e solução de auditoria."
   services="operations-management-suite"
   documentationCenter="na"
   authors="YuriDio"
   manager="swadhwa"
   editor=""/>

<tags
   ms.service="operations-management-suite"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/15/2016"
   ms.author="yurid"/>

# <a name="operations-management-suite-security-and-audit-solution-data-security"></a>Segurança de dados de solução de segurança de conjunto de aplicações de gestão de operações e de auditoria

Para ajudar os clientes prevenir, detetar e responder a ameaças, [segurança de conjunto de aplicações (OMS) de gestão de operações e solução de auditoria](operations-management-suite-overview.md) recolhe e processa dados sobre os recursos, que inclui:

- Registo de eventos de segurança
- Eventos de rastreio para Windows (ETW) do evento
- Eventos de auditoria AppLocker
- Registo de Firewall do Windows
- Eventos de ameaça de análise avançados
- Resultados da avaliação do plano base
- Resultados da avaliação de antimalware
- Resultados da avaliação de atualização/patches
- Fluxos de Syslogs explicitamente estão activados no agente do

Oferecemos fortes compromissos para proteger a privacidade e segurança de dados. Microsoft cumpre as diretrizes segurança e conformidade estritamente — a partir de codificação de funcionamento de um serviço.
Este artigo explica como os dados são geridos e protegidos no OMS segurança e solução de auditoria.

## <a name="data-sources"></a>Origens de dados

Segurança OMS e solução de auditoria analisar os dados a partir do máquinas virtuais e computadores físicos onde está instalado o agente OMS. Segurança OMS e de auditoria solução podem recolher informações de configuração sobre eventos de segurança, tal como eventos do Windows, registos de auditoria, registos do IIS e mensagens do sistema. Exemplos de tais dados são: tipo de sistema operativo e versão, processos, nome do computador, endereços IP, a executar sessão iniciada no utilizador e o ID do inquilino.  

## <a name="data-protection"></a>Proteção de dados

**Separação de dados**: os dados são mantidos logicamente separados cada componente em todo o serviço. Todos os dados está etiquetado por organização. Este etiquetagem persistir durante o ciclo de vida de dados e é aplicada em cada camada do serviço. 

**Acesso a dados**: para fornecer recomendações de segurança e investigar potenciais ameaças de segurança, pessoal da Microsoft poderá aceder às informações recolhidas ou analisadas pelos serviços. Vamos adira os [Termos do Microsoft Online Services](http://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31) e a [Declaração de privacidade](https://www.microsoft.com/privacystatement/en-us/OnlineServices/Default.aspx), que estado que Microsoft não irá utilizar os dados de cliente ou obtêm informações de-la para qualquer fins comerciais publicidade ou semelhantes. Para fornecer recomendações de segurança e investigar potenciais ameaças de segurança, pessoal da Microsoft poderá aceder às informações recolhidas ou analisadas pelos serviços. Apenas utilizamos os dados do cliente, conforme necessário para fornecer-lhe Azure serviços, incluindo efeitos compatíveis com o fornecer desses serviços. Manter todos os direitos para os seus próprios dados.

**Utilização de dados**: a Microsoft utiliza padrões e intelligence ameaça visto através de múltiplos inquilinos para melhorar os nossos capacidades prevenção e detecção; Vamos fazê-lo em conformidade com os compromissos de privacidade descritos na nossa [Declaração de privacidade](https://www.microsoft.com/privacystatement/en-us/OnlineServices/Default.aspx).

> [AZURE.NOTE] Localização de dados está configurada ao nível da área de trabalho OMS, durante a criação de área de trabalho, o que faz parte do processo de configuração de segurança de OMS e auditoria inicial.

## <a name="see-also"></a>Consulte também

Neste documento, o que aprendeu como dados são geridos e protegidos no OMS. Para saber mais sobre solução OMS segurança e de auditoria, consulte o artigo:

- [Descrição geral de gestão conjunto de aplicações (OMS) de operações](operations-management-suite-overview.md)
- [Monitorização e a responder a alertas de segurança na segurança de conjunto de aplicações de gestão de operações e solução de auditoria](oms-security-responding-alerts.md)
- [Monitorizar recursos na segurança de conjunto de aplicações de gestão de operações e solução de auditoria](oms-security-monitoring-resources.md)

