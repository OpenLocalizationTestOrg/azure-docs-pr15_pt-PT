<properties
   pageTitle="Introdução ao centro de segurança do Azure | Microsoft Azure"
   description="Saiba mais sobre o Centro de segurança do Azure, das suas capacidades chaves e como funciona."
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
   ms.date="07/21/2016"
   ms.author="terrylan"/>

# <a name="introduction-to-azure-security-center"></a>Introdução ao centro de segurança do Azure

Saiba mais sobre o Centro de segurança do Azure, das suas capacidades chaves e como funciona.

> [AZURE.NOTE] Este documento apresenta o serviço utilizando uma implementação de exemplo.

## <a name="what-is-azure-security-center"></a>O que é o Centro de segurança do Azure?
 Centro de segurança ajuda-o a evitar, detetar e responder a ameaças com maior visibilidade do e controlo sobre a segurança dos seus recursos Azure. Fornece a segurança integrada monitorização e gestão de políticas entre as subscrições do Azure, ajuda a Deteta ameaças caso contrário, aceda não ser e funciona com uma Ecossistema vasto de soluções de segurança.

##  <a name="key-capabilities"></a>Capacidades de chaves
 Centro de segurança fornece capacidades de prevenção, deteção e resposta que foram criadas ameaça fáceis de utilizar e eficaz para Azure. Capacidades de chaves são:

| | |
|----- |-----|
| Impedir que | Monitoriza o estado de segurança dos seus recursos Azure |
| | Define políticas para as suas subscrições Azure e grupos de recursos com base em requisitos de segurança da sua empresa, os tipos de aplicações que utiliza e a sensibilidade dos seus dados |
| | Utilizações orientadas por política recomendações de segurança para orientar os proprietários de serviço durante o processo de implementação necessário controlos |
| | Rapidamente implementar os serviços de segurança e equipamentos da Microsoft e parceiros |
| Detetar |Automaticamente recolhe e analisa dados de segurança dos recursos do Azure, rede e soluções de parceiros, como antimalware programas e firewalls |
| | Oferece global ameaça informações da empresa a partir de produtos Microsoft e serviços, a unidade Microsoft Crimes Digital (DCU), a segurança resposta centro MSRC (Microsoft) e feeds externos |
| | Aplica-se de análise avançadas, incluindo a aprendizagem automática e a análise de comportamento |
| Responder | Fornece incidentes/alertas de segurança com prioridade alta |
| | Oferece informações para a origem da ataque e recursos afetados |
| | Sugere formas para parar o ataque atual e ajudar a prevenir ataques futuros |

