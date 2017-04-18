<properties
   pageTitle="Preços do Centro de segurança | Microsoft Azure"
   description="Este artigo fornece informações sobre preços do Centro de segurança do Azure."
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
   ms.date="10/12/2016"
   ms.author="terrylan"/>

# <a name="azure-security-center-pricing"></a>Preços do Centro de segurança do Azure

Centro de segurança do Azure ajuda-o a evitar, detetar e responder a ameaças com maior visibilidade do e controlo sobre a segurança dos seus recursos Azure. Fornece a segurança integrada monitorização e gestão de políticas entre as subscrições do Azure, ajuda a Deteta ameaças caso contrário, aceda não ser e funciona com uma Ecossistema vasto de soluções de segurança.

## <a name="pricing-tiers"></a>Preços camadas

Centro de segurança é-lhe fornecido em duas camadas:

- A **camada gratuita** está ativada automaticamente em todas as subscrições do Azure. A camada gratuita fornece visibilidade do Estado de segurança dos seus recursos Azure, a política de segurança básica, a recomendações de segurança e a integração com segurança produtos e serviços de parceiros.
- A **camada padrão** adiciona capacidades de deteção de ameaça avançadas, incluindo intelligence ameaça, análise comportamento, deteção anomalia, incidentes de segurança e ameaça de relatórios de avaliação. Uma **versão de avaliação gratuita de 90 dias** está disponível para a camada padrão.

Para obter mais informações, consulte o Centro de segurança de [preços de página](https://azure.microsoft.com/pricing/details/security-center/).

> [AZURE.NOTE] Centro de segurança utiliza armazenamento Azure guardar dados de segurança gerados a partir da sua nós protegida. Custos associados Este armazenamento não são incluídos o preço do serviço e são cobrados separadamente normal [taxas de armazenamento Azure](https://azure.microsoft.com/pricing/details/storage/blobs/). Aplicadas taxas de armazenamento mesmo durante a versão de avaliação.

## <a name="try-standard-free-for-90-days"></a>Experimente padrão livre para cerca de 90 dias

Uma avaliação gratuita de 90 dias está disponível para a camada padrão. Para obter a versão de avaliação gratuita da camada padrão, selecione o mosaico de **política** na pá **Centro de segurança** . Selecione a subscrição à qual pretende atualizar para o padrão. No pá **política de segurança** , selecione a **camada de preços**. No pá **Escolher a comparar camada** , selecione **padrão – versão de avaliação gratuita**.

![Versão de avaliação gratuita][1]

No final de 90 dias, deverá escolher continuar a utilizar o serviço, podemos iniciará automaticamente a carregar para uma utilização.

## <a name="upgrade-to-standard"></a>Atualizar para o padrão

Atualizar para a camada padrão para adicionar deteção ameaça avançadas. Para obter a camada padrão, selecione o mosaico de **política** na pá **Centro de segurança** . Selecione a subscrição à qual pretende atualizar para o padrão. No pá **política de segurança** , selecione a **camada de preços**. No pá **Escolher a comparar camada** , selecione **padrão**.

![Camada padrão][2]

## <a name="why-upgrade-to-standard"></a>Por que motivo atualizar para o padrão?

A camada padrão do Centro de segurança de fornece todas as funcionalidades da camada gratuita juntamente com deteção ameaça avançadas. Deteção de ameaça Avançadas ajuda a identificar ameaças ativas filtragem os recursos do Azure e fornece-lhe as informações necessárias para responder rapidamente.

Centro de segurança utiliza a análise de segurança avançadas, que vão extremidade para além das abordagens baseado em assinaturas. Descobertas no big data e tecnologias de aprendizagem automática são utilizadas para avaliar eventos através de ferro a nuvem inteira – detetar ameaças que estaria impossibilita identificar utilizar abordagens manuais e as previsões evolução de ataques.

Análise de segurança que estão incluídos a camada padrão é:

- **Análise de ameaça** - procura conhecidos bad intervenientes utilizando intelligence global ameaça de produtos Microsoft e serviços, a unidade Crimes Digital Microsoft, o Microsoft Security Response Center e feeds externos
- **Análise comportamento** - aplica-se conhecidos padrões para descobrir o comportamento malicioso
- **Deteção de anomalia** - utiliza perfis estatística para criar um histórico do plano base. Apresenta um alerta no desvios estabelecida planos base que está em conformidade com um potencial vector de ataque

No pá **alertas de segurança** abaixo, o Centro de segurança detetou um **incidente**de segurança. Um incidente de segurança é uma agregação de todos os alertas de um recurso alinhar com kill padrões de cadeia. Selecionar o incidente de segurança, são mais detalhes sobre o incidente de listas e os alertas relacionados. Selecionar um alerta fornece mais informações sobre essa ocorrência.

![Incidente de segurança][3]

O alerta de **comunicação de rede** abaixo fornece detalhes sobre o alerta. Os detalhes de incluir a sua descrição completa, gravidade, seu estado atual (que neste caso é fechado, que significa que o utilizador tenha apontado ação para a dispensar), o recurso intruso e passos remediação. Também existe uma lista de ligações para os relatórios de informações da empresa ameaça de Microsoft. Estes relatórios podem ser utilizados para fins de defesa e remediação de segurança.

![Detalhes do alerta de segurança][4]

## <a name="enable-data-collection"></a>Ativar a recolha de dados

Para permitir a análise de comportamento máquina virtual, recolha de dados tem de estar ligada. Poderá ter de ativar a recolha de dados quando pela primeira vez aceder ao centro de segurança ou quando estiver a criar uma política de segurança.

Para validar de recolha de dados está ativada, selecione o mosaico de **política** . A **política de segurança** pá abre listar as suas subscrições Azure. Selecione uma subscrição. Se a **recolha de dados** está desativada, alterá-la para ativado e guardar a alteração. Consulte o artigo [Ativar recolha de dados no Centro de segurança do Azure](security-center-enable-data-collection.md).

## <a name="next-steps"></a>Próximos passos

- Neste documento, foram introduzidas para preços do Centro de segurança. Para informações sobre preços adicional, consulte o Centro de segurança de [preços de página](https://azure.microsoft.com/pricing/details/security-center/).
- Para saber mais sobre as capacidades de detecção avançada do Centro de segurança, consulte o artigo [funcionalidades de detecção do Centro de segurança do Azure](security-center-detection-capabilities.md).
- Se tiver questões sobre como utilizar o Centro de segurança, consulte as [Perguntas mais frequentes do Centro de segurança do Azure](security-center-faq.md).
- Se ainda tiver questões sobre como utilizar o Centro de segurança ou Azure, visite os [Fóruns do Azure](https://social.msdn.microsoft.com/Forums/home?forum=AzureSecurityCenter&filter=alltypes&sort=lastpostdesc).

<!--Image references-->
[1]: ./media/security-center-pricing/free-trial.png
[2]: ./media/security-center-pricing/standard.png
[3]: ./media/security-center-pricing/incident.png
[4]: ./media/security-center-pricing/network-alert.png
