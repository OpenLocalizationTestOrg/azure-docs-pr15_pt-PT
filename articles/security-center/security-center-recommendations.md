<properties
   pageTitle="Gerir as recomendações de segurança no Centro de segurança do Azure | Microsoft Azure"
   description="Este documento irá guiar pelo como recomendações no Centro de segurança do Azure ajudam a proteger os recursos do Azure e manter-se em conformidade com as políticas de segurança."
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
   ms.date="09/25/2016"
   ms.author="terrylan"/>

# <a name="managing-security-recommendations-in-azure-security-center"></a>Gerir as recomendações de segurança no Centro de segurança do Azure

Este documento explica como utilizar recomendações no Centro de segurança do Azure para ajudar a proteger os recursos do Azure.

> [AZURE.NOTE] Este documento apresenta o serviço utilizando uma implementação de exemplo.  Não se trata de um guia passo a passo.

## <a name="what-are-security-recommendations"></a>Quais são as recomendações de segurança?
Centro de segurança periodicamente analisa o estado de segurança dos seus recursos Azure. Quando o Centro de segurança identifica potenciais vulnerabilidade de segurança, cria recomendações. As recomendações orientá-lo durante o processo de configuração de controlos necessários.

## <a name="implementing-security-recommendations"></a>Recomendações de segurança de execução

### <a name="set-recommendations"></a>Conjunto de recomendações

[Definição de políticas de segurança no Centro de segurança do Azure](security-center-policies.md), saiba como:

- Configure políticas de segurança.
- Ative a recolha de dados.
- Escolha quais as recomendações para ver como parte da sua política de segurança.

Política recomendações Centro actual à volta de atualizações de sistema, regras do plano base, antimalware programas, [grupos de segurança de rede](../virtual-network/virtual-networks-nsg.md) no sub-redes e interfaces de rede, auditoria de base de dados SQL, encriptação de dados transparente de base de dados do SQL e firewalls de aplicação web.  [Definir políticas de segurança](security-center-policies.md) fornece uma descrição de cada opção de recomendação.

### <a name="monitor-recommendations"></a>Recomendações monitor
Depois de definir uma política de segurança, o Centro de segurança analisa o estado de segurança dos seus recursos para identificar vulnerabilidade potencial. O mosaico de **recomendações** sobre o **Centro de segurança** pá permite-lhe saber o número total de recomendações identificadas por centro de segurança.

![Mosaico de recomendações][1]

Para ver os detalhes de cada recomendação:

1. Clique nas **recomendações de mosaico** num pá **Centro de segurança** . É aberta a pá **recomendações** .

As recomendações são apresentadas num formato de tabela em que cada linha representa um recomendação específica. As colunas de nesta tabela são:

- **Descrição**: explica a recomendação e o que precisa de ser executadas para endereçá-lo.
- **Recurso**: lista os recursos ao qual se aplica esta recomendação.
- **Estado**: descreve o estado atual da Recomendação:
    - **Abrir**: recomendação ainda não foi corrigida.
    - **Em curso**: recomendação está atualmente a ser aplicada aos recursos e não é necessária nenhuma acção por si.
    - **Resolvido**: recomendação já foi concluída (neste caso, a linha vai ser a cinzento).
- **GRAVIDADE**: descreve gravidade dessa recomendação específica:
    - **Alto**: uma vulnerabilidade existe com um recurso significativo (tal como uma aplicação, uma VM ou um grupo de segurança de rede) e necessita de atenção.
    - **Média**: existe uma vulnerabilidade e passos não críticos ou adicionais necessários para a eliminar ou para concluir um processo.
    - **Baixa**: existe uma vulnerabilidade que devem ser dirigidos mas não necessita de atenção imediata. (Por predefinição, não são apresentadas recomendações baixos, mas pode filtrar recomendações baixos se pretender vê-los.)

Utilize a tabela abaixo como uma referência para ajudá-lo a compreender as recomendações disponíveis e o que cada um deles irá fazer se aplicá-lo.

> [AZURE.NOTE] Pretende que entender os [clássica e modelos de implementação do Gestor de recursos](../azure-classic-rm.md) para recursos Azure.

