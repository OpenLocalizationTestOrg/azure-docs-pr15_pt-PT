<properties
   pageTitle="Solucionar uma vulnerabilidades SO no Centro de segurança do Azure | Microsoft Azure"
   description="Este documento mostra-lhe como implementar o recomendação do Centro de segurança do Azure **vulnerabilidades solucionar uma SO**."
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
   ms.date="10/17/2016"
   ms.author="terrylan"/>

# <a name="remediate-os-vulnerabilities-in-azure-security-center"></a>Solucionar uma vulnerabilidades SO no Centro de segurança do Azure

Centro de segurança do Azure diariamente analisa o sistema operativo de máquina virtual (VM) (SO) para configurações que podem fazer a VM mais vulnerável a ataques e recomenda as alterações de configuração resolva este vulnerabilidade. Para mais informações sobre as configurações específicas que está a ser monitorizadas, consulte a [lista de regras de configuração recomendada](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335). Centro de segurança recomenda que resolvem vulnerabilidades quando a configuração do seu VM SO não corresponder às regras de configuração recomendada.

> [AZURE.NOTE] Este documento apresenta o serviço utilizando uma implementação de exemplo.  Não se trata de um guia passo a passo.

## <a name="implement-the-recommendation"></a>Implementar a recomendação

1. Na pá **recomendações** , selecione **vulnerabilidades solucionar uma SO**.
![Solucionar uma vulnerabilidades SO][1]

    O **sistema operativo solucionar uma vulnerabilidades** pá abre e listas seu VMs com configurações do sistema operativo que não correspondem as regras de configuração recomendada.  Para cada VM, identifica o pá:

   - **Ocorreu uma falha regras** – o número de regras falha na configuração do sistema operativo a VM.
   - **Tempo de pesquisa última** – a data e hora em que o Centro de segurança última, verifique a configuração do SO a VM.
   - **Estado** – o estado atual da vulnerabilidade:

        - Abrir: A vulnerabilidade não foi corrigida ainda
        - Em curso: A vulnerabilidade está atualmente a ser aplicada, não é necessária nenhuma acção por si
        - Resolvido: A vulnerabilidade foi já foram resolvida (quando o problema foi resolvido, a entrada a cinzento)
  - **GRAVIDADE** – todas as vulnerabilidades estão definidas para uma gravidade dos mínimo, o que significa que um vulnerabilidade deverão ser resolvida, mas não necessita de atenção imediata.

Selecione uma VM. Uma pá para esse VM abre e apresenta as regras que tem ocorrido uma falha.
   ![Regras de configuração tem ocorrido uma falha][2]

Selecione uma regra. Neste exemplo, permite-selecione a **palavra-passe deve cumprir os requisitos de complexidade**. Uma pá abre-se de que descreva a falha na regra e o impacto. Reveja os detalhes e considere como configurações do sistema operativo serão aplicadas.
  ![Descrição da regra falha][3]

  Centro de segurança utiliza comuns configuração enumeração (CCE) para atribuir identificadores exclusivos para as regras de configuração. As seguintes informações são fornecidas neste pá:

  - NOME – Nome da regra
  - GRAVIDADE – CCE gravidade valor crítica, importante ou aviso
  - CCIED – Identificador exclusivo de CCE para a regra
  - Descrição – Descrição da regra
  - VULNERABILIDADE – Explicação sobre Vulnerabilidade ou risco se regra não é aplicada
  - IMPACTO – Um impacto comercial quando regra é aplicada
  - VALOR do ESPERADO – Valor esperado quando o Centro de segurança analisa a sua configuração VM SO contra a regra
  - OPERAÇÃO regra – Operação de regra utilizada pelo centro de segurança durante a análise de configuração do seu VM SO contra a regra
  - VALOR real-- Devolvido o valor após uma análise de configuração do seu VM SO contra a regra
  - RESULTADO da avaliação –-resultado da análise: passar, falhas


## <a name="see-also"></a>Consulte também

Este artigo mostrava como implementar o recomendação do Centro de segurança "Remediate SO vulnerabilidades." Pode rever o conjunto de regras de configuração [aqui](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335). Centro de segurança utiliza CCE (comuns de configuração de enumeração) para atribuir identificadores exclusivos para as regras de configuração. Visite o site [CCE](http://cce.mitre.org) para obter mais informações.

Para saber mais sobre o Centro de segurança, consulte o seguinte:

- [Definir políticas de segurança no Centro de segurança do Azure](security-center-policies.md) – Saiba como configurar políticas de segurança do seu subscrições Azure e grupos de recursos.
- [Gerir as recomendações de segurança no Centro de segurança do Azure](security-center-recommendations.md) – Saiba como recomendações ajudam a proteger os recursos do Azure.
- [Monitorização de estado de funcionamento de segurança no Centro de segurança do Azure](security-center-monitoring.md) – Saiba como monitorizar o estado de funcionamento dos seus recursos Azure.
- [Gerir e a responder a alertas de segurança no Centro de segurança do Azure](security-center-managing-and-responding-alerts.md) – Saiba como gerir e responder a alertas de segurança.
- [Soluções de parceiros de monitorização com o Centro de segurança do Azure](security-center-partner-solutions.md) – Saiba como monitorizar o estado de funcionamento de soluções de parceiros.
- [Perguntas mais frequentes do Centro de segurança do azure](security-center-faq.md) localizar perguntas mais frequentes sobre como utilizar o serviço.
- Mensagens de [blogue do azure Security](http://blogs.msdn.com/b/azuresecurity/) – localizar blogue sobre Azure segurança e conformidade.

<!--Image references-->
[1]: ./media/security-center-remediate-os-vulnerabilities/recommendation.png
[2]:./media/security-center-remediate-os-vulnerabilities/vm-remediate-os-vulnerabilities.png
[3]: ./media/security-center-remediate-os-vulnerabilities/vulnerability-details.png
