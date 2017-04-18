<properties
   pageTitle="Utilizar o Centro de segurança do Azure para uma resposta a incidentes | Microsoft Azure"
   description="Este documento explica como utilizar o Centro de segurança do Azure para um cenário de resposta incidente."
   services="security-center"
   documentationCenter="na"
   authors="YuriDio"
   manager="swadhwa"
   editor=""/>

<tags
   ms.service="security-center"
   ms.topic="hero-article"
   ms.devlang="na"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/20/2016"
   ms.author="yurid"/>

# <a name="using-azure-security-center-for-an-incident-response"></a>Utilizar o Centro de segurança do Azure para uma resposta a incidentes
Muitas organizações obter informações sobre como responder a incidentes de segurança apenas depois de sofrer um ataque. Para reduzir custos e danos, é importante ter uma resposta a incidentes planear num local antes de um ataque ocorre o mais. Pode utilizar o Centro de segurança do Azure em diferentes fases de uma resposta incidente.

## <a name="incident-response-planning"></a>Planeamento do incidente resposta

Um plano do eficaz depende principais capacidades de três: ser capaz de proteger, detetar e responder a ameaças. Proteção é sobre como prevenir incidentes, deteção é sobre como identificar ameaças mais cedo e resposta é sobre expulsar intruso e restaurar sistemas para mitigar como as consequências decorrentes violação de.