|Recomendação|Descrição|
|-----|-----|
|[Ativar a recolha de dados para subscrições](security-center-enable-data-collection.md)|Recomenda-se de que ativar recolha de dados na política de segurança para cada uma das suas subscrições e todas as máquinas virtuais (VMs) nas suas subscrições.|
|[Solucionar uma vulnerabilidades SO](security-center-remediate-os-vulnerabilities.md)|Recomenda que alinhar as configurações de SO com as regras de configuração recomendada, por exemplo, não permitem palavras-passe seja guardado.|
|[Aplicar atualizações de sistema](security-center-apply-system-updates.md)|Recomenda-se de que implementar em falta de sistema de segurança e atualizações críticas VMs.|
|[Reiniciar o computador após actualizações do sistema](security-center-apply-system-updates.md#reboot-after-system-updates)|Recomenda-se de que reiniciar uma VM para concluir o processo de aplicar atualizações de sistema.|
|[Adicionar uma firewall de aplicação web](security-center-add-web-application-firewall.md)|Recomenda-se de que implemente uma firewall de aplicação web (WAF) para os pontos finais de web. Pode proteger várias aplicações web no Centro de segurança através da adição destas aplicações para o seu implementações WAF existentes. Eletrodomésticos WAF (criados utilizando o modelo de implementação do Gestor de recursos) tem de ser implementada a uma rede virtual em separado. Eletrodomésticos WAF (criados utilizando o modelo de implementação clássico) estão limitados a utilizar um grupo de segurança de rede. Este suporte será expandido para uma implementação totalmente personalizada de um aparelho WAF (clássico) no futuro. Centro de segurança irá Recomendamos que aprovisionar um WAF para o ajudar a proteger-se contra ataques de filtragem de aplicações web no VMs e no ambiente de serviço de aplicação (Auxiliar). Para saber mais sobre Auxiliar, consulte a [Documentação do ambiente de serviço de aplicação](../app-service/app-service-app-service-environments-readme.md). |
|[Finalizar protecção da aplicação](security-center-add-web-application-firewall.md#finalize-application-protection)|Para concluir a configuração de um WAF, o tráfego tem de ser reencaminhado para aparelho WAF. Seguir esta recomendação irá concluir as alterações necessárias configuração.|
|[Adicionar uma Firewall de geração seguinte](security-center-add-next-generation-firewall.md)|Recomenda-se de que adicione uma Firewall de geração seguinte (NGFW) a partir de um parceiro da Microsoft para aumentar a sua protecção de segurança.|
|[Encaminhar o tráfego através do NGFW apenas](security-center-add-next-generation-firewall.md#route-traffic-through-ngfw-only)|Recomenda-se de que configurar regras de grupo (NSG) de segurança de rede forçar o tráfego de entrada para a VM através do seu NGFW.|
|[Instalar Endpoint Protection](security-center-install-endpoint-protection.md)|Recomenda-se de que aprovisionar programas antimalware VMs (apenas para o Windows VMs).|
|[Resolver alertas de estado de funcionamento Endpoint Protection](security-center-resolve-endpoint-protection-health-alerts.md)|Recomenda-se de que resolver falhas de proteção de ponto final.|
|[Ativar o grupos de segurança de rede sub-redes ou máquinas virtuais](security-center-enable-network-security-groups.md)|Recomenda-se de que ativar NSGs num sub-redes ou VMs.|
|[Restringir o acesso através da Internet opostas ponto final](security-center-restrict-access-through-internet-facing-endpoints.md)|Recomenda-se de que configurar regras de tráfego de entrada para NSGs.|
|[Activar o servidor de auditoria de SQL](security-center-enable-auditing-on-sql-servers.md)|Recomenda-se de que ativar auditoria para servidores do Azure SQL (serviço do Azure SQL; não incluir apenas SQL em execução no seu máquinas virtuais).|
|[Ativar a base de dados SQL de auditoria](security-center-enable-auditing-on-sql-databases.md)|Recomenda-se de que ativar auditoria para bases de dados do Azure SQL (serviço do Azure SQL; não incluir apenas SQL em execução no seu máquinas virtuais).|
|[Activar a encriptação de dados transparente no bases de dados SQL](security-center-enable-transparent-data-encryption.md)|Recomenda-se de que ativar encriptação para bases de dados do SQL (apenas para serviço do Azure SQL).|
|[Activar VM agente](security-center-enable-vm-agent.md)|Permite-lhe ver quais VMs requerem o agente VM. O agente VM tem de ser instalado no VMs para aprovisionar patches a digitalização do plano base a digitalização e antimalware programas. O agente VM está instalado por predefinição para VMs que são implementadas do Azure Marketplace. O artigo [VM agente e extensões – parte 2](http://azure.microsoft.com/blog/2014/04/15/vm-agent-and-extensions-part-2/) fornece informações sobre como instalar o agente VM.|
| [Aplicar a encriptação do disco](security-center-apply-disk-encryption.md) |Recomenda-se de que encriptar seus discos VM utilizar encriptação de disco do Azure (Windows e Linux VMs). Encriptação é recomendada para os volumes o sistema operativo e os dados no seu VM.|
|[Fornecer detalhes de contacto de segurança](security-center-provide-security-contact-details.md) | Recomenda que forneça segurança informações de contacto para cada uma das suas subscrições. Informações de contacto são um mensagem de correio electrónico endereço e número de telefone. As informações serão utilizadas para contactá-lo se encontrar erros a nossa equipa de segurança que os recursos são comprometidos. |
| [Atualizar a versão do SO](security-center-update-os-version.md) | Recomenda-se de que atualiza a versão do sistema operativo (OS) do seu serviço de nuvem para a versão mais recente disponível para a sua família SO.  Para saber mais sobre os serviços em nuvem, consulte o artigo [Descrição geral de serviços em nuvem](../cloud-services/cloud-services-choose-me.md). |
| [Avaliação vulnerabilidade não instalada](security-center-vulnerability-assessment-recommendations.md) | Recomenda-se de que instalar uma solução de avaliação de vulnerabilidade no seu VM. |
| [Solucionar uma vulnerabilidades](security-center-vulnerability-assessment-recommendations.md#review-recommendation) | Permite-lhe ver sistema e de aplicações vulnerabilidades detectadas pela solução de avaliação vulnerabilidade instalada no seu VM. |

Pode filtrar e dispensar recomendações.

1. Clique em **Filtrar** a pá **recomendações** . O **filtro** pá abre e selecionar os valores de gravidade e Estado que pretende ver.

    ![Recomendações de filtro][2]

2. Se determinar que uma recomendação não é aplicável, pode dispensar a recomendação e, em seguida, filtrá-lo fora da sua vista. Existem duas formas para dispensar uma recomendação. Uma forma é para o botão direito do rato clique num item e, em seguida, selecione **dispensar**. A outra é Paire o cursor sobre um item, clique nas reticências que aparecem à direita e, em seguida, selecione **dispensar**. Pode ver recomendações despedidas clicando em **filtro**e, em seguida, selecionando **Dismissed**.

    ![Dispensar recomendação][3]

### <a name="apply-recommendations"></a>Aplicar as recomendações
Após rever todas as recomendações, decida que aquele que deve ser aplicada pela primeira vez. Recomendamos que utilize a gravidade tal como o parâmetro principal para avaliar quais recomendações deve ser aplicado pela primeira vez.

Na tabela de recomendações acima, selecione uma recomendação e percorra-lo como um exemplo de como aplicar uma recomendação.

## <a name="see-also"></a>Consulte também
Neste documento, foram introduzidas para recomendações de segurança no Centro de segurança. Para saber mais sobre o Centro de segurança, consulte o seguinte:

- [Definir políticas de segurança no Centro de segurança do Azure](security-center-policies.md) — Saiba como configurar políticas de segurança do seu subscrições Azure e grupos de recursos.
- [Estado de funcionamento de segurança no Centro de segurança do Azure de monitorização](security-center-monitoring.md) — Saiba como monitorizar o estado de funcionamento dos seus recursos Azure.
- [Gerir e a responder a alertas de segurança no Centro de segurança do Azure](security-center-managing-and-responding-alerts.md) — Saiba como gerir e responder a alertas de segurança.
- [Monitorização soluções de parceiros com o Centro de segurança do Azure](security-center-partner-solutions.md) — Saiba como monitorizar o estado de funcionamento de soluções de parceiros.
- [Perguntas mais frequentes do Centro de segurança do Azure](security-center-faq.md) — localizar perguntas mais frequentes sobre como utilizar o serviço.
- [Blogue do Azure Security](http://blogs.msdn.com/b/azuresecurity/) — localizar mensagens de blogue sobre Azure segurança e conformidade.

<!--Image references-->
[1]: ./media/security-center-recommendations/recommendations-tile.png
[2]: ./media/security-center-recommendations/filter-recommendations.png
[3]: ./media/security-center-recommendations/dismiss-recommendations.png