## <a name="introductory-walkthrough"></a>Tutorial introdutório
 Aceder ao centro de segurança a partir do [Azure portal](https://azure.microsoft.com/features/azure-portal/). [Inicie sessão portal do](https://portal.azure.com), selecione **Procurar**e, em seguida, desloque-se para a opção de **Centro de segurança** ou selecione o mosaico do **Centro de segurança** que afixados anteriormente ao dashboard portal.

![Mosaico de segurança no portal do Azure][1]

A partir do Centro de segurança, pode definir políticas de segurança, monitorizar as configurações de segurança e visualizar os alertas de segurança.

### <a name="security-policies"></a>Políticas de segurança

Pode definir políticas para as suas subscrições Azure e grupos de recursos de acordo com os requisitos de segurança da sua empresa. Também pode personalizá-los para os tipos de aplicações que estiver a utilizar ou para a sensibilidade dos dados de cada subscrição. Por exemplo, recursos utilizados para desenvolvimento ou testes poderão ter os requisitos de segurança diferente daqueles utilizado para aplicações de produção. Da mesma forma, aplicações com dados regulamentados como PII precisem de um nível superior de segurança.

> [AZURE.NOTE] Para modificar uma política de segurança ao nível da subscrição ou o nível de grupo de recursos, tem de ser o proprietário da subscrição ou um contribuinte à mesma.

No pá **Centro de segurança** , selecione o mosaico de **política** para obter uma lista das suas subscrições e grupos de recursos.   

![Pá do Centro de segurança][2]

Sobre a **política de segurança** pá, selecione uma subscrição para ver os detalhes da política.

![Subscrição de pá de política de segurança][3]

**Recolha de dados** (consultar acima) permite recolha de dados para uma política de segurança. Ativar fornece:

- Análise diária de todas as suportada máquinas virtuais para monitorização de segurança e recomendações.
- Colecção de eventos de segurança para análise e ameaça detecção.

**Escolher uma conta de armazenamento por região** (consultar acima) permite-lhe escolher, para cada região no qual vai ter máquinas virtuais em execução, a conta de armazenamento onde os dados recolhidos máquinas virtuais estão armazenados. Se não escolher uma conta de armazenamento para cada região, será criado por si. Os dados que são recolhidos estão logicamente isolados a partir de dados dos outros clientes por motivos de segurança.

> [AZURE.NOTE] Recolha de dados e escolhendo uma conta de armazenamento por região é configurado ao nível da subscrição.

Selecione a **política de prevenção** (consultar acima) para abrir a **política de prevenção de** pá. **Mostrar recomendações para** permite-lhe escolher os controlos de segurança que pretende monitorizar e recomendar com base nas necessidades de segurança dos recursos dentro da subscrição.

Em seguida, selecione um grupo de recursos para ver os detalhes de política.

![Grupo de recursos de pá de política de segurança][4]

**Herança** (consultar acima) permite-lhe definir o grupo de recursos, como:

- Herdado (predefinição) que significa que todas as políticas de segurança para este grupo de recursos são herdadas do nível de subscrição.
- Exclusivo que significa que o grupo de recursos terá uma política de segurança personalizada. Terá de efetuar alterações em **Mostrar recomendações para**.

> [AZURE.NOTE] Se existir um conflito entre política de nível de subscrição e a política de nível de grupo de recursos, a política de nível de grupo de recursos terá precedência.

### <a name="security-recommendations"></a>Recomendações de segurança

 Centro de segurança analisa o estado de segurança dos seus recursos Azure para identificar potenciais vulnerabilidade de segurança. Uma lista de recomendações orienta-o processo de configuração controlos necessários. Alguns exemplos incluem:

- Aprovisionamento antimalware para ajudar a identificar e remover software malicioso
- Configurar regras para o tráfego de controlo para máquinas virtuais e grupos de segurança de rede
- Aprovisionamento de firewalls de aplicação web para o ajudar a proteger-se contra ataques esse destino aplicações web
- Implementar actualizações do sistema em falta
- Endereçar configurações do sistema operativo que não correspondem os planos base recomendados

Clique no mosaico de **recomendações** para obter uma lista das recomendações. Clique em cada recomendação para ver informações adicionais ou tomar medidas para resolver o problema.

![Recomendações de segurança no Centro de segurança do Azure][5]

### <a name="resource-health"></a>Estado de funcionamento do recurso

O mosaico do **Estado de funcionamento do recurso segurança** mostra a geral postura de segurança do ambiente por tipo de recurso, incluindo máquinas virtuais, aplicações web e outros recursos.   

Selecione um tipo de recurso no mosaico do **Estado de funcionamento de segurança de recursos** para ver mais informações, incluindo uma lista de qualquer potenciais vulnerabilidade de segurança que tenham sido identificadas. (**Máquinas virtuais** está selecionado no exemplo abaixo.)

![Mosaico do Estado de funcionamento de recursos][6]

### <a name="security-alerts"></a>Alertas de segurança

 Centro de segurança recolhe automaticamente, analisa e integra-se os dados do registo os recursos do Azure, da rede e soluções de parceiros, como antimalware programas e firewalls. Quando são detectadas ameaças, um alerta de segurança é criado. Exemplos de deteção de:

- Comprometida máquinas virtuais comunicar com endereços IP maliciosos conhecidos
- Software maligno avançado detetado utilizando o relatório de erros do Windows
- Força bruta ataques máquinas virtuais
- Alertas de segurança a partir de programas de antimalware integrada e firewalls

Clicar no mosaico de **alertas de segurança** apresenta uma lista de alertas com prioridade alta.

![Alertas de segurança][7]

Selecionar um alerta apresenta mais informações sobre o ataque e sugestões sobre como solucionar uma-lo.

![Detalhes do alerta de segurança][8]

### <a name="partner-solutions"></a>Soluções de parceiros

O mosaico de **soluções de parceiros** permite monitor rapidamente o estado de estado de funcionamento do seu soluções de parceiros integrado com a sua subscrição Azure. Centro de segurança apresenta alertas provenientes das soluções.

Selecione o mosaico de **soluções de parceiros** . Uma pá é aberta apresenta uma lista de todas as soluções de parceiros ligada.

![Soluções de parceiros][9]

## <a name="get-started"></a>Introdução
Para começar a trabalhar com o Centro de segurança, precisa de uma subscrição do Microsoft Azure. Centro de segurança é activado com a sua subscrição Azure. Se não tiver uma subscrição, pode inscrever-se para uma [versão de avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/).

 Aceder ao centro de segurança a partir do [Azure portal](https://azure.microsoft.com/features/azure-portal/). Consulte a [documentação portal](https://azure.microsoft.com/documentation/services/azure-portal/) para obter mais informações.

[Introdução ao centro de segurança do Azure](security-center-get-started.md) rapidamente guias de introdução que através da monitorização de segurança e os componentes de gestão de políticas do Centro de segurança.

## <a name="see-also"></a>Consulte também
Neste documento, foram introduzidas para o Centro de segurança, das suas capacidades chaves e como começar. Para saber mais, consulte o seguinte:

- [Definir políticas de segurança no Centro de segurança do Azure](security-center-policies.md) — Saiba como configurar políticas de segurança do seu subscrições Azure e grupos de recursos.
- [Gerir as recomendações de segurança no Centro de segurança do Azure](security-center-recommendations.md) — Saiba como recomendações ajudam a proteger os recursos do Azure.
- [Estado de funcionamento de segurança no Centro de segurança do Azure de monitorização](security-center-monitoring.md) — Saiba como monitorizar o estado de funcionamento dos seus recursos Azure.
- [Gerir e a responder a alertas de segurança no Centro de segurança do Azure](security-center-managing-and-responding-alerts.md) — Saiba como gerir e responder a alertas de segurança.
- [Monitorização soluções de parceiros com o Centro de segurança do Azure](security-center-partner-solutions.md) — Saiba como monitorizar o estado de funcionamento de soluções de parceiros.
- [Perguntas mais frequentes do Centro de segurança do Azure](security-center-faq.md) — localizar perguntas mais frequentes sobre como utilizar o serviço.
- [Blogue do Azure Security](http://blogs.msdn.com/b/azuresecurity/) — obter as notícias mais recentes do Azure segurança e informações.

<!--Image references-->
[1]: ./media/security-center-intro/security-tile.png
[2]: ./media/security-center-intro/security-center.png
[3]: ./media/security-center-intro/security-policy.png
[4]: ./media/security-center-intro/security-policy-blade.png
[5]: ./media/security-center-intro/recommendations.png
[6]: ./media/security-center-intro/resources-health.png
[7]: ./media/security-center-intro/security-alert.png
[8]: ./media/security-center-intro/security-alert-detail.png
[9]: ./media/security-center-intro/partner-solutions.png