Este artigo irá utilizar as fases de resposta a incidentes de segurança do artigo [Resposta de segurança do Microsoft Azure na nuvem](https://gallery.technet.microsoft.com/Azure-Security-Response-in-dd18c678) , conforme mostrado no diagrama seguinte:

![Ciclo de vida do incidente de resposta](./media/security-center-incident-response/security-center-incident-response-fig1.png)

Pode utilizar o Centro de segurança durante as fases detetar, avaliar e diagnosticar. Eis alguns exemplos de como o Centro de segurança podem ser úteis durante as três fases de resposta incidente inicial:

- **Detetar**: rever a primeira indicação de um inquérito do evento.
    - Exemplo: rever a verificação inicial que um alerta de segurança de alta prioridade foi elevado no dashboard do Centro de segurança.
- **Avaliar**: executar a avaliação inicial para obter mais informações sobre a mesma suspeita.
    - Exemplo: obter mais informações sobre o alerta de segurança.
- **Diagnosticar**: realizar um inquérito técnico e identificar estratégias de retenção, mitigação e solução.
    - Exemplo: Siga os passos de remediação descritos pelo centro de segurança nesse alerta de segurança específico.

O cenário que se segue mostra como tirar partido do Centro de segurança durante as fases detetar, avaliar e diagnosticar/responder de um incidente de segurança. No Centro de segurança, um [incidente de segurança](security-center-incident.md) é uma agregação de todos os alertas de um recurso alinhar com padrões [Eliminar cadeia](https://blogs.technet.microsoft.com/office365security/addressing-your-cxos-top-five-cloud-security-concerns/) . Incidentes apresentado no mosaico de [alertas de segurança](security-center-managing-and-responding-alerts.md) e pá. Um incidente, são a lista de alertas relacionadas, que permite-lhe obter mais informações sobre cada ocorrência. Centro de segurança apresenta também autónomo alertas de segurança podem também ser utilizados para controlar uma atividade suspeita.

## <a name="scenario"></a>Cenário

Contoso recentemente migrado alguns dos seus recursos no local para o Azure, incluindo alguns com base em máquina virtual cargas de trabalho de linha de negócio e bases de dados SQL. Atualmente, Contoso Core computador segurança incidente de resposta da equipa (CSIRT) tem um problema a investigar problemas de segurança devido a informações da empresa de segurança não integrada com as ferramentas de resposta incidente atual. Esta falta de integração apresenta um problema durante a fase de detetar (falsos demasiadas) e durante as fases avaliar e diagnosticar. Como parte desta migração, eles decidiram optar ativamente por participar para o Centro de segurança para o ajudar a resolver este problema.

Primeira fase desta migração terminar após estes onboarded todos os recursos e endereçada todas as recomendações de segurança a partir do Centro de segurança. Contoso CSIRT é o ponto de contacto para lidar com incidentes de segurança do computador. A equipa é composta por um grupo de pessoas com as responsabilidades lidar com qualquer incidente de segurança. Os membros da equipa tem claramente definidos direitos para se certificar de que nenhuma área da resposta fica descoberto.

Para este cenário, iremos concentrar-se nas funções das seguintes pessoas que fazem parte da Contoso CSIRT:

![Ciclo de vida do incidente de resposta](./media/security-center-incident-response/security-center-incident-response-fig2.png)

É Constança em operações de segurança. Responsabilidades típicas do seu incluem:

- A monitorização e responder a ameaças de segurança cerca a hora do dia.
- Crescentes ao proprietário de carga de trabalho de nuvem ou analista de segurança, conforme necessário.

SAM é um analista de segurança e as suas responsabilidades incluem:

- Investigar ataques.
- Alertas para baixo.
- Trabalhar com os proprietários de carga de trabalho para determinar e aplicar atenuações.

Como pode ver, Constança e Sam têm diferentes responsabilidades e tem trabalhar em conjunto para partilhar informações do Centro de segurança.

## <a name="recommended-solution"></a>Solução recomendada

Uma vez que Constança e Sam tiverem diferentes funções, estes irá utilizar diferentes áreas do Centro de segurança para obter informações relevantes para as respetivas atividades diárias. Constança irá utilizar **os alertas de segurança** como parte do seu monitorização diária.

![Alertas de segurança](./media/security-center-incident-response/security-center-incident-response-fig3.png)

Constança irá utilizar os alertas de segurança durante as fases detetar e avaliar. Depois de Constança termina a avaliação inicial, posteriormente, poderá passar o problema Sam se for necessário inquérito adicional. Neste momento, Sam irá utilizar as informações que lhe foram fornecidas pelo centro de segurança, por vezes em conjunto com outras origens de dados, para mover para a fase diagnosticar.


## <a name="how-to-implement-this-solution"></a>Como implementar esta solução

Para ver como utilizaria o Centro de segurança do Azure num cenário de resposta incidente, vamos seguir passos de Constança nas fases detetar e avaliar e, em seguida, consulte o artigo o que faz Sam a diagnosticar o problema.

### <a name="detect-and-assess-incident-response-stages"></a>Detetar e avaliar fases do incidente de resposta

Constança iniciou sessão no portal do Azure e está a funcionar na consola do Centro de segurança. Como parte do seu diária actividades de monitorização, posteriormente, ao rever os alertas de segurança de alta prioridade, executando os seguintes passos:

1. Clique no mosaico de **alertas de segurança** e aceder a pá **alertas de segurança** .
    ![Pá de alerta de segurança](./media/security-center-incident-response/security-center-incident-response-fig4.png)

    > [AZURE.NOTE] Para este cenário Constança vai executar uma avaliação no alerta de atividade de SQL maliciosos, conforme apresentado na figura anterior.
2. Clique no alerta de **atividade de SQL malicioso** e rever os recursos intruso no pá a **atividade de SQL malicioso** :  ![detalhes do incidente](./media/security-center-incident-response/security-center-incident-response-fig5.png)

    Neste pá, Constança pode tomar notas em relação aos recursos intruso, como número de vezes que este ataque aconteceu e, quando foi detetado.
3. Clique na **ataques recurso** para obter mais informações sobre este ataque.

Depois de ler a descrição, Constança autorizada que esta não é um falso positivo e que ela deve passar neste caso para Sam.

### <a name="diagnose-incident-response-stage"></a>Diagnosticar fase do incidente de resposta

SAM recebe as maiúsculas/minúsculas de Constança e inicia a rever os passos de remediação sugerido do Centro de segurança.

![Ciclo de vida do incidente de resposta](./media/security-center-incident-response/security-center-incident-response-fig6.png)

### <a name="additional-resources"></a>Recursos adicionais

A equipa de resposta incidente também pode tirar partido da funcionalidade de [Segurança do Centro de Power BI](security-center-powerbi.md) para ver diferentes tipos de relatórios. Estes relatórios ajudam-las durante a aprofundar para visualizar, analisar e filtrar recomendações e alertas de segurança. Para empresas que utilizam as respetivas informações de segurança e a solução de gestão (SIEM) do evento durante o processo de inquérito, estes também podem [integrar o Centro de segurança com a sua solução](security-center-integrating-alerts-with-log-integration.md). Também pode integrar o registos de auditoria Azure e eventos de segurança de máquina virtual (VM) utilizando a [ferramenta de integração de registo Azure](https://blogs.msdn.microsoft.com/azuresecurity/2016/07/21/microsoft-azure-log-integration-preview/). Para investigar ataque, pode utilizar estas informações em conjunto com as informações que fornece o Centro de segurança.


## <a name="conclusion"></a>Conclusão

Montagem uma equipa antes de um incidente de ocorre é muito importante para a sua organização e positiva irá influenciar incidentes de que forma são processados. Está com dificuldades ferramentas certas para monitorizar recursos pode ajudar a esta equipa de efetuar passos precisos para solucionar uma um incidente de segurança. Pode dar assistência a do Centro de segurança [capacidades de detecção](security-center-detection-capabilities.md) IT para responder a incidentes de segurança e solucionar uma problemas de segurança rapidamente.
